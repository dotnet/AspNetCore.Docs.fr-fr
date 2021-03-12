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
ms.openlocfilehash: dadfceca2431ace558700ae06417f01c99751419
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588280"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="0e48c-103">Didacticiel : mettre à jour les données associées-ASP.NET MVC avec EF Core</span><span class="sxs-lookup"><span data-stu-id="0e48c-103">Tutorial: Update related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="0e48c-104">Dans le didacticiel précédent, vous avez affiché des données associées ; dans ce didacticiel, vous mettez à jour des données associées en mettant à jour des champs de clé étrangère et des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="0e48c-104">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="0e48c-105">Les illustrations suivantes montrent quelques-unes des pages que vous allez utiliser.</span><span class="sxs-lookup"><span data-stu-id="0e48c-105">The following illustrations show some of the pages that you'll work with.</span></span>

![Page Edit pour les cours](update-related-data/_static/course-edit.png)

![Modifier la page de l’instructeur](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="0e48c-108">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="0e48c-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0e48c-109">Personnaliser les pages de cours</span><span class="sxs-lookup"><span data-stu-id="0e48c-109">Customize Courses pages</span></span>
> * <span data-ttu-id="0e48c-110">Ajouter une page de modification de formateur</span><span class="sxs-lookup"><span data-stu-id="0e48c-110">Add Instructors Edit page</span></span>
> * <span data-ttu-id="0e48c-111">Ajouter des cours à la page de modification</span><span class="sxs-lookup"><span data-stu-id="0e48c-111">Add courses to Edit page</span></span>
> * <span data-ttu-id="0e48c-112">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="0e48c-112">Update Delete page</span></span>
> * <span data-ttu-id="0e48c-113">Ajouter des emplacements de bureau et des cours à la page Create</span><span class="sxs-lookup"><span data-stu-id="0e48c-113">Add office location and courses to Create page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0e48c-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="0e48c-114">Prerequisites</span></span>

* [<span data-ttu-id="0e48c-115">Lire les données associées</span><span class="sxs-lookup"><span data-stu-id="0e48c-115">Read related data</span></span>](read-related-data.md)

## <a name="customize-courses-pages"></a><span data-ttu-id="0e48c-116">Personnaliser les pages de cours</span><span class="sxs-lookup"><span data-stu-id="0e48c-116">Customize Courses pages</span></span>

<span data-ttu-id="0e48c-117">Quand une entité de cours est créée, elle doit avoir une relation à un département existant.</span><span class="sxs-lookup"><span data-stu-id="0e48c-117">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="0e48c-118">Pour faciliter cela, le code du modèle généré automatiquement inclut des méthodes de contrôleur, et des vues Create et Edit qui incluent une liste déroulante pour sélectionner le département.</span><span class="sxs-lookup"><span data-stu-id="0e48c-118">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="0e48c-119">La liste déroulante définit la propriété de clé étrangère `Course.DepartmentID`, qui est tout ce dont Entity Framework a besoin pour charger la propriété de navigation `Department` avec l’entité Department appropriée.</span><span class="sxs-lookup"><span data-stu-id="0e48c-119">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="0e48c-120">Vous utilisez le code du modèle généré automatiquement, mais que vous modifiez un peu pour ajouter la gestion des erreurs et trier la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="0e48c-120">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="0e48c-121">Dans *CoursesController.cs*, supprimez les quatre méthodes Create et Edit, et remplacez-les par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0e48c-121">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="0e48c-122">Après la méthode HttpPost `Edit`, créez une méthode qui charge les informations des départements pour la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="0e48c-122">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="0e48c-123">La méthode `PopulateDepartmentsDropDownList` obtient une liste de tous les départements triés par nom, crée une collection `SelectList` pour une liste déroulante et passe la collection à la vue dans `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-123">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="0e48c-124">La méthode accepte le paramètre facultatif `selectedDepartment` qui permet au code appelant de spécifier l’élément sélectionné lors de l’affichage de la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="0e48c-124">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="0e48c-125">La vue passe le nom « DepartmentID » pour le tag helper `<select>` : le helper peut alors rechercher dans l’objet `ViewBag` une `SelectList` nommée « DepartmentID ».</span><span class="sxs-lookup"><span data-stu-id="0e48c-125">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="0e48c-126">La méthode HttpGet `Create` appelle la méthode `PopulateDepartmentsDropDownList` sans définir l’élément sélectionné, car pour un nouveau cours, le département n’est pas encore établi :</span><span class="sxs-lookup"><span data-stu-id="0e48c-126">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="0e48c-127">La méthode HttpGet `Edit` définit l’élément sélectionné, en fonction de l’ID du département qui est déjà affecté au cours à modifier :</span><span class="sxs-lookup"><span data-stu-id="0e48c-127">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="0e48c-128">Les méthodes HttpPost pour `Create` et pour `Edit` incluent également du code qui définit l’élément sélectionné quand elles réaffichent la page après une erreur.</span><span class="sxs-lookup"><span data-stu-id="0e48c-128">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="0e48c-129">Ceci garantit que quand la page est réaffichée pour montrer le message d’erreur, le département qui a été sélectionné le reste.</span><span class="sxs-lookup"><span data-stu-id="0e48c-129">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="0e48c-130">Ajouter .AsNoTracking aux méthodes Details et Delete</span><span class="sxs-lookup"><span data-stu-id="0e48c-130">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="0e48c-131">Pour optimiser les performances des pages Details et Delete pour les cours, ajoutez des appels de `AsNoTracking` dans les méthodes `Details` et HttpGet `Delete`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-131">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="0e48c-132">Modifier les vues des cours</span><span class="sxs-lookup"><span data-stu-id="0e48c-132">Modify the Course views</span></span>

<span data-ttu-id="0e48c-133">Dans *Views/Courses/Create.cshtml*, ajoutez une option « Select Department » à la liste déroulante **Department**, changez la légende de **DepartmentID** en **Department** et ajoutez un message de validation.</span><span class="sxs-lookup"><span data-stu-id="0e48c-133">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-35)]

