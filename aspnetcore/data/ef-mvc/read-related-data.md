---
title: 'Didacticiel : lire les données associées-ASP.NET MVC avec EF Core'
description: Dans ce didacticiel, vous allez lire et afficher les données associées, à savoir les données qu’Entity Framework charge dans les propriétés de navigation.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 9ea3bd7aaa075ae4601af51c7cc90f28a884a096
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587890"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Didacticiel : lire les données associées-ASP.NET MVC avec EF Core

Dans le didacticiel précédent, vous a élaboré le modèle de données School. Dans ce didacticiel, vous allez lire et afficher les données associées, à savoir les données qu’Entity Framework charge dans les propriétés de navigation.

Les illustrations suivantes montrent les pages que vous allez utiliser.

![Page d’index des cours](read-related-data/_static/courses-index.png)

![Page d’index des formateurs](read-related-data/_static/instructors-index.png)

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Découvrir comment charger les données associées
> * Créer une page Courses
> * Créer une page Instructors
> * En savoir plus sur le chargement explicite

## <a name="prerequisites"></a>Prérequis

* [Créer un modèle de données complexe](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Découvrir comment charger les données associées

Il existe plusieurs façons de permettre à un logiciel de mappage relationnel objet (ORM) comme Entity Framework de charger les données associées dans les propriétés de navigation d’une entité :

* Chargement hâtif. Quand l’entité est lue, ses données associées sont également récupérées. Cela génère en général une requête de jointure unique qui récupère toutes les données nécessaires. Vous spécifiez un chargement hâtif dans Entity Framework Core à l’aide des méthodes `Include` et `ThenInclude`.

  ![Exemple de chargement hâtif](read-related-data/_static/eager-loading.png)

  Vous pouvez récupérer une partie des données dans des requêtes distinctes et EF « corrige » les propriétés de navigation.  Autrement dit, EF ajoute automatiquement les entités récupérées séparément là où elles doivent figurer dans les propriétés de navigation des entités précédemment récupérées. Pour la requête qui récupère les données associées, vous pouvez utiliser la méthode `Load` à la place d’une méthode renvoyant une liste ou un objet, telle que `ToList` ou `Single`.

  ![Exemple de requêtes distinctes](read-related-data/_static/separate-queries.png)

* Chargement explicite. Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées. Vous écrivez un code qui récupère les données associées si elles sont nécessaires. Comme dans le cas du chargement hâtif avec des requêtes distinctes, le chargement explicite génère plusieurs requêtes envoyées à la base de données. La différence tient au fait qu’avec le chargement explicite, le code spécifie les propriétés de navigation à charger. Dans Entity Framework Core 1.1, vous pouvez utiliser la méthode `Load` pour effectuer le chargement explicite. Par exemple :

  ![Exemple de chargement explicite](read-related-data/_static/explicit-loading.png)

* Chargement différé. Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées. Toutefois, la première fois que vous essayez d’accéder à une propriété de navigation, les données requises pour cette propriété de navigation sont récupérées automatiquement. Une requête est envoyée à la base de données chaque fois que vous essayez d’obtenir des données à partir d’une propriété de navigation pour la première fois. Entity Framework Core 1.0 ne prend pas en charge le chargement différé.

### <a name="performance-considerations"></a>Considérations relatives aux performances

Si vous savez que vous avez besoin des données associées pour toutes les entités récupérées, le chargement hâtif souvent offre des performances optimales, car une seule requête envoyée à la base de données est généralement plus efficace que les requêtes distinctes pour chaque entité récupérée. Par exemple, supposons que chaque département a dix cours associés. Le chargement hâtif de toutes les données associées générerait une seule requête (de jointure) et un seul aller-retour à la base de données. Une requête distincte pour les cours pour chaque département entraînerait onze allers-retours à la base de données. Les allers-retours supplémentaires à la base de données sont particulièrement nuisibles pour les performances lorsque la latence est élevée.

En revanche, dans certains scénarios, les requêtes distinctes s’avèrent plus efficaces. Le chargement hâtif de toutes les données associées dans une seule requête peut entraîner une jointure très complexe à générer, que SQL Server ne peut pas traiter efficacement. Ou, si vous avez besoin d’accéder aux propriétés de navigation d’entité uniquement pour un sous-ensemble des entités que vous traitez, des requêtes distinctes peuvent être plus performantes, car le chargement hâtif de tous les éléments en amont entraînerait la récupération de plus de données qu’il vous faut. Si les performances sont essentielles, il est préférable de tester les performances des deux façons afin d’effectuer le meilleur choix.

## <a name="create-a-courses-page"></a>Créer une page Courses

L’entité Course inclut une propriété de navigation qui contient l’entité Department du service auquel le cours est affecté. Pour afficher le nom du service affecté dans une liste de cours, vous devez obtenir la propriété Name de l’entité Department qui figure dans la propriété de navigation `Course.Department`.

Créez un contrôleur nommé CoursesController pour le type d’entité Course, en utilisant les mêmes options pour le générateur de modèles automatique **Contrôleur MVC avec vues, utilisant Entity Framework** que vous avez utilisées précédemment pour le contrôleur Students, comme indiqué dans l’illustration suivante :

![Ajouter un contrôleur Courses](read-related-data/_static/add-courses-controller.png)

Ouvrez *CoursesController.cs* et examinez la méthode `Index`. La génération de modèles automatique a spécifié un chargement hâtif pour la propriété de navigation `Department` à l’aide de la méthode `Include`.

Remplacez la méthode `Index` par le code suivant qui utilise un nom plus approprié pour `IQueryable` qui renvoie les entités Course (`courses` à la place de `schoolContext`) :

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Ouvrez *Views/Courses/Index.cshtml* et remplacez le code de modèle par le code suivant. Les modifications apparaissent en surbrillance :

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Vous avez apporté les modifications suivantes au code généré automatiquement :

* Changement de l’en-tête : Index a été remplacé par Courses.

* Ajout d’une colonne **Number** qui affiche la valeur de la propriété `CourseID`. Par défaut, les clés primaires ne sont pas générées automatiquement, car elles ne sont normalement pas significatives pour les utilisateurs finaux. Toutefois, dans le cas présent, la clé primaire est significative et vous voulez l’afficher.

* Modification de la colonne **Department** afin d’afficher le nom du département. Le code affiche la propriété `Name` de l’entité Department qui est chargée dans la propriété de navigation `Department` :

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Exécutez l’application et sélectionnez l’onglet **Courses** pour afficher la liste avec les noms des départements.

![Page d’index des cours](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Créer une page Instructors

Dans cette section, vous allez créer un contrôleur et une vue pour l’entité Instructor afin d’afficher la page Instructors :

![Page d’index des formateurs](read-related-data/_static/instructors-index.png)

Cette page lit et affiche les données associées comme suit :

* La liste des formateurs affiche les données associées de l’entité OfficeAssignment. Il existe une relation un-à-zéro-ou-un entre les entités Instructor et OfficeAssignment. Vous allez utiliser un chargement hâtif pour les entités OfficeAssignment. Comme expliqué précédemment, le chargement hâtif est généralement plus efficace lorsque vous avez besoin des données associées pour toutes les lignes extraites de la table primaire. Dans ce cas, vous souhaitez afficher les affectations de bureaux pour tous les formateurs affichés.

* Lorsque l’utilisateur sélectionne un formateur, les entités Course associées sont affichées. Il existe une relation plusieurs à plusieurs entre les entités Instructor et Course. Vous allez utiliser un chargement hâtif pour les entités Course et les entités Department qui leur sont associées. Dans ce cas, des requêtes distinctes peuvent être plus efficaces, car vous avez besoin de cours uniquement pour le formateur sélectionné. Toutefois, cet exemple montre comment utiliser le chargement hâtif pour des propriétés de navigation dans des entités qui se trouvent elles-mêmes dans des propriétés de navigation.

* Lorsque l’utilisateur sélectionne un cours, les données associées dans le jeu d’entités Enrollment s’affichent. Il existe une relation un-à-plusieurs entre les entités Course et Enrollment. Vous allez utiliser des requêtes distinctes pour les entités Enrollment et les entités Student qui leur sont associées.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Créer un modèle de vue pour la vue d’index des formateurs

La page Instructors affiche des données de trois tables différentes. Par conséquent, vous allez créer un modèle de vue qui comprend trois propriétés, chacune contenant les données d’une des tables.

Dans le dossier *SchoolViewModels*, créez *InstructorIndexData.cs* et remplacez le code existant par le code suivant :

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Créer les vues et le contrôleur de formateurs

Créez un contrôleur de formateurs avec des actions de lecture/écriture EF comme indiqué dans l’illustration suivante :

![Ajouter le contrôleur de formateurs](read-related-data/_static/add-instructors-controller.png)

Ouvrez *InstructorsController.cs* et ajoutez une instruction using pour l’espace de noms ViewModel :

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Remplacez la méthode Index par le code suivant pour effectuer un chargement hâtif des données associées et le placer dans le modèle de vue.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

La méthode accepte des données de route facultatives (`id`) et un paramètre de chaîne de requête (`courseID`) qui fournissent les valeurs d’ID du formateur sélectionné et du cours sélectionné. Ces paramètres sont fournis par les liens hypertexte **Select** dans la page.

Le code commence par créer une instance du modèle de vue et la placer dans la liste des formateurs. Le code spécifie un chargement hâtif pour les propriétés de navigation `Instructor.OfficeAssignment` et `Instructor.CourseAssignments`. Dans la propriété `CourseAssignments`, la propriété `Course` est chargée et, dans ce cadre, les propriétés `Enrollments` et `Department` sont chargées, et dans chaque entité `Enrollment`, la propriété `Student` est chargée.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Étant donné que la vue nécessite toujours l’entité OfficeAssignment, il est plus efficace de l’extraire dans la même requête. Les entités Course sont requises lorsqu’un formateur est sélectionné dans la page web, de sorte qu’une requête individuelle est meilleure que plusieurs requêtes seulement si la page s’affiche plus souvent avec un cours sélectionné que sans.

Le code répète `CourseAssignments` et `Course`, car vous avez besoin de deux propriétés de `Course`. La première chaîne d’appels `ThenInclude` obtient `CourseAssignment.Course`, `Course.Enrollments` et `Enrollment.Student`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

À ce stade dans le code, un autre `ThenInclude` serait pour les propriétés de navigation de `Student`, dont vous n’avez pas besoin. Toutefois, l’appel de `Include` recommence avec les propriétés `Instructor`, donc vous devez parcourir la chaîne à nouveau, cette fois en spécifiant `Course.Department` à la place de `Course.Enrollments`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Le code suivant s’exécute quand un formateur a été sélectionné. Le formateur sélectionné est récupéré à partir de la liste des formateurs dans le modèle d’affichage. La propriété `Courses` du modèle de vue est alors chargée avec les entités Course de la propriété de navigation `CourseAssignments` de ce formateur.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

La méthode `Where` renvoie une collection, mais dans ce cas, les critères transmis à cette méthode entraînent le renvoi d’une seule entité Instructor. La méthode `Single` convertit la collection en une seule entité Instructor, ce qui vous permet d’accéder à la propriété `CourseAssignments` de cette entité. La propriété `CourseAssignments` contient des entités `CourseAssignment`, à partir desquelles vous souhaitez uniquement les entités `Course` associées.

Vous utilisez la méthode `Single` sur une collection lorsque vous savez que la collection aura un seul élément. La méthode Single lève une exception si la collection transmise est vide ou s’il y a plusieurs éléments. Une alternative est `SingleOrDefault`, qui renvoie une valeur par défaut (Null dans ce cas) si la collection est vide. Toutefois, dans ce cas, cela entraînerait encore une exception (en tentant de trouver une propriété `Courses` sur une référence null) et le message d’exception indiquerait moins clairement la cause du problème. Lorsque vous appelez la méthode `Single`, vous pouvez également transmettre la condition Where au lieu d’appeler séparément la méthode `Where` :

```csharp
.Single(i => i.ID == id.Value)
```

À la place de :

```csharp
.Where(i => i.ID == id.Value).Single()
```

Ensuite, si un cours a été sélectionné, le cours sélectionné est récupéré à partir de la liste des cours dans le modèle de vue. Ensuite, la propriété `Enrollments` du modèle de vue est chargée avec les entités Enrollment à partir de la propriété de navigation `Enrollments` de ce cours.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Modifier la vue d’index des formateurs

Dans *Views/Instructors/Index.cshtml*, remplacez le code du modèle par le code suivant. Les modifications sont mises en surbrillance.

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

Vous avez apporté les modifications suivantes au code existant :

* Vous avez changé la classe de modèle en `InstructorIndexData`.

* Vous avez changé le titre de la page en remplaçant **Index** par **Instructors**.

* Il ajoute une colonne **Office** qui affiche `item.OfficeAssignment.Location` uniquement si `item.OfficeAssignment` n’est pas Null. (Comme il s’agit d’une relation un-à-zéro-ou-un, il se peut qu’il n’y ait pas d’entité OfficeAssignment associée.)

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Vous avez ajouté une colonne **Courses** qui affiche les cours animés par chaque formateur. Pour plus d’informations, consultez la section relative à la [transition de ligne explicite](xref:mvc/views/razor#explicit-line-transition) de l' Razor article de syntaxe.

* Ajout de code qui ajoute de manière conditionnelle une classe CSS bootstrap à l' `tr` élément de l’instructeur sélectionné. Cette classe définit une couleur d’arrière-plan pour la ligne sélectionnée.

* Vous avez ajouté un nouveau lien hypertexte étiqueté **Select** immédiatement avant les autres liens dans chaque ligne, ce qui entraîne l’envoi de l’ID du formateur sélectionné à la méthode `Index`.

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Exécutez l’application et sélectionnez l’onglet **Instructors** . La page affiche la propriété d’emplacement des entités OfficeAssignment associées et une cellule de table vide lorsqu’il n’y a pas d’entité OfficeAssignment associée.

![Page d’index des formateurs sans aucun élément sélectionné](read-related-data/_static/instructors-index-no-selection.png)

Dans le fichier *Views/Instructors/Index.cshtml*, après l’élément de fermeture de table (à la fin du fichier), ajoutez le code suivant. Ce code affiche la liste des cours associés à un formateur quand un formateur est sélectionné.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=63-98)]

Ce code lit la propriété `Courses` du modèle de vue pour afficher la liste des cours. Il fournit également un lien hypertexte **Select** qui envoie l’ID du cours sélectionné à la méthode d’action `Index`.

Actualisez la page et sélectionnez un formateur. Vous voyez à présent une grille qui affiche les cours affectés au formateur sélectionné et, pour chaque cours, vous voyez le nom du département affecté.

![Page d’index des formateurs avec un formateur sélectionné](read-related-data/_static/instructors-index-instructor-selected.png)

Après le bloc de code que vous venez d’ajouter, ajoutez le code suivant. Ceci affiche la liste des étudiants qui sont inscrits à un cours quand ce cours est sélectionné.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Ce code lit la propriété Enrollments du modèle de vue pour afficher la liste des étudiants inscrits dans ce cours.

Actualisez la page à nouveau et sélectionnez un formateur. Ensuite, sélectionnez un cours pour afficher la liste des étudiants inscrits et leurs notes.

![Page d’index des formateurs avec un formateur et un cours sélectionnés](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>À propos du chargement explicite

Lorsque vous avez récupéré la liste des formateurs dans *InstructorsController.cs*, vous avez spécifié un chargement hâtif pour la propriété de navigation `CourseAssignments`.

Supposons que vous vous attendiez à ce que les utilisateurs ne souhaitent que rarement voir les inscriptions pour un formateur et un cours sélectionnés. Dans ce cas, vous pouvez charger les données d’inscription uniquement si elles sont demandées. Pour voir un exemple illustrant comment effectuer un chargement explicite, remplacez la méthode `Index` par le code suivant, qui supprime le chargement hâtif pour Enrollments et charge explicitement cette propriété. Les modifications du code apparaissent en surbrillance.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Le nouveau code supprime les appels de la méthode *ThenInclude* pour les données d’inscription à partir du code qui extrait les entités de formateur. Il dépose également `AsNoTracking`.  Si un formateur et un cours sont sélectionnés, le code en surbrillance récupère les entités Enrollment pour le cours sélectionné et les entités Student pour chaque entité Enrollment.

Exécutez l’application, accédez à la page d’index des formateurs et vous ne verrez aucune différence pour ce qui est affiché dans la page, bien que vous ayez modifié la façon dont les données sont récupérées.

## <a name="get-the-code"></a>Obtenir le code

[Télécharger ou afficher l’application complète.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Chargement des données associées découvert
> * Page Courses créée
> * Page Instructors créée
> * Chargement explicite découvert

Passez au tutoriel suivant pour découvrir comment mettre à jour les données associées.

> [!div class="nextstepaction"]
> [Mettre à jour les données associées](update-related-data.md)
