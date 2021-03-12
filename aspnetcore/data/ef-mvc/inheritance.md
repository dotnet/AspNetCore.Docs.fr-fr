---
title: 'Didacticiel : implémenter l’héritage-ASP.NET MVC avec EF Core'
description: Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données en utilisant Entity Framework Core dans une application ASP.NET Core.
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 299e00b223d287c4e2ece3d1e250581e2a7565e5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586369"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="0d3d9-103">Didacticiel : implémenter l’héritage-ASP.NET MVC avec EF Core</span><span class="sxs-lookup"><span data-stu-id="0d3d9-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="0d3d9-104">Dans le didacticiel précédent, vous avez géré les exceptions d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="0d3d9-105">Ce didacticiel vous indiquera comment implémenter l’héritage dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="0d3d9-106">En programmation orientée objet, vous pouvez utiliser l’héritage pour faciliter la réutilisation du code.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="0d3d9-107">Dans ce didacticiel, vous allez modifier les classes `Instructor` et `Student` afin qu’elles dérivent d’une classe de base `Person` qui contient des propriétés telles que `LastName`, communes aux formateurs et aux étudiants.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="0d3d9-108">Vous n’ajouterez ni ne modifierez aucune page web, mais vous modifierez une partie du code et ces modifications seront automatiquement répercutées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="0d3d9-109">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0d3d9-110">Mapper l’héritage à la base de données</span><span class="sxs-lookup"><span data-stu-id="0d3d9-110">Map inheritance to database</span></span>
> * <span data-ttu-id="0d3d9-111">Créer la classe Person</span><span class="sxs-lookup"><span data-stu-id="0d3d9-111">Create the Person class</span></span>
> * <span data-ttu-id="0d3d9-112">Mettre à jour Student et Instructor</span><span class="sxs-lookup"><span data-stu-id="0d3d9-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="0d3d9-113">Ajouter la classe Person au modèle</span><span class="sxs-lookup"><span data-stu-id="0d3d9-113">Add Person to the model</span></span>
> * <span data-ttu-id="0d3d9-114">Créer et mettre à jour des migrations</span><span class="sxs-lookup"><span data-stu-id="0d3d9-114">Create and update migrations</span></span>
> * <span data-ttu-id="0d3d9-115">Tester l’implémentation</span><span class="sxs-lookup"><span data-stu-id="0d3d9-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d3d9-116">Prérequis</span><span class="sxs-lookup"><span data-stu-id="0d3d9-116">Prerequisites</span></span>

* [<span data-ttu-id="0d3d9-117">Gérer l’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="0d3d9-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="0d3d9-118">Mapper l’héritage à la base de données</span><span class="sxs-lookup"><span data-stu-id="0d3d9-118">Map inheritance to database</span></span>

<span data-ttu-id="0d3d9-119">Les classes `Instructor` et `Student` du modèle de données School ont plusieurs propriétés identiques :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Classes Student et Instructor](inheritance/_static/no-inheritance.png)

<span data-ttu-id="0d3d9-121">Supposons que vous souhaitez éliminer le code redondant pour les propriétés partagées par les entités `Instructor` et `Student`.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="0d3d9-122">Ou vous souhaitez écrire un service capable de mettre en forme les noms sans se soucier du fait que le nom provienne d’un formateur ou d’un étudiant.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="0d3d9-123">Vous pouvez créer une classe de base `Person` qui contient uniquement les propriétés partagées, puis paramétrer les classes `Instructor` et `Student` pour qu’elles héritent de cette classe de base, comme indiqué dans l’illustration suivante :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Classes Student et Instructor dérivant de la classe Person](inheritance/_static/inheritance.png)

