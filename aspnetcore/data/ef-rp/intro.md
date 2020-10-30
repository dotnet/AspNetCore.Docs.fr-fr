---
title: ':::no-loc(Razor)::: Pages avec Entity Framework Core dans ASP.NET Core-didacticiel 1 sur 8'
author: rick-anderson
description: 'Montre comment créer une :::no-loc(Razor)::: application pages à l’aide de Entity Framework Core'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/intro
ms.openlocfilehash: c4b4f2b89be2018857abaafb448f052c3848ec59
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054071"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="b6ab9-103">:::no-loc(Razor)::: Pages avec Entity Framework Core dans ASP.NET Core-didacticiel 1 sur 8</span><span class="sxs-lookup"><span data-stu-id="b6ab9-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="b6ab9-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b6ab9-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b6ab9-105">Il s’agit de la première d’une série de didacticiels qui montrent comment utiliser Entity Framework (EF) Core dans une application [ASP.net Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b6ab9-106">Dans ces tutoriels, un site web est créé pour une université fictive nommée Contoso.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b6ab9-107">Le site comprend des fonctionnalités comme l’admission des étudiants, la création de cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b6ab9-108">Le didacticiel utilise l’approche code First.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="b6ab9-109">Pour plus d’informations sur ce didacticiel avec la première approche basée sur la base de données, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="b6ab9-110">Télécharger ou afficher l’application terminée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b6ab9-111">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b6ab9-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="b6ab9-112">Prerequisites</span></span>

* <span data-ttu-id="b6ab9-113">Si vous :::no-loc(Razor)::: débutez avec des pages, consultez la série de didacticiels [prise en main des :::no-loc(Razor)::: pages](xref:tutorials/razor-pages/razor-pages-start) avant de commencer celle-ci.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="b6ab9-116">Moteurs de base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-116">Database engines</span></span>

<span data-ttu-id="b6ab9-117">Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="b6ab9-118">Les instructions Visual Studio Code utilisent [SQLite](https://www.sqlite.org/), moteur de base de données multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="b6ab9-119">Si vous choisissez d’utiliser SQLite, téléchargez et installez un outil tiers pour la gestion et l’affichage d’une base de données SQLite, comme [Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b6ab9-120">Dépannage</span><span class="sxs-lookup"><span data-stu-id="b6ab9-120">Troubleshooting</span></span>

<span data-ttu-id="b6ab9-121">Si vous rencontrez un problème que vous ne pouvez pas résoudre, comparez votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b6ab9-122">Un bon moyen d’obtenir de l’aide est de poster une question sur StackOverflow.com en utilisant le [mot-clé ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou le [mot-clé EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="b6ab9-123">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="b6ab9-123">The sample app</span></span>

<span data-ttu-id="b6ab9-124">L’application générée dans ces didacticiels est le site web de base d’une université.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="b6ab9-125">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b6ab9-126">Voici quelques-uns des écrans créés dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-126">Here are a few of the screens created in the tutorial.</span></span>

![Page d’index des étudiants](intro/_static/students-index30.png)

![Page de modification des étudiants](intro/_static/student-edit30.png)

<span data-ttu-id="b6ab9-129">Le style de l’interface utilisateur de ce site repose sur les modèles de projet intégrés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="b6ab9-130">Ce didacticiel porte sur l’utilisation de EF Core avec ASP.NET Core, et non sur la personnalisation de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="b6ab9-131">Créer le projet d’application web</span><span class="sxs-lookup"><span data-stu-id="b6ab9-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-133">Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-133">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="b6ab9-134">Sélectionnez **Application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-134">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="b6ab9-135">Nommez le projet *ContosoUniversity* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-135">Name the project *ContosoUniversity* .</span></span> <span data-ttu-id="b6ab9-136">Il est important d’utiliser ce nom exact, en respectant l’utilisation des majuscules, de sorte que les espaces de noms correspondent au moment où le code est copié et collé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="b6ab9-137">Sélectionnez **.net Core** et **ASP.net Core 5,0** dans les listes déroulantes, puis sélectionnez **application Web** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ab9-139">Dans un terminal, accédez au dossier dans lequel le dossier de projet doit être créé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="b6ab9-140">Exécutez les commandes suivantes pour créer un :::no-loc(Razor)::: projet de pages et `cd` dans le dossier du nouveau projet :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-140">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b6ab9-141">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="b6ab9-141">Set up the site style</span></span>

<span data-ttu-id="b6ab9-142">Copiez et collez le code suivant dans le fichier *pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="b6ab9-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="b6ab9-143">Le fichier de disposition définit l’en-tête, le pied de page et le menu du site.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="b6ab9-144">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="b6ab9-145">Chaque occurrence de « ContosoUniversity » à « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="b6ab9-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b6ab9-146">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-146">There are three occurrences.</span></span>
* <span data-ttu-id="b6ab9-147">Les entrées du menu d' **hébergement** et de **confidentialité** sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="b6ab9-148">Des entrées sont ajoutées pour **à propos** de, **étudiants** , **cours** , **instructeurs** et **services** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-148">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments** .</span></span>

<span data-ttu-id="b6ab9-149">Dans *pages/index. cshtml* , remplacez le contenu du fichier par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-149">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="b6ab9-150">Le code précédent remplace le texte relatif à ASP.NET Core par le texte de cette application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="b6ab9-151">Exécutez l’application pour vérifier que la page d’accueil (« Home ») s’affiche.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="b6ab9-152">Modèle de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-152">The data model</span></span>

<span data-ttu-id="b6ab9-153">Les sections suivantes créent un modèle de données :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-153">The following sections create a data model:</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="b6ab9-155">Un étudiant peut s’inscrire à un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="b6ab9-156">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="b6ab9-156">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

* <span data-ttu-id="b6ab9-158">Créez un dossier *Models* dans le dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="b6ab9-159">Créez *Models/Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="b6ab9-160">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b6ab9-161">Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b6ab9-162">L’autre nom reconnu automatiquement de la clé primaire de classe `Student` est `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="b6ab9-163">Pour plus d’informations, consultez [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="b6ab9-164">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b6ab9-165">Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b6ab9-166">Dans ce cas, la propriété `Enrollments` d’une entité `Student` contient toutes les entités `Enrollment` associées à cet étudiant.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="b6ab9-167">Par exemple, si une ligne Student dans la base de données est associée à deux lignes Enrollment, la propriété de navigation `Enrollments` contient ces deux entités Enrollment.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="b6ab9-168">Dans la base de données, une ligne Enrollment est associée à une ligne Student si sa colonne StudentID contient la valeur d’ID de l’étudiant.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="b6ab9-169">Par exemple, supposez qu’une ligne Student présente un ID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="b6ab9-170">Les lignes Enrollment associées auront un StudentID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="b6ab9-171">StudentID est une *clé étrangère* dans la table Enrollment.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="b6ab9-172">La propriété `Enrollments` est définie en tant que `ICollection<Enrollment>`, car plusieurs entités Enrollment associées peuvent exister.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="b6ab9-173">Vous pouvez utiliser d’autres types de collection, comme `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="b6ab9-174">Quand vous utilisez `ICollection<Enrollment>`, EF Core crée une collection `HashSet<Enrollment>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="b6ab9-175">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="b6ab9-175">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="b6ab9-177">Créez *Models/Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="b6ab9-178">La propriété `EnrollmentID` est la clé primaire ; cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b6ab9-179">Pour un modèle de données de production, choisissez un modèle et utilisez-le systématiquement.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="b6ab9-180">Ce tutoriel utilise les deux pour montrer qu’ils fonctionnent tous les deux.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="b6ab9-181">L’utilisation de `ID` sans `classname` facilite l’implémentation de certaines modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="b6ab9-182">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b6ab9-183">La présence du point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade`[accepte les valeurs Null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="b6ab9-184">Une note (Grade) de valeur Null est différente d’une note zéro : la valeur Null signifie que la note n’est pas connue ou qu’elle n’a pas encore été attribuée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b6ab9-185">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b6ab9-186">Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="b6ab9-187">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b6ab9-188">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b6ab9-189">EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b6ab9-190">Par exemple, `StudentID` est la clé étrangère pour la propriété de navigation `Student`, car la clé primaire de l’entité `Student` est `ID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b6ab9-191">Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b6ab9-192">Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="b6ab9-193">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="b6ab9-193">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="b6ab9-195">Créez *Models/Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="b6ab9-196">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b6ab9-197">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b6ab9-198">L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="b6ab9-199">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="b6ab9-200">Générer automatiquement des modèles de pages Student</span><span class="sxs-lookup"><span data-stu-id="b6ab9-200">Scaffold Student pages</span></span>

<span data-ttu-id="b6ab9-201">Dans cette section, vous allez utiliser l’outil de génération de modèles automatique ASP.NET Core pour générer :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="b6ab9-202">Classe EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="b6ab9-203">Le contexte est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données déterminé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="b6ab9-204">Il dérive de la classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="b6ab9-205">:::no-loc(Razor)::: les pages qui gèrent les opérations de création, lecture, mise à jour et suppression (CRUD) pour l' `Student` entité.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-205">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-207">Créez un dossier *Pages/Students* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="b6ab9-208">Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Pages/Students* , puis sélectionnez **Ajouter** > **Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-208">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="b6ab9-209">Dans la boîte de dialogue **Ajouter un nouvel élément de structure** :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="b6ab9-210">Dans l’onglet de gauche, sélectionnez **installé > :::no-loc(Razor)::: pages > communes**</span><span class="sxs-lookup"><span data-stu-id="b6ab9-210">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="b6ab9-211">Sélectionnez **:::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-211">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD** .</span></span>
* <span data-ttu-id="b6ab9-212">Dans la boîte de dialogue **Ajouter des :::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-212">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="b6ab9-213">Dans la liste déroulante **Classe de modèle** , sélectionnez **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)** .</span></span>
  * <span data-ttu-id="b6ab9-214">Dans la ligne **Classe du contexte de données** , sélectionnez le signe **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="b6ab9-215">Modifiez le nom du contexte de données pour qu’il se termine par `SchoolContext` plutôt que `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="b6ab9-216">Nom du contexte mis à jour : `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="b6ab9-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="b6ab9-217">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-217">Select **Add** .</span></span>

<span data-ttu-id="b6ab9-218">Les packages suivants sont automatiquement installés :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ab9-220">Exécutez les commandes CLI .NET Core suivantes pour installer les packages NuGet nécessaires :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="b6ab9-221">Le package Microsoft.VisualStudio.Web.CodeGeneration.Design est requis pour la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="b6ab9-222">Bien que l’application ne soit pas appelée à utiliser SQL Server, l’outil de génération de modèles automatique a besoin du package SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="b6ab9-223">Créez un dossier *Pages/Students* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="b6ab9-224">Exécutez la commande suivante pour installer l’[outil de génération de modèles automatique aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="b6ab9-225">Exécutez la commande suivante pour générer automatiquement des modèles de pages Student.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="b6ab9-226">**Sur Windows**</span><span class="sxs-lookup"><span data-stu-id="b6ab9-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="b6ab9-227">**Sur macOS ou Linux**</span><span class="sxs-lookup"><span data-stu-id="b6ab9-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="b6ab9-228">Si l’étape précédente échoue, générez le projet et recommencez l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="b6ab9-229">Le processus de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-229">The scaffolding process:</span></span>

* <span data-ttu-id="b6ab9-230">Crée :::no-loc(Razor)::: des pages dans le dossier *pages/élèves* :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-230">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="b6ab9-231">*Create.cshtml* et *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="b6ab9-232">*Delete.cshtml* et *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="b6ab9-233">*Details.cshtml* et *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="b6ab9-234">*Edit.cshtml* et *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="b6ab9-235">*Index.cshtml* et *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="b6ab9-236">Crée *Data/SchoolContext. cs* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-236">Creates *Data/SchoolContext.cs* .</span></span>
* <span data-ttu-id="b6ab9-237">Ajoute le contexte à l’injection de dépendances dans *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-237">Adds the context to dependency injection in *Startup.cs* .</span></span>
* <span data-ttu-id="b6ab9-238">Ajoute une chaîne de connexion de base de données à *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-238">Adds a database connection string to *:::no-loc(appsettings.json):::* .</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="b6ab9-239">Chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-239">Database connection string</span></span>

<span data-ttu-id="b6ab9-240">L’outil de génération de modèles automatique génère une chaîne de connexion dans le *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-240">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ab9-242">La chaîne de connexion spécifie [SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)base de données locale :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="b6ab9-243">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b6ab9-244">Par défaut, la Base de données locale crée des fichiers *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ab9-246">Raccourcissez la chaîne de connexion SQLite à *cu. db* :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-246">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="b6ab9-247">Mettre à jour la classe du contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-247">Update the database context class</span></span>

<span data-ttu-id="b6ab9-248">La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données déterminé est la classe du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b6ab9-249">Le contexte est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b6ab9-250">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b6ab9-251">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b6ab9-252">Mettez à jour *Data/SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="b6ab9-253">Le code précédent passe du singulier `DbSet<Student> Student` au pluriel `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="b6ab9-254">Pour que le :::no-loc(Razor)::: Code des pages corresponde au nouveau `DBSet` nom, effectuez une modification globale à partir de : `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="b6ab9-254">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="b6ab9-255">À: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="b6ab9-255">to: `_context.Students.`</span></span>

<span data-ttu-id="b6ab9-256">Il y a 8 occurrences.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-256">There are 8 occurrences.</span></span>

<span data-ttu-id="b6ab9-257">Un jeu d’entités contenant plusieurs entités, de nombreux développeurs préfèrent que les `DBSet` noms de propriété soient au pluriel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="b6ab9-258">Code mis en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-258">The highlighted code:</span></span>

* <span data-ttu-id="b6ab9-259">Crée une [propriété \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b6ab9-260">Dans la terminologie d’EF Core :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="b6ab9-261">Un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="b6ab9-262">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="b6ab9-263">Appelle <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="b6ab9-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="b6ab9-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="b6ab9-265">Est appelé quand `SchoolContext` a été initialisé, mais avant que le modèle ait été verrouillé et utilisé pour initialiser le contexte.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="b6ab9-266">Est requis, car ultérieurement dans le didacticiel, l' `Student` entité aura des références aux autres entités.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="b6ab9-267">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="b6ab9-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b6ab9-268">Startup.cs</span></span>

<span data-ttu-id="b6ab9-269">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b6ab9-270">Les services tels que le `SchoolContext` sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="b6ab9-271">Ces services sont fournis par les composants qui requièrent ces services, tels que les :::no-loc(Razor)::: pages, via des paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-271">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="b6ab9-272">Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b6ab9-273">L’outil de génération de modèles automatique a inscrit automatiquement la classe du contexte dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ab9-275">Les lignes en surbrillance suivantes ont été ajoutées par le générateur de modèles :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ab9-277">Vérifiez le code ajouté par les appels de l’échafaudage `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="b6ab9-278">Pour plus d’informations sur l’utilisation d’une base de données de production [, consultez utiliser SQLite pour le développement, SQL Server pour la production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="b6ab9-279">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b6ab9-280">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="b6ab9-281">Ajouter le filtre d’exception de base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-281">Add the database exception filter</span></span>

<span data-ttu-id="b6ab9-282">Ajoutez `AddDatabaseDeveloperPageExceptionFilter` à `ConfigureServices` , comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="b6ab9-284">Ajoutez le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="b6ab9-285">Dans le PMC, entrez la commande suivante pour ajouter le package NuGet :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="b6ab9-287">Le `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` package NuGet fournit ASP.net Core intergiciel pour Entity Framework Core pages d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="b6ab9-288">Cet intergiciel (middleware) permet de détecter et de diagnostiquer les erreurs avec Entity Framework Core migrations.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b6ab9-289">Créer la base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-289">Create the database</span></span>

<span data-ttu-id="b6ab9-290">Mettez à jour *Program.cs* pour créer la base de données si elle n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b6ab9-291">La méthode [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) n’effectue aucune action s’il existe une base de données pour le contexte.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="b6ab9-292">S’il n’existe pas de base de données, elle crée la base de données et le schéma.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="b6ab9-293">`EnsureCreated` active le workflow suivant pour gérer les modifications du modèle de données :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="b6ab9-294">Supprimez la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-294">Delete the database.</span></span> <span data-ttu-id="b6ab9-295">Toutes les données existantes sont perdues.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-295">Any existing data is lost.</span></span>
* <span data-ttu-id="b6ab9-296">Modifiez le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-296">Change the data model.</span></span> <span data-ttu-id="b6ab9-297">Par exemple, ajoutez un champ `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="b6ab9-298">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-298">Run the app.</span></span>
* <span data-ttu-id="b6ab9-299">`EnsureCreated` crée une base de données avec le nouveau schéma.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="b6ab9-300">Ce workflow fonctionne bien à un stade précoce du développement, quand le schéma évolue rapidement, aussi longtemps que vous n’avez pas besoin de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="b6ab9-301">La situation est différente quand les données qui ont été entrées dans la base de données doivent être conservées.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="b6ab9-302">Dans ce cas, procédez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="b6ab9-303">Plus tard dans cette série de tutoriels, vous supprimerez la base de données créée par `EnsureCreated` et procéderez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="b6ab9-304">Une base de données créée par `EnsureCreated` ne peut pas être mise à jour via des migrations.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b6ab9-305">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="b6ab9-305">Test the app</span></span>

* <span data-ttu-id="b6ab9-306">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-306">Run the app.</span></span>
* <span data-ttu-id="b6ab9-307">Sélectionnez le lien **Students** , puis **Créer nouveau** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-307">Select the **Students** link and then **Create New** .</span></span>
* <span data-ttu-id="b6ab9-308">Testez les liens Edit, Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="b6ab9-309">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-309">Seed the database</span></span>

<span data-ttu-id="b6ab9-310">La méthode `EnsureCreated` crée une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="b6ab9-311">Cette section ajoute du code qui remplit la base de données avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="b6ab9-312">Créez *Data/DbInitializer.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="b6ab9-313">Le code vérifie si des étudiants figurent dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="b6ab9-314">S’il n’y a pas d’étudiants, il ajoute des données de test à la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="b6ab9-315">Il crée les données de test dans des tableaux et non dans des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b6ab9-316">Dans *Program.cs* , remplacez l’appel `EnsureCreated` par un appel `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-316">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ab9-318">Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package**  :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="b6ab9-319">Répondre avec `Y` pour supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ab9-321">Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="b6ab9-322">Redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-322">Restart the app.</span></span>
* <span data-ttu-id="b6ab9-323">Sélectionnez la page Students pour examiner les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="b6ab9-324">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-326">Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b6ab9-327">Dans SSOX, sélectionnez **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span></span> <span data-ttu-id="b6ab9-328">Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="b6ab9-329">Développez le nœud **Tables** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="b6ab9-330">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="b6ab9-331">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher le code** pour voir comment le modèle `Student` est mappé au schéma de la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ab9-333">Utilisez votre outil SQLite pour examiner le schéma de base de données et les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="b6ab9-334">Le fichier de base de données est nommé *CU.db* et se trouve dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="b6ab9-335">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="b6ab9-335">Asynchronous code</span></span>

<span data-ttu-id="b6ab9-336">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b6ab9-337">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b6ab9-338">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b6ab9-339">Avec le code synchrone, de nombreux threads peuvent être associés alors qu’ils ne fonctionnent pas car ils sont en attente de la fin des e/s.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b6ab9-340">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b6ab9-341">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="b6ab9-342">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b6ab9-343">Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b6ab9-344">Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="b6ab9-345">Le mot clé `async` fait en sorte que le compilateur :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b6ab9-346">Génère des rappels pour les parties du corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b6ab9-347">Crée l’objet [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="b6ab9-348">Le type de retour `Task<T>` représente le travail en cours.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="b6ab9-349">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b6ab9-350">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b6ab9-351">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b6ab9-352">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b6ab9-353">Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b6ab9-354">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b6ab9-355">Cela inclut `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="b6ab9-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b6ab9-356">mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b6ab9-357">Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b6ab9-358">Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="b6ab9-359">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="b6ab9-360">Considérations relatives aux performances</span><span class="sxs-lookup"><span data-stu-id="b6ab9-360">Performance considerations</span></span>

<span data-ttu-id="b6ab9-361">En général, une page Web ne doit pas charger un nombre arbitraire de lignes.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="b6ab9-362">Une requête doit utiliser la pagination ou une approche de limitation.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="b6ab9-363">Par exemple, la requête ci-dessus peut utiliser `Take` pour limiter les lignes retournées :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="b6ab9-364">L’énumération d’une table volumineuse dans une vue peut retourner une réponse HTTP 200 partiellement construite si une exception de base de données se produit de façon partielle via l’énumération.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="b6ab9-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> la valeur par défaut est 1024.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="b6ab9-366">Les jeux de codes suivants `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b6ab9-367">La pagination est traitée plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b6ab9-368">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="b6ab9-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b6ab9-369">Didacticiel suivant</span><span class="sxs-lookup"><span data-stu-id="b6ab9-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b6ab9-370">Il s’agit de la première d’une série de didacticiels qui montrent comment utiliser Entity Framework (EF) Core dans une application [ASP.net Core :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b6ab9-371">Dans ces tutoriels, un site web est créé pour une université fictive nommée Contoso.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b6ab9-372">Le site comprend des fonctionnalités comme l’admission des étudiants, la création de cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b6ab9-373">Le didacticiel utilise l’approche code First.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="b6ab9-374">Pour plus d’informations sur ce didacticiel avec la première approche basée sur la base de données, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="b6ab9-375">Télécharger ou afficher l’application terminée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b6ab9-376">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b6ab9-377">Prérequis</span><span class="sxs-lookup"><span data-stu-id="b6ab9-377">Prerequisites</span></span>

* <span data-ttu-id="b6ab9-378">Si vous :::no-loc(Razor)::: débutez avec des pages, consultez la série de didacticiels [prise en main des :::no-loc(Razor)::: pages](xref:tutorials/razor-pages/razor-pages-start) avant de commencer celle-ci.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-378">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="b6ab9-381">Moteurs de base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-381">Database engines</span></span>

<span data-ttu-id="b6ab9-382">Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="b6ab9-383">Les instructions Visual Studio Code utilisent [SQLite](https://www.sqlite.org/), moteur de base de données multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="b6ab9-384">Si vous choisissez d’utiliser SQLite, téléchargez et installez un outil tiers pour la gestion et l’affichage d’une base de données SQLite, comme [Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b6ab9-385">Dépannage</span><span class="sxs-lookup"><span data-stu-id="b6ab9-385">Troubleshooting</span></span>

<span data-ttu-id="b6ab9-386">Si vous rencontrez un problème que vous ne pouvez pas résoudre, comparez votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b6ab9-387">Un bon moyen d’obtenir de l’aide est de poster une question sur StackOverflow.com en utilisant le [mot-clé ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou le [mot-clé EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="b6ab9-388">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="b6ab9-388">The sample app</span></span>

<span data-ttu-id="b6ab9-389">L’application générée dans ces didacticiels est le site web de base d’une université.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="b6ab9-390">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b6ab9-391">Voici quelques-uns des écrans créés dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-391">Here are a few of the screens created in the tutorial.</span></span>

![Page d’index des étudiants](intro/_static/students-index30.png)

![Page de modification des étudiants](intro/_static/student-edit30.png)

<span data-ttu-id="b6ab9-394">Le style de l’interface utilisateur de ce site repose sur les modèles de projet intégrés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="b6ab9-395">Le tutoriel traite essentiellement de l’utilisation d’EF Core, et non de la façon de personnaliser l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="b6ab9-396">Suivez le lien en haut de la page pour obtenir le code source du projet terminé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="b6ab9-397">Le dossier *cu30* contient le code de la version 3.0 d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="b6ab9-398">Les fichiers qui reflètent l’état du code pour les tutoriels 1-7 se trouvent dans le dossier *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ab9-400">Pour exécuter l’application après avoir téléchargé le projet terminé :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="b6ab9-401">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-401">Build the project.</span></span>
* <span data-ttu-id="b6ab9-402">Dans la console du Gestionnaire de package (PMC), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="b6ab9-403">Exécutez le projet pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ab9-405">Pour exécuter l’application après avoir téléchargé le projet terminé :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="b6ab9-406">Supprimez *ContosoUniversity.csproj* , puis renommez *ContosoUniversitySQLite.csproj* en *ContosoUniversity.csproj* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-406">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj* .</span></span>
* <span data-ttu-id="b6ab9-407">Dans *Program.cs* , commentez-le `#define Startup` pour qu’il `StartupSQLite` soit utilisé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-407">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="b6ab9-408">Supprimez *appSettings.json* et renommez *appSettingsSQLite.json* en *appSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-408">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json* .</span></span>
* <span data-ttu-id="b6ab9-409">Supprimez le dossier *Migrations* et renommez *MigrationsSQL* en *Migrations* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations* .</span></span>
* <span data-ttu-id="b6ab9-410">Effectuez une recherche globale `#if SQLiteVersion` et supprimez `#if SQLiteVersion` et l' `#endif` instruction associée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="b6ab9-411">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-411">Build the project.</span></span>
* <span data-ttu-id="b6ab9-412">Dans une invite de commandes, exécutez la commande suivante dans le dossier du projet :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="b6ab9-413">Dans votre outil SQLite, exécutez l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="b6ab9-414">Exécutez le projet pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="b6ab9-415">Créer le projet d’application web</span><span class="sxs-lookup"><span data-stu-id="b6ab9-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-417">Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-417">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="b6ab9-418">Sélectionnez **Application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-418">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="b6ab9-419">Nommez le projet *ContosoUniversity* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-419">Name the project *ContosoUniversity* .</span></span> <span data-ttu-id="b6ab9-420">Il est important d’utiliser ce nom exact, en respectant l’utilisation des majuscules, de sorte que les espaces de noms correspondent au moment où le code est copié et collé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="b6ab9-421">Sélectionnez **.NET Core** et **ASP.NET Core 3.0** dans les listes déroulantes, puis **Application web** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ab9-423">Dans un terminal, accédez au dossier dans lequel le dossier de projet doit être créé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="b6ab9-424">Exécutez les commandes suivantes pour créer un :::no-loc(Razor)::: projet de pages et `cd` dans le dossier du nouveau projet :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-424">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b6ab9-425">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="b6ab9-425">Set up the site style</span></span>

<span data-ttu-id="b6ab9-426">Configurez l’en-tête, le pied de page et le menu du site en mettant à jour *Pages/Shared/_Layout.cshtml*  :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="b6ab9-427">Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="b6ab9-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b6ab9-428">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-428">There are three occurrences.</span></span>

* <span data-ttu-id="b6ab9-429">Supprimez les entrées de menu **Home** et **Privacy** et ajoutez les entrées **About** , **Students** , **Courses** , **Instructors** et **Departments** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** .</span></span>

<span data-ttu-id="b6ab9-430">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="b6ab9-431">Dans *Pages/Index.cshtml* , remplacez le contenu du fichier par le code suivant de façon à remplacer le texte relatif à ASP.NET Core par le texte se rapportant à cette application :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-431">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="b6ab9-432">Exécutez l’application pour vérifier que la page d’accueil (« Home ») s’affiche.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="b6ab9-433">Modèle de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-433">The data model</span></span>

<span data-ttu-id="b6ab9-434">Les sections suivantes créent un modèle de données :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-434">The following sections create a data model:</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="b6ab9-436">Un étudiant peut s’inscrire à un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="b6ab9-437">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="b6ab9-437">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

* <span data-ttu-id="b6ab9-439">Créez un dossier *Models* dans le dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="b6ab9-440">Créez *Models/Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="b6ab9-441">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b6ab9-442">Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b6ab9-443">L’autre nom reconnu automatiquement de la clé primaire de classe `Student` est `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="b6ab9-444">Pour plus d’informations, consultez [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="b6ab9-445">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b6ab9-446">Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b6ab9-447">Dans ce cas, la propriété `Enrollments` d’une entité `Student` contient toutes les entités `Enrollment` associées à cet étudiant.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="b6ab9-448">Par exemple, si une ligne Student dans la base de données est associée à deux lignes Enrollment, la propriété de navigation `Enrollments` contient ces deux entités Enrollment.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="b6ab9-449">Dans la base de données, une ligne Enrollment est associée à une ligne Student si sa colonne StudentID contient la valeur d’ID de l’étudiant.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="b6ab9-450">Par exemple, supposez qu’une ligne Student présente un ID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="b6ab9-451">Les lignes Enrollment associées auront un StudentID égal à 1.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="b6ab9-452">StudentID est une *clé étrangère* dans la table Enrollment.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="b6ab9-453">La propriété `Enrollments` est définie en tant que `ICollection<Enrollment>`, car plusieurs entités Enrollment associées peuvent exister.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="b6ab9-454">Vous pouvez utiliser d’autres types de collection, comme `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="b6ab9-455">Quand vous utilisez `ICollection<Enrollment>`, EF Core crée une collection `HashSet<Enrollment>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="b6ab9-456">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="b6ab9-456">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="b6ab9-458">Créez *Models/Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="b6ab9-459">La propriété `EnrollmentID` est la clé primaire ; cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b6ab9-460">Pour un modèle de données de production, choisissez un modèle et utilisez-le systématiquement.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="b6ab9-461">Ce tutoriel utilise les deux pour montrer qu’ils fonctionnent tous les deux.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="b6ab9-462">L’utilisation de `ID` sans `classname` facilite l’implémentation de certaines modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="b6ab9-463">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b6ab9-464">La présence du point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade`[accepte les valeurs Null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="b6ab9-465">Une note (Grade) de valeur Null est différente d’une note zéro : la valeur Null signifie que la note n’est pas connue ou qu’elle n’a pas encore été attribuée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b6ab9-466">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b6ab9-467">Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="b6ab9-468">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b6ab9-469">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b6ab9-470">EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b6ab9-471">Par exemple, `StudentID` est la clé étrangère pour la propriété de navigation `Student`, car la clé primaire de l’entité `Student` est `ID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b6ab9-472">Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b6ab9-473">Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="b6ab9-474">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="b6ab9-474">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="b6ab9-476">Créez *Models/Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="b6ab9-477">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b6ab9-478">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b6ab9-479">L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="b6ab9-480">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="b6ab9-481">Générer automatiquement des modèles de pages Student</span><span class="sxs-lookup"><span data-stu-id="b6ab9-481">Scaffold Student pages</span></span>

<span data-ttu-id="b6ab9-482">Dans cette section, vous allez utiliser l’outil de génération de modèles automatique ASP.NET Core pour générer :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="b6ab9-483">Une classe de *contexte* EF Core.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-483">An EF Core *context* class.</span></span> <span data-ttu-id="b6ab9-484">Le contexte est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données déterminé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="b6ab9-485">Il dérive de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="b6ab9-486">:::no-loc(Razor)::: les pages qui gèrent les opérations de création, lecture, mise à jour et suppression (CRUD) pour l' `Student` entité.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-486">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-488">Créez un dossier *Students* dans le dossier *Pages* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="b6ab9-489">Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Pages/Students* , puis sélectionnez **Ajouter** > **Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-489">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="b6ab9-490">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **:::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-490">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD** .</span></span>
* <span data-ttu-id="b6ab9-491">Dans la boîte de dialogue **Ajouter des :::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-491">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="b6ab9-492">Dans la liste déroulante **Classe de modèle** , sélectionnez **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)** .</span></span>
  * <span data-ttu-id="b6ab9-493">Dans la ligne **Classe du contexte de données** , sélectionnez le signe **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="b6ab9-494">Remplacez le nom du contexte de données *ContosoUniversity.Models.ContosoUniversityContext* par *ContosoUniversity.Data.SchoolContext* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext* .</span></span>
  * <span data-ttu-id="b6ab9-495">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-495">Select **Add** .</span></span>

<span data-ttu-id="b6ab9-496">Les packages suivants sont automatiquement installés :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ab9-498">Exécutez les commandes CLI .NET Core suivantes pour installer les packages NuGet nécessaires :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="b6ab9-499">Le package Microsoft.VisualStudio.Web.CodeGeneration.Design est requis pour la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="b6ab9-500">Bien que l’application ne soit pas appelée à utiliser SQL Server, l’outil de génération de modèles automatique a besoin du package SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="b6ab9-501">Créez un dossier *Pages/Students* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="b6ab9-502">Exécutez la commande suivante pour installer l’[outil de génération de modèles automatique aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="b6ab9-503">Exécutez la commande suivante pour générer automatiquement des modèles de pages Student.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="b6ab9-504">**Sur Windows**</span><span class="sxs-lookup"><span data-stu-id="b6ab9-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="b6ab9-505">**Sur macOS ou Linux**</span><span class="sxs-lookup"><span data-stu-id="b6ab9-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="b6ab9-506">Si vous rencontrez un problème à l’étape précédente, générez le projet et recommencez l’étape de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="b6ab9-507">Le processus de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-507">The scaffolding process:</span></span>

* <span data-ttu-id="b6ab9-508">Crée :::no-loc(Razor)::: des pages dans le dossier *pages/élèves* :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-508">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="b6ab9-509">*Create.cshtml* et *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="b6ab9-510">*Delete.cshtml* et *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="b6ab9-511">*Details.cshtml* et *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="b6ab9-512">*Edit.cshtml* et *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="b6ab9-513">*Index.cshtml* et *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="b6ab9-514">Crée *Data/SchoolContext. cs* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-514">Creates *Data/SchoolContext.cs* .</span></span>
* <span data-ttu-id="b6ab9-515">Ajoute le contexte à l’injection de dépendances dans *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-515">Adds the context to dependency injection in *Startup.cs* .</span></span>
* <span data-ttu-id="b6ab9-516">Ajoute une chaîne de connexion de base de données à *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-516">Adds a database connection string to *:::no-loc(appsettings.json):::* .</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="b6ab9-517">Chaîne de connexion de base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ab9-519">Le *:::no-loc(appsettings.json):::* fichier spécifie la chaîne de connexion [SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)la base de données locale.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-519">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="b6ab9-520">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b6ab9-521">Par défaut, la Base de données locale crée des fichiers *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ab9-523">Modifiez la chaîne de connexion de sorte qu’elle pointe vers un fichier de base de données SQLite nommé *CU.db*  :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-523">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="b6ab9-524">Mettre à jour la classe du contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-524">Update the database context class</span></span>

<span data-ttu-id="b6ab9-525">La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données déterminé est la classe du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b6ab9-526">Le contexte est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b6ab9-527">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b6ab9-528">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b6ab9-529">Mettez à jour *Data/SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="b6ab9-530">Le code en surbrillance crée une propriété [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b6ab9-531">Dans la terminologie d’EF Core :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-531">In EF Core terminology:</span></span>

* <span data-ttu-id="b6ab9-532">Un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="b6ab9-533">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b6ab9-534">Comme un jeu d’entités contient plusieurs entités, les propriétés DBSet doivent être des noms au pluriel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="b6ab9-535">Comme l’outil de génération de modèles automatique a créé un DBSet `Student`, cette étape le remplace par le pluriel `Students`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="b6ab9-536">Pour que le :::no-loc(Razor)::: Code des pages corresponde au nouveau nom de DBSet, effectuez une modification globale sur l’ensemble du projet de `_context.Student` à `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-536">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="b6ab9-537">Il y a 8 occurrences.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-537">There are 8 occurrences.</span></span>

<span data-ttu-id="b6ab9-538">Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="b6ab9-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b6ab9-539">Startup.cs</span></span>

<span data-ttu-id="b6ab9-540">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b6ab9-541">Certains services (comme le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b6ab9-542">Ces services sont fournis par les composants qui requièrent ces services (tels que les :::no-loc(Razor)::: pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-542">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b6ab9-543">Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b6ab9-544">L’outil de génération de modèles automatique a inscrit automatiquement la classe du contexte dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-546">Dans `ConfigureServices`, les lignes en surbrillance ont été ajoutées par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ab9-548">Dans `ConfigureServices`, vérifiez que le code ajouté par l’outil de génération de modèles automatique appelle `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="b6ab9-549">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b6ab9-550">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b6ab9-551">Créer la base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-551">Create the database</span></span>

<span data-ttu-id="b6ab9-552">Mettez à jour *Program.cs* pour créer la base de données si elle n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b6ab9-553">La méthode [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) n’effectue aucune action s’il existe une base de données pour le contexte.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="b6ab9-554">S’il n’existe pas de base de données, elle crée la base de données et le schéma.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="b6ab9-555">`EnsureCreated` active le workflow suivant pour gérer les modifications du modèle de données :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="b6ab9-556">Supprimez la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-556">Delete the database.</span></span> <span data-ttu-id="b6ab9-557">Toutes les données existantes sont perdues.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-557">Any existing data is lost.</span></span>
* <span data-ttu-id="b6ab9-558">Modifiez le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-558">Change the data model.</span></span> <span data-ttu-id="b6ab9-559">Par exemple, ajoutez un champ `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="b6ab9-560">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-560">Run the app.</span></span>
* <span data-ttu-id="b6ab9-561">`EnsureCreated` crée une base de données avec le nouveau schéma.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="b6ab9-562">Ce workflow fonctionne bien à un stade précoce du développement, quand le schéma évolue rapidement, aussi longtemps que vous n’avez pas besoin de conserver les données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="b6ab9-563">La situation est différente quand les données qui ont été entrées dans la base de données doivent être conservées.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="b6ab9-564">Dans ce cas, procédez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="b6ab9-565">Plus tard dans cette série de tutoriels, vous supprimerez la base de données créée par `EnsureCreated` et procéderez à des migrations.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="b6ab9-566">Une base de données créée par `EnsureCreated` ne peut pas être mise à jour via des migrations.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b6ab9-567">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="b6ab9-567">Test the app</span></span>

* <span data-ttu-id="b6ab9-568">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-568">Run the app.</span></span>
* <span data-ttu-id="b6ab9-569">Sélectionnez le lien **Students** , puis **Créer nouveau** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-569">Select the **Students** link and then **Create New** .</span></span>
* <span data-ttu-id="b6ab9-570">Testez les liens Edit, Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="b6ab9-571">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-571">Seed the database</span></span>

<span data-ttu-id="b6ab9-572">La méthode `EnsureCreated` crée une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="b6ab9-573">Cette section ajoute du code qui remplit la base de données avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="b6ab9-574">Créez *Data/DbInitializer.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="b6ab9-575">Le code vérifie si des étudiants figurent dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="b6ab9-576">S’il n’y a pas d’étudiants, il ajoute des données de test à la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="b6ab9-577">Il crée les données de test dans des tableaux et non dans des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="b6ab9-578">Dans *Program.cs* , remplacez l’appel `EnsureCreated` par un appel `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-578">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ab9-580">Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package**  :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ab9-582">Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="b6ab9-583">Redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-583">Restart the app.</span></span>

* <span data-ttu-id="b6ab9-584">Sélectionnez la page Students pour examiner les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="b6ab9-585">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-587">Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b6ab9-588">Dans SSOX, sélectionnez **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span></span> <span data-ttu-id="b6ab9-589">Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="b6ab9-590">Développez le nœud **Tables** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="b6ab9-591">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="b6ab9-592">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher le code** pour voir comment le modèle `Student` est mappé au schéma de la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ab9-594">Utilisez votre outil SQLite pour examiner le schéma de base de données et les données amorcées.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="b6ab9-595">Le fichier de base de données est nommé *CU.db* et se trouve dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="b6ab9-596">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="b6ab9-596">Asynchronous code</span></span>

<span data-ttu-id="b6ab9-597">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b6ab9-598">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b6ab9-599">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b6ab9-600">Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b6ab9-601">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b6ab9-602">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="b6ab9-603">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b6ab9-604">Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b6ab9-605">Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="b6ab9-606">Le mot clé `async` fait en sorte que le compilateur :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b6ab9-607">Génère des rappels pour les parties du corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b6ab9-608">Crée l’objet [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="b6ab9-609">Le type de retour `Task<T>` représente le travail en cours.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="b6ab9-610">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b6ab9-611">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b6ab9-612">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b6ab9-613">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b6ab9-614">Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b6ab9-615">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b6ab9-616">Cela inclut `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="b6ab9-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b6ab9-617">mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b6ab9-618">Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b6ab9-619">Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="b6ab9-620">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b6ab9-621">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="b6ab9-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b6ab9-622">Didacticiel suivant</span><span class="sxs-lookup"><span data-stu-id="b6ab9-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b6ab9-623">L’exemple d’application Web Contoso University montre comment créer une :::no-loc(Razor)::: application ASP.net Core pages à l’aide d’Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="b6ab9-624">L’exemple d’application est un site web pour une université Contoso fictive.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b6ab9-625">Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b6ab9-626">Cette page est la première d’une série de didacticiels qui expliquent comment générer l’exemple d’application Contoso University.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="b6ab9-627">Télécharger ou afficher l’application terminée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b6ab9-628">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b6ab9-629">Prérequis</span><span class="sxs-lookup"><span data-stu-id="b6ab9-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="b6ab9-632">Vous êtes familiarisé avec les [ :::no-loc(Razor)::: pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-632">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="b6ab9-633">Les nouveaux programmeurs doivent effectuer la [prise en main des :::no-loc(Razor)::: pages avant de](xref:tutorials/razor-pages/razor-pages-start) commencer cette série.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-633">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b6ab9-634">Dépannage</span><span class="sxs-lookup"><span data-stu-id="b6ab9-634">Troubleshooting</span></span>

<span data-ttu-id="b6ab9-635">Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b6ab9-636">Un bon moyen d’obtenir de l’aide est de publier une question sur [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="b6ab9-637">L’application web Contoso University</span><span class="sxs-lookup"><span data-stu-id="b6ab9-637">The Contoso University web app</span></span>

<span data-ttu-id="b6ab9-638">L’application générée dans ces didacticiels est le site web de base d’une université.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="b6ab9-639">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b6ab9-640">Voici quelques-uns des écrans créés dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-640">Here are a few of the screens created in the tutorial.</span></span>

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

<span data-ttu-id="b6ab9-643">Le style de l’interface utilisateur de ce site est proche de ce qui est généré par les modèles prédéfinis.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="b6ab9-644">Ce didacticiel se concentre sur EF Core avec :::no-loc(Razor)::: les pages, et non sur l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-644">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="b6ab9-645">Créer l' :::no-loc(Razor)::: application Web ContosoUniversity pages</span><span class="sxs-lookup"><span data-stu-id="b6ab9-645">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-647">Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-647">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="b6ab9-648">Créez une application web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="b6ab9-649">Nommez le projet **ContosoUniversity** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-649">Name the project **ContosoUniversity** .</span></span> <span data-ttu-id="b6ab9-650">Il est important de nommer le projet *ContosoUniversity* afin que les espaces de noms correspondent quand le code est copié/collé.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="b6ab9-651">Sélectionnez **ASP.NET Core 2.1** dans la liste déroulante, puis sélectionnez **Application web** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application** .</span></span>

<span data-ttu-id="b6ab9-652">Pour obtenir des images des étapes précédentes, consultez [créer une :::no-loc(Razor)::: application Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-652">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="b6ab9-653">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b6ab9-655">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="b6ab9-655">Set up the site style</span></span>

<span data-ttu-id="b6ab9-656">Quelques changements permettent de définir le menu, la disposition et la page d’accueil du site.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="b6ab9-657">Mettez à jour *Pages/Shared/_Layout.cshtml* avec les changements suivants :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="b6ab9-658">Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="b6ab9-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b6ab9-659">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-659">There are three occurrences.</span></span>

* <span data-ttu-id="b6ab9-660">Ajoutez des entrées de menu pour **Students** , **Courses** , **Instructors** et **Departments** , et supprimez l’entrée de menu **Contact** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-660">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="b6ab9-661">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-661">The changes are highlighted.</span></span> <span data-ttu-id="b6ab9-662">(Tout le balisage n’est *pas* affiché.)</span><span class="sxs-lookup"><span data-stu-id="b6ab9-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="b6ab9-663">Dans *Pages/Index.cshtml* , remplacez le contenu du fichier par le code suivant afin de remplacer le texte sur ASP.NET et MVC par le texte concernant cette application :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-663">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="b6ab9-664">Créer le modèle de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-664">Create the data model</span></span>

<span data-ttu-id="b6ab9-665">Créez des classes d’entités pour l’application Contoso University.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="b6ab9-666">Commencez par les trois entités suivantes :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-666">Start with the following three entities:</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="b6ab9-668">Il existe une relation un-à-plusieurs entre les entités `Student` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="b6ab9-669">Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="b6ab9-670">Un étudiant peut s’inscrire à autant de cours qu’il le souhaite.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="b6ab9-671">Un cours peut avoir une quantité illimitée d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="b6ab9-672">Dans les sections suivantes, une classe pour chacune de ces entités est créée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="b6ab9-673">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="b6ab9-673">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

<span data-ttu-id="b6ab9-675">Créez un dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-675">Create a *Models* folder.</span></span> <span data-ttu-id="b6ab9-676">Dans le dossier *Models* , créez un fichier de classe nommé *Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="b6ab9-677">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="b6ab9-678">Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b6ab9-679">Dans `classnameID`, `classname` est le nom de la classe.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="b6ab9-680">L’autre clé primaire reconnue automatiquement est `StudentID` dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="b6ab9-681">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b6ab9-682">Les propriétés de navigation établissent des liaisons à d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="b6ab9-683">Ici, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à ce `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="b6ab9-684">Par exemple, si une ligne Student dans la base de données a deux lignes Enrollment associées, la propriété de navigation `Enrollments` contient ces deux entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="b6ab9-685">Une ligne `Enrollment` associée est une ligne qui contient la valeur de clé primaire de cette étudiant dans la colonne `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="b6ab9-686">Par exemple, supposez que l’étudiant avec ID=1 a deux lignes dans la table `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="b6ab9-687">La table `Enrollment` a deux lignes avec `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="b6ab9-688">`StudentID` est une clé étrangère dans la table `Enrollment` qui spécifie l’étudiant dans la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="b6ab9-689">Si une propriété de navigation peut contenir plusieurs entités, la propriété de navigation doit être un type de liste, tel que `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="b6ab9-690">Vous pouvez spécifier `ICollection<T>`, ou un type tel que `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="b6ab9-691">Quand vous utilisez `ICollection<T>`, EF Core crée une collection `HashSet<T>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="b6ab9-692">Les propriétés de navigation qui contiennent plusieurs entités proviennent de relations plusieurs-à-plusieurs et un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="b6ab9-693">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="b6ab9-693">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="b6ab9-695">Dans le dossier *Models* , créez un fichier *Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="b6ab9-696">La propriété `EnrollmentID` est la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="b6ab9-697">Cette entité utilise le modèle `classnameID` au lieu de `ID` comme l’entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="b6ab9-698">En général, les développeurs choisissent un modèle et l’utilisent dans tout le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="b6ab9-699">Dans un prochain didacticiel, nous verrons qu’utiliser ID sans classname simplifie l’implémentation de l’héritage dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="b6ab9-700">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b6ab9-701">Le point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` est nullable.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="b6ab9-702">Une note (Grade) qui a la valeur Null est différente d’une note égale à zéro : la valeur Null signifie qu’une note n’est pas connue ou n’a pas encore été affectée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b6ab9-703">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b6ab9-704">Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="b6ab9-705">L’entité `Student` diffère de la propriété de navigation `Student.Enrollments`, qui contient plusieurs entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="b6ab9-706">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b6ab9-707">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b6ab9-708">EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b6ab9-709">Par exemple, `StudentID` pour la propriété de navigation `Student`, puisque la clé primaire de l’entité `Student` est `ID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b6ab9-710">Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b6ab9-711">Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="b6ab9-712">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="b6ab9-712">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="b6ab9-714">Dans le dossier *Models* , créez un fichier *Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="b6ab9-715">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b6ab9-716">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b6ab9-717">L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="b6ab9-718">Générer automatiquement le modèle d’étudiant</span><span class="sxs-lookup"><span data-stu-id="b6ab9-718">Scaffold the student model</span></span>

<span data-ttu-id="b6ab9-719">Dans cette section, le modèle d’étudiant est généré automatiquement.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="b6ab9-720">Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création (Create), de lecture (Read), de mise à jour (Update) et de suppression (Delete) (CRUD) pour le modèle d’étudiant.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="b6ab9-721">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-721">Build the project.</span></span>
* <span data-ttu-id="b6ab9-722">Créez le dossier *Pages/Students* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6ab9-724">Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Pages/Students* > **Ajouter** > **Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-724">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="b6ab9-725">Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **:::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-725">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD** .</span></span>

<span data-ttu-id="b6ab9-726">Complétez la boîte de dialogue **Ajouter des :::no-loc(Razor)::: pages à l’aide de Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-726">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b6ab9-727">Dans la liste déroulante **Classe de modèle** , sélectionnez **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)** .</span></span>
* <span data-ttu-id="b6ab9-728">Dans la ligne **Classe de contexte de données** , sélectionnez le signe **+** (plus) et remplacez le nom généré par **ContosoUniversity.Models.SchoolContext** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext** .</span></span>
* <span data-ttu-id="b6ab9-729">Dans la liste déroulante **Classe de contexte de données** , sélectionnez **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="b6ab9-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="b6ab9-730">Sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-730">Select **Add** .</span></span>

![Boîte de dialogue CRUD](intro/_static/s1.png)

<span data-ttu-id="b6ab9-732">Consultez [Générer automatiquement le modèle de film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) si vous rencontrez un problème à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6ab9-734">Exécutez les commandes suivantes pour générer automatiquement le modèle d’étudiant.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="b6ab9-735">Le processus de génération de modèles automatique a créé et changé les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="b6ab9-736">Fichiers créés</span><span class="sxs-lookup"><span data-stu-id="b6ab9-736">Files created</span></span>

* <span data-ttu-id="b6ab9-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="b6ab9-738">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="b6ab9-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="b6ab9-739">Mises à jour du fichier</span><span class="sxs-lookup"><span data-stu-id="b6ab9-739">File updates</span></span>

* <span data-ttu-id="b6ab9-740">*Startup.cs*  : Les changements apportés à ce fichier sont détaillés dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="b6ab9-741">*:::no-loc(appsettings.json):::* : La chaîne de connexion utilisée pour se connecter à une base de données locale est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-741">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b6ab9-742">Examiner le contexte inscrit avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="b6ab9-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b6ab9-743">ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b6ab9-744">Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b6ab9-745">Ces services sont fournis par les composants qui requièrent ces services (tels que les :::no-loc(Razor)::: pages) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-745">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b6ab9-746">Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b6ab9-747">L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b6ab9-748">Examinez la méthode `ConfigureServices` dans *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-748">Examine the `ConfigureServices` method in *Startup.cs* .</span></span> <span data-ttu-id="b6ab9-749">La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="b6ab9-750">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b6ab9-751">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="b6ab9-752">Mettre à jour la méthode Main</span><span class="sxs-lookup"><span data-stu-id="b6ab9-752">Update main</span></span>

<span data-ttu-id="b6ab9-753">Dans *Program.cs* , modifiez la méthode `Main` pour effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-753">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="b6ab9-754">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b6ab9-755">Appelez [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="b6ab9-756">Supprimez le contexte une fois la méthode `EnsureCreated` exécutée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="b6ab9-757">Le code suivant montre le fichier *Program.cs* mis à jour.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="b6ab9-758">`EnsureCreated` garantit que la base de données existe pour le contexte.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="b6ab9-759">Si elle existe, aucune action n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-759">If it exists, no action is taken.</span></span> <span data-ttu-id="b6ab9-760">Si elle n’existe pas, la base de données et tous ses schéma sont créés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="b6ab9-761">`EnsureCreated` n’utilise pas de migrations pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="b6ab9-762">Une base de données créée avec `EnsureCreated` ne peut pas être mise à jour à l’aide de migrations par la suite.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="b6ab9-763">`EnsureCreated` est appelée au démarrage de l’application, ce qui active le flux de travail suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="b6ab9-764">Supprimez la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-764">Delete the DB.</span></span>
* <span data-ttu-id="b6ab9-765">Modification du schéma de base de données (par exemple, ajout d’un champ `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="b6ab9-766">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-766">Run the app.</span></span>
* <span data-ttu-id="b6ab9-767">`EnsureCreated` crée une base de données avec la colonne `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="b6ab9-768">`EnsureCreated` est pratique au début du développement quand le schéma évolue rapidement.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="b6ab9-769">Plus loin dans le tutoriel, la base de données est supprimée et les migrations sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b6ab9-770">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="b6ab9-770">Test the app</span></span>

<span data-ttu-id="b6ab9-771">Exécutez l’application et acceptez la :::no-loc(cookie)::: stratégie.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-771">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="b6ab9-772">Cette application ne conserve pas les informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="b6ab9-773">Pour en savoir plus sur la :::no-loc(cookie)::: stratégie au niveau de la [prise en charge de l’union européenne règlement général sur la protection des données (RGPD)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-773">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="b6ab9-774">Sélectionnez le lien **Students** , puis **Créer nouveau** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-774">Select the **Students** link and then **Create New** .</span></span>
* <span data-ttu-id="b6ab9-775">Testez les liens Edit, Details et Delete.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="b6ab9-776">Examiner le contexte de base de données SchoolContext</span><span class="sxs-lookup"><span data-stu-id="b6ab9-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="b6ab9-777">La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données spécifié est la classe de contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="b6ab9-778">Le contexte de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b6ab9-779">Il spécifie les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b6ab9-780">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b6ab9-781">Mettez à jour *SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="b6ab9-782">Le code en surbrillance crée une propriété [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b6ab9-783">Dans la terminologie d’EF Core :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-783">In EF Core terminology:</span></span>

* <span data-ttu-id="b6ab9-784">Un jeu d’entités correspond généralement à une table de base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="b6ab9-785">Une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b6ab9-786">`DbSet<Enrollment>` et `DbSet<Course>` peuvent être omis.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="b6ab9-787">EF Core les inclut implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="b6ab9-788">Pour ce didacticiel, conservez `DbSet<Enrollment>` et `DbSet<Course>` dans le `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="b6ab9-789">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="b6ab9-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b6ab9-790">La chaîne de connexion spécifie [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="b6ab9-791">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b6ab9-792">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b6ab9-793">Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="b6ab9-794">Ajouter du code pour initialiser la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="b6ab9-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="b6ab9-795">EF Core crée une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="b6ab9-796">Dans cette section, une méthode `Initialize` est écrite pour la remplir avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="b6ab9-797">Dans le dossier *Data* , créez un fichier de classe nommé *DbInitializer.cs* et ajoutez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="b6ab9-798">Remarque : le code précédent utilise `Models` pour l’espace de noms ( `namespace ContosoUniversity.Models` ) au lieu de `Data` .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="b6ab9-799">`Models` est cohérent avec le code généré par l’outil de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="b6ab9-800">Pour plus d’informations, consultez [ce problème de génération de modèles automatique GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="b6ab9-801">Le code vérifie s’il existe des étudiants dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="b6ab9-802">S’il n’y en a pas, la base de données est initialisée avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="b6ab9-803">Il charge les données de test dans des tableaux plutôt que des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b6ab9-804">La méthode `EnsureCreated` crée automatiquement la base de données pour le contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="b6ab9-805">Si la base de données existe, `EnsureCreated` retourne sans modifier la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="b6ab9-806">Dans *Program.cs* , modifiez la méthode `Main` pour appeler `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-806">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="b6ab9-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6ab9-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6ab9-808">Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package**  :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6ab9-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6ab9-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6ab9-810">Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db* .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="b6ab9-811">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="b6ab9-811">View the DB</span></span>

<span data-ttu-id="b6ab9-812">Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="b6ab9-813">Il sera donc « SchoolContext-{GUID} ».</span><span class="sxs-lookup"><span data-stu-id="b6ab9-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="b6ab9-814">Le GUID est différent pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="b6ab9-815">Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="b6ab9-816">Dans SSOX, cliquez sur **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span></span>

<span data-ttu-id="b6ab9-817">Développez le nœud **Tables** .</span><span class="sxs-lookup"><span data-stu-id="b6ab9-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="b6ab9-818">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="b6ab9-819">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="b6ab9-819">Asynchronous code</span></span>

<span data-ttu-id="b6ab9-820">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b6ab9-821">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b6ab9-822">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b6ab9-823">Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b6ab9-824">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b6ab9-825">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="b6ab9-826">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b6ab9-827">Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b6ab9-828">Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="b6ab9-829">Le mot clé `async` fait en sorte que le compilateur :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b6ab9-830">Génère des rappels pour les parties du corps de méthode.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b6ab9-831">Crée automatiquement l’objet [Task](/dotnet/api/system.threading.tasks.task) qui est retourné.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="b6ab9-832">Pour plus d’informations, consultez [Type de retour Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="b6ab9-833">Le type de retour implicite `Task` représente le travail en cours.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="b6ab9-834">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b6ab9-835">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b6ab9-836">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b6ab9-837">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b6ab9-838">Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :</span><span class="sxs-lookup"><span data-stu-id="b6ab9-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b6ab9-839">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="b6ab9-840">Cela comprend `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="b6ab9-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b6ab9-841">mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b6ab9-842">Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b6ab9-843">Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="b6ab9-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="b6ab9-844">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="b6ab9-845">Dans le didacticiel suivant, nous allons examiner les opérations CRUD de base (créer, lire, mettre à jour, supprimer).</span><span class="sxs-lookup"><span data-stu-id="b6ab9-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="b6ab9-846">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b6ab9-846">Additional resources</span></span>

* [<span data-ttu-id="b6ab9-847">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="b6ab9-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="b6ab9-848">Next</span><span class="sxs-lookup"><span data-stu-id="b6ab9-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
