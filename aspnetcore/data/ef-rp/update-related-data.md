---
title: Partie 7, Razor pages avec EF Core dans ASP.net Core mettre à jour les données associées
author: rick-anderson
description: Partie 7 de Razor pages et Entity Framework série de didacticiels.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 3ec88a862697c540a1a98e733c31d76922f81f7c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060532"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a>Partie 7, Razor pages avec EF Core dans ASP.net Core mettre à jour les données associées

De [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Le tutoriel suivant montre comment mettre à jour les données associées. Les illustrations suivantes montrent certaines des pages finalisées.

![Page de modification de cours](update-related-data/_static/course-edit30.png)
![Page de modification de formateur](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Mettre à jour les pages de création et de modification du cours

Le code généré automatiquement pour les pages de création et de modification du cours contient la liste déroulante des départements de l’université qui affiche l’ID des départements (un nombre entier). La liste déroulante doit afficher le nom du département. Ces deux pages ont donc besoin d’une liste de noms de départements. Pour fournir cette liste, utilisez une classe de base pour les pages Create et Edit.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Créer une classe de base pour la création et la modification d’un cours

Créez un fichier *Pages/Courses/DepartmentNamePageModel.cs* comportant le code suivant :

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Le code précédent crée un [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) pour contenir la liste des noms de département.  Si `selectedDepartment` est spécifié, ce département est sélectionné dans le `SelectList`. 

Les classes de modèle de page Create et Edit doivent dériver de `DepartmentNamePageModel`.

### <a name="update-the-course-create-page-model"></a>Mettre à jour le modèle de page de création de cours

Un cours est affecté à un département. La classe de base des pages Create et Edit fournit un `SelectList` pour la sélection du département. La liste déroulante qui utilise `SelectList` définit la propriété de clé étrangère `Course.DepartmentID`. EF Core utilise la clé étrangère `Course.DepartmentID` pour charger la propriété de navigation `Department`.

![Créer le cours](update-related-data/_static/ddl30.png)

Mettez à jour *Pages/Courses/Create.cshtml.cs* à l’aide du code suivant :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Le code précédent :

* Dérive de `DepartmentNamePageModel`.
* Utilise `TryUpdateModelAsync` pour empêcher la [sur-validation](xref:data/ef-rp/crud#overposting).
* Supprime `ViewData["DepartmentID"]`. `DepartmentNameSL` à partir de la classe de base est un modèle fortement typé et sera utilisé par la Razor page. Les modèles fortement typés sont préférables aux modèles faiblement typés. Pour plus d’informations, consultez [Données faiblement typées (ViewData et ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-no-locrazor-page"></a>Mettre à jour la page de création de cours Razor

Mettez à jour *Pages/Courses/Create.cshtml* à l’aide du code suivant :

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Le code précédent apporte les modifications suivantes :

* Modifie la légende de **DepartmentID** en **Department** . 
* Il remplace `"ViewBag.DepartmentID"` par `DepartmentNameSL` (à partir de la classe de base).
* Il ajoute l’option « Select Department ». Si vous n’avez pas encore sélectionné de département, ce changement affiche l’option « Select Department » dans la liste déroulante, plutôt que le premier département.
* Il ajoute un message de validation quand le département n’est pas sélectionné.

La Razor page utilise le [tag Helper Select](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Testez la page Create. La page Create affiche le nom du département plutôt que son ID.

### <a name="update-the-course-edit-page-model"></a>Mettre à jour le modèle de page de modification du cours

Mettez à jour *Pages/Courses/Edit.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Les modifications sont semblables à celles effectuées dans le modèle de page Create. Dans le code précédent, `PopulateDepartmentsDropDownList` passe l’ID du département, ce qui sélectionne le département correspondant dans la liste déroulante.

### <a name="update-the-course-edit-no-locrazor-page"></a>Mettre à jour la page de modification du cours Razor

Mettez à jour *Pages/Courses/Edit.cshtml* avec le code suivant :

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Le code précédent apporte les modifications suivantes :

* Affiche l’identificateur du cours. En général la clé primaire (PK) d’une entité n’est pas affichée. Les clés primaires sont généralement sans signification pour les utilisateurs. Dans ce cas, la clé est le numéro de cours.
* Change la légende de la liste déroulante en remplaçant **DepartmentID** par **Department** .
* Il remplace `"ViewBag.DepartmentID"` par `DepartmentNameSL` (à partir de la classe de base).

La page contient un champ masqué (`<input type="hidden">`) pour le numéro de cours. L’ajout d’un Tag Helper `<label>` avec `asp-for="Course.CourseID"` n’élimine pas la nécessité de la présence du champ masqué. `<input type="hidden">` est obligatoire pour que le numéro de cours soit inclus dans les données publiées quand l’utilisateur clique sur **Save** .

## <a name="update-the-course-details-and-delete-pages"></a>Mettre à jour les pages de détails et de suppression du cours

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) peut améliorer les performances quand le suivi n’est pas nécessaire.

### <a name="update-the-course-page-models"></a>Mettre à jour les modèles de pages du cours

Mettez à jour *Pages/Courses/Delete.cshtml.cs* à l’aide du code suivant pour ajouter `AsNoTracking` :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Apportez la même modification dans le fichier *Pages/Courses/Details.cshtml.cs*  :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a>Mettre à jour les pages de cours Razor

Mettez à jour *Pages/Courses/Delete.cshtml* à l’aide du code suivant :

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Apportez les mêmes modifications à la page Details.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Tester les pages de cours

Testez les pages de création, de modification, de détails et de suppression.

## <a name="update-the-instructor-create-and-edit-pages"></a>Mettre à jour les pages de création et de modification du formateur

Les instructeurs peuvent enseigner dans n’importe quel nombre de cours. L’illustration suivante montre la page de modification du formateur avec un tableau comportant les cases à cocher qui correspondent aux cours.

![Page de modification de formateur avec des cours](update-related-data/_static/instructor-edit-courses30.png)

Les cases à cocher permettent de modifier les cours auxquels un formateur est affecté. Une case à cocher est affichée pour chaque cours de la base de données. Les cours auxquels le formateur est affecté sont cochés. L’utilisateur peut cocher ou décocher les cases pour changer les affectations de cours. Si le nombre de cours était bien plus important, une autre interface utilisateur serait probablement plus pratique. Toutefois, la méthode de gestion d’une relation plusieurs à plusieurs présentée ici resterait la même. Pour créer ou supprimer des relations, vous devez manipuler une entité de jointure.

### <a name="create-a-class-for-assigned-courses-data"></a>Créer une classe pour les données de cours affectées

Créez *SchoolViewModels/AssignedCourseData.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

La classe `AssignedCourseData` contient des données permettant de créer les cases à cocher pour les cours affectés à un formateur.

### <a name="create-an-instructor-page-model-base-class"></a>Créer une classe de base pour le modèle de page du formateur

Créez la classe de base *Pages/Instructors/InstructorCoursesPageModel.cs*  :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

`InstructorCoursesPageModel` est la classe de base que vous utiliserez pour les modèles de page Edit et Create. `PopulateAssignedCourseData` lit toutes les entités `Course` pour renseigner `AssignedCourseDataList`. Pour chaque cours, le code définit le `CourseID`, le titre, et si le formateur est affecté au cours. Un [HashSet](/dotnet/api/system.collections.generic.hashset-1) est utilisé pour permettre des recherches efficaces.

Étant donné que la Razor page n’a pas de collection d’entités course, le Binder de modèle ne peut pas mettre à jour automatiquement la `CourseAssignments` propriété de navigation. Au lieu d’utiliser le classeur de modèles pour mettre à jour la propriété de navigation `CourseAssignments`, vous faites cela dans la nouvelle méthode `UpdateInstructorCourses`. Par conséquent, vous devez exclure la propriété `CourseAssignments` de la liaison de modèle. Cela ne nécessite aucune modification du code qui appelle `TryUpdateModel` car vous utilisez la surcharge avec les propriétés déclarées et `CourseAssignments` ne figure pas dans la liste d’inclusion.

Si aucune case n’a été cochée, le code de `UpdateInstructorCourses` initialise la propriété de navigation `CourseAssignments` avec une collection vide et retourne :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Ensuite, le code exécute une boucle dans tous les cours de la base de données, et compare les cours actuellement affectés au formateur à ceux qui sont sélectionnés dans la vue. Pour faciliter des recherches efficaces, les deux dernières collections sont stockées dans des objets `HashSet`.

Si la case pour un cours a été cochée mais que le cours n’est pas dans la propriété de navigation `Instructor.CourseAssignments`, le cours est ajouté à la collection dans la propriété de navigation.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Si la case pour un cours a été cochée mais que le cours est dans la propriété de navigation `Instructor.CourseAssignments`, le cours est supprimé de la propriété de navigation.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Gérer l’emplacement du bureau

La page de modification doit également gérer la relation « un à zéro/zéro ou un » qui existe entre l’entité de l’instructeur et l’entité `OfficeAssignment`. Le code de modification du formateur doit gérer les scénarios suivants : 

* Si l’utilisateur efface l’attribution d'un bureau, supprimer l'entité `OfficeAssignment`. 
* Si l’utilisateur entre une attribution et qu'elle était vide, créer une nouvelle entité `OfficeAssignment`. 
* Si l’utilisateur change l’attribution de bureau, mettre à jour l’entité `OfficeAssignment`.

### <a name="update-the-instructor-edit-page-model"></a>Mettre à jour le modèle de page de modification du formateur

Mettez à jour *Pages/Instructors/Edit.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Le code précédent :

* Obtient l’entité `Instructor` actuelle de la base de données à l’aide d’un chargement hâtif des propriétés de navigation `OfficeAssignment`, `CourseAssignment` et `CourseAssignment.Course`.
* Met à jour l’entité `Instructor` récupérée avec les valeurs du classeur de modèles. `TryUpdateModel` empêche la [survalidation](xref:data/ef-rp/crud#overposting).
* Si l’emplacement du bureau est vide, définit `Instructor.OfficeAssignment` avec la valeur null. Lorsque `Instructor.OfficeAssignment` est null, la ligne correspondante dans la table `OfficeAssignment` est supprimée.
* Appelle `PopulateAssignedCourseData` dans `OnGetAsync` afin de fournir des informations pour les cases à cocher à l’aide de la classe de modèle de vue `AssignedCourseData`.
* Appelle `UpdateInstructorCourses` dans `OnPostAsync` pour appliquer les informations des cases à cocher à l’entité de formateur en cours de modification.
* Appelle `PopulateAssignedCourseData` et `UpdateInstructorCourses` dans `OnPostAsync` si `TryUpdateModel` échoue. Ces appels de méthode restaurent les données de cours affectées qui ont été entrées dans la page lorsqu’elles sont réaffichées avec un message d’erreur.

### <a name="update-the-instructor-edit-no-locrazor-page"></a>Mettre à jour la page de modification du formateur Razor

Mettez à jour *Pages/Instructors/Edit.cshtml* avec le code suivant :

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Le code précédent crée une table HTML avec trois colonnes. Chaque colonne a une case à cocher et une légende contenant le numéro et le titre du cours. Les cases à cocher ont toutes le même nom (« selectedCourses »). L’utilisation du même nom signale au classeur de modèles qu’il doit les traiter en tant que groupe. L’attribut de valeur de chaque case à cocher est défini sur `CourseID`. Quand la page est publiée, le classeur de modèles passe un tableau composé des valeurs `CourseID` correspondant uniquement aux cases à cocher sélectionnées.

Lors de l’affichage initial des cases à cocher, les cours affectés au formateur sont cochés.

Remarque : L’approche adoptée ici pour modifier les données de cours des formateurs fonctionne bien quand il y a un nombre limité de cours. Pour les collections qui sont beaucoup plus grandes, une interface utilisateur différente et une autre méthode de mise à jour seraient plus utiles et plus efficaces.

Exécutez l’application et testez la page de modification du formateur qui vient d’être mise à jour. Changez certaines affectations de cours. Les modifications sont répercutées dans la page Index.

### <a name="update-the-instructor-create-page"></a>Mettre à jour la page de création du formateur

Mettez à jour le modèle de page de création de formateur et la Razor page avec du code similaire à la page d’édition :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Testez la page de création de l'instructeur.

## <a name="update-the-instructor-delete-page"></a>Mettre à jour la page de suppression du formateur

Mettez à jour *Pages/Instructors/Delete.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Le code précédent apporte les modifications suivantes :

* Utilise un chargement hâtif pour la propriété de navigation `CourseAssignments`. Les `CourseAssignments` doivent être inclus ou ils ne seront pas supprimés lorsque l'instructeur est supprimé. Pour éviter d’avoir à les lire, configurez la suppression en cascade dans la base de données.

* Si le formateur à supprimer est attribué en tant qu’administrateur d’un département, supprime l’attribution de l'instructeur de ces départements.

Exécutez l’application et testez la page de suppression.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Didacticiel précédent](xref:data/ef-rp/read-related-data) 
>  [Didacticiel suivant](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ce didacticiel illustre la mise à jour de données associées. Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, [téléchargez ou affichez l’application terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [Instructions de téléchargement](xref:index#how-to-download-a-sample).

Les illustrations suivantes montrent certaines des pages terminées.

![Page de modification de cours](update-related-data/_static/course-edit.png)
![Page de modification de formateur](update-related-data/_static/instructor-edit-courses.png)

Examinez et testez les pages des cours Create et Edit. Créez un nouveau cours. Le service est sélectionné par sa clé primaire (entier), pas par son nom. Modifiez le nouveau cours. Lorsque vous avez terminé le test, supprimez le nouveau cours.

## <a name="create-a-base-class-to-share-common-code"></a>Créer une classe de base pour partager le code commun

Les pages de création et de modification de cours ont chacune besoin d’une liste de noms de départements. Créez la classe de base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* pour les pages Create et Edit :

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Le code précédent crée un [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) pour contenir la liste des noms de département.  Si `selectedDepartment` est spécifié, ce département est sélectionné dans le `SelectList`. 

Les classes de modèle de page Create et Edit doivent dériver de `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Personnaliser les pages de cours

Quand une entité de cours est créée, elle doit avoir une relation à un département existant. Pour ajouter un département lors de la création d’un cours, la classe de base pour Create et Edit contient une liste déroulante de sélection du département. La liste déroulante définit la propriété de clé étrangère `Course.DepartmentID`. EF Core utilise la clé étrangère `Course.DepartmentID` pour charger la propriété de navigation `Department`.

![Créer le cours](update-related-data/_static/ddl.png)

Mettez à jour le modèle de la page Create avec le code suivant : 

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Le code précédent :

* Dérive de `DepartmentNamePageModel`.
* Utilise `TryUpdateModelAsync` pour empêcher la [sur-validation](xref:data/ef-rp/crud#overposting).
* Il remplace `ViewData["DepartmentID"]` par `DepartmentNameSL` (à partir de la classe de base).

`ViewData["DepartmentID"]` est remplacé par le `DepartmentNameSL` fortement typé. Les modèles fortement typés sont préférables aux modèles faiblement typés. Pour plus d’informations, consultez [Données faiblement typées (ViewData et ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Mettre à jour la page de création de cours

Mettez à jour *Pages/Courses/Create.cshtml* à l’aide du code suivant :

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Le balisage précédent apporte les modifications suivantes :

* Modifie la légende de **DepartmentID** en **Department** . 
* Il remplace `"ViewBag.DepartmentID"` par `DepartmentNameSL` (à partir de la classe de base).
* Il ajoute l’option « Select Department ». Cette modification entraîne l’affichage de « Select Department » plutôt que du premier département.
* Il ajoute un message de validation quand le département n’est pas sélectionné.

La Razor page utilise le [tag Helper Select](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Testez la page Create. La page Create affiche le nom du département plutôt que son ID.

### <a name="update-the-courses-edit-page"></a>Mise à jour de la page Edit d'un cours.

Remplacez le code *Pages/Courses/Edit.cshtml.cs* par le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Les modifications sont semblables à celles effectuées dans le modèle de page Create. Dans le code précédent, `PopulateDepartmentsDropDownList` transmet l’ID de département, ce qui sélectionne le département spécifié dans la liste déroulante.

Mettez à jour *Pages/Courses/Edit.cshtml* avec le balisage suivant :

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Le balisage précédent apporte les modifications suivantes :

* Affiche l’identificateur du cours. En général la clé primaire (PK) d’une entité n’est pas affichée. Les clés primaires sont généralement sans signification pour les utilisateurs. Dans ce cas, la clé est le numéro de cours.
* Modifie la légende de **DepartmentID** en **Department** . 
* Il remplace `"ViewBag.DepartmentID"` par `DepartmentNameSL` (à partir de la classe de base).

La page contient un champ masqué (`<input type="hidden">`) pour le numéro de cours. L’ajout d’un Tag Helper `<label>` avec `asp-for="Course.CourseID"` n’élimine pas la nécessité de la présence du champ masqué. `<input type="hidden">` est obligatoire pour que le numéro de cours soit inclus dans les données publiées quand l’utilisateur clique sur **Save** .

Testez le code mis à jour. Créez, modifiez et supprimez un cours.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Ajouter AsNoTracking aux modèles de page Details et Delete

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) peut améliorer les performances quand le suivi n’est pas nécessaire. Ajoutez `AsNoTracking` dans les modèles de page Details et Delete. Le code suivant montre le modèle de page Delete mis à jour :

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Mettez à jour la méthode `OnGetAsync` dans le fichier *Pages/Courses/Details.cshtml.cs*  :

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Modifier les pages Delete et Details

Mettez à jour la Razor page de suppression avec le balisage suivant :

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Apportez les mêmes modifications à la page Details.

### <a name="test-the-course-pages"></a>Tester les pages de cours

Testez les fonctionnalités de création, de modification, de détails et de suppression.

## <a name="update-the-instructor-pages"></a>Mettre à jour les pages d'instructeur

Les sections suivantes mettent à jour les pages d'instructeur.

### <a name="add-office-location"></a>Ajouter l’emplacement du bureau

Lors de la modification d’un enregistrement de formateur, vous souhaiterez peut-être mettre à jour l’attribution de bureau du formateur. L’entité `Instructor` a une relation un-à-zéro-ou-un avec l’entité `OfficeAssignment`. Le code de formateur doit gérer ce qui suit :

* Si l’utilisateur efface l’attribution d'un bureau, supprimer l'entité `OfficeAssignment`. 
* Si l’utilisateur entre une attribution et qu'elle était vide, créer une nouvelle entité `OfficeAssignment`. 
* Si l’utilisateur change l’attribution de bureau, mettre à jour l’entité `OfficeAssignment`.

Mettez à jour le modèle de page Edit d'instructeur avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Le code précédent :

* Obtient l'entité `Instructor` en cours à partir de la base de données à l’aide d’un chargement hâtif de la propriété de navigation `OfficeAssignment`.
* Met à jour l’entité `Instructor` récupérée avec les valeurs du classeur de modèles. `TryUpdateModel` empêche la [survalidation](xref:data/ef-rp/crud#overposting).
* Si l’emplacement du bureau est vide, définit `Instructor.OfficeAssignment` avec la valeur null. Lorsque `Instructor.OfficeAssignment` est null, la ligne correspondante dans la table `OfficeAssignment` est supprimée.

### <a name="update-the-instructor-edit-page"></a>Mettre à jour la page de modification de formateur

Mettez à jour *Pages/Instructors/Edit.cshtml* avec l’emplacement du bureau :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Vérifiez que vous pouvez modifier l'emplacement de bureau d'un instructeur.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Ajouter des affectations de cours à la page Edit de l'instructeur

Les instructeurs peuvent enseigner dans n’importe quel nombre de cours. Dans cette section, vous ajoutez la possibilité de modifier les affectations de cours. L’illustration suivante montre la page de modification de l'instructeur mise à jour :

![Page de modification de formateur avec des cours](update-related-data/_static/instructor-edit-courses.png)

`Course` et `Instructor` entretiennent une relation plusieurs-à-plusieurs. Pour ajouter et supprimer des relations, vous ajoutez et supprimez des entités à partir du jeu d’entités de jointures `CourseAssignments`.

Les cases à cocher permettent de changer les cours auxquels un formateur est assigné. Une case à cocher est affichée pour chaque cours dans la base de données. Les cours auxquels le formateur est affecté sont cochés. L’utilisateur peut cocher ou décocher les cases pour changer les affectations de cours. Si le nombre de cours était beaucoup plus élevé :

* Vous utiliseriez sans doute une interface utilisateur différente pour afficher les cours.
* La méthode de manipulation d’une entité de jointure pour créer ou supprimer des relations ne changerait pas.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Ajouter des classes pour prendre en charge Create et Edit des pages d'instructeur

Créez *SchoolViewModels/AssignedCourseData.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

La classe `AssignedCourseData` contient des données pour créer les cases à cocher pour les cours attribués par un formateur.

Créez la classe de base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* :

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel` est la classe de base que vous utiliserez pour les modèles de page Edit et Create. `PopulateAssignedCourseData` lit toutes les entités `Course` pour renseigner `AssignedCourseDataList`. Pour chaque cours, le code définit le `CourseID`, le titre, et si le formateur est affecté au cours. Un [HashSet](/dotnet/api/system.collections.generic.hashset-1) est utilisé pour créer des recherches efficaces.

### <a name="instructors-edit-page-model"></a>Modèle de page de modification de formateur

Mettez à jour le modèle de page Edit d'instructeur avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Le code précédent gère les changements d’affectation de bureau.

Mettre à jour la vue de l’instructeur Razor :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Quand vous collez le code dans Visual Studio, les sauts de ligne sont modifiés de telle manière que le code est rompu. Appuyez une fois sur Ctrl + Z pour annuler la mise en forme automatique. Ctrl + Z corrige les sauts de ligne afin qu’ils aient l’aspect visible ici. La mise en retrait ne doit pas nécessairement être parfaite, mais les lignes `@:</tr><tr>`, `@:<td>`, `@:</td>` et `@:</tr>` doivent chacune être sur une seule distincte comme indiqué. Avec le bloc de nouveau code sélectionné, appuyez trois fois sur Tab pour aligner le nouveau code avec le code existant. Votez ou vérifiez l’état de ce bogue [avec ce lien](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Le code précédent crée une table HTML avec trois colonnes. Chaque colonne a une case à cocher et une légende contenant le numéro et le titre du cours. Les cases à cocher ont toutes le même nom (« selectedCourses »). L’utilisation du même nom signale au classeur de modèles qu’il doit les traiter en tant que groupe. L’attribut de valeur de chaque case à cocher est défini sur `CourseID`. Quand la page est publiée, le classeur de modèles transmet un tableau composé des valeurs `CourseID` correspondant uniquement aux cases à cocher sélectionnées.

Lors de l’affichage initial des cases à cocher, les cours affectés au formateur ont des attributs cochés.

Exécutez l’application et testez la page Edit des formateurs mise à jour. Changez certaines affectations de cours. Les modifications sont répercutées dans la page Index.

Remarque : L’approche adoptée ici pour modifier les données de cours des formateurs fonctionne bien quand il y a un nombre limité de cours. Pour les collections qui sont beaucoup plus grandes, une interface utilisateur différente et une autre méthode de mise à jour seraient plus utiles et plus efficaces.

### <a name="update-the-instructors-create-page"></a>Mise à jour de la page de création des instructeurs

Mettez à jour le modèle de page de création de formateur avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Le code précédent est similaire au code de *Pages/Instructors/Edit.cshtml.cs* .

Mettez à jour la Razor page de création de l’instructeur avec le balisage suivant :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Testez la page de création de l'instructeur.

## <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Mettez à jour le modèle de page de suppression avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Le code précédent apporte les modifications suivantes :

* Utilise un chargement hâtif pour la propriété de navigation `CourseAssignments`. Les `CourseAssignments` doivent être inclus ou ils ne seront pas supprimés lorsque l'instructeur est supprimé. Pour éviter d’avoir à les lire, configurez la suppression en cascade dans la base de données.

* Si le formateur à supprimer est attribué en tant qu’administrateur d’un département, supprime l’attribution de l'instructeur de ces départements.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel (Partie 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Version YouTube de ce tutoriel (Partie 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Précédent](xref:data/ef-rp/read-related-data) 
>  [Suivant](xref:data/ef-rp/concurrency)

::: moniker-end
