---
title: 'Didacticiel : en savoir plus sur les scénarios avancés-ASP.NET MVC avec EF Core'
description: Ce tutoriel présente plusieurs rubriques pratiques pour aller au-delà des principes de base du développement d’applications web ASP.NET Core qui utilisent Entity Framework Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/ef-mvc/advanced
ms.openlocfilehash: 386be395399bf4131e4b6c8cac8221f994e8b7c5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054383"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="6282b-103">Didacticiel : en savoir plus sur les scénarios avancés-ASP.NET MVC avec EF Core</span><span class="sxs-lookup"><span data-stu-id="6282b-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="6282b-104">Dans le didacticiel précédent, vous avez implémenté l’héritage TPH (table par hiérarchie).</span><span class="sxs-lookup"><span data-stu-id="6282b-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="6282b-105">Ce didacticiel présente plusieurs rubriques qu’il est utile de connaître lorsque vous allez au-delà des principes de base du développement d’applications web ASP.NET Core qui utilisent Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6282b-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="6282b-106">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="6282b-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6282b-107">Exécuter des requêtes SQL brutes</span><span class="sxs-lookup"><span data-stu-id="6282b-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="6282b-108">Appeler une requête pour retourner des entités</span><span class="sxs-lookup"><span data-stu-id="6282b-108">Call a query to return entities</span></span>
> * <span data-ttu-id="6282b-109">Appeler une requête pour retourner d’autres types</span><span class="sxs-lookup"><span data-stu-id="6282b-109">Call a query to return other types</span></span>
> * <span data-ttu-id="6282b-110">Appeler une requête de mise à jour</span><span class="sxs-lookup"><span data-stu-id="6282b-110">Call an update query</span></span>
> * <span data-ttu-id="6282b-111">Examiner les requêtes SQL</span><span class="sxs-lookup"><span data-stu-id="6282b-111">Examine SQL queries</span></span>
> * <span data-ttu-id="6282b-112">Créer une couche d’abstraction</span><span class="sxs-lookup"><span data-stu-id="6282b-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="6282b-113">En savoir plus sur la Détection automatique des modifications</span><span class="sxs-lookup"><span data-stu-id="6282b-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="6282b-114">En savoir plus sur le code source et les plans de développement EF Core</span><span class="sxs-lookup"><span data-stu-id="6282b-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="6282b-115">Apprendre à utiliser du code dynamique LINQ pour simplifier le code</span><span class="sxs-lookup"><span data-stu-id="6282b-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6282b-116">Prérequis</span><span class="sxs-lookup"><span data-stu-id="6282b-116">Prerequisites</span></span>

