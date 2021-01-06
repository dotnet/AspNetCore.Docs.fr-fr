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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060532"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="e2891-103">Partie 7, Razor pages avec EF Core dans ASP.net Core mettre à jour les données associées</span><span class="sxs-lookup"><span data-stu-id="e2891-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="e2891-104">De [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e2891-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2891-105">Le tutoriel suivant montre comment mettre à jour les données associées.</span><span class="sxs-lookup"><span data-stu-id="e2891-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="e2891-106">Les illustrations suivantes montrent certaines des pages finalisées.</span><span class="sxs-lookup"><span data-stu-id="e2891-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="e2891-107">![Page de modification de cours](update-related-data/_static/course-edit30.png)
![Page de modification de formateur](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="e2891-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="e2891-108">Mettre à jour les pages de création et de modification du cours</span><span class="sxs-lookup"><span data-stu-id="e2891-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="e2891-109">Le code généré automatiquement pour les pages de création et de modification du cours contient la liste déroulante des départements de l’université qui affiche l’ID des départements (un nombre entier).</span><span class="sxs-lookup"><span data-stu-id="e2891-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="e2891-110">La liste déroulante doit afficher le nom du département. Ces deux pages ont donc besoin d’une liste de noms de départements.</span><span class="sxs-lookup"><span data-stu-id="e2891-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="e2891-111">Pour fournir cette liste, utilisez une classe de base pour les pages Create et Edit.</span><span class="sxs-lookup"><span data-stu-id="e2891-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="e2891-112">Créer une classe de base pour la création et la modification d’un cours</span><span class="sxs-lookup"><span data-stu-id="e2891-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="e2891-113">Créez un fichier *Pages/Courses/DepartmentNamePageModel.cs* comportant le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="e2891-114">Le code précédent crée un [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) pour contenir la liste des noms de département. </span><span class="sxs-lookup"><span data-stu-id="e2891-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="e2891-115">Si `selectedDepartment` est spécifié, ce département est sélectionné dans le `SelectList`. </span><span class="sxs-lookup"><span data-stu-id="e2891-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e2891-116">Les classes de modèle de page Create et Edit doivent dériver de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="e2891-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="e2891-117">Mettre à jour le modèle de page de création de cours</span><span class="sxs-lookup"><span data-stu-id="e2891-117">Update the Course Create page model</span></span>

<span data-ttu-id="e2891-118">Un cours est affecté à un département.</span><span class="sxs-lookup"><span data-stu-id="e2891-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="e2891-119">La classe de base des pages Create et Edit fournit un `SelectList` pour la sélection du département.</span><span class="sxs-lookup"><span data-stu-id="e2891-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="e2891-120">La liste déroulante qui utilise `SelectList` définit la propriété de clé étrangère `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="e2891-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e2891-121">EF Core utilise la clé étrangère `Course.DepartmentID` pour charger la propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="e2891-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Créer le cours](update-related-data/_static/ddl30.png)

<span data-ttu-id="e2891-123">Mettez à jour *Pages/Courses/Create.cshtml.cs* à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e2891-124">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e2891-124">The preceding code:</span></span>

* <span data-ttu-id="e2891-125">Dérive de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="e2891-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e2891-126">Utilise `TryUpdateModelAsync` pour empêcher la [sur-validation](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="e2891-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e2891-127">Supprime `ViewData["DepartmentID"]`.</span><span class="sxs-lookup"><span data-stu-id="e2891-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="e2891-128">`DepartmentNameSL` à partir de la classe de base est un modèle fortement typé et sera utilisé par la Razor page.</span><span class="sxs-lookup"><span data-stu-id="e2891-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="e2891-129">Les modèles fortement typés sont préférables aux modèles faiblement typés.</span><span class="sxs-lookup"><span data-stu-id="e2891-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e2891-130">Pour plus d’informations, consultez [Données faiblement typées (ViewData et ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="e2891-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-no-locrazor-page"></a><span data-ttu-id="e2891-131">Mettre à jour la page de création de cours Razor</span><span class="sxs-lookup"><span data-stu-id="e2891-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="e2891-132">Mettez à jour *Pages/Courses/Create.cshtml* à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e2891-133">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="e2891-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e2891-134">Modifie la légende de **DepartmentID** en **Department**. </span><span class="sxs-lookup"><span data-stu-id="e2891-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e2891-135">Il remplace `"ViewBag.DepartmentID"` par `DepartmentNameSL` (à partir de la classe de base).</span><span class="sxs-lookup"><span data-stu-id="e2891-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e2891-136">Il ajoute l’option « Select Department ».</span><span class="sxs-lookup"><span data-stu-id="e2891-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="e2891-137">Si vous n’avez pas encore sélectionné de département, ce changement affiche l’option « Select Department » dans la liste déroulante, plutôt que le premier département.</span><span class="sxs-lookup"><span data-stu-id="e2891-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="e2891-138">Il ajoute un message de validation quand le département n’est pas sélectionné.</span><span class="sxs-lookup"><span data-stu-id="e2891-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e2891-139">La Razor page utilise le [tag Helper Select](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="e2891-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e2891-140">Testez la page Create.</span><span class="sxs-lookup"><span data-stu-id="e2891-140">Test the Create page.</span></span> <span data-ttu-id="e2891-141">La page Create affiche le nom du département plutôt que son ID.</span><span class="sxs-lookup"><span data-stu-id="e2891-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="e2891-142">Mettre à jour le modèle de page de modification du cours</span><span class="sxs-lookup"><span data-stu-id="e2891-142">Update the Course Edit page model</span></span>

<span data-ttu-id="e2891-143">Mettez à jour *Pages/Courses/Edit.cshtml.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="e2891-144">Les modifications sont semblables à celles effectuées dans le modèle de page Create.</span><span class="sxs-lookup"><span data-stu-id="e2891-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e2891-145">Dans le code précédent, `PopulateDepartmentsDropDownList` passe l’ID du département, ce qui sélectionne le département correspondant dans la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="e2891-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-no-locrazor-page"></a><span data-ttu-id="e2891-146">Mettre à jour la page de modification du cours Razor</span><span class="sxs-lookup"><span data-stu-id="e2891-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="e2891-147">Mettez à jour *Pages/Courses/Edit.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e2891-148">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="e2891-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e2891-149">Affiche l’identificateur du cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-149">Displays the course ID.</span></span> <span data-ttu-id="e2891-150">En général la clé primaire (PK) d’une entité n’est pas affichée.</span><span class="sxs-lookup"><span data-stu-id="e2891-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e2891-151">Les clés primaires sont généralement sans signification pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="e2891-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e2891-152">Dans ce cas, la clé est le numéro de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e2891-153">Change la légende de la liste déroulante en remplaçant **DepartmentID** par **Department**.</span><span class="sxs-lookup"><span data-stu-id="e2891-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e2891-154">Il remplace `"ViewBag.DepartmentID"` par `DepartmentNameSL` (à partir de la classe de base).</span><span class="sxs-lookup"><span data-stu-id="e2891-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e2891-155">La page contient un champ masqué (`<input type="hidden">`) pour le numéro de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e2891-156">L’ajout d’un Tag Helper `<label>` avec `asp-for="Course.CourseID"` n’élimine pas la nécessité de la présence du champ masqué.</span><span class="sxs-lookup"><span data-stu-id="e2891-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e2891-157">`<input type="hidden">` est obligatoire pour que le numéro de cours soit inclus dans les données publiées quand l’utilisateur clique sur **Save**.</span><span class="sxs-lookup"><span data-stu-id="e2891-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="e2891-158">Mettre à jour les pages de détails et de suppression du cours</span><span class="sxs-lookup"><span data-stu-id="e2891-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="e2891-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) peut améliorer les performances quand le suivi n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="e2891-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="e2891-160">Mettre à jour les modèles de pages du cours</span><span class="sxs-lookup"><span data-stu-id="e2891-160">Update the Course page models</span></span>

<span data-ttu-id="e2891-161">Mettez à jour *Pages/Courses/Delete.cshtml.cs* à l’aide du code suivant pour ajouter `AsNoTracking` :</span><span class="sxs-lookup"><span data-stu-id="e2891-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="e2891-162">Apportez la même modification dans le fichier *Pages/Courses/Details.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="e2891-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a><span data-ttu-id="e2891-163">Mettre à jour les pages de cours Razor</span><span class="sxs-lookup"><span data-stu-id="e2891-163">Update the Course Razor pages</span></span>

<span data-ttu-id="e2891-164">Mettez à jour *Pages/Courses/Delete.cshtml* à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="e2891-165">Apportez les mêmes modifications à la page Details.</span><span class="sxs-lookup"><span data-stu-id="e2891-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="e2891-166">Tester les pages de cours</span><span class="sxs-lookup"><span data-stu-id="e2891-166">Test the Course pages</span></span>

<span data-ttu-id="e2891-167">Testez les pages de création, de modification, de détails et de suppression.</span><span class="sxs-lookup"><span data-stu-id="e2891-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="e2891-168">Mettre à jour les pages de création et de modification du formateur</span><span class="sxs-lookup"><span data-stu-id="e2891-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="e2891-169">Les instructeurs peuvent enseigner dans n’importe quel nombre de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e2891-170">L’illustration suivante montre la page de modification du formateur avec un tableau comportant les cases à cocher qui correspondent aux cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Page de modification de formateur avec des cours](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="e2891-172">Les cases à cocher permettent de modifier les cours auxquels un formateur est affecté.</span><span class="sxs-lookup"><span data-stu-id="e2891-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e2891-173">Une case à cocher est affichée pour chaque cours de la base de données.</span><span class="sxs-lookup"><span data-stu-id="e2891-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="e2891-174">Les cours auxquels le formateur est affecté sont cochés.</span><span class="sxs-lookup"><span data-stu-id="e2891-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="e2891-175">L’utilisateur peut cocher ou décocher les cases pour changer les affectations de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="e2891-176">Si le nombre de cours était bien plus important, une autre interface utilisateur serait probablement plus pratique.</span><span class="sxs-lookup"><span data-stu-id="e2891-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="e2891-177">Toutefois, la méthode de gestion d’une relation plusieurs à plusieurs présentée ici resterait la même.</span><span class="sxs-lookup"><span data-stu-id="e2891-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="e2891-178">Pour créer ou supprimer des relations, vous devez manipuler une entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="e2891-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="e2891-179">Créer une classe pour les données de cours affectées</span><span class="sxs-lookup"><span data-stu-id="e2891-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="e2891-180">Créez *SchoolViewModels/AssignedCourseData.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e2891-181">La classe `AssignedCourseData` contient des données permettant de créer les cases à cocher pour les cours affectés à un formateur.</span><span class="sxs-lookup"><span data-stu-id="e2891-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="e2891-182">Créer une classe de base pour le modèle de page du formateur</span><span class="sxs-lookup"><span data-stu-id="e2891-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="e2891-183">Créez la classe de base *Pages/Instructors/InstructorCoursesPageModel.cs* :</span><span class="sxs-lookup"><span data-stu-id="e2891-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="e2891-184">`InstructorCoursesPageModel` est la classe de base que vous utiliserez pour les modèles de page Edit et Create.</span><span class="sxs-lookup"><span data-stu-id="e2891-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="e2891-185">`PopulateAssignedCourseData` lit toutes les entités `Course` pour renseigner `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="e2891-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e2891-186">Pour chaque cours, le code définit le `CourseID`, le titre, et si le formateur est affecté au cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e2891-187">Un [HashSet](/dotnet/api/system.collections.generic.hashset-1) est utilisé pour permettre des recherches efficaces.</span><span class="sxs-lookup"><span data-stu-id="e2891-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="e2891-188">Étant donné que la Razor page n’a pas de collection d’entités course, le Binder de modèle ne peut pas mettre à jour automatiquement la `CourseAssignments` propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="e2891-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e2891-189">Au lieu d’utiliser le classeur de modèles pour mettre à jour la propriété de navigation `CourseAssignments`, vous faites cela dans la nouvelle méthode `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="e2891-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="e2891-190">Par conséquent, vous devez exclure la propriété `CourseAssignments` de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="e2891-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="e2891-191">Cela ne nécessite aucune modification du code qui appelle `TryUpdateModel` car vous utilisez la surcharge avec les propriétés déclarées et `CourseAssignments` ne figure pas dans la liste d’inclusion.</span><span class="sxs-lookup"><span data-stu-id="e2891-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload with declared properties and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="e2891-192">Si aucune case n’a été cochée, le code de `UpdateInstructorCourses` initialise la propriété de navigation `CourseAssignments` avec une collection vide et retourne :</span><span class="sxs-lookup"><span data-stu-id="e2891-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="e2891-193">Ensuite, le code exécute une boucle dans tous les cours de la base de données, et compare les cours actuellement affectés au formateur à ceux qui sont sélectionnés dans la vue.</span><span class="sxs-lookup"><span data-stu-id="e2891-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="e2891-194">Pour faciliter des recherches efficaces, les deux dernières collections sont stockées dans des objets `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="e2891-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="e2891-195">Si la case pour un cours a été cochée mais que le cours n’est pas dans la propriété de navigation `Instructor.CourseAssignments`, le cours est ajouté à la collection dans la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="e2891-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="e2891-196">Si la case pour un cours a été cochée mais que le cours est dans la propriété de navigation `Instructor.CourseAssignments`, le cours est supprimé de la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="e2891-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="e2891-197">Gérer l’emplacement du bureau</span><span class="sxs-lookup"><span data-stu-id="e2891-197">Handle office location</span></span>

<span data-ttu-id="e2891-198">La page de modification doit également gérer la relation « un à zéro/zéro ou un » qui existe entre l’entité de l’instructeur et l’entité `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="e2891-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e2891-199">Le code de modification du formateur doit gérer les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="e2891-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="e2891-200">Si l’utilisateur efface l’attribution d'un bureau, supprimer l'entité `OfficeAssignment`. </span><span class="sxs-lookup"><span data-stu-id="e2891-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e2891-201">Si l’utilisateur entre une attribution et qu'elle était vide, créer une nouvelle entité `OfficeAssignment`. </span><span class="sxs-lookup"><span data-stu-id="e2891-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e2891-202">Si l’utilisateur change l’attribution de bureau, mettre à jour l’entité `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="e2891-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="e2891-203">Mettre à jour le modèle de page de modification du formateur</span><span class="sxs-lookup"><span data-stu-id="e2891-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="e2891-204">Mettez à jour *Pages/Instructors/Edit.cshtml.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="e2891-205">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e2891-205">The preceding code:</span></span>

* <span data-ttu-id="e2891-206">Obtient l’entité `Instructor` actuelle de la base de données à l’aide d’un chargement hâtif des propriétés de navigation `OfficeAssignment`, `CourseAssignment` et `CourseAssignment.Course`.</span><span class="sxs-lookup"><span data-stu-id="e2891-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="e2891-207">Met à jour l’entité `Instructor` récupérée avec les valeurs du classeur de modèles.</span><span class="sxs-lookup"><span data-stu-id="e2891-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e2891-208">`TryUpdateModel` empêche la [survalidation](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="e2891-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e2891-209">Si l’emplacement du bureau est vide, définit `Instructor.OfficeAssignment` avec la valeur null.</span><span class="sxs-lookup"><span data-stu-id="e2891-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e2891-210">Lorsque `Instructor.OfficeAssignment` est null, la ligne correspondante dans la table `OfficeAssignment` est supprimée.</span><span class="sxs-lookup"><span data-stu-id="e2891-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="e2891-211">Appelle `PopulateAssignedCourseData` dans `OnGetAsync` afin de fournir des informations pour les cases à cocher à l’aide de la classe de modèle de vue `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="e2891-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="e2891-212">Appelle `UpdateInstructorCourses` dans `OnPostAsync` pour appliquer les informations des cases à cocher à l’entité de formateur en cours de modification.</span><span class="sxs-lookup"><span data-stu-id="e2891-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="e2891-213">Appelle `PopulateAssignedCourseData` et `UpdateInstructorCourses` dans `OnPostAsync` si `TryUpdateModel` échoue.</span><span class="sxs-lookup"><span data-stu-id="e2891-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="e2891-214">Ces appels de méthode restaurent les données de cours affectées qui ont été entrées dans la page lorsqu’elles sont réaffichées avec un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="e2891-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-no-locrazor-page"></a><span data-ttu-id="e2891-215">Mettre à jour la page de modification du formateur Razor</span><span class="sxs-lookup"><span data-stu-id="e2891-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="e2891-216">Mettez à jour *Pages/Instructors/Edit.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="e2891-217">Le code précédent crée une table HTML avec trois colonnes.</span><span class="sxs-lookup"><span data-stu-id="e2891-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e2891-218">Chaque colonne a une case à cocher et une légende contenant le numéro et le titre du cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="e2891-219">Les cases à cocher ont toutes le même nom (« selectedCourses »).</span><span class="sxs-lookup"><span data-stu-id="e2891-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e2891-220">L’utilisation du même nom signale au classeur de modèles qu’il doit les traiter en tant que groupe.</span><span class="sxs-lookup"><span data-stu-id="e2891-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e2891-221">L’attribut de valeur de chaque case à cocher est défini sur `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="e2891-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="e2891-222">Quand la page est publiée, le classeur de modèles passe un tableau composé des valeurs `CourseID` correspondant uniquement aux cases à cocher sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="e2891-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="e2891-223">Lors de l’affichage initial des cases à cocher, les cours affectés au formateur sont cochés.</span><span class="sxs-lookup"><span data-stu-id="e2891-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="e2891-224">Remarque : L’approche adoptée ici pour modifier les données de cours des formateurs fonctionne bien quand il y a un nombre limité de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e2891-225">Pour les collections qui sont beaucoup plus grandes, une interface utilisateur différente et une autre méthode de mise à jour seraient plus utiles et plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="e2891-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="e2891-226">Exécutez l’application et testez la page de modification du formateur qui vient d’être mise à jour.</span><span class="sxs-lookup"><span data-stu-id="e2891-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="e2891-227">Changez certaines affectations de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-227">Change some course assignments.</span></span> <span data-ttu-id="e2891-228">Les modifications sont répercutées dans la page Index.</span><span class="sxs-lookup"><span data-stu-id="e2891-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="e2891-229">Mettre à jour la page de création du formateur</span><span class="sxs-lookup"><span data-stu-id="e2891-229">Update the Instructor Create page</span></span>

<span data-ttu-id="e2891-230">Mettez à jour le modèle de page de création de formateur et la Razor page avec du code similaire à la page d’édition :</span><span class="sxs-lookup"><span data-stu-id="e2891-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="e2891-231">Testez la page de création de l'instructeur.</span><span class="sxs-lookup"><span data-stu-id="e2891-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="e2891-232">Mettre à jour la page de suppression du formateur</span><span class="sxs-lookup"><span data-stu-id="e2891-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="e2891-233">Mettez à jour *Pages/Instructors/Delete.cshtml.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="e2891-234">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="e2891-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e2891-235">Utilise un chargement hâtif pour la propriété de navigation `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="e2891-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e2891-236">Les `CourseAssignments` doivent être inclus ou ils ne seront pas supprimés lorsque l'instructeur est supprimé.</span><span class="sxs-lookup"><span data-stu-id="e2891-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e2891-237">Pour éviter d’avoir à les lire, configurez la suppression en cascade dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="e2891-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e2891-238">Si le formateur à supprimer est attribué en tant qu’administrateur d’un département, supprime l’attribution de l'instructeur de ces départements.</span><span class="sxs-lookup"><span data-stu-id="e2891-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="e2891-239">Exécutez l’application et testez la page de suppression.</span><span class="sxs-lookup"><span data-stu-id="e2891-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e2891-240">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="e2891-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2891-241">[Didacticiel précédent](xref:data/ef-rp/read-related-data) 
>  [Didacticiel suivant](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="e2891-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e2891-242">Ce didacticiel illustre la mise à jour de données associées.</span><span class="sxs-lookup"><span data-stu-id="e2891-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="e2891-243">Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, [téléchargez ou affichez l’application terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="e2891-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="e2891-244">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e2891-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e2891-245">Les illustrations suivantes montrent certaines des pages terminées.</span><span class="sxs-lookup"><span data-stu-id="e2891-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="e2891-246">![Page de modification de cours](update-related-data/_static/course-edit.png)
![Page de modification de formateur](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="e2891-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="e2891-247">Examinez et testez les pages des cours Create et Edit.</span><span class="sxs-lookup"><span data-stu-id="e2891-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="e2891-248">Créez un nouveau cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-248">Create a new course.</span></span> <span data-ttu-id="e2891-249">Le service est sélectionné par sa clé primaire (entier), pas par son nom.</span><span class="sxs-lookup"><span data-stu-id="e2891-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="e2891-250">Modifiez le nouveau cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-250">Edit the new course.</span></span> <span data-ttu-id="e2891-251">Lorsque vous avez terminé le test, supprimez le nouveau cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="e2891-252">Créer une classe de base pour partager le code commun</span><span class="sxs-lookup"><span data-stu-id="e2891-252">Create a base class to share common code</span></span>

<span data-ttu-id="e2891-253">Les pages de création et de modification de cours ont chacune besoin d’une liste de noms de départements.</span><span class="sxs-lookup"><span data-stu-id="e2891-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="e2891-254">Créez la classe de base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* pour les pages Create et Edit :</span><span class="sxs-lookup"><span data-stu-id="e2891-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="e2891-255">Le code précédent crée un [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) pour contenir la liste des noms de département. </span><span class="sxs-lookup"><span data-stu-id="e2891-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="e2891-256">Si `selectedDepartment` est spécifié, ce département est sélectionné dans le `SelectList`. </span><span class="sxs-lookup"><span data-stu-id="e2891-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e2891-257">Les classes de modèle de page Create et Edit doivent dériver de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="e2891-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="e2891-258">Personnaliser les pages de cours</span><span class="sxs-lookup"><span data-stu-id="e2891-258">Customize the Courses Pages</span></span>

<span data-ttu-id="e2891-259">Quand une entité de cours est créée, elle doit avoir une relation à un département existant.</span><span class="sxs-lookup"><span data-stu-id="e2891-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="e2891-260">Pour ajouter un département lors de la création d’un cours, la classe de base pour Create et Edit contient une liste déroulante de sélection du département.</span><span class="sxs-lookup"><span data-stu-id="e2891-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="e2891-261">La liste déroulante définit la propriété de clé étrangère `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="e2891-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e2891-262">EF Core utilise la clé étrangère `Course.DepartmentID` pour charger la propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="e2891-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Créer le cours](update-related-data/_static/ddl.png)

<span data-ttu-id="e2891-264">Mettez à jour le modèle de la page Create avec le code suivant : </span><span class="sxs-lookup"><span data-stu-id="e2891-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="e2891-265">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e2891-265">The preceding code:</span></span>

* <span data-ttu-id="e2891-266">Dérive de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="e2891-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e2891-267">Utilise `TryUpdateModelAsync` pour empêcher la [sur-validation](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="e2891-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e2891-268">Il remplace `ViewData["DepartmentID"]` par `DepartmentNameSL` (à partir de la classe de base).</span><span class="sxs-lookup"><span data-stu-id="e2891-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e2891-269">`ViewData["DepartmentID"]` est remplacé par le `DepartmentNameSL` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="e2891-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="e2891-270">Les modèles fortement typés sont préférables aux modèles faiblement typés.</span><span class="sxs-lookup"><span data-stu-id="e2891-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e2891-271">Pour plus d’informations, consultez [Données faiblement typées (ViewData et ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="e2891-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="e2891-272">Mettre à jour la page de création de cours</span><span class="sxs-lookup"><span data-stu-id="e2891-272">Update the Courses Create page</span></span>

<span data-ttu-id="e2891-273">Mettez à jour *Pages/Courses/Create.cshtml* à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e2891-274">Le balisage précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="e2891-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="e2891-275">Modifie la légende de **DepartmentID** en **Department**. </span><span class="sxs-lookup"><span data-stu-id="e2891-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e2891-276">Il remplace `"ViewBag.DepartmentID"` par `DepartmentNameSL` (à partir de la classe de base).</span><span class="sxs-lookup"><span data-stu-id="e2891-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e2891-277">Il ajoute l’option « Select Department ».</span><span class="sxs-lookup"><span data-stu-id="e2891-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="e2891-278">Cette modification entraîne l’affichage de « Select Department » plutôt que du premier département.</span><span class="sxs-lookup"><span data-stu-id="e2891-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="e2891-279">Il ajoute un message de validation quand le département n’est pas sélectionné.</span><span class="sxs-lookup"><span data-stu-id="e2891-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e2891-280">La Razor page utilise le [tag Helper Select](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="e2891-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e2891-281">Testez la page Create.</span><span class="sxs-lookup"><span data-stu-id="e2891-281">Test the Create page.</span></span> <span data-ttu-id="e2891-282">La page Create affiche le nom du département plutôt que son ID.</span><span class="sxs-lookup"><span data-stu-id="e2891-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="e2891-283">Mise à jour de la page Edit d'un cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="e2891-284">Remplacez le code *Pages/Courses/Edit.cshtml.cs* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="e2891-285">Les modifications sont semblables à celles effectuées dans le modèle de page Create.</span><span class="sxs-lookup"><span data-stu-id="e2891-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e2891-286">Dans le code précédent, `PopulateDepartmentsDropDownList` transmet l’ID de département, ce qui sélectionne le département spécifié dans la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="e2891-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="e2891-287">Mettez à jour *Pages/Courses/Edit.cshtml* avec le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e2891-288">Le balisage précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="e2891-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="e2891-289">Affiche l’identificateur du cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-289">Displays the course ID.</span></span> <span data-ttu-id="e2891-290">En général la clé primaire (PK) d’une entité n’est pas affichée.</span><span class="sxs-lookup"><span data-stu-id="e2891-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e2891-291">Les clés primaires sont généralement sans signification pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="e2891-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e2891-292">Dans ce cas, la clé est le numéro de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e2891-293">Modifie la légende de **DepartmentID** en **Department**. </span><span class="sxs-lookup"><span data-stu-id="e2891-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e2891-294">Il remplace `"ViewBag.DepartmentID"` par `DepartmentNameSL` (à partir de la classe de base).</span><span class="sxs-lookup"><span data-stu-id="e2891-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e2891-295">La page contient un champ masqué (`<input type="hidden">`) pour le numéro de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e2891-296">L’ajout d’un Tag Helper `<label>` avec `asp-for="Course.CourseID"` n’élimine pas la nécessité de la présence du champ masqué.</span><span class="sxs-lookup"><span data-stu-id="e2891-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e2891-297">`<input type="hidden">` est obligatoire pour que le numéro de cours soit inclus dans les données publiées quand l’utilisateur clique sur **Save**.</span><span class="sxs-lookup"><span data-stu-id="e2891-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="e2891-298">Testez le code mis à jour.</span><span class="sxs-lookup"><span data-stu-id="e2891-298">Test the updated code.</span></span> <span data-ttu-id="e2891-299">Créez, modifiez et supprimez un cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="e2891-300">Ajouter AsNoTracking aux modèles de page Details et Delete</span><span class="sxs-lookup"><span data-stu-id="e2891-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="e2891-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) peut améliorer les performances quand le suivi n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="e2891-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="e2891-302">Ajoutez `AsNoTracking` dans les modèles de page Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="e2891-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="e2891-303">Le code suivant montre le modèle de page Delete mis à jour :</span><span class="sxs-lookup"><span data-stu-id="e2891-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="e2891-304">Mettez à jour la méthode `OnGetAsync` dans le fichier *Pages/Courses/Details.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="e2891-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="e2891-305">Modifier les pages Delete et Details</span><span class="sxs-lookup"><span data-stu-id="e2891-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="e2891-306">Mettez à jour la Razor page de suppression avec le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="e2891-307">Apportez les mêmes modifications à la page Details.</span><span class="sxs-lookup"><span data-stu-id="e2891-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="e2891-308">Tester les pages de cours</span><span class="sxs-lookup"><span data-stu-id="e2891-308">Test the Course pages</span></span>

<span data-ttu-id="e2891-309">Testez les fonctionnalités de création, de modification, de détails et de suppression.</span><span class="sxs-lookup"><span data-stu-id="e2891-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="e2891-310">Mettre à jour les pages d'instructeur</span><span class="sxs-lookup"><span data-stu-id="e2891-310">Update the instructor pages</span></span>

<span data-ttu-id="e2891-311">Les sections suivantes mettent à jour les pages d'instructeur.</span><span class="sxs-lookup"><span data-stu-id="e2891-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="e2891-312">Ajouter l’emplacement du bureau</span><span class="sxs-lookup"><span data-stu-id="e2891-312">Add office location</span></span>

<span data-ttu-id="e2891-313">Lors de la modification d’un enregistrement de formateur, vous souhaiterez peut-être mettre à jour l’attribution de bureau du formateur.</span><span class="sxs-lookup"><span data-stu-id="e2891-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="e2891-314">L’entité `Instructor` a une relation un-à-zéro-ou-un avec l’entité `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="e2891-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e2891-315">Le code de formateur doit gérer ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="e2891-315">The instructor code must handle:</span></span>

* <span data-ttu-id="e2891-316">Si l’utilisateur efface l’attribution d'un bureau, supprimer l'entité `OfficeAssignment`. </span><span class="sxs-lookup"><span data-stu-id="e2891-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e2891-317">Si l’utilisateur entre une attribution et qu'elle était vide, créer une nouvelle entité `OfficeAssignment`. </span><span class="sxs-lookup"><span data-stu-id="e2891-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e2891-318">Si l’utilisateur change l’attribution de bureau, mettre à jour l’entité `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="e2891-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="e2891-319">Mettez à jour le modèle de page Edit d'instructeur avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="e2891-320">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e2891-320">The preceding code:</span></span>

* <span data-ttu-id="e2891-321">Obtient l'entité `Instructor` en cours à partir de la base de données à l’aide d’un chargement hâtif de la propriété de navigation `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="e2891-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="e2891-322">Met à jour l’entité `Instructor` récupérée avec les valeurs du classeur de modèles.</span><span class="sxs-lookup"><span data-stu-id="e2891-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e2891-323">`TryUpdateModel` empêche la [survalidation](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="e2891-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e2891-324">Si l’emplacement du bureau est vide, définit `Instructor.OfficeAssignment` avec la valeur null.</span><span class="sxs-lookup"><span data-stu-id="e2891-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e2891-325">Lorsque `Instructor.OfficeAssignment` est null, la ligne correspondante dans la table `OfficeAssignment` est supprimée.</span><span class="sxs-lookup"><span data-stu-id="e2891-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="e2891-326">Mettre à jour la page de modification de formateur</span><span class="sxs-lookup"><span data-stu-id="e2891-326">Update the instructor Edit page</span></span>

<span data-ttu-id="e2891-327">Mettez à jour *Pages/Instructors/Edit.cshtml* avec l’emplacement du bureau :</span><span class="sxs-lookup"><span data-stu-id="e2891-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="e2891-328">Vérifiez que vous pouvez modifier l'emplacement de bureau d'un instructeur.</span><span class="sxs-lookup"><span data-stu-id="e2891-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="e2891-329">Ajouter des affectations de cours à la page Edit de l'instructeur</span><span class="sxs-lookup"><span data-stu-id="e2891-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="e2891-330">Les instructeurs peuvent enseigner dans n’importe quel nombre de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e2891-331">Dans cette section, vous ajoutez la possibilité de modifier les affectations de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="e2891-332">L’illustration suivante montre la page de modification de l'instructeur mise à jour :</span><span class="sxs-lookup"><span data-stu-id="e2891-332">The following image shows the updated instructor Edit page:</span></span>

![Page de modification de formateur avec des cours](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="e2891-334">`Course` et `Instructor` entretiennent une relation plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="e2891-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="e2891-335">Pour ajouter et supprimer des relations, vous ajoutez et supprimez des entités à partir du jeu d’entités de jointures `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="e2891-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="e2891-336">Les cases à cocher permettent de changer les cours auxquels un formateur est assigné.</span><span class="sxs-lookup"><span data-stu-id="e2891-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e2891-337">Une case à cocher est affichée pour chaque cours dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="e2891-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="e2891-338">Les cours auxquels le formateur est affecté sont cochés.</span><span class="sxs-lookup"><span data-stu-id="e2891-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="e2891-339">L’utilisateur peut cocher ou décocher les cases pour changer les affectations de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="e2891-340">Si le nombre de cours était beaucoup plus élevé :</span><span class="sxs-lookup"><span data-stu-id="e2891-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="e2891-341">Vous utiliseriez sans doute une interface utilisateur différente pour afficher les cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="e2891-342">La méthode de manipulation d’une entité de jointure pour créer ou supprimer des relations ne changerait pas.</span><span class="sxs-lookup"><span data-stu-id="e2891-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="e2891-343">Ajouter des classes pour prendre en charge Create et Edit des pages d'instructeur</span><span class="sxs-lookup"><span data-stu-id="e2891-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="e2891-344">Créez *SchoolViewModels/AssignedCourseData.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e2891-345">La classe `AssignedCourseData` contient des données pour créer les cases à cocher pour les cours attribués par un formateur.</span><span class="sxs-lookup"><span data-stu-id="e2891-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="e2891-346">Créez la classe de base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="e2891-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="e2891-347">`InstructorCoursesPageModel` est la classe de base que vous utiliserez pour les modèles de page Edit et Create.</span><span class="sxs-lookup"><span data-stu-id="e2891-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="e2891-348">`PopulateAssignedCourseData` lit toutes les entités `Course` pour renseigner `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="e2891-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e2891-349">Pour chaque cours, le code définit le `CourseID`, le titre, et si le formateur est affecté au cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e2891-350">Un [HashSet](/dotnet/api/system.collections.generic.hashset-1) est utilisé pour créer des recherches efficaces.</span><span class="sxs-lookup"><span data-stu-id="e2891-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="e2891-351">Modèle de page de modification de formateur</span><span class="sxs-lookup"><span data-stu-id="e2891-351">Instructors Edit page model</span></span>

<span data-ttu-id="e2891-352">Mettez à jour le modèle de page Edit d'instructeur avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="e2891-353">Le code précédent gère les changements d’affectation de bureau.</span><span class="sxs-lookup"><span data-stu-id="e2891-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="e2891-354">Mettre à jour la vue de l’instructeur Razor :</span><span class="sxs-lookup"><span data-stu-id="e2891-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="e2891-355">Quand vous collez le code dans Visual Studio, les sauts de ligne sont modifiés de telle manière que le code est rompu.</span><span class="sxs-lookup"><span data-stu-id="e2891-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="e2891-356">Appuyez une fois sur Ctrl + Z pour annuler la mise en forme automatique.</span><span class="sxs-lookup"><span data-stu-id="e2891-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="e2891-357">Ctrl + Z corrige les sauts de ligne afin qu’ils aient l’aspect visible ici.</span><span class="sxs-lookup"><span data-stu-id="e2891-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="e2891-358">La mise en retrait ne doit pas nécessairement être parfaite, mais les lignes `@:</tr><tr>`, `@:<td>`, `@:</td>` et `@:</tr>` doivent chacune être sur une seule distincte comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="e2891-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="e2891-359">Avec le bloc de nouveau code sélectionné, appuyez trois fois sur Tab pour aligner le nouveau code avec le code existant.</span><span class="sxs-lookup"><span data-stu-id="e2891-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="e2891-360">Votez ou vérifiez l’état de ce bogue [avec ce lien](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="e2891-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="e2891-361">Le code précédent crée une table HTML avec trois colonnes.</span><span class="sxs-lookup"><span data-stu-id="e2891-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e2891-362">Chaque colonne a une case à cocher et une légende contenant le numéro et le titre du cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="e2891-363">Les cases à cocher ont toutes le même nom (« selectedCourses »).</span><span class="sxs-lookup"><span data-stu-id="e2891-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e2891-364">L’utilisation du même nom signale au classeur de modèles qu’il doit les traiter en tant que groupe.</span><span class="sxs-lookup"><span data-stu-id="e2891-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e2891-365">L’attribut de valeur de chaque case à cocher est défini sur `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="e2891-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="e2891-366">Quand la page est publiée, le classeur de modèles transmet un tableau composé des valeurs `CourseID` correspondant uniquement aux cases à cocher sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="e2891-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="e2891-367">Lors de l’affichage initial des cases à cocher, les cours affectés au formateur ont des attributs cochés.</span><span class="sxs-lookup"><span data-stu-id="e2891-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="e2891-368">Exécutez l’application et testez la page Edit des formateurs mise à jour.</span><span class="sxs-lookup"><span data-stu-id="e2891-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="e2891-369">Changez certaines affectations de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-369">Change some course assignments.</span></span> <span data-ttu-id="e2891-370">Les modifications sont répercutées dans la page Index.</span><span class="sxs-lookup"><span data-stu-id="e2891-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="e2891-371">Remarque : L’approche adoptée ici pour modifier les données de cours des formateurs fonctionne bien quand il y a un nombre limité de cours.</span><span class="sxs-lookup"><span data-stu-id="e2891-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e2891-372">Pour les collections qui sont beaucoup plus grandes, une interface utilisateur différente et une autre méthode de mise à jour seraient plus utiles et plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="e2891-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="e2891-373">Mise à jour de la page de création des instructeurs</span><span class="sxs-lookup"><span data-stu-id="e2891-373">Update the instructors Create page</span></span>

<span data-ttu-id="e2891-374">Mettez à jour le modèle de page de création de formateur avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="e2891-375">Le code précédent est similaire au code de *Pages/Instructors/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2891-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="e2891-376">Mettez à jour la Razor page de création de l’instructeur avec le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="e2891-377">Testez la page de création de l'instructeur.</span><span class="sxs-lookup"><span data-stu-id="e2891-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="e2891-378">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="e2891-378">Update the Delete page</span></span>

<span data-ttu-id="e2891-379">Mettez à jour le modèle de page de suppression avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e2891-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="e2891-380">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="e2891-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e2891-381">Utilise un chargement hâtif pour la propriété de navigation `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="e2891-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e2891-382">Les `CourseAssignments` doivent être inclus ou ils ne seront pas supprimés lorsque l'instructeur est supprimé.</span><span class="sxs-lookup"><span data-stu-id="e2891-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e2891-383">Pour éviter d’avoir à les lire, configurez la suppression en cascade dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="e2891-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e2891-384">Si le formateur à supprimer est attribué en tant qu’administrateur d’un département, supprime l’attribution de l'instructeur de ces départements.</span><span class="sxs-lookup"><span data-stu-id="e2891-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2891-385">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e2891-385">Additional resources</span></span>

* [<span data-ttu-id="e2891-386">Version YouTube de ce tutoriel (Partie 1)</span><span class="sxs-lookup"><span data-stu-id="e2891-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="e2891-387">Version YouTube de ce tutoriel (Partie 2)</span><span class="sxs-lookup"><span data-stu-id="e2891-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="e2891-388">[Précédent](xref:data/ef-rp/read-related-data) 
>  [Suivant](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="e2891-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