<span data-ttu-id="0e48c-134">Dans *Views/Courses/Edit.cshtml*, faites les mêmes modifications pour le champ Department que ce que vous venez de faire dans *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0e48c-134">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="0e48c-135">Également dans *Views/Courses/Edit.cshtml*, ajoutez un champ de numéro de cours avant le champ **Title**.</span><span class="sxs-lookup"><span data-stu-id="0e48c-135">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="0e48c-136">Comme le numéro de cours est la clé primaire, il est affiché mais ne peut pas être modifié.</span><span class="sxs-lookup"><span data-stu-id="0e48c-136">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="0e48c-137">Il existe déjà un champ masqué (`<input type="hidden">`) pour le numéro de cours dans la vue Edit.</span><span class="sxs-lookup"><span data-stu-id="0e48c-137">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="0e48c-138">L’ajout d’un tag helper `<label>` n’élimine la nécessité d’avoir le champ masqué, car cela n’a pas comme effet que le numéro de cours est inclut dans les données envoyées quand l’utilisateur clique sur **Save** dans la page **Edit**.</span><span class="sxs-lookup"><span data-stu-id="0e48c-138">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="0e48c-139">Dans *Views/Courses/Delete.cshtml*, ajoutez un champ pour le numéro de cours en haut et changez l’ID de département en nom de département.</span><span class="sxs-lookup"><span data-stu-id="0e48c-139">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="0e48c-140">Dans *Views/Courses/Details.cshtml*, faites la même modification que celle que vous venez de faire pour *Delete.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0e48c-140">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="0e48c-141">Tester les pages de cours</span><span class="sxs-lookup"><span data-stu-id="0e48c-141">Test the Course pages</span></span>

<span data-ttu-id="0e48c-142">Exécutez l’application, sélectionnez l’onglet **Courses**, cliquez sur **Create New** et entrez les données pour un nouveau cours :</span><span class="sxs-lookup"><span data-stu-id="0e48c-142">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![Page Create pour les cours](update-related-data/_static/course-create.png)

