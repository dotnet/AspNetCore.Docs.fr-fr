---
title: Razor Pages avec Entity Framework Core dans ASP.NET Core-didacticiel 1 sur 8
author: rick-anderson
description: Montre comment créer une Razor application pages à l’aide de Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 2f6299511b568a70c638dd5d4c735bf22adb5e95
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057423"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="60490-103">Razor Pages avec Entity Framework Core dans ASP.NET Core-didacticiel 1 sur 8</span><span class="sxs-lookup"><span data-stu-id="60490-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="60490-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="60490-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="60490-105">Il s’agit de la première d’une série de didacticiels qui montrent comment utiliser Entity Framework (EF) Core dans une application [ASP.net Core Razor pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="60490-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="60490-106">Dans ces tutoriels, un site web est créé pour une université fictive nommée Contoso.</span><span class="sxs-lookup"><span data-stu-id="60490-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="60490-107">Le site comprend des fonctionnalités comme l’admission des étudiants, la création de cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="60490-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="60490-108">Le didacticiel utilise l’approche code First.</span><span class="sxs-lookup"><span data-stu-id="60490-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="60490-109">Pour plus d’informations sur ce didacticiel avec la première approche basée sur la base de données, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="60490-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="60490-110">Télécharger ou afficher l’application terminée.</span><span class="sxs-lookup"><span data-stu-id="60490-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="60490-111">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="60490-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="60490-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="60490-112">Prerequisites</span></span>

* <span data-ttu-id="60490-113">Si vous Razor débutez avec des pages, consultez la série de didacticiels [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) avant de commencer celle-ci.</span><span class="sxs-lookup"><span data-stu-id="60490-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="60490-116">Moteurs de base de données</span><span class="sxs-lookup"><span data-stu-id="60490-116">Database engines</span></span>

<span data-ttu-id="60490-117">Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="60490-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="60490-118">Les instructions Visual Studio Code utilisent [SQLite](https://www.sqlite.org/), moteur de base de données multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="60490-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="60490-119">Si vous choisissez d’utiliser SQLite, téléchargez et installez un outil tiers pour la gestion et l’affichage d’une base de données SQLite, comme [Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="60490-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="60490-120">Dépannage</span><span class="sxs-lookup"><span data-stu-id="60490-120">Troubleshooting</span></span>

<span data-ttu-id="60490-121">Si vous rencontrez un problème que vous ne pouvez pas résoudre, comparez votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="60490-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="60490-122">Un bon moyen d’obtenir de l’aide est de poster une question sur StackOverflow.com en utilisant le [mot-clé ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou le [mot-clé EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="60490-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="60490-123">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="60490-123">The sample app</span></span>

<span data-ttu-id="60490-124">L’application générée dans ces didacticiels est le site web de base d’une université.</span><span class="sxs-lookup"><span data-stu-id="60490-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="60490-125">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="60490-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="60490-126">Voici quelques-uns des écrans créés dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="60490-126">Here are a few of the screens created in the tutorial.</span></span>

![Page d’index des étudiants](intro/_static/students-index30.png)

![Page de modification des étudiants](intro/_static/student-edit30.png)

<span data-ttu-id="60490-129">Le style de l’interface utilisateur de ce site repose sur les modèles de projet intégrés.</span><span class="sxs-lookup"><span data-stu-id="60490-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="60490-130">Ce didacticiel porte sur l’utilisation de EF Core avec ASP.NET Core, et non sur la personnalisation de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60490-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="60490-131">Créer le projet d’application web</span><span class="sxs-lookup"><span data-stu-id="60490-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="60490-133">Démarrez Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="60490-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="60490-134">Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **ASP.net Core application Web** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="60490-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="60490-135">Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `ContosoUniversity` pour **nom du projet**.</span><span class="sxs-lookup"><span data-stu-id="60490-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="60490-136">Il est important d’utiliser ce nom exact, y compris la mise en majuscules, donc chaque correspond à la `namespace` copie du code.</span><span class="sxs-lookup"><span data-stu-id="60490-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="60490-137">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="60490-137">Select **Create**.</span></span>
1. <span data-ttu-id="60490-138">Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :</span><span class="sxs-lookup"><span data-stu-id="60490-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="60490-139">**.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.</span><span class="sxs-lookup"><span data-stu-id="60490-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="60490-140">**ASP.net Core application Web**.</span><span class="sxs-lookup"><span data-stu-id="60490-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="60490-141"> 
       Créer ![ Boîte de dialogue Nouveau projet de ASP.NET Core](~/data/ef-rp/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="60490-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-rp/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="60490-143">Dans un terminal, accédez au dossier dans lequel le dossier de projet doit être créé.</span><span class="sxs-lookup"><span data-stu-id="60490-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="60490-144">Exécutez les commandes suivantes pour créer un Razor projet de pages et `cd` dans le dossier du nouveau projet :</span><span class="sxs-lookup"><span data-stu-id="60490-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="60490-145">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="60490-145">Set up the site style</span></span>

<span data-ttu-id="60490-146">Copiez et collez le code suivant dans le fichier *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="60490-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="60490-147">Le fichier de disposition définit l’en-tête, le pied de page et le menu du site.</span><span class="sxs-lookup"><span data-stu-id="60490-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="60490-148">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="60490-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="60490-149">Chaque occurrence de « ContosoUniversity » à « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="60490-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="60490-150">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="60490-150">There are three occurrences.</span></span>
* <span data-ttu-id="60490-151">Les entrées du menu d' **hébergement** et de **confidentialité** sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="60490-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="60490-152">Des entrées sont ajoutées pour **à propos** de, **étudiants**, **cours**, **instructeurs** et **services**.</span><span class="sxs-lookup"><span data-stu-id="60490-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="60490-153">Dans *pages/index. cshtml*, remplacez le contenu du fichier par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="60490-154">Le code précédent remplace le texte relatif à ASP.NET Core par le texte de cette application.</span><span class="sxs-lookup"><span data-stu-id="60490-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="60490-155">Exécutez l’application pour vérifier que la page d’accueil (« Home ») s’affiche.</span><span class="sxs-lookup"><span data-stu-id="60490-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="60490-156">le modèle de données</span><span class="sxs-lookup"><span data-stu-id="60490-156">The data model</span></span>

<span data-ttu-id="60490-157">Les sections suivantes créent un modèle de données :</span><span class="sxs-lookup"><span data-stu-id="60490-157">The following sections create a data model:</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="60490-159">Un étudiant peut s’inscrire à un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="60490-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="60490-160">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="60490-160">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

* <span data-ttu-id="60490-162">Créez un dossier *Models* dans le dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="60490-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="60490-163">Créez *Models/Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="60490-164">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="60490-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="60490-165">Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.</span><span class="sxs-lookup"><span data-stu-id="60490-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="60490-166">L’autre nom reconnu automatiquement de la clé primaire de classe `Student` est `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="60490-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="60490-167">Pour plus d’informations, consultez [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="60490-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="60490-168">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="60490-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="60490-169">Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="60490-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="60490-170">Dans ce cas, la propriété `Enrollments` d’une entité `Student` contient toutes les entités `Enrollment` associées à cet étudiant.</span><span class="sxs-lookup"><span data-stu-id="60490-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="60490-171">Par exemple, si une ligne Student dans la base de données est associée à deux lignes Enrollment, la propriété de navigation `Enrollments` contient ces deux entités Enrollment.</span><span class="sxs-lookup"><span data-stu-id="60490-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="60490-172">Dans la base de données, une ligne Enrollment est associée à une ligne Student si sa colonne StudentID contient la valeur d’ID de l’étudiant.</span><span class="sxs-lookup"><span data-stu-id="60490-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="60490-173">Par exemple, supposez qu’une ligne Student présente un ID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="60490-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="60490-174">Les lignes Enrollment associées auront un StudentID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="60490-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="60490-175">StudentID est une *clé étrangère* dans la table Enrollment.</span><span class="sxs-lookup"><span data-stu-id="60490-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="60490-176">La propriété `Enrollments` est définie en tant que `ICollection<Enrollment>`, car plusieurs entités Enrollment associées peuvent exister.</span><span class="sxs-lookup"><span data-stu-id="60490-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="60490-177">Vous pouvez utiliser d’autres types de collection, comme `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="60490-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="60490-178">Quand vous utilisez `ICollection<Enrollment>`, EF Core crée une collection `HashSet<Enrollment>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="60490-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="60490-179">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="60490-179">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="60490-181">Créez *Models/Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="60490-182">La propriété `EnrollmentID` est la clé primaire ; cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même.</span><span class="sxs-lookup"><span data-stu-id="60490-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="60490-183">Pour un modèle de données de production, choisissez un modèle et utilisez-le systématiquement.</span><span class="sxs-lookup"><span data-stu-id="60490-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="60490-184">Ce tutoriel utilise les deux pour montrer qu’ils fonctionnent tous les deux.</span><span class="sxs-lookup"><span data-stu-id="60490-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="60490-185">L’utilisation de `ID` sans `classname` facilite l’implémentation de certaines modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="60490-186">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="60490-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="60490-187">La présence du point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade`[accepte les valeurs Null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="60490-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="60490-188">Une note (Grade) de valeur Null est différente d’une note zéro : la valeur Null signifie que la note n’est pas connue ou qu’elle n’a pas encore été attribuée.</span><span class="sxs-lookup"><span data-stu-id="60490-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="60490-189">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="60490-190">Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="60490-191">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="60490-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="60490-192">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="60490-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="60490-193">EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="60490-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="60490-194">Par exemple, `StudentID` est la clé étrangère pour la propriété de navigation `Student`, car la clé primaire de l’entité `Student` est `ID`.</span><span class="sxs-lookup"><span data-stu-id="60490-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="60490-195">Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="60490-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="60490-196">Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="60490-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="60490-197">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="60490-197">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="60490-199">Créez *Models/Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="60490-200">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="60490-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="60490-201">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="60490-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="60490-202">L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="60490-203">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="60490-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="60490-204">Générer automatiquement des modèles de pages Student</span><span class="sxs-lookup"><span data-stu-id="60490-204">Scaffold Student pages</span></span>

<span data-ttu-id="60490-205">Dans cette section, vous allez utiliser l’outil de génération de modèles automatique ASP.NET Core pour générer :</span><span class="sxs-lookup"><span data-stu-id="60490-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="60490-206">Classe EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="60490-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="60490-207">Le contexte est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données déterminé.</span><span class="sxs-lookup"><span data-stu-id="60490-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="60490-208">Il dérive de la classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="60490-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="60490-209">Razor les pages qui gèrent les opérations de création, lecture, mise à jour et suppression (CRUD) pour l' `Student` entité.</span><span class="sxs-lookup"><span data-stu-id="60490-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60490-211">Créez un dossier *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="60490-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="60490-212">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Pages/Students*, puis sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="60490-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="60490-213">Dans la boîte de dialogue **Ajouter un nouvel élément de structure** :</span><span class="sxs-lookup"><span data-stu-id="60490-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="60490-214">Dans l’onglet de gauche, sélectionnez **installé > Razor pages > communes**</span><span class="sxs-lookup"><span data-stu-id="60490-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="60490-215">Sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="60490-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="60490-216">Dans la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="60490-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="60490-217">Dans la liste déroulante **Classe de modèle**, sélectionnez **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="60490-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="60490-218">Dans la ligne **Classe du contexte de données**, sélectionnez le signe **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="60490-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="60490-219">Modifiez le nom du contexte de données pour qu’il se termine par `SchoolContext` plutôt que `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="60490-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="60490-220">Nom du contexte mis à jour : `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="60490-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
    * <span data-ttu-id="60490-221">Sélectionnez **Ajouter** pour terminer l’ajout de la classe de contexte de données.</span><span class="sxs-lookup"><span data-stu-id="60490-221">Select **Add** to finish adding the data context class.</span></span>
   * <span data-ttu-id="60490-222">Sélectionnez **Ajouter** pour terminer la boîte de dialogue **Ajouter des Razor pages** .</span><span class="sxs-lookup"><span data-stu-id="60490-222">Select **Add** to finish the **Add Razor Pages** dialog.</span></span>

<span data-ttu-id="60490-223">Les packages suivants sont automatiquement installés :</span><span class="sxs-lookup"><span data-stu-id="60490-223">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="60490-225">Exécutez les commandes CLI .NET Core suivantes pour installer les packages NuGet nécessaires :</span><span class="sxs-lookup"><span data-stu-id="60490-225">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="60490-226">Le package Microsoft.VisualStudio.Web.CodeGeneration.Design est requis pour la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="60490-226">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="60490-227">Bien que l’application ne soit pas appelée à utiliser SQL Server, l’outil de génération de modèles automatique a besoin du package SQL Server.</span><span class="sxs-lookup"><span data-stu-id="60490-227">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="60490-228">Créez un dossier *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="60490-228">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="60490-229">Exécutez la commande suivante pour installer l’[outil de génération de modèles automatique aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="60490-229">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="60490-230">Exécutez la commande suivante pour générer automatiquement des modèles de pages Student.</span><span class="sxs-lookup"><span data-stu-id="60490-230">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="60490-231">**Sur Windows**</span><span class="sxs-lookup"><span data-stu-id="60490-231">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="60490-232">**Sur macOS ou Linux**</span><span class="sxs-lookup"><span data-stu-id="60490-232">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="60490-233">Si l’étape précédente échoue, générez le projet et recommencez l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="60490-233">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="60490-234">Le processus de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="60490-234">The scaffolding process:</span></span>

* <span data-ttu-id="60490-235">Crée Razor des pages dans le dossier *pages/élèves* :</span><span class="sxs-lookup"><span data-stu-id="60490-235">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="60490-236">*Create.cshtml* et *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-236">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="60490-237">*Delete.cshtml* et *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-237">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="60490-238">*Details.cshtml* et *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-238">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="60490-239">*Edit.cshtml* et *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-239">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="60490-240">*Index.cshtml* et *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-240">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="60490-241">Crée *Data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="60490-241">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="60490-242">Ajoute le contexte à l’injection de dépendances dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="60490-242">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="60490-243">Ajoute une chaîne de connexion de base de données à *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="60490-243">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="60490-244">Chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="60490-244">Database connection string</span></span>

<span data-ttu-id="60490-245">L’outil de génération de modèles automatique génère une chaîne de connexion dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="60490-245">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="60490-247">La chaîne de connexion spécifie [SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)base de données locale :</span><span class="sxs-lookup"><span data-stu-id="60490-247">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="60490-248">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="60490-248">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="60490-249">Par défaut, la Base de données locale crée des fichiers *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="60490-249">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="60490-251">Raccourcissez la chaîne de connexion SQLite à *cu. db*:</span><span class="sxs-lookup"><span data-stu-id="60490-251">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="60490-252">Mettre à jour la classe du contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="60490-252">Update the database context class</span></span>

<span data-ttu-id="60490-253">La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données déterminé est la classe du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-253">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="60490-254">Le contexte est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="60490-254">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="60490-255">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-255">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="60490-256">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="60490-256">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="60490-257">Mettez à jour *Data/SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-257">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="60490-258">Le code précédent passe du singulier `DbSet<Student> Student` au pluriel `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="60490-258">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="60490-259">Pour que le Razor Code des pages corresponde au nouveau `DBSet` nom, effectuez une modification globale à partir de : `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="60490-259">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="60490-260">À: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="60490-260">to: `_context.Students.`</span></span>

<span data-ttu-id="60490-261">Il y a 8 occurrences.</span><span class="sxs-lookup"><span data-stu-id="60490-261">There are 8 occurrences.</span></span>

<span data-ttu-id="60490-262">Un jeu d’entités contenant plusieurs entités, de nombreux développeurs préfèrent que les `DBSet` noms de propriété soient au pluriel.</span><span class="sxs-lookup"><span data-stu-id="60490-262">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="60490-263">Code mis en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="60490-263">The highlighted code:</span></span>

* <span data-ttu-id="60490-264">Crée une [propriété \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="60490-264">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="60490-265">Dans la terminologie d’EF Core :</span><span class="sxs-lookup"><span data-stu-id="60490-265">In EF Core terminology:</span></span>
  * <span data-ttu-id="60490-266">Un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-266">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="60490-267">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="60490-267">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="60490-268">Appelle <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="60490-268">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="60490-269">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="60490-269">`OnModelCreating`:</span></span>
  * <span data-ttu-id="60490-270">Est appelé quand `SchoolContext` a été initialisé, mais avant que le modèle ait été verrouillé et utilisé pour initialiser le contexte.</span><span class="sxs-lookup"><span data-stu-id="60490-270">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="60490-271">Est requis, car ultérieurement dans le didacticiel, l' `Student` entité aura des références aux autres entités.</span><span class="sxs-lookup"><span data-stu-id="60490-271">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="60490-272">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="60490-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="60490-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="60490-273">Startup.cs</span></span>

<span data-ttu-id="60490-274">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="60490-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="60490-275">Les services tels que le `SchoolContext` sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="60490-275">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="60490-276">Ces services sont fournis par les composants qui requièrent ces services, tels que les Razor pages, via des paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="60490-276">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="60490-277">Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="60490-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="60490-278">L’outil de génération de modèles automatique a inscrit automatiquement la classe du contexte dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="60490-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="60490-280">Les lignes en surbrillance suivantes ont été ajoutées par le générateur de modèles :</span><span class="sxs-lookup"><span data-stu-id="60490-280">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="60490-282">Vérifiez le code ajouté par les appels de l’échafaudage `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="60490-282">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="60490-283">Pour plus d’informations sur l’utilisation d’une base de données de production [, consultez utiliser SQLite pour le développement, SQL Server pour la production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="60490-283">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="60490-284">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="60490-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="60490-285">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="60490-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="60490-286">Ajouter le filtre d’exception de base de données</span><span class="sxs-lookup"><span data-stu-id="60490-286">Add the database exception filter</span></span>

<span data-ttu-id="60490-287">Ajoutez <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> à `ConfigureServices` , comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-287">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-288">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="60490-289">Ajoutez le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="60490-289">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="60490-290">Dans le PMC, entrez ce qui suit pour ajouter le package NuGet :</span><span class="sxs-lookup"><span data-stu-id="60490-290">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="60490-292">Le `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` package NuGet fournit ASP.net Core intergiciel pour Entity Framework Core pages d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="60490-292">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="60490-293">Cet intergiciel (middleware) permet de détecter et de diagnostiquer les erreurs avec Entity Framework Core migrations.</span><span class="sxs-lookup"><span data-stu-id="60490-293">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="60490-294">Le `AddDatabaseDeveloperPageExceptionFilter` fournit des informations d’erreur utiles dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="60490-294">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="60490-295">Créer la base de données</span><span class="sxs-lookup"><span data-stu-id="60490-295">Create the database</span></span>

<span data-ttu-id="60490-296">Mettez à jour *Program.cs* pour créer la base de données si elle n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="60490-296">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="60490-297">La méthode [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) n’effectue aucune action s’il existe une base de données pour le contexte.</span><span class="sxs-lookup"><span data-stu-id="60490-297">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="60490-298">S’il n’existe pas de base de données, elle crée la base de données et le schéma.</span><span class="sxs-lookup"><span data-stu-id="60490-298">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="60490-299">`EnsureCreated` active le workflow suivant pour gérer les modifications du modèle de données :</span><span class="sxs-lookup"><span data-stu-id="60490-299">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="60490-300">Supprimez la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-300">Delete the database.</span></span> <span data-ttu-id="60490-301">Toutes les données existantes sont perdues.</span><span class="sxs-lookup"><span data-stu-id="60490-301">Any existing data is lost.</span></span>
* <span data-ttu-id="60490-302">Modifiez le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-302">Change the data model.</span></span> <span data-ttu-id="60490-303">Par exemple, ajoutez un champ `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="60490-303">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="60490-304">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="60490-304">Run the app.</span></span>
* <span data-ttu-id="60490-305">`EnsureCreated` crée une base de données avec le nouveau schéma.</span><span class="sxs-lookup"><span data-stu-id="60490-305">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="60490-306">Ce workflow fonctionne bien à un stade précoce du développement, quand le schéma évolue rapidement, aussi longtemps que vous n’avez pas besoin de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="60490-306">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="60490-307">La situation est différente quand les données qui ont été entrées dans la base de données doivent être conservées.</span><span class="sxs-lookup"><span data-stu-id="60490-307">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="60490-308">Dans ce cas, procédez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="60490-308">When that is the case, use migrations.</span></span>

<span data-ttu-id="60490-309">Plus tard dans cette série de tutoriels, vous supprimerez la base de données créée par `EnsureCreated` et procéderez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="60490-309">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="60490-310">Une base de données créée par `EnsureCreated` ne peut pas être mise à jour via des migrations.</span><span class="sxs-lookup"><span data-stu-id="60490-310">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="60490-311">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="60490-311">Test the app</span></span>

* <span data-ttu-id="60490-312">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="60490-312">Run the app.</span></span>
* <span data-ttu-id="60490-313">Sélectionnez le lien **Students**, puis **Créer nouveau**.</span><span class="sxs-lookup"><span data-stu-id="60490-313">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="60490-314">Testez les liens Edit, Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="60490-314">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="60490-315">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="60490-315">Seed the database</span></span>

<span data-ttu-id="60490-316">La méthode `EnsureCreated` crée une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="60490-316">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="60490-317">Cette section ajoute du code qui remplit la base de données avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="60490-317">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="60490-318">Créez *Data/DbInitializer.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-318">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="60490-319">Le code vérifie si des étudiants figurent dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-319">The code checks if there are any students in the database.</span></span> <span data-ttu-id="60490-320">S’il n’y a pas d’étudiants, il ajoute des données de test à la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-320">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="60490-321">Il crée les données de test dans des tableaux et non dans des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="60490-321">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="60490-322">Dans *Program.cs*, remplacez l’appel `EnsureCreated` par un appel `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="60490-322">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="60490-323">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-323">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="60490-324">Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package** :</span><span class="sxs-lookup"><span data-stu-id="60490-324">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="60490-325">Répondre avec `Y` pour supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-325">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-326">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-326">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="60490-327">Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="60490-327">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="60490-328">Redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="60490-328">Restart the app.</span></span>
* <span data-ttu-id="60490-329">Sélectionnez la page Students pour examiner les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="60490-329">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="60490-330">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="60490-330">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-331">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60490-332">Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="60490-332">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="60490-333">Dans SSOX, sélectionnez **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="60490-333">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="60490-334">Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.</span><span class="sxs-lookup"><span data-stu-id="60490-334">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="60490-335">Développez le nœud **Tables**.</span><span class="sxs-lookup"><span data-stu-id="60490-335">Expand the **Tables** node.</span></span>
* <span data-ttu-id="60490-336">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="60490-336">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="60490-337">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher le code** pour voir comment le modèle `Student` est mappé au schéma de la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-337">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-338">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-338">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="60490-339">Utilisez votre outil SQLite pour examiner le schéma de base de données et les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="60490-339">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="60490-340">Le fichier de base de données est nommé *CU.db* et se trouve dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="60490-340">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="60490-341">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="60490-341">Asynchronous code</span></span>

<span data-ttu-id="60490-342">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="60490-342">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="60490-343">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="60490-343">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="60490-344">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="60490-344">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="60490-345">Avec le code synchrone, de nombreux threads peuvent être associés alors qu’ils ne fonctionnent pas car ils sont en attente de la fin des e/s.</span><span class="sxs-lookup"><span data-stu-id="60490-345">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="60490-346">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="60490-346">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="60490-347">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="60490-347">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="60490-348">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="60490-348">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="60490-349">Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="60490-349">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="60490-350">Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="60490-350">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="60490-351">Le mot clé `async` fait en sorte que le compilateur :</span><span class="sxs-lookup"><span data-stu-id="60490-351">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="60490-352">Génère des rappels pour les parties du corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="60490-352">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="60490-353">Crée l’objet [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="60490-353">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="60490-354">Le type de retour `Task` représente le travail en cours.</span><span class="sxs-lookup"><span data-stu-id="60490-354">The `Task` return type represents ongoing work.</span></span>
* <span data-ttu-id="60490-355">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="60490-355">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="60490-356">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="60490-356">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="60490-357">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="60490-357">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="60490-358">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="60490-358">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="60490-359">Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :</span><span class="sxs-lookup"><span data-stu-id="60490-359">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="60490-360">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="60490-360">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="60490-361">Cela inclut `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="60490-361">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="60490-362">mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="60490-362">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="60490-363">Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="60490-363">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="60490-364">Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-364">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="60490-365">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="60490-365">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="60490-366">Considérations relatives aux performances</span><span class="sxs-lookup"><span data-stu-id="60490-366">Performance considerations</span></span>

<span data-ttu-id="60490-367">En général, une page Web ne doit pas charger un nombre arbitraire de lignes.</span><span class="sxs-lookup"><span data-stu-id="60490-367">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="60490-368">Une requête doit utiliser la pagination ou une approche de limitation.</span><span class="sxs-lookup"><span data-stu-id="60490-368">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="60490-369">Par exemple, la requête ci-dessus peut utiliser `Take` pour limiter les lignes retournées :</span><span class="sxs-lookup"><span data-stu-id="60490-369">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="60490-370">L’énumération d’une table volumineuse dans une vue peut retourner une réponse HTTP 200 partiellement construite si une exception de base de données se produit de façon partielle via l’énumération.</span><span class="sxs-lookup"><span data-stu-id="60490-370">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="60490-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> la valeur par défaut est 1024.</span><span class="sxs-lookup"><span data-stu-id="60490-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="60490-372">Les jeux de codes suivants `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="60490-372">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="60490-373">La pagination est traitée plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="60490-373">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="60490-374">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="60490-374">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="60490-375">Didacticiel suivant</span><span class="sxs-lookup"><span data-stu-id="60490-375">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="60490-376">Il s’agit de la première d’une série de didacticiels qui montrent comment utiliser Entity Framework (EF) Core dans une application [ASP.net Core Razor pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="60490-376">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="60490-377">Dans ces tutoriels, un site web est créé pour une université fictive nommée Contoso.</span><span class="sxs-lookup"><span data-stu-id="60490-377">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="60490-378">Le site comprend des fonctionnalités comme l’admission des étudiants, la création de cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="60490-378">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="60490-379">Le didacticiel utilise l’approche code First.</span><span class="sxs-lookup"><span data-stu-id="60490-379">The tutorial uses the code first approach.</span></span> <span data-ttu-id="60490-380">Pour plus d’informations sur ce didacticiel avec la première approche basée sur la base de données, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="60490-380">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="60490-381">Télécharger ou afficher l’application terminée.</span><span class="sxs-lookup"><span data-stu-id="60490-381">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="60490-382">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="60490-382">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="60490-383">Prérequis</span><span class="sxs-lookup"><span data-stu-id="60490-383">Prerequisites</span></span>

* <span data-ttu-id="60490-384">Si vous Razor débutez avec des pages, consultez la série de didacticiels [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) avant de commencer celle-ci.</span><span class="sxs-lookup"><span data-stu-id="60490-384">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-385">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-385">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="60490-387">Moteurs de base de données</span><span class="sxs-lookup"><span data-stu-id="60490-387">Database engines</span></span>

<span data-ttu-id="60490-388">Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="60490-388">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="60490-389">Les instructions Visual Studio Code utilisent [SQLite](https://www.sqlite.org/), moteur de base de données multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="60490-389">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="60490-390">Si vous choisissez d’utiliser SQLite, téléchargez et installez un outil tiers pour la gestion et l’affichage d’une base de données SQLite, comme [Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="60490-390">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="60490-391">Dépannage</span><span class="sxs-lookup"><span data-stu-id="60490-391">Troubleshooting</span></span>

<span data-ttu-id="60490-392">Si vous rencontrez un problème que vous ne pouvez pas résoudre, comparez votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="60490-392">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="60490-393">Un bon moyen d’obtenir de l’aide est de poster une question sur StackOverflow.com en utilisant le [mot-clé ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou le [mot-clé EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="60490-393">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="60490-394">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="60490-394">The sample app</span></span>

<span data-ttu-id="60490-395">L’application générée dans ces didacticiels est le site web de base d’une université.</span><span class="sxs-lookup"><span data-stu-id="60490-395">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="60490-396">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="60490-396">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="60490-397">Voici quelques-uns des écrans créés dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="60490-397">Here are a few of the screens created in the tutorial.</span></span>

![Page d’index des étudiants](intro/_static/students-index30.png)

![Page de modification des étudiants](intro/_static/student-edit30.png)

<span data-ttu-id="60490-400">Le style de l’interface utilisateur de ce site repose sur les modèles de projet intégrés.</span><span class="sxs-lookup"><span data-stu-id="60490-400">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="60490-401">Le tutoriel traite essentiellement de l’utilisation d’EF Core, et non de la façon de personnaliser l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60490-401">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="60490-402">Suivez le lien en haut de la page pour obtenir le code source du projet terminé.</span><span class="sxs-lookup"><span data-stu-id="60490-402">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="60490-403">Le dossier *cu30* contient le code de la version 3.0 d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60490-403">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="60490-404">Les fichiers qui reflètent l’état du code pour les tutoriels 1-7 se trouvent dans le dossier *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="60490-404">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-405">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="60490-406">Pour exécuter l’application après avoir téléchargé le projet terminé :</span><span class="sxs-lookup"><span data-stu-id="60490-406">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="60490-407">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="60490-407">Build the project.</span></span>
* <span data-ttu-id="60490-408">Dans la console du Gestionnaire de package (PMC), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="60490-408">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="60490-409">Exécutez le projet pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-409">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="60490-411">Pour exécuter l’application après avoir téléchargé le projet terminé :</span><span class="sxs-lookup"><span data-stu-id="60490-411">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="60490-412">Supprimez *ContosoUniversity.csproj*, puis renommez *ContosoUniversitySQLite.csproj* en *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="60490-412">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="60490-413">Dans *Program.cs*, commentez-le `#define Startup` pour qu’il `StartupSQLite` soit utilisé.</span><span class="sxs-lookup"><span data-stu-id="60490-413">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="60490-414">Supprimez *appSettings.json* et renommez *appSettingsSQLite.json* en *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="60490-414">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="60490-415">Supprimez le dossier *Migrations* et renommez *MigrationsSQL* en *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="60490-415">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="60490-416">Effectuez une recherche globale `#if SQLiteVersion` et supprimez `#if SQLiteVersion` et l' `#endif` instruction associée.</span><span class="sxs-lookup"><span data-stu-id="60490-416">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="60490-417">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="60490-417">Build the project.</span></span>
* <span data-ttu-id="60490-418">Dans une invite de commandes, exécutez la commande suivante dans le dossier du projet :</span><span class="sxs-lookup"><span data-stu-id="60490-418">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="60490-419">Dans votre outil SQLite, exécutez l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="60490-419">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="60490-420">Exécutez le projet pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-420">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="60490-421">Créer le projet d’application web</span><span class="sxs-lookup"><span data-stu-id="60490-421">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60490-423">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="60490-423">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="60490-424">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="60490-424">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="60490-425">Nommez le projet *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="60490-425">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="60490-426">Il est important d’utiliser ce nom exact, en respectant l’utilisation des majuscules, de sorte que les espaces de noms correspondent au moment où le code est copié et collé.</span><span class="sxs-lookup"><span data-stu-id="60490-426">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="60490-427">Sélectionnez **.NET Core** et **ASP.NET Core 3.0** dans les listes déroulantes, puis **Application web**.</span><span class="sxs-lookup"><span data-stu-id="60490-427">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-428">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-428">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="60490-429">Dans un terminal, accédez au dossier dans lequel le dossier de projet doit être créé.</span><span class="sxs-lookup"><span data-stu-id="60490-429">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="60490-430">Exécutez les commandes suivantes pour créer un Razor projet de pages et `cd` dans le dossier du nouveau projet :</span><span class="sxs-lookup"><span data-stu-id="60490-430">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="60490-431">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="60490-431">Set up the site style</span></span>

<span data-ttu-id="60490-432">Configurez l’en-tête, le pied de page et le menu du site en mettant à jour *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="60490-432">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="60490-433">Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="60490-433">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="60490-434">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="60490-434">There are three occurrences.</span></span>

* <span data-ttu-id="60490-435">Supprimez les entrées de menu **Home** et **Privacy** et ajoutez les entrées **About**, **Students**, **Courses**, **Instructors** et **Departments**.</span><span class="sxs-lookup"><span data-stu-id="60490-435">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="60490-436">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="60490-436">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="60490-437">Dans *Pages/Index.cshtml*, remplacez le contenu du fichier par le code suivant de façon à remplacer le texte relatif à ASP.NET Core par le texte se rapportant à cette application :</span><span class="sxs-lookup"><span data-stu-id="60490-437">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="60490-438">Exécutez l’application pour vérifier que la page d’accueil (« Home ») s’affiche.</span><span class="sxs-lookup"><span data-stu-id="60490-438">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="60490-439">le modèle de données</span><span class="sxs-lookup"><span data-stu-id="60490-439">The data model</span></span>

<span data-ttu-id="60490-440">Les sections suivantes créent un modèle de données :</span><span class="sxs-lookup"><span data-stu-id="60490-440">The following sections create a data model:</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="60490-442">Un étudiant peut s’inscrire à un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="60490-442">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="60490-443">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="60490-443">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

* <span data-ttu-id="60490-445">Créez un dossier *Models* dans le dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="60490-445">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="60490-446">Créez *Models/Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-446">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="60490-447">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="60490-447">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="60490-448">Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.</span><span class="sxs-lookup"><span data-stu-id="60490-448">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="60490-449">L’autre nom reconnu automatiquement de la clé primaire de classe `Student` est `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="60490-449">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="60490-450">Pour plus d’informations, consultez [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="60490-450">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="60490-451">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="60490-451">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="60490-452">Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="60490-452">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="60490-453">Dans ce cas, la propriété `Enrollments` d’une entité `Student` contient toutes les entités `Enrollment` associées à cet étudiant.</span><span class="sxs-lookup"><span data-stu-id="60490-453">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="60490-454">Par exemple, si une ligne Student dans la base de données est associée à deux lignes Enrollment, la propriété de navigation `Enrollments` contient ces deux entités Enrollment.</span><span class="sxs-lookup"><span data-stu-id="60490-454">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="60490-455">Dans la base de données, une ligne Enrollment est associée à une ligne Student si sa colonne StudentID contient la valeur d’ID de l’étudiant.</span><span class="sxs-lookup"><span data-stu-id="60490-455">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="60490-456">Par exemple, supposez qu’une ligne Student présente un ID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="60490-456">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="60490-457">Les lignes Enrollment associées auront un StudentID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="60490-457">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="60490-458">StudentID est une *clé étrangère* dans la table Enrollment.</span><span class="sxs-lookup"><span data-stu-id="60490-458">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="60490-459">La propriété `Enrollments` est définie en tant que `ICollection<Enrollment>`, car plusieurs entités Enrollment associées peuvent exister.</span><span class="sxs-lookup"><span data-stu-id="60490-459">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="60490-460">Vous pouvez utiliser d’autres types de collection, comme `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="60490-460">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="60490-461">Quand vous utilisez `ICollection<Enrollment>`, EF Core crée une collection `HashSet<Enrollment>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="60490-461">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="60490-462">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="60490-462">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="60490-464">Créez *Models/Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-464">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="60490-465">La propriété `EnrollmentID` est la clé primaire ; cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même.</span><span class="sxs-lookup"><span data-stu-id="60490-465">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="60490-466">Pour un modèle de données de production, choisissez un modèle et utilisez-le systématiquement.</span><span class="sxs-lookup"><span data-stu-id="60490-466">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="60490-467">Ce tutoriel utilise les deux pour montrer qu’ils fonctionnent tous les deux.</span><span class="sxs-lookup"><span data-stu-id="60490-467">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="60490-468">L’utilisation de `ID` sans `classname` facilite l’implémentation de certaines modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-468">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="60490-469">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="60490-469">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="60490-470">La présence du point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade`[accepte les valeurs Null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="60490-470">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="60490-471">Une note (Grade) de valeur Null est différente d’une note zéro : la valeur Null signifie que la note n’est pas connue ou qu’elle n’a pas encore été attribuée.</span><span class="sxs-lookup"><span data-stu-id="60490-471">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="60490-472">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-472">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="60490-473">Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-473">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="60490-474">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="60490-474">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="60490-475">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="60490-475">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="60490-476">EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="60490-476">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="60490-477">Par exemple, `StudentID` est la clé étrangère pour la propriété de navigation `Student`, car la clé primaire de l’entité `Student` est `ID`.</span><span class="sxs-lookup"><span data-stu-id="60490-477">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="60490-478">Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="60490-478">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="60490-479">Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="60490-479">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="60490-480">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="60490-480">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="60490-482">Créez *Models/Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-482">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="60490-483">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="60490-483">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="60490-484">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="60490-484">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="60490-485">L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-485">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="60490-486">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="60490-486">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="60490-487">Générer automatiquement des modèles de pages Student</span><span class="sxs-lookup"><span data-stu-id="60490-487">Scaffold Student pages</span></span>

<span data-ttu-id="60490-488">Dans cette section, vous allez utiliser l’outil de génération de modèles automatique ASP.NET Core pour générer :</span><span class="sxs-lookup"><span data-stu-id="60490-488">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="60490-489">Une classe de *contexte* EF Core.</span><span class="sxs-lookup"><span data-stu-id="60490-489">An EF Core *context* class.</span></span> <span data-ttu-id="60490-490">Le contexte est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données déterminé.</span><span class="sxs-lookup"><span data-stu-id="60490-490">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="60490-491">Il dérive de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="60490-491">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="60490-492">Razor les pages qui gèrent les opérations de création, lecture, mise à jour et suppression (CRUD) pour l' `Student` entité.</span><span class="sxs-lookup"><span data-stu-id="60490-492">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-493">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60490-494">Créez un dossier *Students* dans le dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="60490-494">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="60490-495">Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Pages/Students*, puis sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="60490-495">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="60490-496">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="60490-496">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="60490-497">Dans la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="60490-497">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="60490-498">Dans la liste déroulante **Classe de modèle**, sélectionnez **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="60490-498">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="60490-499">Dans la ligne **Classe du contexte de données**, sélectionnez le signe **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="60490-499">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="60490-500">Remplacez le nom du contexte de données *ContosoUniversity.Models.ContosoUniversityContext* par *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="60490-500">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="60490-501">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="60490-501">Select **Add**.</span></span>

<span data-ttu-id="60490-502">Les packages suivants sont automatiquement installés :</span><span class="sxs-lookup"><span data-stu-id="60490-502">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-503">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-503">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="60490-504">Exécutez les commandes CLI .NET Core suivantes pour installer les packages NuGet nécessaires :</span><span class="sxs-lookup"><span data-stu-id="60490-504">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="60490-505">Le package Microsoft.VisualStudio.Web.CodeGeneration.Design est requis pour la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="60490-505">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="60490-506">Bien que l’application ne soit pas appelée à utiliser SQL Server, l’outil de génération de modèles automatique a besoin du package SQL Server.</span><span class="sxs-lookup"><span data-stu-id="60490-506">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="60490-507">Créez un dossier *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="60490-507">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="60490-508">Exécutez la commande suivante pour installer l’[outil de génération de modèles automatique aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="60490-508">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="60490-509">Exécutez la commande suivante pour générer automatiquement des modèles de pages Student.</span><span class="sxs-lookup"><span data-stu-id="60490-509">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="60490-510">**Sur Windows**</span><span class="sxs-lookup"><span data-stu-id="60490-510">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="60490-511">**Sur macOS ou Linux**</span><span class="sxs-lookup"><span data-stu-id="60490-511">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="60490-512">Si vous rencontrez un problème à l’étape précédente, générez le projet et recommencez l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="60490-512">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="60490-513">Le processus de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="60490-513">The scaffolding process:</span></span>

* <span data-ttu-id="60490-514">Crée Razor des pages dans le dossier *pages/élèves* :</span><span class="sxs-lookup"><span data-stu-id="60490-514">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="60490-515">*Create.cshtml* et *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-515">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="60490-516">*Delete.cshtml* et *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-516">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="60490-517">*Details.cshtml* et *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-517">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="60490-518">*Edit.cshtml* et *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-518">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="60490-519">*Index.cshtml* et *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-519">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="60490-520">Crée *Data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="60490-520">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="60490-521">Ajoute le contexte à l’injection de dépendances dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="60490-521">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="60490-522">Ajoute une chaîne de connexion de base de données à *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="60490-522">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="60490-523">Chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="60490-523">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-524">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-524">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="60490-525">Le *appsettings.json* fichier spécifie la chaîne de connexion [SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)la base de données locale.</span><span class="sxs-lookup"><span data-stu-id="60490-525">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="60490-526">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="60490-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="60490-527">Par défaut, la Base de données locale crée des fichiers *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="60490-527">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-528">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-528">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="60490-529">Modifiez la chaîne de connexion de sorte qu’elle pointe vers un fichier de base de données SQLite nommé *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="60490-529">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="60490-530">Mettre à jour la classe du contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="60490-530">Update the database context class</span></span>

<span data-ttu-id="60490-531">La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données déterminé est la classe du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-531">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="60490-532">Le contexte est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="60490-532">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="60490-533">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-533">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="60490-534">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="60490-534">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="60490-535">Mettez à jour *Data/SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-535">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="60490-536">Le code en surbrillance crée une propriété [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="60490-536">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="60490-537">Dans la terminologie d’EF Core :</span><span class="sxs-lookup"><span data-stu-id="60490-537">In EF Core terminology:</span></span>

* <span data-ttu-id="60490-538">Un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-538">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="60490-539">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="60490-539">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="60490-540">Comme un jeu d’entités contient plusieurs entités, les propriétés DBSet doivent être des noms au pluriel.</span><span class="sxs-lookup"><span data-stu-id="60490-540">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="60490-541">Comme l’outil de génération de modèles automatique a créé un DBSet `Student`, cette étape le remplace par le pluriel `Students`.</span><span class="sxs-lookup"><span data-stu-id="60490-541">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="60490-542">Pour que le Razor Code des pages corresponde au nouveau nom de DBSet, effectuez une modification globale sur l’ensemble du projet de `_context.Student` à `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="60490-542">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="60490-543">Il y a 8 occurrences.</span><span class="sxs-lookup"><span data-stu-id="60490-543">There are 8 occurrences.</span></span>

<span data-ttu-id="60490-544">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="60490-544">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="60490-545">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="60490-545">Startup.cs</span></span>

<span data-ttu-id="60490-546">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="60490-546">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="60490-547">Certains services (comme le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="60490-547">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="60490-548">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="60490-548">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="60490-549">Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="60490-549">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="60490-550">L’outil de génération de modèles automatique a inscrit automatiquement la classe du contexte dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="60490-550">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-551">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-551">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60490-552">Dans `ConfigureServices`, les lignes en surbrillance ont été ajoutées par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="60490-552">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-553">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-553">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="60490-554">Dans `ConfigureServices`, vérifiez que le code ajouté par l’outil de génération de modèles automatique appelle `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="60490-554">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="60490-555">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="60490-555">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="60490-556">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="60490-556">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="60490-557">Créer la base de données</span><span class="sxs-lookup"><span data-stu-id="60490-557">Create the database</span></span>

<span data-ttu-id="60490-558">Mettez à jour *Program.cs* pour créer la base de données si elle n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="60490-558">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="60490-559">La méthode [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) n’effectue aucune action s’il existe une base de données pour le contexte.</span><span class="sxs-lookup"><span data-stu-id="60490-559">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="60490-560">S’il n’existe pas de base de données, elle crée la base de données et le schéma.</span><span class="sxs-lookup"><span data-stu-id="60490-560">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="60490-561">`EnsureCreated` active le workflow suivant pour gérer les modifications du modèle de données :</span><span class="sxs-lookup"><span data-stu-id="60490-561">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="60490-562">Supprimez la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-562">Delete the database.</span></span> <span data-ttu-id="60490-563">Toutes les données existantes sont perdues.</span><span class="sxs-lookup"><span data-stu-id="60490-563">Any existing data is lost.</span></span>
* <span data-ttu-id="60490-564">Modifiez le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-564">Change the data model.</span></span> <span data-ttu-id="60490-565">Par exemple, ajoutez un champ `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="60490-565">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="60490-566">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="60490-566">Run the app.</span></span>
* <span data-ttu-id="60490-567">`EnsureCreated` crée une base de données avec le nouveau schéma.</span><span class="sxs-lookup"><span data-stu-id="60490-567">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="60490-568">Ce workflow fonctionne bien à un stade précoce du développement, quand le schéma évolue rapidement, aussi longtemps que vous n’avez pas besoin de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="60490-568">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="60490-569">La situation est différente quand les données qui ont été entrées dans la base de données doivent être conservées.</span><span class="sxs-lookup"><span data-stu-id="60490-569">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="60490-570">Dans ce cas, procédez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="60490-570">When that is the case, use migrations.</span></span>

<span data-ttu-id="60490-571">Plus tard dans cette série de tutoriels, vous supprimerez la base de données créée par `EnsureCreated` et procéderez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="60490-571">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="60490-572">Une base de données créée par `EnsureCreated` ne peut pas être mise à jour via des migrations.</span><span class="sxs-lookup"><span data-stu-id="60490-572">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="60490-573">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="60490-573">Test the app</span></span>

* <span data-ttu-id="60490-574">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="60490-574">Run the app.</span></span>
* <span data-ttu-id="60490-575">Sélectionnez le lien **Students**, puis **Créer nouveau**.</span><span class="sxs-lookup"><span data-stu-id="60490-575">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="60490-576">Testez les liens Edit, Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="60490-576">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="60490-577">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="60490-577">Seed the database</span></span>

<span data-ttu-id="60490-578">La méthode `EnsureCreated` crée une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="60490-578">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="60490-579">Cette section ajoute du code qui remplit la base de données avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="60490-579">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="60490-580">Créez *Data/DbInitializer.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-580">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="60490-581">Le code vérifie si des étudiants figurent dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-581">The code checks if there are any students in the database.</span></span> <span data-ttu-id="60490-582">S’il n’y a pas d’étudiants, il ajoute des données de test à la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-582">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="60490-583">Il crée les données de test dans des tableaux et non dans des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="60490-583">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="60490-584">Dans *Program.cs*, remplacez l’appel `EnsureCreated` par un appel `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="60490-584">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="60490-585">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-585">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="60490-586">Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package** :</span><span class="sxs-lookup"><span data-stu-id="60490-586">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-587">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-587">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="60490-588">Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="60490-588">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="60490-589">Redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="60490-589">Restart the app.</span></span>

* <span data-ttu-id="60490-590">Sélectionnez la page Students pour examiner les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="60490-590">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="60490-591">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="60490-591">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-592">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-592">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60490-593">Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="60490-593">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="60490-594">Dans SSOX, sélectionnez **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="60490-594">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="60490-595">Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.</span><span class="sxs-lookup"><span data-stu-id="60490-595">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="60490-596">Développez le nœud **Tables**.</span><span class="sxs-lookup"><span data-stu-id="60490-596">Expand the **Tables** node.</span></span>
* <span data-ttu-id="60490-597">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="60490-597">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="60490-598">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher le code** pour voir comment le modèle `Student` est mappé au schéma de la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-598">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-599">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-599">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="60490-600">Utilisez votre outil SQLite pour examiner le schéma de base de données et les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="60490-600">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="60490-601">Le fichier de base de données est nommé *CU.db* et se trouve dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="60490-601">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="60490-602">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="60490-602">Asynchronous code</span></span>

<span data-ttu-id="60490-603">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="60490-603">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="60490-604">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="60490-604">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="60490-605">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="60490-605">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="60490-606">Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent.</span><span class="sxs-lookup"><span data-stu-id="60490-606">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="60490-607">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="60490-607">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="60490-608">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="60490-608">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="60490-609">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="60490-609">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="60490-610">Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="60490-610">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="60490-611">Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="60490-611">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="60490-612">Le mot clé `async` fait en sorte que le compilateur :</span><span class="sxs-lookup"><span data-stu-id="60490-612">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="60490-613">Génère des rappels pour les parties du corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="60490-613">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="60490-614">Crée l’objet [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="60490-614">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="60490-615">Le type de retour `Task<T>` représente le travail en cours.</span><span class="sxs-lookup"><span data-stu-id="60490-615">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="60490-616">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="60490-616">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="60490-617">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="60490-617">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="60490-618">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="60490-618">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="60490-619">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="60490-619">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="60490-620">Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :</span><span class="sxs-lookup"><span data-stu-id="60490-620">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="60490-621">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="60490-621">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="60490-622">Cela inclut `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="60490-622">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="60490-623">mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="60490-623">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="60490-624">Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="60490-624">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="60490-625">Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-625">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="60490-626">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="60490-626">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="60490-627">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="60490-627">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="60490-628">Didacticiel suivant</span><span class="sxs-lookup"><span data-stu-id="60490-628">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="60490-629">L’exemple d’application Web Contoso University montre comment créer une Razor application ASP.net Core pages à l’aide d’Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="60490-629">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="60490-630">L’exemple d’application est un site web pour une université Contoso fictive.</span><span class="sxs-lookup"><span data-stu-id="60490-630">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="60490-631">Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="60490-631">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="60490-632">Cette page est la première d’une série de didacticiels qui expliquent comment générer l’exemple d’application Contoso University.</span><span class="sxs-lookup"><span data-stu-id="60490-632">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="60490-633">Télécharger ou afficher l’application terminée.</span><span class="sxs-lookup"><span data-stu-id="60490-633">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="60490-634">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="60490-634">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="60490-635">Prérequis</span><span class="sxs-lookup"><span data-stu-id="60490-635">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-636">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-636">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-637">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-637">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="60490-638">Vous êtes familiarisé avec les [ Razor pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="60490-638">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="60490-639">Les nouveaux programmeurs doivent effectuer la [prise en main des Razor pages avant de](xref:tutorials/razor-pages/razor-pages-start) commencer cette série.</span><span class="sxs-lookup"><span data-stu-id="60490-639">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="60490-640">Dépannage</span><span class="sxs-lookup"><span data-stu-id="60490-640">Troubleshooting</span></span>

<span data-ttu-id="60490-641">Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="60490-641">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="60490-642">Un bon moyen d’obtenir de l’aide est de publier une question sur [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="60490-642">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="60490-643">L’application web Contoso University</span><span class="sxs-lookup"><span data-stu-id="60490-643">The Contoso University web app</span></span>

<span data-ttu-id="60490-644">L’application générée dans ces didacticiels est le site web de base d’une université.</span><span class="sxs-lookup"><span data-stu-id="60490-644">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="60490-645">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="60490-645">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="60490-646">Voici quelques-uns des écrans créés dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="60490-646">Here are a few of the screens created in the tutorial.</span></span>

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

<span data-ttu-id="60490-649">Le style de l’interface utilisateur de ce site est proche de ce qui est généré par les modèles prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="60490-649">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="60490-650">Ce didacticiel se concentre sur EF Core avec Razor les pages, et non sur l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60490-650">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="60490-651">Créer l' Razor application Web ContosoUniversity pages</span><span class="sxs-lookup"><span data-stu-id="60490-651">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-652">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-652">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60490-653">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="60490-653">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="60490-654">Créez une application web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60490-654">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="60490-655">Nommez le projet **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="60490-655">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="60490-656">Il est important de nommer le projet *ContosoUniversity* afin que les espaces de noms correspondent quand le code est copié/collé.</span><span class="sxs-lookup"><span data-stu-id="60490-656">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="60490-657">Sélectionnez **ASP.NET Core 2.1** dans la liste déroulante, puis sélectionnez **Application web**.</span><span class="sxs-lookup"><span data-stu-id="60490-657">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="60490-658">Pour obtenir des images des étapes précédentes, consultez [créer une Razor application Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="60490-658">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="60490-659">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="60490-659">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-660">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-660">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="60490-661">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="60490-661">Set up the site style</span></span>

<span data-ttu-id="60490-662">Quelques changements permettent de définir le menu, la disposition et la page d’accueil du site.</span><span class="sxs-lookup"><span data-stu-id="60490-662">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="60490-663">Mettez à jour *Pages/Shared/_Layout.cshtml* avec les changements suivants :</span><span class="sxs-lookup"><span data-stu-id="60490-663">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="60490-664">Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="60490-664">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="60490-665">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="60490-665">There are three occurrences.</span></span>

* <span data-ttu-id="60490-666">Ajoutez des entrées de menu pour **Students**, **Courses**, **Instructors** et **Departments**, et supprimez l’entrée de menu **Contact**.</span><span class="sxs-lookup"><span data-stu-id="60490-666">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="60490-667">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="60490-667">The changes are highlighted.</span></span> <span data-ttu-id="60490-668">(Tout le balisage n’est *pas* affiché.)</span><span class="sxs-lookup"><span data-stu-id="60490-668">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="60490-669">Dans *Pages/Index.cshtml*, remplacez le contenu du fichier par le code suivant afin de remplacer le texte sur ASP.NET et MVC par le texte concernant cette application :</span><span class="sxs-lookup"><span data-stu-id="60490-669">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="60490-670">Créer le modèle de données</span><span class="sxs-lookup"><span data-stu-id="60490-670">Create the data model</span></span>

<span data-ttu-id="60490-671">Créez des classes d’entités pour l’application Contoso University.</span><span class="sxs-lookup"><span data-stu-id="60490-671">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="60490-672">Commencez par les trois entités suivantes :</span><span class="sxs-lookup"><span data-stu-id="60490-672">Start with the following three entities:</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="60490-674">Il existe une relation un-à-plusieurs entre les entités `Student` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="60490-674">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="60490-675">Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="60490-675">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="60490-676">Un étudiant peut s’inscrire à autant de cours qu’il le souhaite.</span><span class="sxs-lookup"><span data-stu-id="60490-676">A student can enroll in any number of courses.</span></span> <span data-ttu-id="60490-677">Un cours peut avoir une quantité illimitée d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="60490-677">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="60490-678">Dans les sections suivantes, une classe pour chacune de ces entités est créée.</span><span class="sxs-lookup"><span data-stu-id="60490-678">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="60490-679">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="60490-679">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

<span data-ttu-id="60490-681">Créez un dossier *Models*.</span><span class="sxs-lookup"><span data-stu-id="60490-681">Create a *Models* folder.</span></span> <span data-ttu-id="60490-682">Dans le dossier *Models*, créez un fichier de classe nommé *Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-682">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="60490-683">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="60490-683">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="60490-684">Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.</span><span class="sxs-lookup"><span data-stu-id="60490-684">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="60490-685">Dans `classnameID`, `classname` est le nom de la classe.</span><span class="sxs-lookup"><span data-stu-id="60490-685">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="60490-686">L’autre clé primaire reconnue automatiquement est `StudentID` dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="60490-686">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="60490-687">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="60490-687">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="60490-688">Les propriétés de navigation établissent des liaisons à d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="60490-688">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="60490-689">Ici, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à ce `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-689">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="60490-690">Par exemple, si une ligne Student dans la base de données a deux lignes Enrollment associées, la propriété de navigation `Enrollments` contient ces deux entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="60490-690">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="60490-691">Une ligne `Enrollment` associée est une ligne qui contient la valeur de clé primaire de cette étudiant dans la colonne `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="60490-691">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="60490-692">Par exemple, supposez que l’étudiant avec ID=1 a deux lignes dans la table `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="60490-692">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="60490-693">La table `Enrollment` a deux lignes avec `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="60490-693">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="60490-694">`StudentID` est une clé étrangère dans la table `Enrollment` qui spécifie l’étudiant dans la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-694">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="60490-695">Si une propriété de navigation peut contenir plusieurs entités, la propriété de navigation doit être un type de liste, tel que `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="60490-695">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="60490-696">Vous pouvez spécifier `ICollection<T>`, ou un type tel que `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="60490-696">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="60490-697">Quand vous utilisez `ICollection<T>`, EF Core crée une collection `HashSet<T>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="60490-697">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="60490-698">Les propriétés de navigation qui contiennent plusieurs entités proviennent de relations plusieurs-à-plusieurs et un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="60490-698">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="60490-699">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="60490-699">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="60490-701">Dans le dossier *Models*, créez un fichier *Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-701">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="60490-702">La propriété `EnrollmentID` est la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="60490-702">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="60490-703">Cette entité utilise le modèle `classnameID` au lieu de `ID` comme l’entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-703">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="60490-704">En général, les développeurs choisissent un modèle et l’utilisent dans tout le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-704">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="60490-705">Dans un prochain didacticiel, nous verrons qu’utiliser ID sans classname simplifie l’implémentation de l’héritage dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-705">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="60490-706">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="60490-706">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="60490-707">Le point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` est nullable.</span><span class="sxs-lookup"><span data-stu-id="60490-707">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="60490-708">Une note (Grade) qui a la valeur Null est différente d’une note égale à zéro : la valeur Null signifie qu’une note n’est pas connue ou n’a pas encore été affectée.</span><span class="sxs-lookup"><span data-stu-id="60490-708">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="60490-709">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-709">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="60490-710">Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="60490-710">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="60490-711">L’entité `Student` diffère de la propriété de navigation `Student.Enrollments`, qui contient plusieurs entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="60490-711">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="60490-712">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="60490-712">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="60490-713">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="60490-713">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="60490-714">EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="60490-714">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="60490-715">Par exemple, `StudentID` pour la propriété de navigation `Student`, puisque la clé primaire de l’entité `Student` est `ID`.</span><span class="sxs-lookup"><span data-stu-id="60490-715">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="60490-716">Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="60490-716">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="60490-717">Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="60490-717">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="60490-718">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="60490-718">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="60490-720">Dans le dossier *Models*, créez un fichier *Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-720">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="60490-721">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="60490-721">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="60490-722">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="60490-722">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="60490-723">L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-723">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="60490-724">Générer automatiquement le modèle d’étudiant</span><span class="sxs-lookup"><span data-stu-id="60490-724">Scaffold the student model</span></span>

<span data-ttu-id="60490-725">Dans cette section, le modèle d’étudiant est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="60490-725">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="60490-726">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création (Create), de lecture (Read), de mise à jour (Update) et de suppression (Delete) (CRUD) pour le modèle d’étudiant.</span><span class="sxs-lookup"><span data-stu-id="60490-726">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="60490-727">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="60490-727">Build the project.</span></span>
* <span data-ttu-id="60490-728">Créez le dossier *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="60490-728">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="60490-729">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-729">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="60490-730">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Pages/Students* > **Ajouter** > **Nouvel élément généré automatiquement**.</span><span class="sxs-lookup"><span data-stu-id="60490-730">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="60490-731">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="60490-731">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="60490-732">Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="60490-732">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="60490-733">Dans la liste déroulante **Classe de modèle**, sélectionnez **Student (ContosoUniversity.Models)**.</span><span class="sxs-lookup"><span data-stu-id="60490-733">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="60490-734">Dans la ligne **Classe de contexte de données**, sélectionnez le signe **+** (plus) et remplacez le nom généré par **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="60490-734">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="60490-735">Dans la liste déroulante **Classe de contexte de données**, sélectionnez **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="60490-735">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="60490-736">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="60490-736">Select **Add**.</span></span>

![Boîte de dialogue CRUD](intro/_static/s1.png)

<span data-ttu-id="60490-738">Consultez [Générer automatiquement le modèle de film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) si vous rencontrez un problème à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="60490-738">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-739">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-739">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="60490-740">Exécutez les commandes suivantes pour générer automatiquement le modèle d’étudiant.</span><span class="sxs-lookup"><span data-stu-id="60490-740">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="60490-741">Le processus de génération de modèles automatique a créé et changé les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="60490-741">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="60490-742">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="60490-742">Files created</span></span>

* <span data-ttu-id="60490-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="60490-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="60490-744">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="60490-744">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="60490-745">Mises à jour du fichier</span><span class="sxs-lookup"><span data-stu-id="60490-745">File updates</span></span>

* <span data-ttu-id="60490-746">*Startup.cs* : Les changements apportés à ce fichier sont détaillés dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="60490-746">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="60490-747">*appsettings.json* : La chaîne de connexion utilisée pour se connecter à une base de données locale est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="60490-747">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="60490-748">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="60490-748">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="60490-749">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="60490-749">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="60490-750">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="60490-750">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="60490-751">Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="60490-751">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="60490-752">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="60490-752">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="60490-753">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="60490-753">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="60490-754">Examinez la méthode `ConfigureServices` dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="60490-754">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="60490-755">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="60490-755">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="60490-756">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="60490-756">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="60490-757">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="60490-757">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="60490-758">Mettre à jour la méthode Main</span><span class="sxs-lookup"><span data-stu-id="60490-758">Update main</span></span>

<span data-ttu-id="60490-759">Dans *Program.cs*, modifiez la méthode `Main` pour effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="60490-759">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="60490-760">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="60490-760">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="60490-761">Appelez [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="60490-761">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="60490-762">Supprimez le contexte une fois la méthode `EnsureCreated` exécutée.</span><span class="sxs-lookup"><span data-stu-id="60490-762">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="60490-763">Le code suivant montre le fichier *Program.cs* mis à jour.</span><span class="sxs-lookup"><span data-stu-id="60490-763">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="60490-764">`EnsureCreated` garantit que la base de données existe pour le contexte.</span><span class="sxs-lookup"><span data-stu-id="60490-764">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="60490-765">Si elle existe, aucune action n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="60490-765">If it exists, no action is taken.</span></span> <span data-ttu-id="60490-766">Si elle n’existe pas, la base de données et tous ses schéma sont créés.</span><span class="sxs-lookup"><span data-stu-id="60490-766">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="60490-767">`EnsureCreated` n’utilise pas de migrations pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-767">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="60490-768">Une base de données créée avec `EnsureCreated` ne peut pas être mise à jour à l’aide de migrations par la suite.</span><span class="sxs-lookup"><span data-stu-id="60490-768">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="60490-769">`EnsureCreated` est appelée au démarrage de l’application, ce qui active le flux de travail suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-769">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="60490-770">Supprimez la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-770">Delete the DB.</span></span>
* <span data-ttu-id="60490-771">Modification du schéma de base de données (par exemple, ajout d’un champ `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="60490-771">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="60490-772">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="60490-772">Run the app.</span></span>
* <span data-ttu-id="60490-773">`EnsureCreated` crée une base de données avec la colonne `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="60490-773">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="60490-774">`EnsureCreated` est pratique au début du développement quand le schéma évolue rapidement.</span><span class="sxs-lookup"><span data-stu-id="60490-774">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="60490-775">Plus loin dans le tutoriel, la base de données est supprimée et les migrations sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="60490-775">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="60490-776">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="60490-776">Test the app</span></span>

<span data-ttu-id="60490-777">Exécutez l’application et acceptez la cookie stratégie.</span><span class="sxs-lookup"><span data-stu-id="60490-777">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="60490-778">Cette application ne conserve pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="60490-778">This app doesn't keep personal information.</span></span> <span data-ttu-id="60490-779">Pour en savoir plus sur la cookie stratégie au niveau de la [prise en charge de l’union européenne règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="60490-779">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="60490-780">Sélectionnez le lien **Students**, puis **Créer nouveau**.</span><span class="sxs-lookup"><span data-stu-id="60490-780">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="60490-781">Testez les liens Edit, Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="60490-781">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="60490-782">Examiner le contexte de base de données SchoolContext</span><span class="sxs-lookup"><span data-stu-id="60490-782">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="60490-783">La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données spécifié est la classe de contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-783">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="60490-784">Le contexte de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="60490-784">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="60490-785">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="60490-785">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="60490-786">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="60490-786">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="60490-787">Mettez à jour *SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-787">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="60490-788">Le code en surbrillance crée une propriété [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="60490-788">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="60490-789">Dans la terminologie d’EF Core :</span><span class="sxs-lookup"><span data-stu-id="60490-789">In EF Core terminology:</span></span>

* <span data-ttu-id="60490-790">Un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-790">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="60490-791">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="60490-791">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="60490-792">`DbSet<Enrollment>` et `DbSet<Course>` peuvent être omis.</span><span class="sxs-lookup"><span data-stu-id="60490-792">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="60490-793">EF Core les inclut implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="60490-793">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="60490-794">Pour ce didacticiel, conservez `DbSet<Enrollment>` et `DbSet<Course>` dans le `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="60490-794">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="60490-795">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="60490-795">SQL Server Express LocalDB</span></span>

<span data-ttu-id="60490-796">La chaîne de connexion spécifie [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="60490-796">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="60490-797">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="60490-797">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="60490-798">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="60490-798">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="60490-799">Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="60490-799">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="60490-800">Ajouter du code pour initialiser la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="60490-800">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="60490-801">EF Core crée une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="60490-801">EF Core creates an empty DB.</span></span> <span data-ttu-id="60490-802">Dans cette section, une méthode `Initialize` est écrite pour la remplir avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="60490-802">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="60490-803">Dans le dossier *Data*, créez un fichier de classe nommé *DbInitializer.cs* et ajoutez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="60490-803">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="60490-804">Remarque : le code précédent utilise `Models` pour l’espace de noms ( `namespace ContosoUniversity.Models` ) au lieu de `Data` .</span><span class="sxs-lookup"><span data-stu-id="60490-804">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="60490-805">`Models` est cohérent avec le code généré par l’outil de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="60490-805">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="60490-806">Pour plus d’informations, consultez [ce problème de génération de modèles automatique GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="60490-806">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="60490-807">Le code vérifie s’il existe des étudiants dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-807">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="60490-808">S’il n’y en a pas, la base de données est initialisée avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="60490-808">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="60490-809">Il charge les données de test dans des tableaux plutôt que des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="60490-809">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="60490-810">La méthode `EnsureCreated` crée automatiquement la base de données pour le contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-810">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="60490-811">Si la base de données existe, `EnsureCreated` retourne sans modifier la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-811">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="60490-812">Dans *Program.cs*, modifiez la méthode `Main` pour appeler `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="60490-812">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="60490-813">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60490-813">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="60490-814">Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package** :</span><span class="sxs-lookup"><span data-stu-id="60490-814">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="60490-815">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="60490-815">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="60490-816">Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="60490-816">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="60490-817">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="60490-817">View the DB</span></span>

<span data-ttu-id="60490-818">Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.</span><span class="sxs-lookup"><span data-stu-id="60490-818">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="60490-819">Il sera donc « SchoolContext-{GUID} ».</span><span class="sxs-lookup"><span data-stu-id="60490-819">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="60490-820">Le GUID est différent pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60490-820">The GUID will be different for each user.</span></span>
<span data-ttu-id="60490-821">Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="60490-821">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="60490-822">Dans SSOX, cliquez sur **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="60490-822">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="60490-823">Développez le nœud **Tables**.</span><span class="sxs-lookup"><span data-stu-id="60490-823">Expand the **Tables** node.</span></span>

<span data-ttu-id="60490-824">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="60490-824">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="60490-825">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="60490-825">Asynchronous code</span></span>

<span data-ttu-id="60490-826">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="60490-826">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="60490-827">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="60490-827">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="60490-828">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="60490-828">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="60490-829">Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent.</span><span class="sxs-lookup"><span data-stu-id="60490-829">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="60490-830">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="60490-830">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="60490-831">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="60490-831">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="60490-832">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="60490-832">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="60490-833">Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="60490-833">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="60490-834">Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="60490-834">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="60490-835">Le mot clé `async` fait en sorte que le compilateur :</span><span class="sxs-lookup"><span data-stu-id="60490-835">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="60490-836">Génère des rappels pour les parties du corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="60490-836">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="60490-837">Crée automatiquement l’objet [Task](/dotnet/api/system.threading.tasks.task) qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="60490-837">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="60490-838">Pour plus d’informations, consultez [Type de retour Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="60490-838">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="60490-839">Le type de retour implicite `Task` représente le travail en cours.</span><span class="sxs-lookup"><span data-stu-id="60490-839">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="60490-840">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="60490-840">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="60490-841">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="60490-841">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="60490-842">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="60490-842">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="60490-843">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="60490-843">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="60490-844">Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :</span><span class="sxs-lookup"><span data-stu-id="60490-844">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="60490-845">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="60490-845">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="60490-846">Cela comprend `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="60490-846">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="60490-847">mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="60490-847">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="60490-848">Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="60490-848">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="60490-849">Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="60490-849">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="60490-850">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="60490-850">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="60490-851">Dans le didacticiel suivant, nous allons examiner les opérations CRUD de base (créer, lire, mettre à jour, supprimer).</span><span class="sxs-lookup"><span data-stu-id="60490-851">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="60490-852">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="60490-852">Additional resources</span></span>

* [<span data-ttu-id="60490-853">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="60490-853">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="60490-854">Next</span><span class="sxs-lookup"><span data-stu-id="60490-854">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