* [<span data-ttu-id="6282b-117">Implémenter l’héritage</span><span class="sxs-lookup"><span data-stu-id="6282b-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="6282b-118">Exécuter des requêtes SQL brutes</span><span class="sxs-lookup"><span data-stu-id="6282b-118">Perform raw SQL queries</span></span>

<span data-ttu-id="6282b-119">L’un des avantages d’utiliser Entity Framework est que cela évite de lier votre code trop étroitement à une méthode particulière de stockage des données.</span><span class="sxs-lookup"><span data-stu-id="6282b-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="6282b-120">Il le fait en générant des requêtes et des commandes SQL pour vous, ce qui vous évite d’avoir à les écrire vous-même.</span><span class="sxs-lookup"><span data-stu-id="6282b-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="6282b-121">Mais, dans certains scénarios exceptionnels, vous devez exécuter des requêtes SQL spécifiques que vous créez manuellement.</span><span class="sxs-lookup"><span data-stu-id="6282b-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="6282b-122">Pour ces scénarios, l’API Entity Framework Code First comprend des méthodes qui vous permettent de transmettre des commandes SQL directement à la base de données.</span><span class="sxs-lookup"><span data-stu-id="6282b-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="6282b-123">Les options suivantes sont disponibles dans EF Core 1.0 :</span><span class="sxs-lookup"><span data-stu-id="6282b-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="6282b-124">Utilisez la méthode `DbSet.FromSql` pour les requêtes qui renvoient des types d’entités.</span><span class="sxs-lookup"><span data-stu-id="6282b-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="6282b-125">Les objets renvoyés doivent être du type attendu par l’objet `DbSet` et ils sont automatiquement suivis par le contexte de base de données, sauf si vous [désactivez le suivi](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="6282b-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="6282b-126">Utilisez `Database.ExecuteSqlCommand` pour les commandes ne se rapportant pas aux requêtes.</span><span class="sxs-lookup"><span data-stu-id="6282b-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="6282b-127">Si vous avez besoin d’exécuter une requête qui renvoie des types qui ne sont pas des entités, vous pouvez utiliser ADO.NET avec la connexion de base de données fournie par EF.</span><span class="sxs-lookup"><span data-stu-id="6282b-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="6282b-128">Les données renvoyées ne font pas l’objet d’un suivi par le contexte de base de données, même si vous utilisez cette méthode pour récupérer des types d’entités.</span><span class="sxs-lookup"><span data-stu-id="6282b-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="6282b-129">Comme c’est toujours le cas lorsque vous exécutez des commandes SQL dans une application web, vous devez prendre des précautions pour protéger votre site contre des attaques par injection de code SQL.</span><span class="sxs-lookup"><span data-stu-id="6282b-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="6282b-130">Une manière de procéder consiste à utiliser des requêtes paramétrables pour vous assurer que les chaînes soumises par une page web ne peuvent pas être interprétées comme des commandes SQL.</span><span class="sxs-lookup"><span data-stu-id="6282b-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="6282b-131">Dans ce didacticiel, vous utiliserez des requêtes paramétrables lors de l’intégration de l’entrée utilisateur dans une requête.</span><span class="sxs-lookup"><span data-stu-id="6282b-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="6282b-132">Appeler une requête pour retourner des entités</span><span class="sxs-lookup"><span data-stu-id="6282b-132">Call a query to return entities</span></span>

<span data-ttu-id="6282b-133">La classe `DbSet<TEntity>` fournit une méthode que vous pouvez utiliser pour exécuter une requête qui renvoie une entité de type `TEntity`.</span><span class="sxs-lookup"><span data-stu-id="6282b-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="6282b-134">Pour voir comment cela fonctionne vous allez modifier le code dans la méthode `Details` du contrôleur Department.</span><span class="sxs-lookup"><span data-stu-id="6282b-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="6282b-135">Dans *DepartmentsController.cs* , dans la méthode `Details`, remplacez le code qui récupère un service par un appel de méthode `FromSql`, comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="6282b-135">In *DepartmentsController.cs* , in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="6282b-136">Pour vérifier que le nouveau code fonctionne correctement, sélectionnez l’onglet **Departments** , puis **Details** pour l’un des services.</span><span class="sxs-lookup"><span data-stu-id="6282b-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Détails du service](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="6282b-138">Appeler une requête pour retourner d’autres types</span><span class="sxs-lookup"><span data-stu-id="6282b-138">Call a query to return other types</span></span>

<span data-ttu-id="6282b-139">Précédemment, vous avez créé une grille de statistiques des étudiants pour la page About, qui montrait le nombre d’étudiants pour chaque date d’inscription.</span><span class="sxs-lookup"><span data-stu-id="6282b-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="6282b-140">Vous avez obtenu les données à partir du jeu d’entités Students (`_context.Students`) et utilisé LINQ pour projeter les résultats dans une liste d’objets de modèle de vue `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="6282b-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="6282b-141">Supposons que vous voulez écrire le code SQL lui-même plutôt qu’utiliser LINQ.</span><span class="sxs-lookup"><span data-stu-id="6282b-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="6282b-142">Pour ce faire, vous avez besoin d’exécuter une requête SQL qui renvoie autre chose que des objets d’entité.</span><span class="sxs-lookup"><span data-stu-id="6282b-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="6282b-143">Dans EF Core 1.0, une manière de procéder consiste à écrire du code ADO.NET et à obtenir la connexion de base de données à partir d’EF.</span><span class="sxs-lookup"><span data-stu-id="6282b-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="6282b-144">Dans *HomeController.cs* , remplacez la méthode `About` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="6282b-144">In *HomeController.cs* , replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="6282b-145">Ajoutez une instruction using :</span><span class="sxs-lookup"><span data-stu-id="6282b-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="6282b-146">Exécutez l’application et accédez à la page About.</span><span class="sxs-lookup"><span data-stu-id="6282b-146">Run the app and go to the About page.</span></span> <span data-ttu-id="6282b-147">Elle affiche les mêmes données qu’auparavant.</span><span class="sxs-lookup"><span data-stu-id="6282b-147">It displays the same data it did before.</span></span>

![Page About](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="6282b-149">Appeler une requête de mise à jour</span><span class="sxs-lookup"><span data-stu-id="6282b-149">Call an update query</span></span>

<span data-ttu-id="6282b-150">Supposons que les administrateurs de Contoso University veuillent effectuer des modifications globales dans la base de données, comme par exemple modifier le nombre de crédits pour chaque cours.</span><span class="sxs-lookup"><span data-stu-id="6282b-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="6282b-151">Si l’université a un grand nombre de cours, il serait inefficace de les récupérer tous sous forme d’entités et de les modifier individuellement.</span><span class="sxs-lookup"><span data-stu-id="6282b-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="6282b-152">Dans cette section, vous allez implémenter une page web permettant à l’utilisateur de spécifier un facteur selon lequel il convient de modifier le nombre de crédits pour tous les cours, et vous effectuerez la modification en exécutant une instruction SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="6282b-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="6282b-153">La page web ressemblera à l’illustration suivante :</span><span class="sxs-lookup"><span data-stu-id="6282b-153">The web page will look like the following illustration:</span></span>

![Page de mise à jour des crédits de cours](advanced/_static/update-credits.png)

<span data-ttu-id="6282b-155">Dans *CoursesController.cs* , ajoutez les méthodes UpdateCourseCredits pour HttpGet et HttpPost :</span><span class="sxs-lookup"><span data-stu-id="6282b-155">In *CoursesController.cs* , add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="6282b-156">Lorsque le contrôleur traite une demande HttpGet, rien n’est renvoyé dans `ViewData["RowsAffected"]` et la vue affiche une zone de texte vide et un bouton d’envoi, comme indiqué dans l’illustration précédente.</span><span class="sxs-lookup"><span data-stu-id="6282b-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="6282b-157">Lorsque vous cliquez sur le bouton **Update** , la méthode HttpPost est appelée et le multiplicateur a la valeur entrée dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="6282b-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="6282b-158">Le code exécute alors l’instruction SQL qui met à jour les cours et renvoie le nombre de lignes affectées à la vue dans `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="6282b-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="6282b-159">Lorsque la vue obtient une valeur `RowsAffected`, elle affiche le nombre de lignes mis à jour.</span><span class="sxs-lookup"><span data-stu-id="6282b-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="6282b-160">Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Views/Courses* , puis cliquez sur **Ajouter > Nouvel élément** .</span><span class="sxs-lookup"><span data-stu-id="6282b-160">In **Solution Explorer** , right-click the *Views/Courses* folder, and then click **Add > New Item** .</span></span>

<span data-ttu-id="6282b-161">Dans la boîte de dialogue **Ajouter un nouvel élément** , cliquez sur **ASP.net Core** sous **installé** dans le volet gauche, cliquez sur **Razor Afficher** , puis nommez la nouvelle vue *UpdateCourseCredits. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="6282b-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View** , and name the new view *UpdateCourseCredits.cshtml* .</span></span>

<span data-ttu-id="6282b-162">Dans *Views/Courses/UpdateCourseCredits.cshtml* , remplacez le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="6282b-162">In *Views/Courses/UpdateCourseCredits.cshtml* , replace the template code with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="6282b-163">Exécutez la méthode `UpdateCourseCredits` en sélectionnant l’onglet **Courses** , puis en ajoutant « /UpdateCourseCredits » à la fin de l’URL dans la barre d’adresse du navigateur (par exemple : `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="6282b-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="6282b-164">Entrez un nombre dans la zone de texte :</span><span class="sxs-lookup"><span data-stu-id="6282b-164">Enter a number in the text box:</span></span>

![Page de mise à jour des crédits de cours](advanced/_static/update-credits.png)

<span data-ttu-id="6282b-166">Cliquez sur **Update** .</span><span class="sxs-lookup"><span data-stu-id="6282b-166">Click **Update** .</span></span> <span data-ttu-id="6282b-167">Vous voyez le nombre de lignes affectées :</span><span class="sxs-lookup"><span data-stu-id="6282b-167">You see the number of rows affected:</span></span>

![Page de mise à jour des crédits de cours – lignes affectées](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="6282b-169">Cliquez sur **Revenir à la liste** pour afficher la liste des cours avec le nombre révisé de crédits.</span><span class="sxs-lookup"><span data-stu-id="6282b-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="6282b-170">Notez que le code de production garantit que les mises à jour fourniront toujours des données valides.</span><span class="sxs-lookup"><span data-stu-id="6282b-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="6282b-171">Le code simplifié indiqué ici peut multiplier le nombre de crédits suffisamment pour générer des nombres supérieurs à 5.</span><span class="sxs-lookup"><span data-stu-id="6282b-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="6282b-172">(La `Credits` propriété a un `[Range(0, 5)]` attribut.) La requête de mise à jour fonctionne, mais les données non valides peuvent entraîner des résultats inattendus dans d’autres parties du système qui supposent que le nombre de crédits est inférieur ou égal à 5.</span><span class="sxs-lookup"><span data-stu-id="6282b-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="6282b-173">Pour plus d’informations sur les requêtes SQL brutes, consultez [Requêtes SQL brutes](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="6282b-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="6282b-174">Examiner les requêtes SQL</span><span class="sxs-lookup"><span data-stu-id="6282b-174">Examine SQL queries</span></span>

<span data-ttu-id="6282b-175">Il est parfois utile de pouvoir voir les requêtes SQL réelles qui sont envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="6282b-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="6282b-176">Les fonctionnalité de journalisation intégrées pour ASP.NET Core sont utilisées automatiquement par EF Core pour écrire des journaux qui contiennent le code SQL pour les requêtes et les mises à jour.</span><span class="sxs-lookup"><span data-stu-id="6282b-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="6282b-177">Dans cette section, vous verrez des exemples de journalisation SQL.</span><span class="sxs-lookup"><span data-stu-id="6282b-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="6282b-178">Ouvrez *StudentsController.cs* et dans la méthode `Details`, définissez un point d’arrêt sur l’instruction `if (student == null)`.</span><span class="sxs-lookup"><span data-stu-id="6282b-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="6282b-179">Exécutez l’application en mode débogage et accédez à la page Details d’un étudiant.</span><span class="sxs-lookup"><span data-stu-id="6282b-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="6282b-180">Accédez à la fenêtre **Output** qui indique la sortie de débogage. Vous voyez la requête :</span><span class="sxs-lookup"><span data-stu-id="6282b-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="6282b-181">Vous pouvez remarquer ici quelque chose susceptible de vous surprendre : l’instruction SQL sélectionne jusqu’à 2 lignes (`TOP(2)`) à partir de la table Person.</span><span class="sxs-lookup"><span data-stu-id="6282b-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="6282b-182">La méthode `SingleOrDefaultAsync` ne se résout pas à 1 ligne sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="6282b-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="6282b-183">Voici pourquoi :</span><span class="sxs-lookup"><span data-stu-id="6282b-183">Here's why:</span></span>

* <span data-ttu-id="6282b-184">Si la requête retourne plusieurs lignes, la méthode retourne la valeur Null.</span><span class="sxs-lookup"><span data-stu-id="6282b-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="6282b-185">Pour déterminer si la requête retourne plusieurs lignes, EF doit vérifier s’il retourne au moins 2.</span><span class="sxs-lookup"><span data-stu-id="6282b-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="6282b-186">Notez que vous n’êtes pas tenu d’utiliser le mode débogage et de vous arrêter à un point d’arrêt pour obtenir la sortie de journalisation dans la fenêtre **Output** .</span><span class="sxs-lookup"><span data-stu-id="6282b-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="6282b-187">C’est simplement un moyen pratique d’arrêter la journalisation au stade où vous voulez examiner la sortie.</span><span class="sxs-lookup"><span data-stu-id="6282b-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="6282b-188">Si vous ne le faites pas, la journalisation se poursuit et vous devez revenir en arrière pour rechercher les parties qui vous intéressent.</span><span class="sxs-lookup"><span data-stu-id="6282b-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="6282b-189">Créer une couche d’abstraction</span><span class="sxs-lookup"><span data-stu-id="6282b-189">Create an abstraction layer</span></span>

<span data-ttu-id="6282b-190">De nombreux développeurs écrivent du code pour implémenter les modèles d’unité de travail et de référentiel comme un wrapper autour du code qui fonctionne avec Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="6282b-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="6282b-191">Ces modèles sont destinés à créer une couche d’abstraction entre la couche d’accès aux données et la couche de logique métier d’une application.</span><span class="sxs-lookup"><span data-stu-id="6282b-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="6282b-192">L’implémentation de ces modèles peut favoriser l’isolation de votre application face à des modifications dans le magasin de données et peut faciliter le test unitaire automatisé ou le développement piloté par les tests (TDD).</span><span class="sxs-lookup"><span data-stu-id="6282b-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="6282b-193">Toutefois, l’écriture de code supplémentaire pour implémenter ces modèles n’est pas toujours le meilleur choix pour les applications qui utilisent EF, et ce pour plusieurs raisons :</span><span class="sxs-lookup"><span data-stu-id="6282b-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="6282b-194">La classe de contexte EF elle-même isole votre code face au code spécifique de magasin de données.</span><span class="sxs-lookup"><span data-stu-id="6282b-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="6282b-195">La classe de contexte EF peut agir comme une classe d’unité de travail pour les mises à jour de base de données que vous effectuez à l’aide d’EF.</span><span class="sxs-lookup"><span data-stu-id="6282b-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="6282b-196">EF inclut des fonctionnalités pour implémenter TDD sans écrire de code de référentiel.</span><span class="sxs-lookup"><span data-stu-id="6282b-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="6282b-197">Pour plus d’informations sur la façon d’implémenter les modèles d’unité de travail et de référentiel, consultez [la version Entity Framework 5 de cette série de didacticiels](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="6282b-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="6282b-198">Entity Framework Core implémente un fournisseur de base de données en mémoire qui peut être utilisé pour les tests.</span><span class="sxs-lookup"><span data-stu-id="6282b-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="6282b-199">Pour plus d’informations, consultez [Tester avec InMemory](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="6282b-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="6282b-200">Détection automatique des modifications</span><span class="sxs-lookup"><span data-stu-id="6282b-200">Automatic change detection</span></span>

<span data-ttu-id="6282b-201">Entity Framework détermine la manière dont une entité a changé (et par conséquent les mises à jour qui doivent être envoyées à la base de données) en comparant les valeurs en cours d’une entité avec les valeurs d’origine.</span><span class="sxs-lookup"><span data-stu-id="6282b-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="6282b-202">Les valeurs d’origine sont stockées lorsque l’entité fait l’objet d’une requête ou d’une jointure.</span><span class="sxs-lookup"><span data-stu-id="6282b-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="6282b-203">Certaines des méthodes qui provoquent la détection automatique des modifications sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="6282b-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="6282b-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="6282b-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="6282b-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="6282b-205">DbContext.Entry</span></span>

* <span data-ttu-id="6282b-206">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="6282b-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="6282b-207">Si vous effectuez le suivi d’un grand nombre d’entités et que vous appelez l’une de ces méthodes de nombreuses fois dans une boucle, vous pouvez obtenir des améliorations significatives des performances en désactivant temporairement la détection automatique des modifications à l’aide de la propriété `ChangeTracker.AutoDetectChangesEnabled`.</span><span class="sxs-lookup"><span data-stu-id="6282b-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="6282b-208">Exemple :</span><span class="sxs-lookup"><span data-stu-id="6282b-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="6282b-209">Code source et plans de développement EF Core</span><span class="sxs-lookup"><span data-stu-id="6282b-209">EF Core source code and development plans</span></span>

<span data-ttu-id="6282b-210">La source de Entity Framework Core est [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) .</span><span class="sxs-lookup"><span data-stu-id="6282b-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="6282b-211">Le dépôt EF Core contient les builds nocturnes, le suivi des problèmes, les spécifications des fonctionnalités, les notes des réunions de conception et [la feuille de route de développement futur](https://github.com/dotnet/efcore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="6282b-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="6282b-212">Vous pouvez signaler ou rechercher des bogues, et apporter votre contribution.</span><span class="sxs-lookup"><span data-stu-id="6282b-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="6282b-213">Bien que le code source soit ouvert, Entity Framework Core est entièrement pris en charge comme produit Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6282b-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="6282b-214">L’équipe Microsoft Entity Framework garde le contrôle sur le choix des contributions qui sont acceptées et teste toutes les modifications du code pour garantir la qualité de chaque version.</span><span class="sxs-lookup"><span data-stu-id="6282b-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="6282b-215">Ingénierie à rebours à partir de la base de données existante</span><span class="sxs-lookup"><span data-stu-id="6282b-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="6282b-216">Pour rétroconcevoir un modèle de données comprenant des classes d’entité issues d’une base de données existante, utilisez la commande [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="6282b-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="6282b-217">Consultez le [didacticiel de prise en main](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="6282b-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="6282b-218">Utiliser du code dynamique LINQ pour simplifier le code</span><span class="sxs-lookup"><span data-stu-id="6282b-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="6282b-219">Le [troisième didacticiel de cette série](sort-filter-page.md) montre comment écrire du code LINQ en codant en dur les noms des colonnes dans une instruction `switch`.</span><span class="sxs-lookup"><span data-stu-id="6282b-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="6282b-220">Avec deux colonnes sélectionnables, cela fonctionne correctement, mais si vous avez de nombreuses colonnes, le code peut devenir très détaillé.</span><span class="sxs-lookup"><span data-stu-id="6282b-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="6282b-221">Pour résoudre ce problème, vous pouvez utiliser la méthode `EF.Property` pour spécifier le nom de la propriété sous forme de chaîne.</span><span class="sxs-lookup"><span data-stu-id="6282b-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="6282b-222">Pour tester cette approche, remplacez la méthode `Index` dans `StudentsController` par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="6282b-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="6282b-223">Remerciements</span><span class="sxs-lookup"><span data-stu-id="6282b-223">Acknowledgments</span></span>

<span data-ttu-id="6282b-224">Tom Dykstra et Rick Anderson (twitter @RickAndMSFT) ont rédigé ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="6282b-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="6282b-225">Rowan Miller, Diego Vega et d’autres membres de l’équipe Entity Framework ont participé à la revue du code et ont aidé à résoudre les problèmes qui se sont posés lorsque nous avons écrit le code pour ces didacticiels.</span><span class="sxs-lookup"><span data-stu-id="6282b-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="6282b-226">John Parente et Paul Goldman ont travaillé sur la mise à jour de ce tutoriel pour ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="6282b-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="6282b-227">Résolution des erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="6282b-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="6282b-228">ContosoUniversity.dll est utilisé par un autre processus</span><span class="sxs-lookup"><span data-stu-id="6282b-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="6282b-229">Message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="6282b-229">Error message:</span></span>

> <span data-ttu-id="6282b-230">Impossible d’ouvrir ’...bin\Debug\netcoreapp1.0\ContosoUniversity.dll’ en écriture -- ’Le processus ne peut pas accéder au fichier ’...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll’, car il est en cours d’utilisation par un autre processus.</span><span class="sxs-lookup"><span data-stu-id="6282b-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="6282b-231">Solution :</span><span class="sxs-lookup"><span data-stu-id="6282b-231">Solution:</span></span>

<span data-ttu-id="6282b-232">Arrêtez le site dans IIS Express.</span><span class="sxs-lookup"><span data-stu-id="6282b-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="6282b-233">Accédez à la barre d’état système de Windows, recherchez IIS Express, cliquez avec le bouton droit sur son icône, sélectionnez le site Contoso University, puis cliquez sur **Arrêter le site** .</span><span class="sxs-lookup"><span data-stu-id="6282b-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site** .</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="6282b-234">Migration structurée sans code dans les méthodes Up et Down</span><span class="sxs-lookup"><span data-stu-id="6282b-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="6282b-235">Cause probable :</span><span class="sxs-lookup"><span data-stu-id="6282b-235">Possible cause:</span></span>

<span data-ttu-id="6282b-236">Les commandes CLI d’EF ne ferment et n’enregistrent pas automatiquement des fichiers de code.</span><span class="sxs-lookup"><span data-stu-id="6282b-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="6282b-237">Si vous avez des modifications non enregistrées lorsque vous exécutez la commande `migrations add`, EF ne trouve pas vos modifications.</span><span class="sxs-lookup"><span data-stu-id="6282b-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="6282b-238">Solution :</span><span class="sxs-lookup"><span data-stu-id="6282b-238">Solution:</span></span>

<span data-ttu-id="6282b-239">Exécutez la commande `migrations remove`, enregistrez vos modifications de code et réexécutez la commande `migrations add`.</span><span class="sxs-lookup"><span data-stu-id="6282b-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="6282b-240">Erreurs lors de l’exécution de la mise à jour de base de données</span><span class="sxs-lookup"><span data-stu-id="6282b-240">Errors while running database update</span></span>

<span data-ttu-id="6282b-241">Vous pouvez obtenir d’autres erreurs en apportant des modifications au schéma dans une base de données qui comporte déjà des données.</span><span class="sxs-lookup"><span data-stu-id="6282b-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="6282b-242">Si vous obtenez des erreurs de migration que vous ne pouvez pas résoudre, vous pouvez changer le nom de la base de données dans la chaîne de connexion ou supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="6282b-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="6282b-243">Avec une nouvelle base de données, il n’y a pas de données à migrer et la commande de mise à jour de base de données a beaucoup plus de chances de s’exécuter sans erreur.</span><span class="sxs-lookup"><span data-stu-id="6282b-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="6282b-244">L’approche la plus simple consiste à renommer la base de données dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6282b-244">The simplest approach is to rename the database in *appsettings.json* .</span></span> <span data-ttu-id="6282b-245">La prochaine fois que vous exécuterez `database update`, une nouvelle base de données sera créée.</span><span class="sxs-lookup"><span data-stu-id="6282b-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="6282b-246">Pour supprimer une base de données dans SSOX, cliquez avec le bouton droit sur la base de données, cliquez sur **Supprimer** , puis, dans la boîte de dialogue **Supprimer la base de données** , sélectionnez **Fermer les connexions existantes** et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="6282b-246">To delete a database in SSOX, right-click the database, click **Delete** , and then in the **Delete Database** dialog box select **Close existing connections** and click **OK** .</span></span>

<span data-ttu-id="6282b-247">Pour supprimer une base de données à l’aide de l’interface CLI, exécutez la commande CLI `database drop` :</span><span class="sxs-lookup"><span data-stu-id="6282b-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="6282b-248">Erreur lors de la localisation de l’instance SQL Server</span><span class="sxs-lookup"><span data-stu-id="6282b-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="6282b-249">Message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="6282b-249">Error Message:</span></span>

> <span data-ttu-id="6282b-250">Une erreur liée au réseau ou propre à une instance s’est produite lors de l’établissement d’une connexion à SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6282b-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="6282b-251">Le serveur est introuvable ou inaccessible.</span><span class="sxs-lookup"><span data-stu-id="6282b-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="6282b-252">Vérifiez que le nom de l’instance est correct et que SQL Server est configuré pour autoriser les connexions à distance.</span><span class="sxs-lookup"><span data-stu-id="6282b-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="6282b-253">(fournisseur : interfaces réseau SQL, erreur : 26 - Erreur lors de la localisation du serveur/de l’instance spécifiés)</span><span class="sxs-lookup"><span data-stu-id="6282b-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="6282b-254">Solution :</span><span class="sxs-lookup"><span data-stu-id="6282b-254">Solution:</span></span>

<span data-ttu-id="6282b-255">Vérifiez la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="6282b-255">Check the connection string.</span></span> <span data-ttu-id="6282b-256">Si vous avez supprimé manuellement le fichier de base de données, modifiez le nom de la base de données dans la chaîne de construction pour recommencer avec une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="6282b-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="6282b-257">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="6282b-257">Get the code</span></span>

[<span data-ttu-id="6282b-258">Télécharger ou afficher l’application complète.</span><span class="sxs-lookup"><span data-stu-id="6282b-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="6282b-259">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6282b-259">Additional resources</span></span>

<span data-ttu-id="6282b-260">Pour plus d’informations sur EF Core, consultez la [documentation sur Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="6282b-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="6282b-261">Un ouvrage est également disponible : [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="6282b-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="6282b-262">Pour plus d’informations sur le déploiement d’une application web, consultez <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="6282b-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="6282b-263">Pour plus d’informations sur les autres rubriques associées à ASP.NET Core MVC, par exemple l’authentification et l’autorisation, consultez <xref:index>.</span><span class="sxs-lookup"><span data-stu-id="6282b-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6282b-264">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="6282b-264">Next steps</span></span>

<span data-ttu-id="6282b-265">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="6282b-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6282b-266">Exécuter des requêtes SQL brutes</span><span class="sxs-lookup"><span data-stu-id="6282b-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="6282b-267">Appeler une requête pour retourner des entités</span><span class="sxs-lookup"><span data-stu-id="6282b-267">Called a query to return entities</span></span>
> * <span data-ttu-id="6282b-268">Appeler une requête pour retourner d’autres types</span><span class="sxs-lookup"><span data-stu-id="6282b-268">Called a query to return other types</span></span>
> * <span data-ttu-id="6282b-269">Appeler une requête de mise à jour</span><span class="sxs-lookup"><span data-stu-id="6282b-269">Called an update query</span></span>
> * <span data-ttu-id="6282b-270">Examiner les requêtes SQL</span><span class="sxs-lookup"><span data-stu-id="6282b-270">Examined SQL queries</span></span>
> * <span data-ttu-id="6282b-271">Créer une couche d’abstraction</span><span class="sxs-lookup"><span data-stu-id="6282b-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="6282b-272">En savoir plus sur la détection automatique des modifications</span><span class="sxs-lookup"><span data-stu-id="6282b-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="6282b-273">En savoir plus sur le code source et les plans de développement EF Core</span><span class="sxs-lookup"><span data-stu-id="6282b-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="6282b-274">Utiliser du code dynamique LINQ pour simplifier le code</span><span class="sxs-lookup"><span data-stu-id="6282b-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="6282b-275">Cette étape termine cette série de tutoriels sur l’utilisation d’Entity Framework Core dans une application ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="6282b-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="6282b-276">Cette série a fait appel à une nouvelle base de données ; une alternative consiste à rétroconcevoir un modèle à partir d’une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="6282b-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6282b-277">Didacticiel : EF Core avec MVC, base de données existante</span><span class="sxs-lookup"><span data-stu-id="6282b-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
