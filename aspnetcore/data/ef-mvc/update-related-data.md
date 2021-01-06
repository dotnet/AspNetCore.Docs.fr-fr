---
title: 'Didacticiel : mettre à jour les données associées-ASP.NET MVC avec EF Core'
description: Dans ce didacticiel, vous mettez à jour des données associées en mettant à jour des champs de clé étrangère et des propriétés de navigation.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
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
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: f41d0b1d7ba46ef4867e0d160dc61f21042ce403
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053941"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Didacticiel : mettre à jour les données associées-ASP.NET MVC avec EF Core

Dans le didacticiel précédent, vous avez affiché des données associées ; dans ce didacticiel, vous mettez à jour des données associées en mettant à jour des champs de clé étrangère et des propriétés de navigation.

Les illustrations suivantes montrent quelques-unes des pages que vous allez utiliser.

![Page Edit pour les cours](update-related-data/_static/course-edit.png)

![Page Edit pour les formateurs](update-related-data/_static/instructor-edit-courses.png)

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Personnaliser les pages de cours
> * Ajouter une page de modification de formateur
> * Ajouter des cours à la page de modification
> * Mettre à jour la page Delete
> * Ajouter des emplacements de bureau et des cours à la page Create

## <a name="prerequisites"></a>Conditions préalables requises

* [Lire les données associées](read-related-data.md)

## <a name="customize-courses-pages"></a>Personnaliser les pages de cours

Quand une entité de cours est créée, elle doit avoir une relation à un département existant. Pour faciliter cela, le code du modèle généré automatiquement inclut des méthodes de contrôleur, et des vues Create et Edit qui incluent une liste déroulante pour sélectionner le département. La liste déroulante définit la propriété de clé étrangère `Course.DepartmentID`, qui est tout ce dont Entity Framework a besoin pour charger la propriété de navigation `Department` avec l’entité Department appropriée. Vous utilisez le code du modèle généré automatiquement, mais que vous modifiez un peu pour ajouter la gestion des erreurs et trier la liste déroulante.

Dans *CoursesController.cs*, supprimez les quatre méthodes Create et Edit, et remplacez-les par le code suivant :

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Après la méthode HttpPost `Edit`, créez une méthode qui charge les informations des départements pour la liste déroulante.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

La méthode `PopulateDepartmentsDropDownList` obtient une liste de tous les départements triés par nom, crée une collection `SelectList` pour une liste déroulante et passe la collection à la vue dans `ViewBag`. La méthode accepte le paramètre facultatif `selectedDepartment` qui permet au code appelant de spécifier l’élément sélectionné lors de l’affichage de la liste déroulante. La vue passe le nom « DepartmentID » pour le tag helper `<select>` : le helper peut alors rechercher dans l’objet `ViewBag` une `SelectList` nommée « DepartmentID ».

La méthode HttpGet `Create` appelle la méthode `PopulateDepartmentsDropDownList` sans définir l’élément sélectionné, car pour un nouveau cours, le département n’est pas encore établi :

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

La méthode HttpGet `Edit` définit l’élément sélectionné, en fonction de l’ID du département qui est déjà affecté au cours à modifier :

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Les méthodes HttpPost pour `Create` et pour `Edit` incluent également du code qui définit l’élément sélectionné quand elles réaffichent la page après une erreur. Ceci garantit que quand la page est réaffichée pour montrer le message d’erreur, le département qui a été sélectionné le reste.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Ajouter .AsNoTracking aux méthodes Details et Delete

Pour optimiser les performances des pages Details et Delete pour les cours, ajoutez des appels de `AsNoTracking` dans les méthodes `Details` et HttpGet `Delete`.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Modifier les vues des cours

Dans *Views/Courses/Create.cshtml*, ajoutez une option « Select Department » à la liste déroulante **Department**, changez la légende de **DepartmentID** en **Department** et ajoutez un message de validation.

