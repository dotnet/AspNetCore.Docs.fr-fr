---
title: 'Didacticiel : prise en main de EF Core dans une application Web MVC ASP.NET'
description: Ce document est le premier d’une série de didacticiels qui expliquent comment générer à partir de zéro l’exemple d’application Contoso University.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 36d72e037087399c8893d5ecb4a6fffdca3a3608
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054240"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="82bc1-103">Didacticiel : prise en main de EF Core dans une application Web MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="82bc1-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="82bc1-104">Ce tutoriel n’a **pas** été mis à jour vers ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="82bc1-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="82bc1-105">La [ :::no-loc(Razor)::: version des pages](xref:data/ef-rp/intro) a été mise à jour.</span><span class="sxs-lookup"><span data-stu-id="82bc1-105">The [:::no-loc(Razor)::: Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="82bc1-106">La plupart des modifications de code pour le ASP.NET Core 3,0 et la version ultérieure de ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="82bc1-106">Most of the code changes for the ASP.NET Core 3.0 and later version of this tutorial:</span></span>

* <span data-ttu-id="82bc1-107">Se trouvent dans les fichiers *Startup.cs* et *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="82bc1-107">Are in the *Startup.cs* and *Program.cs* files.</span></span>
* <span data-ttu-id="82bc1-108">Se trouve dans la [ :::no-loc(Razor)::: version des pages](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="82bc1-108">Can be found in the [:::no-loc(Razor)::: Pages version](xref:data/ef-rp/intro).</span></span> 

<span data-ttu-id="82bc1-109">Pour plus d’informations sur le moment où cette mise à jour pourrait avoir lieu, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span><span class="sxs-lookup"><span data-stu-id="82bc1-109">For information on when this might be updated, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="82bc1-110">L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET Core 2.2 MVC à l’aide d’Entity Framework (EF) Core 2.2 et de Visual Studio 2017 ou 2019.</span><span class="sxs-lookup"><span data-stu-id="82bc1-110">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="82bc1-111">L’exemple d’application est un site web pour une université Contoso fictive.</span><span class="sxs-lookup"><span data-stu-id="82bc1-111">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="82bc1-112">Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs.</span><span class="sxs-lookup"><span data-stu-id="82bc1-112">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="82bc1-113">Ce document est le premier d’une série de didacticiels qui expliquent comment générer à partir de zéro l’exemple d’application Contoso University.</span><span class="sxs-lookup"><span data-stu-id="82bc1-113">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="82bc1-114">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="82bc1-114">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="82bc1-115">Créer une application web ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="82bc1-115">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="82bc1-116">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="82bc1-116">Set up the site style</span></span>
> * <span data-ttu-id="82bc1-117">En savoir plus sur les packages NuGet EF Core</span><span class="sxs-lookup"><span data-stu-id="82bc1-117">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="82bc1-118">Créer le modèle de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-118">Create the data model</span></span>
> * <span data-ttu-id="82bc1-119">Créer le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-119">Create the database context</span></span>
> * <span data-ttu-id="82bc1-120">Inscrire le contexte pour l’injection de dépendance</span><span class="sxs-lookup"><span data-stu-id="82bc1-120">Register the context for dependency injection</span></span>
> * <span data-ttu-id="82bc1-121">Initialiser la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="82bc1-121">Initialize the database with test data</span></span>
> * <span data-ttu-id="82bc1-122">Créer un contrôleur et des vues</span><span class="sxs-lookup"><span data-stu-id="82bc1-122">Create a controller and views</span></span>
> * <span data-ttu-id="82bc1-123">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-123">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="82bc1-124">Prérequis</span><span class="sxs-lookup"><span data-stu-id="82bc1-124">Prerequisites</span></span>

* [<span data-ttu-id="82bc1-125">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="82bc1-125">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="82bc1-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec les charges de travail suivantes :</span><span class="sxs-lookup"><span data-stu-id="82bc1-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="82bc1-127">Charge de travail **Développement web et ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="82bc1-127">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="82bc1-128">Charge de travail **Développement multiplateforme .NET Core**</span><span class="sxs-lookup"><span data-stu-id="82bc1-128">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="82bc1-129">Dépannage</span><span class="sxs-lookup"><span data-stu-id="82bc1-129">Troubleshooting</span></span>

<span data-ttu-id="82bc1-130">Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span><span class="sxs-lookup"><span data-stu-id="82bc1-130">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="82bc1-131">Pour obtenir la liste des erreurs courantes et comment les résoudre, consultez [la section Dépannage du dernier didacticiel de la série](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="82bc1-131">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="82bc1-132">Si vous n’y trouvez pas ce dont vous avez besoin, vous pouvez publier une question sur StackOverflow.com pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="82bc1-132">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="82bc1-133">Il s’agit d’une série de 10 didacticiels, dont chacun s’appuie sur les opérations réalisées dans les précédents.</span><span class="sxs-lookup"><span data-stu-id="82bc1-133">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="82bc1-134">Pensez à enregistrer une copie du projet à la fin de chaque didacticiel réussi.</span><span class="sxs-lookup"><span data-stu-id="82bc1-134">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="82bc1-135">Ainsi, si vous rencontrez des problèmes, vous pouvez recommencer à la fin du didacticiel précédent au lieu de revenir au début de la série entière.</span><span class="sxs-lookup"><span data-stu-id="82bc1-135">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="82bc1-136">Application web Contoso University</span><span class="sxs-lookup"><span data-stu-id="82bc1-136">Contoso University web app</span></span>

<span data-ttu-id="82bc1-137">L’application que vous allez générer dans ces didacticiels est un site web simple d’université.</span><span class="sxs-lookup"><span data-stu-id="82bc1-137">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="82bc1-138">Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs.</span><span class="sxs-lookup"><span data-stu-id="82bc1-138">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="82bc1-139">Voici quelques écrans que vous allez créer.</span><span class="sxs-lookup"><span data-stu-id="82bc1-139">Here are a few of the screens you'll create.</span></span>

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="82bc1-142">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="82bc1-142">Create web app</span></span>

* <span data-ttu-id="82bc1-143">Ouvrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="82bc1-143">Open Visual Studio.</span></span>

* <span data-ttu-id="82bc1-144">Dans le menu **Fichier** , sélectionnez **Nouveau > Projet** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-144">From the **File** menu, select **New > Project** .</span></span>

* <span data-ttu-id="82bc1-145">Dans le volet gauche, sélectionnez **Installé > Visual C# > Web** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-145">From the left pane, select **Installed > Visual C# > Web** .</span></span>

* <span data-ttu-id="82bc1-146">Sélectionnez le modèle de projet **Application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-146">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="82bc1-147">Entrez **ContosoUniversity** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-147">Enter **ContosoUniversity** as the name and click **OK** .</span></span>

  ![Boîte de dialogue Nouveau projet](intro/_static/new-project2.png)

* <span data-ttu-id="82bc1-149">Patientez jusqu’à l’affichage de la boîte de dialogue **Nouvelle application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-149">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="82bc1-150">Sélectionnez **.NET Core** , **ASP.NET Core 2.2** et le modèle **Application web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-150">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="82bc1-151">Assurez-vous que **l’authentification** est définie sur **aucune authentification** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-151">Make sure **Authentication** is set to **No Authentication** .</span></span>

* <span data-ttu-id="82bc1-152">Sélectionnez **OK** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-152">Select **OK**</span></span>

  ![Boîte de dialogue Nouveau projet ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="82bc1-154">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="82bc1-154">Set up the site style</span></span>

<span data-ttu-id="82bc1-155">Quelques changements simples configureront le menu, la disposition et la page d’accueil du site.</span><span class="sxs-lookup"><span data-stu-id="82bc1-155">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="82bc1-156">Ouvrez *Views/Shared/_Layout.cshtml* et apportez les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="82bc1-156">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="82bc1-157">Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ».</span><span class="sxs-lookup"><span data-stu-id="82bc1-157">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="82bc1-158">Il y a trois occurrences.</span><span class="sxs-lookup"><span data-stu-id="82bc1-158">There are three occurrences.</span></span>

* <span data-ttu-id="82bc1-159">Ajoutez des entrées de menu pour **About** , **Students** , **Courses** , **Instructors** , et **Departments** , et supprimez l’entrée de menu **Privacy** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-159">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="82bc1-160">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="82bc1-160">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="82bc1-161">Dans *Views/Home/Index.cshtml* , remplacez le contenu du fichier par le code suivant, afin de remplacer le texte relatif à ASP.NET et MVC par le texte relatif à cette application :</span><span class="sxs-lookup"><span data-stu-id="82bc1-161">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="82bc1-162">Appuyez sur Ctrl+F5 pour exécuter le projet ou choisissez **Déboguer > Exécuter sans débogage** dans le menu.</span><span class="sxs-lookup"><span data-stu-id="82bc1-162">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="82bc1-163">Vous voyez la page d’accueil avec des onglets pour les pages que vous allez créer dans ces didacticiels.</span><span class="sxs-lookup"><span data-stu-id="82bc1-163">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Page d’accueil de Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="82bc1-165">À propos des packages NuGet EF Core</span><span class="sxs-lookup"><span data-stu-id="82bc1-165">About EF Core NuGet packages</span></span>

<span data-ttu-id="82bc1-166">Pour ajouter la prise en charge d’EF Core à un projet, installez le fournisseur de bases de données que vous souhaitez cibler.</span><span class="sxs-lookup"><span data-stu-id="82bc1-166">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="82bc1-167">Ce didacticiel utilise SQL Server et le package de fournisseur est [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="82bc1-167">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="82bc1-168">Ce package étant inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), vous n’avez pas besoin de le référencer.</span><span class="sxs-lookup"><span data-stu-id="82bc1-168">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="82bc1-169">Le package EF SQL Server et ses dépendances (`Microsoft.EntityFrameworkCore` et `Microsoft.EntityFrameworkCore.Relational`) fournissent la prise en charge du runtime pour EF.</span><span class="sxs-lookup"><span data-stu-id="82bc1-169">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="82bc1-170">Vous ajouterez un package d’outils ultérieurement, dans le didacticiel [Migrations](migrations.md).</span><span class="sxs-lookup"><span data-stu-id="82bc1-170">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="82bc1-171">Pour obtenir des informations sur les autres fournisseurs de bases de données qui sont disponibles pour Entity Framework Core, consultez [Fournisseurs de bases de données](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="82bc1-171">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="82bc1-172">Créer le modèle de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-172">Create the data model</span></span>

<span data-ttu-id="82bc1-173">Ensuite, vous allez créer des classes d’entités pour l’application Contoso University.</span><span class="sxs-lookup"><span data-stu-id="82bc1-173">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="82bc1-174">Vous commencerez avec les trois entités suivantes.</span><span class="sxs-lookup"><span data-stu-id="82bc1-174">You'll start with the following three entities.</span></span>

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="82bc1-176">Il existe une relation un-à-plusieurs entre les entités `Student` et `Enrollment`, et une relation un-à-plusieurs entre les entités `Course` et `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-176">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="82bc1-177">En d’autres termes, un étudiant peut être inscrit dans un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.</span><span class="sxs-lookup"><span data-stu-id="82bc1-177">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="82bc1-178">Dans les sections suivantes, vous allez créer une classe pour chacune de ces entités.</span><span class="sxs-lookup"><span data-stu-id="82bc1-178">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="82bc1-179">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="82bc1-179">The Student entity</span></span>

![Diagramme de l’entité Student](intro/_static/student-entity.png)

<span data-ttu-id="82bc1-181">Dans le dossier *Models* , créez un fichier de classe nommé *Student.cs* et remplacez le code du modèle par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="82bc1-181">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="82bc1-182">La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe.</span><span class="sxs-lookup"><span data-stu-id="82bc1-182">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="82bc1-183">Par défaut, Entity Framework interprète une propriété nommée `ID` ou `classnameID` comme étant la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="82bc1-183">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="82bc1-184">La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="82bc1-184">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="82bc1-185">Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité.</span><span class="sxs-lookup"><span data-stu-id="82bc1-185">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="82bc1-186">Dans ce cas, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à l’entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-186">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="82bc1-187">En d’autres termes, si une ligne Student donnée dans la base de données a deux lignes Enrollment associées (lignes qui contiennent la valeur de clé primaire de cet étudiant dans la colonne de clé étrangère StudentID), la propriété de navigation `Enrollments` de cette entité `Student` contiendra ces deux entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-187">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="82bc1-188">Si une propriété de navigation peut contenir plusieurs entités (comme dans des relations plusieurs à plusieurs ou un -à-plusieurs), son type doit être une liste dans laquelle les entrées peuvent être ajoutées, supprimées et mises à jour, telle que `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-188">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="82bc1-189">Vous pouvez spécifier `ICollection<T>` ou un type tel que `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-189">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="82bc1-190">Si vous spécifiez `ICollection<T>`, EF crée une collection `HashSet<T>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="82bc1-190">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="82bc1-191">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="82bc1-191">The Enrollment entity</span></span>

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="82bc1-193">Dans le dossier *Models* , créez *Enrollment.cs* et remplacez le code existant par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="82bc1-193">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="82bc1-194">La propriété `EnrollmentID` sera la clé primaire. Cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même, comme vous l’avez vu dans l’entité `Student`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-194">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="82bc1-195">En général, vous choisissez un modèle et l’utilisez dans tout votre modèle de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-195">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="82bc1-196">Ici, la variante illustre que vous pouvez utiliser l’un ou l’autre modèle.</span><span class="sxs-lookup"><span data-stu-id="82bc1-196">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="82bc1-197">Dans un [prochain didacticiel](inheritance.md), vous verrez comment l’utilisation de l’ID sans classname simplifie l’implémentation de l’héritage dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-197">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="82bc1-198">La propriété `Grade` est un `enum`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="82bc1-199">Le point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` est nullable.</span><span class="sxs-lookup"><span data-stu-id="82bc1-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="82bc1-200">Une note (Grade) qui a la valeur Null est différente d’une note égale à zéro : la valeur Null signifie qu’une note n’est pas connue ou n’a pas encore été affectée.</span><span class="sxs-lookup"><span data-stu-id="82bc1-200">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="82bc1-201">La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="82bc1-202">Une entité `Enrollment` est associée à une entité `Student`, donc la propriété peut contenir uniquement une entité `Student` unique (contrairement à la propriété de navigation `Student.Enrollments` que vous avez vue précédemment, qui peut contenir plusieurs entités `Enrollment`).</span><span class="sxs-lookup"><span data-stu-id="82bc1-202">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="82bc1-203">La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="82bc1-204">Une entité `Enrollment` est associée à une entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="82bc1-205">Entity Framework interprète une propriété comme une propriété de clé étrangère si elle est nommée `<navigation property name><primary key property name>` (par exemple, `StudentID` pour la propriété de navigation `Student`, puisque la clé primaire de l’entité `Student` est `ID`).</span><span class="sxs-lookup"><span data-stu-id="82bc1-205">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="82bc1-206">Les propriétés de clé étrangère peuvent également être nommées simplement `<primary key property name>` (par exemple, `CourseID`, puisque la clé primaire de l’entité `Course` est `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="82bc1-206">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="82bc1-207">L’entité Course</span><span class="sxs-lookup"><span data-stu-id="82bc1-207">The Course entity</span></span>

![Diagramme de l’entité Course](intro/_static/course-entity.png)

<span data-ttu-id="82bc1-209">Dans le dossier *Models* , créez *cs* et remplacez le code existant par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="82bc1-209">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="82bc1-210">La propriété `Enrollments` est une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="82bc1-210">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="82bc1-211">Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-211">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="82bc1-212">Nous fournirons plus de détails sur l’attribut `DatabaseGenerated` dans un [didacticiel ultérieur](complex-data-model.md) de cette série.</span><span class="sxs-lookup"><span data-stu-id="82bc1-212">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="82bc1-213">En fait, cet attribut vous permet d’entrer la clé primaire pour le cours plutôt que de laisser la base de données la générer.</span><span class="sxs-lookup"><span data-stu-id="82bc1-213">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="82bc1-214">Créer le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-214">Create the database context</span></span>

<span data-ttu-id="82bc1-215">La classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifié est la classe de contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-215">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="82bc1-216">Vous créez cette classe en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-216">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="82bc1-217">Dans votre code, vous spécifiez les entités qui sont incluses dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-217">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="82bc1-218">Vous pouvez également personnaliser un certain comportement d’Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="82bc1-218">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="82bc1-219">Dans ce projet, la classe est nommée `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-219">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="82bc1-220">Dans le dossier du projet, créez un dossier nommé *Data* .</span><span class="sxs-lookup"><span data-stu-id="82bc1-220">In the project folder, create a folder named *Data* .</span></span>

<span data-ttu-id="82bc1-221">Dans ce dossier *Data* , créez un nouveau fichier de classe nommé *SchoolContext.cs* et remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="82bc1-221">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="82bc1-222">Ce code crée une propriété `DbSet` pour chaque jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="82bc1-222">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="82bc1-223">Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.</span><span class="sxs-lookup"><span data-stu-id="82bc1-223">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="82bc1-224">Vous pourriez avoir omis les instructions `DbSet<Enrollment>` et `DbSet<Course>`, et cela fonctionnerait de la même façon.</span><span class="sxs-lookup"><span data-stu-id="82bc1-224">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="82bc1-225">Entity Framework les inclurait implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-225">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="82bc1-226">Quand la base de données est créée, EF crée des tables dont les noms sont identiques aux noms de propriété `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-226">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="82bc1-227">Les noms de propriété pour les collections sont généralement pluriels (Students plutôt que Student), mais les développeurs ne sont pas tous d’accord sur la nécessité d’utiliser des noms de tables au pluriel.</span><span class="sxs-lookup"><span data-stu-id="82bc1-227">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="82bc1-228">Pour ces didacticiels, vous remplacerez le comportement par défaut en spécifiant des noms de tables au singulier dans le contexte DbContext.</span><span class="sxs-lookup"><span data-stu-id="82bc1-228">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="82bc1-229">Pour ce faire, ajoutez le code en surbrillance suivant après la dernière propriété DbSet.</span><span class="sxs-lookup"><span data-stu-id="82bc1-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="82bc1-230">Inscrire SchoolContext</span><span class="sxs-lookup"><span data-stu-id="82bc1-230">Register the SchoolContext</span></span>

<span data-ttu-id="82bc1-231">ASP.NET Core implémente l’[injection de dépendance](../../fundamentals/dependency-injection.md) par défaut.</span><span class="sxs-lookup"><span data-stu-id="82bc1-231">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="82bc1-232">Des services (tels que le contexte de base de données EF) sont inscrits avec l’injection de dépendance au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="82bc1-232">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="82bc1-233">Ces services sont affectés aux composants qui les nécessitent (tels que les contrôleurs MVC) par le biais de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="82bc1-233">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="82bc1-234">Vous verrez le code de constructeur de contrôleur qui obtient une instance de contexte plus loin dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="82bc1-234">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="82bc1-235">Pour inscrire `SchoolContext` en tant que service, ouvrez *Startup.cs* et ajoutez les lignes en surbrillance à la méthode `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-235">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="82bc1-236">Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="82bc1-237">Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="82bc1-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="82bc1-238">Ajoutez des instructions `using` pour les espaces de noms `ContosoUniversity.Data` et `Microsoft.EntityFrameworkCore`, puis générez le projet.</span><span class="sxs-lookup"><span data-stu-id="82bc1-238">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="82bc1-239">Ouvrez le *:::no-loc(appsettings.json):::* fichier et ajoutez une chaîne de connexion comme indiqué dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="82bc1-239">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="82bc1-240">Base de données locale SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="82bc1-240">SQL Server Express LocalDB</span></span>

<span data-ttu-id="82bc1-241">La chaîne de connexion spécifie une base de données SQL Server LocalDB.</span><span class="sxs-lookup"><span data-stu-id="82bc1-241">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="82bc1-242">LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="82bc1-242">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="82bc1-243">LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe.</span><span class="sxs-lookup"><span data-stu-id="82bc1-243">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="82bc1-244">Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-244">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="82bc1-245">Initialiser la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="82bc1-245">Initialize DB with test data</span></span>

<span data-ttu-id="82bc1-246">Entity Framework créera une base de données vide pour vous.</span><span class="sxs-lookup"><span data-stu-id="82bc1-246">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="82bc1-247">Dans cette section, vous écrivez une méthode qui est appelée après la création de la base de données pour la remplir avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="82bc1-247">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="82bc1-248">Là, vous allez utiliser la méthode `EnsureCreated` pour créer automatiquement la base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-248">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="82bc1-249">Dans un [didacticiel ultérieur](migrations.md), vous verrez comment traiter les modifications des modèles à l’aide des migrations Code First pour modifier le schéma de base de données au lieu de supprimer et de recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-249">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="82bc1-250">Dans le dossier *Data* , créez un nouveau fichier de classe nommé *DbInitializer.cs* et remplacez le code de modèle par le code suivant, qui entraîne la création d’une base de données, si nécessaire, et charge les données de test dans la nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-250">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="82bc1-251">Le code vérifie si des étudiants figurent dans la base de données et, dans la négative, il suppose que la base de données est nouvelle et doit être initialement peuplée avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="82bc1-251">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="82bc1-252">Il charge les données de test dans des tableaux plutôt que des collections `List<T>` afin d’optimiser les performances.</span><span class="sxs-lookup"><span data-stu-id="82bc1-252">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="82bc1-253">Dans *Program.cs* , modifiez la méthode `Main` pour effectuer les opérations suivantes au démarrage de l’application :</span><span class="sxs-lookup"><span data-stu-id="82bc1-253">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="82bc1-254">Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="82bc1-254">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="82bc1-255">Appeler la méthode de remplissage initial, en lui transmettant le contexte.</span><span class="sxs-lookup"><span data-stu-id="82bc1-255">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="82bc1-256">Supprimer le contexte une fois l’exécution de la méthode de peuplement initial terminée.</span><span class="sxs-lookup"><span data-stu-id="82bc1-256">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="82bc1-257">Ajoutez des instructions `using` :</span><span class="sxs-lookup"><span data-stu-id="82bc1-257">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="82bc1-258">Dans les didacticiels plus anciens, vous pouvez voir un code similaire dans la méthode `Configure`, dans *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="82bc1-258">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs* .</span></span> <span data-ttu-id="82bc1-259">Nous vous recommandons d’utiliser la méthode `Configure` uniquement pour configurer le pipeline de demande.</span><span class="sxs-lookup"><span data-stu-id="82bc1-259">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="82bc1-260">Le code de démarrage d’application doit figurer dans la méthode `Main`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-260">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="82bc1-261">À présent, la première fois que vous exécutez l’application, la base de données est créée et initialement peuplée avec les données de test.</span><span class="sxs-lookup"><span data-stu-id="82bc1-261">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="82bc1-262">Chaque fois que vous changez votre modèle de données, vous pouvez supprimer la base de données, mettre à jour votre méthode de peuplement initial et repartir de la même façon avec une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-262">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="82bc1-263">Dans les didacticiels suivants, vous verrez comment modifier la base de données quand le modèle de données change, sans supprimer et recréer la base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-263">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="82bc1-264">Créer un contrôleur et des vues</span><span class="sxs-lookup"><span data-stu-id="82bc1-264">Create controller and views</span></span>

<span data-ttu-id="82bc1-265">Ensuite, vous utiliserez le moteur de génération de modèles automatique dans Visual Studio pour ajouter un contrôleur MVC et les vues qu’utilisera EF pour exécuter des requêtes de données et enregistrer les données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-265">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="82bc1-266">La création automatique de vues et de méthodes d’action CRUD porte le nom de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="82bc1-266">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="82bc1-267">La génération de modèles automatique diffère de la génération de code dans la mesure où le code obtenu par génération de modèles automatique est un point de départ que vous pouvez modifier pour prendre en compte vos propres exigences, tandis qu’en général vous ne modifiez pas le code généré.</span><span class="sxs-lookup"><span data-stu-id="82bc1-267">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="82bc1-268">Lorsque vous avez besoin de personnaliser le code généré, vous utilisez des classes partielles ou vous regénérez le code en cas de changements.</span><span class="sxs-lookup"><span data-stu-id="82bc1-268">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="82bc1-269">Cliquez avec le bouton droit sur le dossier **Contrôleurs** dans l’ **Explorateur de solutions** , puis sélectionnez **Ajouter > Nouvel élément généré automatiquement** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-269">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item** .</span></span>

* <span data-ttu-id="82bc1-270">Dans la boîte de dialogue **Ajouter un modèle automatique** :</span><span class="sxs-lookup"><span data-stu-id="82bc1-270">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="82bc1-271">Sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-271">Select **MVC controller with views, using Entity Framework** .</span></span>

  * <span data-ttu-id="82bc1-272">Cliquez sur **Add** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-272">Click **Add** .</span></span> <span data-ttu-id="82bc1-273">La boîte de dialogue **Ajouter un contrôleur MVC avec vues, utilisant Entity Framework** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="82bc1-273">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Génération de modèles automatique – Étudiant](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="82bc1-275">Dans **Classe de modèle** , sélectionnez **Student** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-275">In **Model class** select **Student** .</span></span>

  * <span data-ttu-id="82bc1-276">Dans **Classe du contexte de données** , sélectionnez **SchoolContext** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-276">In **Data context class** select **SchoolContext** .</span></span>

  * <span data-ttu-id="82bc1-277">Acceptez la valeur par défaut **StudentsController** comme nom.</span><span class="sxs-lookup"><span data-stu-id="82bc1-277">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="82bc1-278">Cliquez sur **Add** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-278">Click **Add** .</span></span>

  <span data-ttu-id="82bc1-279">Lorsque vous cliquez sur **Ajouter** , le moteur de génération de modèles automatique de Visual Studio crée un fichier *StudentsController.cs* et un ensemble de vues (fichiers *.cshtml* ) qui fonctionnent avec le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="82bc1-279">When you click **Add** , the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="82bc1-280">(Le moteur de génération de modèles automatique peut également créer le contexte de base de données pour vous, si vous ne l’avez pas déjà créé manuellement, comme vous l’avez fait précédemment pour ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="82bc1-280">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="82bc1-281">Vous pouvez spécifier une nouvelle classe de contexte dans la zone **Ajouter un contrôleur** en cliquant sur le signe plus à droite de **Classe du contexte de données** .</span><span class="sxs-lookup"><span data-stu-id="82bc1-281">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class** .</span></span>  <span data-ttu-id="82bc1-282">Visual Studio crée ensuite votre classe `DbContext` ainsi que le contrôleur et les vues.)</span><span class="sxs-lookup"><span data-stu-id="82bc1-282">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="82bc1-283">Vous pouvez remarquer que le contrôleur accepte un `SchoolContext` comme paramètre de constructeur.</span><span class="sxs-lookup"><span data-stu-id="82bc1-283">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="82bc1-284">L’injection de dépendance ASP.NET Core s’occupe de la transmission d’une instance de `SchoolContext` dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="82bc1-284">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="82bc1-285">Vous avez configuré cela dans le fichier *Startup.cs* précédemment.</span><span class="sxs-lookup"><span data-stu-id="82bc1-285">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="82bc1-286">Le contrôleur contient une méthode d’action `Index`, qui affiche tous les étudiants dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-286">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="82bc1-287">La méthode obtient la liste des étudiants du jeu d’entités Students en lisant la propriété `Students` de l’instance de contexte de base de données :</span><span class="sxs-lookup"><span data-stu-id="82bc1-287">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="82bc1-288">Vous découvrirez les éléments de programmation asynchrones dans ce code, plus loin dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="82bc1-288">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="82bc1-289">La vue *Views/Students/Index.cshtml* affiche cette liste dans une table :</span><span class="sxs-lookup"><span data-stu-id="82bc1-289">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="82bc1-290">Appuyez sur Ctrl+F5 pour exécuter le projet ou choisissez **Déboguer > Exécuter sans débogage** dans le menu.</span><span class="sxs-lookup"><span data-stu-id="82bc1-290">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="82bc1-291">Cliquez sur l’onglet Students pour afficher les données de test que la méthode `DbInitializer.Initialize` a insérées.</span><span class="sxs-lookup"><span data-stu-id="82bc1-291">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="82bc1-292">Selon l’étroitesse de votre fenêtre de navigateur, vous verrez le lien de l’onglet `Students` en haut de la page ou vous devrez cliquer sur l’icône de navigation dans le coin supérieur droit pour afficher le lien.</span><span class="sxs-lookup"><span data-stu-id="82bc1-292">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Page d’accueil étroite de Contoso University](intro/_static/home-page-narrow.png)

![Page d’index des étudiants](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="82bc1-295">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-295">View the database</span></span>

<span data-ttu-id="82bc1-296">Lorsque vous avez démarré l’application, la méthode `DbInitializer.Initialize` appelle `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-296">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="82bc1-297">EF a vu qu’il n’y avait pas de base de données et en a donc créé une, puis le reste du code de la méthode `Initialize` a rempli la base de données avec des données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-297">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="82bc1-298">Vous pouvez utiliser l’ **Explorateur d’objets SQL Server** (SSOX) pour afficher la base de données dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="82bc1-298">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="82bc1-299">Fermez le navigateur.</span><span class="sxs-lookup"><span data-stu-id="82bc1-299">Close the browser.</span></span>

<span data-ttu-id="82bc1-300">Si la fenêtre SSOX n’est pas déjà ouverte, sélectionnez-la dans le menu **Affichage** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="82bc1-300">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="82bc1-301">Dans SSOX, cliquez sur **\MSSQLLocalDB > bases de données** , puis cliquez sur l’entrée correspondant au nom de la base de données qui se trouve dans la chaîne de connexion de votre *:::no-loc(appsettings.json):::* fichier.</span><span class="sxs-lookup"><span data-stu-id="82bc1-301">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in your *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="82bc1-302">Développez le nœud **Tables** pour afficher les tables de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-302">Expand the **Tables** node to see the tables in your database.</span></span>

![Tables dans SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="82bc1-304">Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes qui ont été créées et les lignes qui ont été insérées dans la table.</span><span class="sxs-lookup"><span data-stu-id="82bc1-304">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Table Student dans SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="82bc1-306">Les fichiers de base de données *.mdf* et *.ldf* se trouvent dans le dossier *C:\Utilisateurs\\\<yourusername>* .</span><span class="sxs-lookup"><span data-stu-id="82bc1-306">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="82bc1-307">Étant donné que vous appelez `EnsureCreated` dans la méthode d’initialiseur qui s’exécute au démarrage de l’application, vous pouvez maintenant apporter une modification à la classe `Student`, supprimer la base de données ou réexécuter l’application, et la base de données serait automatiquement recréée conformément à votre modification.</span><span class="sxs-lookup"><span data-stu-id="82bc1-307">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="82bc1-308">Par exemple, si vous ajoutez une propriété `EmailAddress` à la classe `Student`, vous voyez une nouvelle colonne `EmailAddress` dans la table recréée.</span><span class="sxs-lookup"><span data-stu-id="82bc1-308">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="82bc1-309">Conventions</span><span class="sxs-lookup"><span data-stu-id="82bc1-309">Conventions</span></span>

<span data-ttu-id="82bc1-310">La quantité de code que vous deviez écrire pour qu’Entity Framework puisse créer une base de données complète pour vous est minimale en raison de l’utilisation de conventions ou d’hypothèses effectuées par Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="82bc1-310">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="82bc1-311">Les noms des propriétés `DbSet` sont utilisés comme noms de tables.</span><span class="sxs-lookup"><span data-stu-id="82bc1-311">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="82bc1-312">Pour les entités non référencées par une propriété `DbSet`, les noms des classes d’entités sont utilisés comme noms de tables.</span><span class="sxs-lookup"><span data-stu-id="82bc1-312">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="82bc1-313">Les noms des propriétés d’entités sont utilisés comme noms de colonnes.</span><span class="sxs-lookup"><span data-stu-id="82bc1-313">Entity property names are used for column names.</span></span>

* <span data-ttu-id="82bc1-314">Les propriétés d’entité nommées ID ou classnameID sont reconnues comme propriétés de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="82bc1-314">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="82bc1-315">Une propriété est interprétée comme une propriété de clé étrangère si elle est nommée *\<navigation property name>\<primary key property name>* (par exemple, `StudentID` pour la `Student` propriété de navigation, puisque la `Student` clé primaire de l’entité est `ID` ).</span><span class="sxs-lookup"><span data-stu-id="82bc1-315">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="82bc1-316">Les propriétés de clé étrangère peuvent également être nommées simplement *\<primary key property name>* (par exemple, `EnrollmentID` puisque la `Enrollment` clé primaire de l’entité est `EnrollmentID` ).</span><span class="sxs-lookup"><span data-stu-id="82bc1-316">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="82bc1-317">Le comportement conventionnel peut être remplacé.</span><span class="sxs-lookup"><span data-stu-id="82bc1-317">Conventional behavior can be overridden.</span></span> <span data-ttu-id="82bc1-318">Par exemple, vous pouvez spécifier explicitement les noms de tables, comme vous l’avez vu précédemment dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="82bc1-318">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="82bc1-319">De plus, vous pouvez définir des noms de colonne et définir une propriété quelconque en tant que clé primaire ou clé étrangère, comme vous le verrez dans un [didacticiel ultérieur](complex-data-model.md) dans cette série.</span><span class="sxs-lookup"><span data-stu-id="82bc1-319">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="82bc1-320">Code asynchrone</span><span class="sxs-lookup"><span data-stu-id="82bc1-320">Asynchronous code</span></span>

<span data-ttu-id="82bc1-321">La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.</span><span class="sxs-lookup"><span data-stu-id="82bc1-321">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="82bc1-322">Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés.</span><span class="sxs-lookup"><span data-stu-id="82bc1-322">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="82bc1-323">Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés.</span><span class="sxs-lookup"><span data-stu-id="82bc1-323">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="82bc1-324">Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent.</span><span class="sxs-lookup"><span data-stu-id="82bc1-324">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="82bc1-325">Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes.</span><span class="sxs-lookup"><span data-stu-id="82bc1-325">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="82bc1-326">Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.</span><span class="sxs-lookup"><span data-stu-id="82bc1-326">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="82bc1-327">Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution, mais dans les situations de faible trafic, la baisse de performances est négligeable, alors qu’en cas de trafic élevé, l’amélioration potentielle des performances est importante.</span><span class="sxs-lookup"><span data-stu-id="82bc1-327">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="82bc1-328">Dans le code suivant, le mot clé `async`, la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` provoquent l’exécution asynchrone du code.</span><span class="sxs-lookup"><span data-stu-id="82bc1-328">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="82bc1-329">Le mot clé `async` indique au compilateur de générer des rappels pour les parties du corps de la méthode et pour créer automatiquement l’objet `Task<IActionResult>` qui est renvoyé.</span><span class="sxs-lookup"><span data-stu-id="82bc1-329">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="82bc1-330">Le type de retour `Task<IActionResult>` représente le travail en cours avec un résultat de type `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-330">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="82bc1-331">Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties.</span><span class="sxs-lookup"><span data-stu-id="82bc1-331">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="82bc1-332">La première partie se termine par l’opération qui est démarrée de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="82bc1-332">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="82bc1-333">La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.</span><span class="sxs-lookup"><span data-stu-id="82bc1-333">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="82bc1-334">`ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-334">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="82bc1-335">Voici quelques éléments à connaître lorsque vous écrivez un code asynchrone qui utilise Entity Framework :</span><span class="sxs-lookup"><span data-stu-id="82bc1-335">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="82bc1-336">Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="82bc1-336">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="82bc1-337">Cela inclut, par exemple, `ToListAsync`, `SingleOrDefaultAsync` et `SaveChangesAsync`,</span><span class="sxs-lookup"><span data-stu-id="82bc1-337">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="82bc1-338">mais pas les instructions qui ne font, par exemple, que changer `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-338">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="82bc1-339">Un contexte EF n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.</span><span class="sxs-lookup"><span data-stu-id="82bc1-339">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="82bc1-340">Lorsque vous appelez une méthode EF asynchrone quelconque, utilisez toujours le mot clé `await`.</span><span class="sxs-lookup"><span data-stu-id="82bc1-340">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="82bc1-341">Si vous souhaitez tirer profit des meilleures performances du code asynchrone, assurez-vous que tous les packages de bibliothèque que vous utilisez (par exemple pour changer de page) utilisent également du code asynchrone s’ils appellent des méthodes Entity Framework qui provoquent l’envoi des requêtes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="82bc1-341">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="82bc1-342">Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble du code asynchrone](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="82bc1-342">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="82bc1-343">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="82bc1-343">Get the code</span></span>

[<span data-ttu-id="82bc1-344">Télécharger ou afficher l’application complète.</span><span class="sxs-lookup"><span data-stu-id="82bc1-344">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="82bc1-345">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="82bc1-345">Next steps</span></span>

<span data-ttu-id="82bc1-346">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="82bc1-346">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="82bc1-347">Créer une application web ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="82bc1-347">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="82bc1-348">Configurer le style du site</span><span class="sxs-lookup"><span data-stu-id="82bc1-348">Set up the site style</span></span>
> * <span data-ttu-id="82bc1-349">En savoir plus sur les packages NuGet EF Core</span><span class="sxs-lookup"><span data-stu-id="82bc1-349">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="82bc1-350">Créer le modèle de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-350">Created the data model</span></span>
> * <span data-ttu-id="82bc1-351">Créer le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-351">Created the database context</span></span>
> * <span data-ttu-id="82bc1-352">Inscrire le contexte SchoolContext</span><span class="sxs-lookup"><span data-stu-id="82bc1-352">Registered the SchoolContext</span></span>
> * <span data-ttu-id="82bc1-353">Initialiser la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="82bc1-353">Initialized DB with test data</span></span>
> * <span data-ttu-id="82bc1-354">Créer un contrôleur et des vues</span><span class="sxs-lookup"><span data-stu-id="82bc1-354">Created controller and views</span></span>
> * <span data-ttu-id="82bc1-355">Afficher la base de données</span><span class="sxs-lookup"><span data-stu-id="82bc1-355">Viewed the database</span></span>

<span data-ttu-id="82bc1-356">Dans le prochain didacticiel, vous apprendrez à effectuer des opérations CRUD de base (créer, lire, mettre à jour, supprimer).</span><span class="sxs-lookup"><span data-stu-id="82bc1-356">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="82bc1-357">Passez au tutoriel suivant pour découvrir comment effectuer des opérations CRUD de base (créer, lire, mettre à jour, supprimer).</span><span class="sxs-lookup"><span data-stu-id="82bc1-357">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="82bc1-358">Implémenter la fonctionnalité CRUD de base</span><span class="sxs-lookup"><span data-stu-id="82bc1-358">Implement basic CRUD functionality</span></span>](crud.md)