<span data-ttu-id="0e48c-144">Cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="0e48c-144">Click **Create**.</span></span> <span data-ttu-id="0e48c-145">La page Index des cours est affichée avec le nouveau cours ajouté à la liste.</span><span class="sxs-lookup"><span data-stu-id="0e48c-145">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="0e48c-146">Le nom du département dans la liste de la page Index provient de la propriété de navigation, ce qui montre que la relation a été établie correctement.</span><span class="sxs-lookup"><span data-stu-id="0e48c-146">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="0e48c-147">Cliquez sur **Edit** pour un cours dans la page Index des cours.</span><span class="sxs-lookup"><span data-stu-id="0e48c-147">Click **Edit** on a course in the Courses Index page.</span></span>

![Page Edit pour les cours](update-related-data/_static/course-edit.png)

<span data-ttu-id="0e48c-149">Modifiez les données dans la page et cliquez sur **Save**.</span><span class="sxs-lookup"><span data-stu-id="0e48c-149">Change data on the page and click **Save**.</span></span> <span data-ttu-id="0e48c-150">La page Index des cours est affichée avec les données du cours mises à jour.</span><span class="sxs-lookup"><span data-stu-id="0e48c-150">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-instructors-edit-page"></a><span data-ttu-id="0e48c-151">Ajouter une page de modification de formateur</span><span class="sxs-lookup"><span data-stu-id="0e48c-151">Add Instructors Edit page</span></span>

<span data-ttu-id="0e48c-152">Quand vous modifiez un enregistrement de formateur, vous voulez avoir la possibilité de mettre à jour l’attribution du bureau du formateur.</span><span class="sxs-lookup"><span data-stu-id="0e48c-152">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="0e48c-153">L’entité Instructor a une relation un-à-zéro ou un-à-un avec l’entité OfficeAssignment, ce qui signifie que votre code doit gérer les situations suivantes :</span><span class="sxs-lookup"><span data-stu-id="0e48c-153">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="0e48c-154">Si l’utilisateur efface l’attribution du bureau et qu’il existait une valeur à l’origine, supprimez l’entité OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="0e48c-154">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="0e48c-155">Si l’utilisateur entre une attribution de bureau et qu’elle était vide à l’origine, créez une entité OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="0e48c-155">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="0e48c-156">Si l’utilisateur change la valeur d’une attribution de bureau, changez la valeur dans une entité OfficeAssignment existante.</span><span class="sxs-lookup"><span data-stu-id="0e48c-156">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="0e48c-157">Mettre à jour le contrôleur Instructors</span><span class="sxs-lookup"><span data-stu-id="0e48c-157">Update the Instructors controller</span></span>

<span data-ttu-id="0e48c-158">Dans *InstructorsController.cs*, modifiez le code de la méthode HttpGet `Edit` afin qu’elle charge la propriété de navigation `OfficeAssignment` de l’entité Instructor et appelle `AsNoTracking` :</span><span class="sxs-lookup"><span data-stu-id="0e48c-158">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

<span data-ttu-id="0e48c-159">Remplacez la méthode HttpPost `Edit` par le code suivant pour gérer les mises à jour des attributions de bureau :</span><span class="sxs-lookup"><span data-stu-id="0e48c-159">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="0e48c-160">Le code effectue les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="0e48c-160">The code does the following:</span></span>

* <span data-ttu-id="0e48c-161">Il change le nom de la méthode `EditPost`, car la signature est maintenant la même que celle de la méthode HttpGet `Edit` (l’attribut `ActionName` spécifie que l’URL `/Edit/` est encore utilisée).</span><span class="sxs-lookup"><span data-stu-id="0e48c-161">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