[!code-cshtml[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

Dans *Views/Courses/Edit.cshtml*, faites les mêmes modifications pour le champ Department que ce que vous venez de faire dans *Create.cshtml*.

Également dans *Views/Courses/Edit.cshtml*, ajoutez un champ de numéro de cours avant le champ **Title**. Comme le numéro de cours est la clé primaire, il est affiché mais ne peut pas être modifié.

[!code-cshtml[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Il existe déjà un champ masqué (`<input type="hidden">`) pour le numéro de cours dans la vue Edit. L’ajout d’un tag helper `<label>` n’élimine la nécessité d’avoir le champ masqué, car cela n’a pas comme effet que le numéro de cours est inclut dans les données envoyées quand l’utilisateur clique sur **Save** dans la page **Edit**.

Dans *Views/Courses/Delete.cshtml*, ajoutez un champ pour le numéro de cours en haut et changez l’ID de département en nom de département.

[!code-cshtml[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

Dans *Views/Courses/Details.cshtml*, faites la même modification que celle que vous venez de faire pour *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Tester les pages de cours

Exécutez l’application, sélectionnez l’onglet **Courses**, cliquez sur **Create New** et entrez les données pour un nouveau cours :

![Page Create pour les cours](update-related-data/_static/course-create.png)

Cliquez sur **Créer**. La page Index des cours est affichée avec le nouveau cours ajouté à la liste. Le nom du département dans la liste de la page Index provient de la propriété de navigation, ce qui montre que la relation a été établie correctement.

Cliquez sur **Edit** pour un cours dans la page Index des cours.

![Page Edit pour les cours](update-related-data/_static/course-edit.png)

Modifiez les données dans la page et cliquez sur **Save**. La page Index des cours est affichée avec les données du cours mises à jour.

## <a name="add-instructors-edit-page"></a>Ajouter une page de modification de formateur

Quand vous modifiez un enregistrement de formateur, vous voulez avoir la possibilité de mettre à jour l’attribution du bureau du formateur. L’entité Instructor a une relation un-à-zéro ou un-à-un avec l’entité OfficeAssignment, ce qui signifie que votre code doit gérer les situations suivantes :

* Si l’utilisateur efface l’attribution du bureau et qu’il existait une valeur à l’origine, supprimez l’entité OfficeAssignment.

* Si l’utilisateur entre une attribution de bureau et qu’elle était vide à l’origine, créez une entité OfficeAssignment.

* Si l’utilisateur change la valeur d’une attribution de bureau, changez la valeur dans une entité OfficeAssignment existante.

### <a name="update-the-instructors-controller"></a>Mettre à jour le contrôleur Instructors

Dans *InstructorsController.cs*, modifiez le code de la méthode HttpGet `Edit` afin qu’elle charge la propriété de navigation `OfficeAssignment` de l’entité Instructor et appelle `AsNoTracking` :

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

Remplacez la méthode HttpPost `Edit` par le code suivant pour gérer les mises à jour des attributions de bureau :

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Le code effectue les actions suivantes :

* Il change le nom de la méthode `EditPost`, car la signature est maintenant la même que celle de la méthode HttpGet `Edit` (l’attribut `ActionName` spécifie que l’URL `/Edit/` est encore utilisée).

* Obtient l’entité Instructor actuelle auprès de la base de données en utilisant le chargement hâtif pour la propriété de navigation `OfficeAssignment`. C’est identique à ce que vous avez fait dans la méthode HttpGet `Edit`.

* Elle met à jour l’entité Instructor récupérée avec des valeurs dans le classeur de modèles. La `TryUpdateModel` surcharge vous permet de déclarer les propriétés que vous souhaitez inclure. Ceci empêche la survalidation, comme expliqué dans le [deuxième didacticiel](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Si l’emplacement du bureau est vide, il définit la propriété Instructor.OfficeAssignment sur null, de façon que la ligne correspondante dans la table OfficeAssignment soit supprimée.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Il enregistre les modifications dans la base de données.

### <a name="update-the-instructor-edit-view"></a>Mettre à jour la vue de modification des formateurs

Dans *Views/Instructors/Edit.cshtml*, ajoutez un nouveau champ pour la modification de l’emplacement du bureau, à la fin et avant le bouton **Save** :

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Exécutez l’application, sélectionnez l’onglet **Instructors**, puis cliquez sur **Edit** pour un formateur. Modifiez **Office Location** et cliquez sur **Save**.

![Page Edit pour les formateurs](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Ajouter des cours à la page de modification

Les instructeurs peuvent enseigner dans n’importe quel nombre de cours. Maintenant, vous allez améliorer la page de modification des formateurs en ajoutant la possibilité de modifier les affectations de cours avec un groupe de cases à cocher, comme le montre la capture d’écran suivante :

![Page de modification de formateur avec des cours](update-related-data/_static/instructor-edit-courses.png)

La relation entre les entités Course et Instructor est plusieurs-à-plusieurs. Pour ajouter et supprimer des relations, vous ajoutez et vous supprimez des entités dans le jeu d’entités de la jointure CourseAssignments.

L’interface utilisateur qui vous permet de changer les cours auxquels un formateur est affecté est un groupe de cases à cocher. Une case à cocher est affichée pour chaque cours de la base de données, et ceux auxquels le formateur est actuellement affecté sont sélectionnés. L’utilisateur peut cocher ou décocher les cases pour changer les affectations de cours. Si le nombre de cours était beaucoup plus important, vous pourriez utiliser une autre méthode de présentation des données dans la vue, mais vous utiliseriez la même méthode de manipulation d’une entité de jointure pour créer ou supprimer des relations.

### <a name="update-the-instructors-controller"></a>Mettre à jour le contrôleur Instructors

Pour fournir des données à la vue pour la liste de cases à cocher, vous utilisez une classe de modèle de vue.

Créez *AssignedCourseData.cs* dans le dossier *SchoolViewModels* et remplacez le code existant par le code suivant :

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Dans *InstructorsController.cs*, remplacez la méthode HttpGet `Edit` par le code suivant. Les modifications sont mises en surbrillance.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Le code ajoute un chargement hâtif pour la propriété de navigation `Courses` et appelle la nouvelle méthode `PopulateAssignedCourseData` pour fournir des informations pour le tableau de cases à cocher avec la classe de modèle de vue `AssignedCourseData`.

Le code de la méthode`PopulateAssignedCourseData` lit toutes les entités Course pour charger une liste de cours avec la classe de modèle de vue. Pour chaque cours, le code vérifie s’il existe dans la propriété de navigation `Courses` du formateur. Pour créer une recherche efficace quand il est vérifié si un cours est affecté au formateur, les cours affectés au formateur sont placés dans une collection `HashSet`. La propriété `Assigned` est définie sur true pour les cours auxquels le formateur est affecté. La vue utilise cette propriété pour déterminer quelles cases doivent être affichées cochées. Enfin, la liste est passée à la vue dans `ViewData`.

Ensuite, ajoutez le code qui est exécuté quand l’utilisateur clique sur **Save**. Remplacez la méthode `EditPost` par le code suivant et ajoutez une nouvelle méthode qui met à jour la propriété de navigation `Courses` de l’entité Instructor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

La signature de la méthode diffère maintenant de celle de la méthode HttpGet `Edit` : le nom de la méthode change donc de `EditPost` en `Edit`.

Comme la vue n’a pas de collection d’entités Course, le classeur de modèles ne peut pas mettre à jour automatiquement la propriété de navigation `CourseAssignments`. Au lieu d’utiliser le classeur de modèles pour mettre à jour la propriété de navigation `CourseAssignments`, vous faites cela dans la nouvelle méthode `UpdateInstructorCourses`. Par conséquent, vous devez exclure la `CourseAssignments` propriété de la liaison de modèle. Cela ne nécessite aucune modification du code qui appelle `TryUpdateModel` car vous utilisez la surcharge qui requiert une approbation explicite et qui `CourseAssignments` ne figure pas dans la liste d’inclusion.

Si aucune case n’a été cochée, le code de `UpdateInstructorCourses` initialise la propriété de navigation `CourseAssignments` avec une collection vide et retourne :

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Le code boucle ensuite à travers tous les cours dans la base de données, et vérifie chaque cours par rapport à ceux actuellement affectés au formateur relativement à ceux qui ont été sélectionnés dans la vue. Pour faciliter des recherches efficaces, les deux dernières collections sont stockées dans des objets `HashSet`.

Si la case pour un cours a été cochée mais que le cours n’est pas dans la propriété de navigation `Instructor.CourseAssignments`, le cours est ajouté à la collection dans la propriété de navigation.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Si la case pour un cours a été cochée mais que le cours est dans la propriété de navigation `Instructor.CourseAssignments`, le cours est supprimé de la propriété de navigation.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Mettre à jour les vues des formateurs

Dans *Views/Instructors/Edit.cshtml*, ajoutez un champ **Courses** avec un tableau de cases à cocher, en ajoutant le code suivant immédiatement après le code les éléments `div` pour le champ **Office** et avant l’élément `div` pour le bouton **Save**.

<a id="notepad"></a>
> [!NOTE]
> Quand vous collez le code dans Visual Studio, les sauts de ligne peuvent être changés d’une façon qui casse le code. Si le code semble différent après un collage, appuyez une fois sur Ctrl+Z pour annuler la mise en forme automatique. Ceci permet de corriger les sauts de ligne de façon à ce qu’ils apparaissent comme ce que vous voyez ici. L’indentation ne doit pas nécessairement être parfaite, mais les lignes `@:</tr><tr>`, `@:<td>`, `@:</td>` et `@:</tr>` doivent chacune tenir sur une seule ligne comme dans l’illustration, sinon vous recevrez une erreur d’exécution. Avec le bloc de nouveau code sélectionné, appuyez trois fois sur Tab pour aligner le nouveau code avec le code existant. Ce problème est résolu dans Visual Studio 2019.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Ce code crée un tableau HTML qui a trois colonnes. Dans chaque colonne se trouve une case à cocher, suivie d’une légende qui est constituée du numéro et du titre du cours. Toutes les cases à cocher ont le même nom (« selectedCourses »), qui indique au classeur de modèles qu’ils doivent être traités comme un groupe. L’attribut de valeur de chaque case à cocher est défini sur la valeur de `CourseID`. Quand la page est envoyée, le classeur de modèles passe un tableau au contrôleur, constitué des valeurs de `CourseID` seulement pour les cases qui sont cochées.

Quand les cases à cocher sont affichées à l’origine, celles qui correspondent à des cours affectés au formateur ont des attributs cochés, qui les sélectionnent (ils les affichent cochées).

Exécutez l’application, sélectionnez l’onglet **Instructors**, puis cliquez sur **Edit** pour un formateur pour voir la page **Edit**.

![Page de modification de formateur avec des cours](update-related-data/_static/instructor-edit-courses.png)

Changez quelques affectations de cours et cliquez sur Save. Les modifications que vous apportez sont reflétées dans la page Index.

> [!NOTE]
> L’approche adoptée ici pour modifier les données des cours des formateurs fonctionne bien le nombre de cours est limité. Pour les collections qui sont beaucoup plus volumineuses, une autre interface utilisateur et une autre méthode de mise à jour seraient nécessaires.

## <a name="update-delete-page"></a>Mettre à jour la page Delete

Dans *InstructorsController.cs*, supprimez la méthode `DeleteConfirmed` et insérez à la place le code suivant.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Ce code apporte les modifications suivantes :

* Il effectue un chargement hâtif pour la propriété de navigation `CourseAssignments`. Vous devez inclure ceci car sinon, EF ne dispose pas d’informations sur les entités `CourseAssignment` associées et ne les supprime pas. Pour éviter de devoir les lire ici, vous pouvez configurer une suppression en cascade dans la base de données.

* Si le formateur à supprimer est attribué en tant qu’administrateur d’un département, supprime l’attribution de l'instructeur de ces départements.

## <a name="add-office-location-and-courses-to-create-page"></a>Ajouter des emplacements de bureau et des cours à la page Create

Dans *InstructorsController.cs*, supprimez les méthodes HttpGet et HttpPost `Create`, puis ajoutez le code suivant à leur place :

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Ce code est similaire à ce que vous avez vu pour les méthodes `Edit`, excepté qu’initialement, aucun cours n’est sélectionné. La méthode HttpGet `Create` appelle la méthode `PopulateAssignedCourseData`, non pas en raison du fait qu’il peut exister des cours sélectionnés, mais pour pouvoir fournir une collection vide pour la boucle `foreach` dans la vue (sinon, le code de la vue lèverait une exception de référence null).

La méthode HttpPost `Create` ajoute chaque cours sélectionné à la propriété de navigation `CourseAssignments` avant de vérifier s’il y a des erreurs de validation et ajoute le nouveau formateur à la base de données. Les cours sont ajoutés même s’il existe des erreurs de modèle : ainsi, quand c’est le cas (par exemple si l’utilisateur a tapé une date non valide) et que la page est réaffichée avec un message d’erreur, les sélections de cours qui ont été faites sont automatiquement restaurées.

Notez que pour pouvoir ajouter des cours à la propriété de navigation `CourseAssignments`, vous devez initialiser la propriété en tant que collection vide :

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Comme alternative à cette opération dans le code du contrôleur, vous pouvez l’effectuer dans le modèle Instructor en modifiant le getter de propriété pour créer automatiquement la collection si elle n’existe pas, comme le montre l’exemple suivant :

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

Si vous modifiez la propriété `CourseAssignments` de cette façon, vous pouvez supprimer le code d’initialisation explicite de la propriété dans le contrôleur.

Dans *Views/Instructor/Create.cshtml*, ajoutez une zone de texte pour l’emplacement du bureau et des cases à cocher pour les cours avant le bouton Envoyer. Comme dans le cas de la page Edit, [corrigez la mise en forme si Visual Studio remet en forme le code quand vous le collez](#notepad).

[!code-cshtml[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Testez en exécutant l’application et en créant un formateur.

## <a name="handling-transactions"></a>Gestion des transactions

Comme expliqué dans le [didacticiel CRUD](crud.md), Entity Framework implémente implicitement les transactions. Pour les scénarios où vous avez besoin de plus de contrôle, par exemple si vous voulez inclure des opérations effectuées en dehors d’Entity Framework dans une transaction, consultez [Transactions](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Obtenir le code

[Télécharger ou afficher l’application complète.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Personnalisez les pages de cours
> * Ajoutez une page de modification de formateur
> * Ajoutez des cours à la page de modification
> * Mettez à jour la page Delete
> * Emplacements de bureau et cours ajoutés à la page Create

Passez au tutoriel suivant pour découvrir comment gérer les conflits d’accès concurrentiel.

> [!div class="nextstepaction"]
> [Gérer les conflits d’accès concurrentiel](concurrency.md)