<span data-ttu-id="0d3d9-125">Il existe plusieurs façons de représenter cette structure d’héritage dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="0d3d9-126">Vous pouvez avoir une table de personnes qui inclut des informations sur les étudiants et les formateurs dans une table unique.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="0d3d9-127">Certaines des colonnes pourraient s’appliquer uniquement aux formateurs (HireDate), certaines uniquement aux étudiants (EnrollmentDate) et certaines aux deux (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="0d3d9-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="0d3d9-128">En règle générale, vous pouvez avoir une colonne de discriminateur pour indiquer le type que chaque ligne représente.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="0d3d9-129">Par exemple, la colonne de discriminateur peut avoir « Instructor » pour les formateurs et « Student » pour les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Exemple TPH (table par hiérarchie)](inheritance/_static/tph.png)

<span data-ttu-id="0d3d9-131">Ce modèle de génération d’une structure d’héritage d’entité à partir d’une table de base de données unique porte le nom d’héritage TPH (table par hiérarchie).</span><span class="sxs-lookup"><span data-stu-id="0d3d9-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="0d3d9-132">Une alternative consiste à faire en sorte que la base de données ressemble plus à la structure d’héritage.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="0d3d9-133">Par exemple, vous pouvez avoir uniquement les champs de nom dans la table Person, et des tables Instructor et Student distinctes avec les champs de date.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

> [!WARNING]
> <span data-ttu-id="0d3d9-134">La table par type (TPT) n’est pas prise en charge par EF Core 3. x, mais elle a été implémentée dans [EF Core 5,0](/ef/core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="0d3d9-134">Table Per Type (TPT) is not supported by EF Core 3.x, however it is has been implemented in [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan).</span></span>

![Héritage TPT (table par type)](inheritance/_static/tpt.png)

<span data-ttu-id="0d3d9-136">Ce modèle consistant à créer une table de base de données pour chaque classe d’entité est appelé héritage TPT (table par type).</span><span class="sxs-lookup"><span data-stu-id="0d3d9-136">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="0d3d9-137">Une autre option encore consiste à mapper tous les types non abstraits à des tables individuelles.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-137">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="0d3d9-138">Toutes les propriétés d’une classe, y compris les propriétés héritées, sont mappées aux colonnes de la table correspondante.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-138">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="0d3d9-139">Ce modèle porte le nom d’héritage TPC (table par classe concrète).</span><span class="sxs-lookup"><span data-stu-id="0d3d9-139">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="0d3d9-140">Si vous avez implémenté l’héritage TPC pour les classes Person, Student et Instructor comme indiqué précédemment, les tables Student et Instructor ne seraient pas différentes avant et après l’implémentation de l’héritage.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-140">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="0d3d9-141">Les modèles d’héritage TPC et TPH fournissent généralement de meilleures performances que les modèles d’héritage TPT, car les modèles TPT peuvent entraîner des requêtes de jointure complexes.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-141">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="0d3d9-142">Ce didacticiel montre comment implémenter l’héritage TPH.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-142">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="0d3d9-143">TPH est le seul modèle d’héritage pris en charge par Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-143">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="0d3d9-144">Vous allez créer une classe `Person`, modifier les classes `Instructor` et `Student` à dériver de `Person`, ajouter la nouvelle classe à `DbContext` et créer une migration.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-144">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="0d3d9-145">Pensez à enregistrer une copie du projet avant d’apporter les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-145">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="0d3d9-146">Ensuite, si vous rencontrez des problèmes et devez recommencer, il sera plus facile de démarrer à partir du projet enregistré que d’annuler les étapes effectuées pour ce didacticiel ou de retourner au début de la série entière.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-146">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="0d3d9-147">Créer la classe Person</span><span class="sxs-lookup"><span data-stu-id="0d3d9-147">Create the Person class</span></span>

<span data-ttu-id="0d3d9-148">Dans le dossier Models, créez Person.cs et remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-148">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="0d3d9-149">Mettre à jour Student et Instructor</span><span class="sxs-lookup"><span data-stu-id="0d3d9-149">Update Instructor and Student</span></span>

<span data-ttu-id="0d3d9-150">Dans *Instructor.cs*, dérivez la classe Instructor de la classe Person et supprimez les champs de clé et de nom.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-150">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="0d3d9-151">Le code ressemblera à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-151">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="0d3d9-152">Apportez les mêmes modifications dans *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-152">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="0d3d9-153">Ajouter la classe Person au modèle</span><span class="sxs-lookup"><span data-stu-id="0d3d9-153">Add Person to the model</span></span>

<span data-ttu-id="0d3d9-154">Ajoutez le type d’entité Person à *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-154">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="0d3d9-155">Les nouvelles lignes apparaissent en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-155">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="0d3d9-156">C’est là tout ce dont Entity Framework a besoin pour configurer l’héritage TPH (table par hiérarchie).</span><span class="sxs-lookup"><span data-stu-id="0d3d9-156">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="0d3d9-157">Comme vous le verrez, lorsque la base de données sera mise à jour, elle aura une table Person à la place des tables Student et Instructor.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-157">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="0d3d9-158">Créer et mettre à jour des migrations</span><span class="sxs-lookup"><span data-stu-id="0d3d9-158">Create and update migrations</span></span>

<span data-ttu-id="0d3d9-159">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-159">Save your changes and build the project.</span></span> <span data-ttu-id="0d3d9-160">Ensuite, ouvrez la fenêtre de commande dans le dossier du projet et entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-160">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="0d3d9-161">N’exécutez pas encore la commande `database update`.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-161">Don't run the `database update` command yet.</span></span> <span data-ttu-id="0d3d9-162">Cette commande entraîne une perte de données, car elle supprime la table Instructor et renomme la table Student en Person.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-162">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="0d3d9-163">Vous devez fournir un code personnalisé pour préserver les données existantes.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-163">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="0d3d9-164">Ouvrez *migrations/ \<timestamp> _Inheritance. cs* et remplacez la `Up` méthode par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-164">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="0d3d9-165">Ce code prend en charge les tâches de mise à jour de base de données suivantes :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-165">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="0d3d9-166">Supprime les contraintes de clé étrangère et les index qui pointent vers la table Student.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-166">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="0d3d9-167">Renomme la table Instructor en Person et apporte les modifications nécessaires pour qu’elle stocke les données des étudiants :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-167">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="0d3d9-168">Ajoute une EnrollmentDate nullable pour les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-168">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="0d3d9-169">Ajoute la colonne Discriminator pour indiquer si une ligne est pour un étudiant ou un formateur.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-169">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="0d3d9-170">Rend HireDate nullable étant donné que les lignes d’étudiant n’ont pas de dates d’embauche.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-170">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="0d3d9-171">Ajoute un champ temporaire qui sera utilisé pour mettre à jour les clés étrangères qui pointent vers les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-171">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="0d3d9-172">Lorsque vous copiez des étudiants dans la table Person, ils obtiennent de nouvelles valeurs de clés primaires.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-172">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="0d3d9-173">Copie des données à partir de la table Student dans la table Person.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-173">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="0d3d9-174">Cela entraîne l’affectation de nouvelles valeurs de clés primaires aux étudiants.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-174">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="0d3d9-175">Corrige les valeurs de clés étrangères qui pointent vers les étudiants.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-175">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="0d3d9-176">Crée de nouveau les index et les contraintes de clé étrangère, désormais pointées vers la table Person.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-176">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="0d3d9-177">(Si vous aviez utilisé un GUID à la place d’un entier comme type de clé primaire, les valeurs des clés primaires des étudiants n’auraient pas changé, et plusieurs de ces étapes auraient pu être omises.)</span><span class="sxs-lookup"><span data-stu-id="0d3d9-177">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="0d3d9-178">Exécutez la commande `database update` :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-178">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="0d3d9-179">(Dans un système de production, vous apporteriez les modifications correspondantes à la méthode `Down` au cas où vous auriez à l’utiliser pour revenir à la version précédente de la base de données.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-179">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="0d3d9-180">Pour ce didacticiel, vous n’utiliserez pas la méthode `Down`.)</span><span class="sxs-lookup"><span data-stu-id="0d3d9-180">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="0d3d9-181">Vous pouvez obtenir d’autres erreurs en apportant des modifications au schéma dans une base de données qui comporte déjà des données.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-181">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="0d3d9-182">Si vous obtenez des erreurs de migration que vous ne pouvez pas résoudre, vous pouvez changer le nom de la base de données dans la chaîne de connexion ou supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-182">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="0d3d9-183">Avec une nouvelle base de données, il n’y a pas de données à migrer et la commande de mise à jour de base de données a plus de chances de s’exécuter sans erreur.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-183">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="0d3d9-184">Pour supprimer la base de données, utilisez SSOX ou exécutez la commande CLI `database drop`.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-184">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="0d3d9-185">Tester l’implémentation</span><span class="sxs-lookup"><span data-stu-id="0d3d9-185">Test the implementation</span></span>

<span data-ttu-id="0d3d9-186">Exécutez l’application et essayez différentes pages.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-186">Run the app and try various pages.</span></span> <span data-ttu-id="0d3d9-187">Tout fonctionne comme avant.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-187">Everything works the same as it did before.</span></span>

<span data-ttu-id="0d3d9-188">Dans l’**Explorateur d’objets SQL Server**, développez **Data Connections/SchoolContext** puis **Tables**, et vous constatez que les tables Student et Instructor ont été remplacées par une table Person.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-188">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="0d3d9-189">Ouvrez le concepteur de la table Person et vous constatez qu’elle possède toutes les colonnes qui existaient dans les tables Student et Instructor.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-189">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Table Person dans SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="0d3d9-191">Cliquez avec le bouton droit sur la table Person, puis cliquez sur **Afficher les données de la table** pour voir la colonne de discriminateur.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-191">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Table Person dans SSOX - données de la table](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="0d3d9-193">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="0d3d9-193">Get the code</span></span>

[<span data-ttu-id="0d3d9-194">Télécharger ou afficher l’application complète.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-194">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="0d3d9-195">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0d3d9-195">Additional resources</span></span>

<span data-ttu-id="0d3d9-196">Pour plus d’informations sur l’héritage dans Entity Framework Core, consultez [Héritage](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="0d3d9-196">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0d3d9-197">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="0d3d9-197">Next steps</span></span>

<span data-ttu-id="0d3d9-198">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="0d3d9-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0d3d9-199">Mappez l’héritage à la base de données</span><span class="sxs-lookup"><span data-stu-id="0d3d9-199">Mapped inheritance to database</span></span>
> * <span data-ttu-id="0d3d9-200">Créez la classe Person</span><span class="sxs-lookup"><span data-stu-id="0d3d9-200">Created the Person class</span></span>
> * <span data-ttu-id="0d3d9-201">Mettez à jour Student et Instructor</span><span class="sxs-lookup"><span data-stu-id="0d3d9-201">Updated Instructor and Student</span></span>
> * <span data-ttu-id="0d3d9-202">Classe Person ajoutée au modèle</span><span class="sxs-lookup"><span data-stu-id="0d3d9-202">Added Person to the model</span></span>
> * <span data-ttu-id="0d3d9-203">Migrations créées et mises à jour</span><span class="sxs-lookup"><span data-stu-id="0d3d9-203">Created and update migrations</span></span>
> * <span data-ttu-id="0d3d9-204">Implémentation testée</span><span class="sxs-lookup"><span data-stu-id="0d3d9-204">Tested the implementation</span></span>

<span data-ttu-id="0d3d9-205">Passez au tutoriel suivant pour découvrir comment gérer divers scénarios Entity Framework relativement avancés.</span><span class="sxs-lookup"><span data-stu-id="0d3d9-205">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0d3d9-206">Rubrique suivante : Rubriques avancées</span><span class="sxs-lookup"><span data-stu-id="0d3d9-206">Next: Advanced topics</span></span>](advanced.md)