* <span data-ttu-id="0e48c-162">Obtient l’entité Instructor actuelle auprès de la base de données en utilisant le chargement hâtif pour la propriété de navigation `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-162">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="0e48c-163">C’est identique à ce que vous avez fait dans la méthode HttpGet `Edit`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-163">This is the same as what you did in the HttpGet `Edit` method.</span></span>

* <span data-ttu-id="0e48c-164">Elle met à jour l’entité Instructor récupérée avec des valeurs dans le classeur de modèles.</span><span class="sxs-lookup"><span data-stu-id="0e48c-164">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="0e48c-165">La `TryUpdateModel` surcharge vous permet de déclarer les propriétés que vous souhaitez inclure.</span><span class="sxs-lookup"><span data-stu-id="0e48c-165">The `TryUpdateModel` overload enables you to declare the properties you want to include.</span></span> <span data-ttu-id="0e48c-166">Ceci empêche la survalidation, comme expliqué dans le [deuxième didacticiel](crud.md).</span><span class="sxs-lookup"><span data-stu-id="0e48c-166">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* <span data-ttu-id="0e48c-167">Si l’emplacement du bureau est vide, il définit la propriété Instructor.OfficeAssignment sur null, de façon que la ligne correspondante dans la table OfficeAssignment soit supprimée.</span><span class="sxs-lookup"><span data-stu-id="0e48c-167">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* <span data-ttu-id="0e48c-168">Il enregistre les modifications dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0e48c-168">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="0e48c-169">Mettre à jour la vue de modification des formateurs</span><span class="sxs-lookup"><span data-stu-id="0e48c-169">Update the Instructor Edit view</span></span>

<span data-ttu-id="0e48c-170">Dans *Views/Instructors/Edit.cshtml*, ajoutez un nouveau champ pour la modification de l’emplacement du bureau, à la fin et avant le bouton **Save** :</span><span class="sxs-lookup"><span data-stu-id="0e48c-170">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="0e48c-171">Exécutez l’application, sélectionnez l’onglet **Instructors**, puis cliquez sur **Edit** pour un formateur.</span><span class="sxs-lookup"><span data-stu-id="0e48c-171">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="0e48c-172">Modifiez **Office Location** et cliquez sur **Save**.</span><span class="sxs-lookup"><span data-stu-id="0e48c-172">Change the **Office Location** and click **Save**.</span></span>

![Page Edit pour les formateurs](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a><span data-ttu-id="0e48c-174">Ajouter des cours à la page de modification</span><span class="sxs-lookup"><span data-stu-id="0e48c-174">Add courses to Edit page</span></span>

<span data-ttu-id="0e48c-175">Les instructeurs peuvent enseigner dans n’importe quel nombre de cours.</span><span class="sxs-lookup"><span data-stu-id="0e48c-175">Instructors may teach any number of courses.</span></span> <span data-ttu-id="0e48c-176">Maintenant, vous allez améliorer la page de modification des formateurs en ajoutant la possibilité de modifier les affectations de cours avec un groupe de cases à cocher, comme le montre la capture d’écran suivante :</span><span class="sxs-lookup"><span data-stu-id="0e48c-176">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Page de modification de formateur avec des cours](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="0e48c-178">La relation entre les entités Course et Instructor est plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="0e48c-178">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="0e48c-179">Pour ajouter et supprimer des relations, vous ajoutez et vous supprimez des entités dans le jeu d’entités de la jointure CourseAssignments.</span><span class="sxs-lookup"><span data-stu-id="0e48c-179">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="0e48c-180">L’interface utilisateur qui vous permet de changer les cours auxquels un formateur est affecté est un groupe de cases à cocher.</span><span class="sxs-lookup"><span data-stu-id="0e48c-180">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="0e48c-181">Une case à cocher est affichée pour chaque cours de la base de données, et ceux auxquels le formateur est actuellement affecté sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="0e48c-181">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="0e48c-182">L’utilisateur peut cocher ou décocher les cases pour changer les affectations de cours.</span><span class="sxs-lookup"><span data-stu-id="0e48c-182">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="0e48c-183">Si le nombre de cours était beaucoup plus important, vous pourriez utiliser une autre méthode de présentation des données dans la vue, mais vous utiliseriez la même méthode de manipulation d’une entité de jointure pour créer ou supprimer des relations.</span><span class="sxs-lookup"><span data-stu-id="0e48c-183">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="0e48c-184">Mettre à jour le contrôleur Instructors</span><span class="sxs-lookup"><span data-stu-id="0e48c-184">Update the Instructors controller</span></span>

<span data-ttu-id="0e48c-185">Pour fournir des données à la vue pour la liste de cases à cocher, vous utilisez une classe de modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="0e48c-185">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="0e48c-186">Créez *AssignedCourseData.cs* dans le dossier *SchoolViewModels* et remplacez le code existant par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0e48c-186">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="0e48c-187">Dans *InstructorsController.cs*, remplacez la méthode HttpGet `Edit` par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0e48c-187">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="0e48c-188">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="0e48c-188">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="0e48c-189">Le code ajoute un chargement hâtif pour la propriété de navigation `Courses` et appelle la nouvelle méthode `PopulateAssignedCourseData` pour fournir des informations pour le tableau de cases à cocher avec la classe de modèle de vue `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-189">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="0e48c-190">Le code de la méthode`PopulateAssignedCourseData` lit toutes les entités Course pour charger une liste de cours avec la classe de modèle de vue.</span><span class="sxs-lookup"><span data-stu-id="0e48c-190">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="0e48c-191">Pour chaque cours, le code vérifie s’il existe dans la propriété de navigation `Courses` du formateur.</span><span class="sxs-lookup"><span data-stu-id="0e48c-191">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="0e48c-192">Pour créer une recherche efficace quand il est vérifié si un cours est affecté au formateur, les cours affectés au formateur sont placés dans une collection `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-192">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="0e48c-193">La propriété `Assigned` est définie sur true pour les cours auxquels le formateur est affecté.</span><span class="sxs-lookup"><span data-stu-id="0e48c-193">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="0e48c-194">La vue utilise cette propriété pour déterminer quelles cases doivent être affichées cochées.</span><span class="sxs-lookup"><span data-stu-id="0e48c-194">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="0e48c-195">Enfin, la liste est passée à la vue dans `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-195">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="0e48c-196">Ensuite, ajoutez le code qui est exécuté quand l’utilisateur clique sur **Save**.</span><span class="sxs-lookup"><span data-stu-id="0e48c-196">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="0e48c-197">Remplacez la méthode `EditPost` par le code suivant et ajoutez une nouvelle méthode qui met à jour la propriété de navigation `Courses` de l’entité Instructor.</span><span class="sxs-lookup"><span data-stu-id="0e48c-197">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="0e48c-198">La signature de la méthode diffère maintenant de celle de la méthode HttpGet `Edit` : le nom de la méthode change donc de `EditPost` en `Edit`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-198">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="0e48c-199">Comme la vue n’a pas de collection d’entités Course, le classeur de modèles ne peut pas mettre à jour automatiquement la propriété de navigation `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-199">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="0e48c-200">Au lieu d’utiliser le classeur de modèles pour mettre à jour la propriété de navigation `CourseAssignments`, vous faites cela dans la nouvelle méthode `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-200">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="0e48c-201">Par conséquent, vous devez exclure la `CourseAssignments` propriété de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="0e48c-201">Therefore, you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="0e48c-202">Cela ne nécessite aucune modification du code qui appelle `TryUpdateModel` car vous utilisez la surcharge qui requiert une approbation explicite et qui `CourseAssignments` ne figure pas dans la liste d’inclusion.</span><span class="sxs-lookup"><span data-stu-id="0e48c-202">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload that requires explicit approval and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="0e48c-203">Si aucune case n’a été cochée, le code de `UpdateInstructorCourses` initialise la propriété de navigation `CourseAssignments` avec une collection vide et retourne :</span><span class="sxs-lookup"><span data-stu-id="0e48c-203">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="0e48c-204">Le code boucle ensuite à travers tous les cours dans la base de données, et vérifie chaque cours par rapport à ceux actuellement affectés au formateur relativement à ceux qui ont été sélectionnés dans la vue.</span><span class="sxs-lookup"><span data-stu-id="0e48c-204">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="0e48c-205">Pour faciliter des recherches efficaces, les deux dernières collections sont stockées dans des objets `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-205">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="0e48c-206">Si la case pour un cours a été cochée mais que le cours n’est pas dans la propriété de navigation `Instructor.CourseAssignments`, le cours est ajouté à la collection dans la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="0e48c-206">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="0e48c-207">Si la case pour un cours a été cochée mais que le cours est dans la propriété de navigation `Instructor.CourseAssignments`, le cours est supprimé de la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="0e48c-207">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="0e48c-208">Mettre à jour les vues des formateurs</span><span class="sxs-lookup"><span data-stu-id="0e48c-208">Update the Instructor views</span></span>

<span data-ttu-id="0e48c-209">Dans *Views/Instructors/Edit.cshtml*, ajoutez un champ **Courses** avec un tableau de cases à cocher, en ajoutant le code suivant immédiatement après le code les éléments `div` pour le champ **Office** et avant l’élément `div` pour le bouton **Save**.</span><span class="sxs-lookup"><span data-stu-id="0e48c-209">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="0e48c-210">Quand vous collez le code dans Visual Studio, les sauts de ligne peuvent être changés d’une façon qui casse le code.</span><span class="sxs-lookup"><span data-stu-id="0e48c-210">When you paste the code in Visual Studio, line breaks might be changed in a way that breaks the code.</span></span> <span data-ttu-id="0e48c-211">Si le code semble différent après un collage, appuyez une fois sur Ctrl+Z pour annuler la mise en forme automatique.</span><span class="sxs-lookup"><span data-stu-id="0e48c-211">If the code looks different after pasting, press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="0e48c-212">Ceci permet de corriger les sauts de ligne de façon à ce qu’ils apparaissent comme ce que vous voyez ici.</span><span class="sxs-lookup"><span data-stu-id="0e48c-212">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="0e48c-213">L’indentation ne doit pas nécessairement être parfaite, mais les lignes `@:</tr><tr>`, `@:<td>`, `@:</td>` et `@:</tr>` doivent chacune tenir sur une seule ligne comme dans l’illustration, sinon vous recevrez une erreur d’exécution.</span><span class="sxs-lookup"><span data-stu-id="0e48c-213">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="0e48c-214">Avec le bloc de nouveau code sélectionné, appuyez trois fois sur Tab pour aligner le nouveau code avec le code existant.</span><span class="sxs-lookup"><span data-stu-id="0e48c-214">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="0e48c-215">Ce problème est résolu dans Visual Studio 2019.</span><span class="sxs-lookup"><span data-stu-id="0e48c-215">This problem is fixed in Visual Studio 2019.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="0e48c-216">Ce code crée un tableau HTML qui a trois colonnes.</span><span class="sxs-lookup"><span data-stu-id="0e48c-216">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="0e48c-217">Dans chaque colonne se trouve une case à cocher, suivie d’une légende qui est constituée du numéro et du titre du cours.</span><span class="sxs-lookup"><span data-stu-id="0e48c-217">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="0e48c-218">Toutes les cases à cocher ont le même nom (« selectedCourses »), qui indique au classeur de modèles qu’ils doivent être traités comme un groupe.</span><span class="sxs-lookup"><span data-stu-id="0e48c-218">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="0e48c-219">L’attribut de valeur de chaque case à cocher est défini sur la valeur de `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-219">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="0e48c-220">Quand la page est envoyée, le classeur de modèles passe un tableau au contrôleur, constitué des valeurs de `CourseID` seulement pour les cases qui sont cochées.</span><span class="sxs-lookup"><span data-stu-id="0e48c-220">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="0e48c-221">Quand les cases à cocher sont affichées à l’origine, celles qui correspondent à des cours affectés au formateur ont des attributs cochés, qui les sélectionnent (ils les affichent cochées).</span><span class="sxs-lookup"><span data-stu-id="0e48c-221">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="0e48c-222">Exécutez l’application, sélectionnez l’onglet **Instructors**, puis cliquez sur **Edit** pour un formateur pour voir la page **Edit**.</span><span class="sxs-lookup"><span data-stu-id="0e48c-222">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Page de modification de formateur avec des cours](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="0e48c-224">Changez quelques affectations de cours et cliquez sur Save.</span><span class="sxs-lookup"><span data-stu-id="0e48c-224">Change some course assignments and click Save.</span></span> <span data-ttu-id="0e48c-225">Les modifications que vous apportez sont reflétées dans la page Index.</span><span class="sxs-lookup"><span data-stu-id="0e48c-225">The changes you make are reflected on the Index page.</span></span>

> [!NOTE]
> <span data-ttu-id="0e48c-226">L’approche adoptée ici pour modifier les données des cours des formateurs fonctionne bien le nombre de cours est limité.</span><span class="sxs-lookup"><span data-stu-id="0e48c-226">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="0e48c-227">Pour les collections qui sont beaucoup plus volumineuses, une autre interface utilisateur et une autre méthode de mise à jour seraient nécessaires.</span><span class="sxs-lookup"><span data-stu-id="0e48c-227">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-delete-page"></a><span data-ttu-id="0e48c-228">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="0e48c-228">Update Delete page</span></span>

<span data-ttu-id="0e48c-229">Dans *InstructorsController.cs*, supprimez la méthode `DeleteConfirmed` et insérez à la place le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0e48c-229">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="0e48c-230">Ce code apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="0e48c-230">This code makes the following changes:</span></span>

* <span data-ttu-id="0e48c-231">Il effectue un chargement hâtif pour la propriété de navigation `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="0e48c-231">Does eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="0e48c-232">Vous devez inclure ceci car sinon, EF ne dispose pas d’informations sur les entités `CourseAssignment` associées et ne les supprime pas.</span><span class="sxs-lookup"><span data-stu-id="0e48c-232">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span> <span data-ttu-id="0e48c-233">Pour éviter de devoir les lire ici, vous pouvez configurer une suppression en cascade dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0e48c-233">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="0e48c-234">Si le formateur à supprimer est attribué en tant qu’administrateur d’un département, supprime l’attribution de l'instructeur de ces départements.</span><span class="sxs-lookup"><span data-stu-id="0e48c-234">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-create-page"></a><span data-ttu-id="0e48c-235">Ajouter des emplacements de bureau et des cours à la page Create</span><span class="sxs-lookup"><span data-stu-id="0e48c-235">Add office location and courses to Create page</span></span>

<span data-ttu-id="0e48c-236">Dans *InstructorsController.cs*, supprimez les méthodes HttpGet et HttpPost `Create`, puis ajoutez le code suivant à leur place :</span><span class="sxs-lookup"><span data-stu-id="0e48c-236">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="0e48c-237">Ce code est similaire à ce que vous avez vu pour les méthodes `Edit`, excepté qu’initialement, aucun cours n’est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="0e48c-237">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="0e48c-238">La méthode HttpGet `Create` appelle la méthode `PopulateAssignedCourseData`, non pas en raison du fait qu’il peut exister des cours sélectionnés, mais pour pouvoir fournir une collection vide pour la boucle `foreach` dans la vue (sinon, le code de la vue lèverait une exception de référence null).</span><span class="sxs-lookup"><span data-stu-id="0e48c-238">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="0e48c-239">La méthode HttpPost `Create` ajoute chaque cours sélectionné à la propriété de navigation `CourseAssignments` avant de vérifier s’il y a des erreurs de validation et ajoute le nouveau formateur à la base de données.</span><span class="sxs-lookup"><span data-stu-id="0e48c-239">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="0e48c-240">Les cours sont ajoutés même s’il existe des erreurs de modèle : ainsi, quand c’est le cas (par exemple si l’utilisateur a tapé une date non valide) et que la page est réaffichée avec un message d’erreur, les sélections de cours qui ont été faites sont automatiquement restaurées.</span><span class="sxs-lookup"><span data-stu-id="0e48c-240">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="0e48c-241">Notez que pour pouvoir ajouter des cours à la propriété de navigation `CourseAssignments`, vous devez initialiser la propriété en tant que collection vide :</span><span class="sxs-lookup"><span data-stu-id="0e48c-241">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="0e48c-242">Comme alternative à cette opération dans le code du contrôleur, vous pouvez l’effectuer dans le modèle Instructor en modifiant le getter de propriété pour créer automatiquement la collection si elle n’existe pas, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0e48c-242">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

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

<span data-ttu-id="0e48c-243">Si vous modifiez la propriété `CourseAssignments` de cette façon, vous pouvez supprimer le code d’initialisation explicite de la propriété dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0e48c-243">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="0e48c-244">Dans *Views/Instructor/Create.cshtml*, ajoutez une zone de texte pour l’emplacement du bureau et des cases à cocher pour les cours avant le bouton Envoyer.</span><span class="sxs-lookup"><span data-stu-id="0e48c-244">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="0e48c-245">Comme dans le cas de la page Edit, [corrigez la mise en forme si Visual Studio remet en forme le code quand vous le collez](#notepad).</span><span class="sxs-lookup"><span data-stu-id="0e48c-245">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="0e48c-246">Testez en exécutant l’application et en créant un formateur.</span><span class="sxs-lookup"><span data-stu-id="0e48c-246">Test by running the app and creating an instructor.</span></span>

## <a name="handling-transactions"></a><span data-ttu-id="0e48c-247">Gestion des transactions</span><span class="sxs-lookup"><span data-stu-id="0e48c-247">Handling Transactions</span></span>

<span data-ttu-id="0e48c-248">Comme expliqué dans le [didacticiel CRUD](crud.md), Entity Framework implémente implicitement les transactions.</span><span class="sxs-lookup"><span data-stu-id="0e48c-248">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="0e48c-249">Pour les scénarios où vous avez besoin de plus de contrôle, par exemple si vous voulez inclure des opérations effectuées en dehors d’Entity Framework dans une transaction, consultez [Transactions](/ef/core/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="0e48c-249">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="0e48c-250">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="0e48c-250">Get the code</span></span>

[<span data-ttu-id="0e48c-251">Télécharger ou afficher l’application complète.</span><span class="sxs-lookup"><span data-stu-id="0e48c-251">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="0e48c-252">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="0e48c-252">Next steps</span></span>

<span data-ttu-id="0e48c-253">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="0e48c-253">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0e48c-254">Personnalisez les pages de cours</span><span class="sxs-lookup"><span data-stu-id="0e48c-254">Customized Courses pages</span></span>
> * <span data-ttu-id="0e48c-255">Ajoutez une page de modification de formateur</span><span class="sxs-lookup"><span data-stu-id="0e48c-255">Added Instructors Edit page</span></span>
> * <span data-ttu-id="0e48c-256">Ajoutez des cours à la page de modification</span><span class="sxs-lookup"><span data-stu-id="0e48c-256">Added courses to Edit page</span></span>
> * <span data-ttu-id="0e48c-257">Mettez à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="0e48c-257">Updated Delete page</span></span>
> * <span data-ttu-id="0e48c-258">Emplacements de bureau et cours ajoutés à la page Create</span><span class="sxs-lookup"><span data-stu-id="0e48c-258">Added office location and courses to Create page</span></span>

<span data-ttu-id="0e48c-259">Passez au tutoriel suivant pour découvrir comment gérer les conflits d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="0e48c-259">Advance to the next tutorial to learn how to handle concurrency conflicts.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0e48c-260">Gérer les conflits d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="0e48c-260">Handle concurrency conflicts</span></span>](concurrency.md)
