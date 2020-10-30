---
title: 'Didacticiel : en savoir plus sur les scénarios avancés-ASP.NET MVC avec EF Core'
description: Ce tutoriel présente plusieurs rubriques pratiques pour aller au-delà des principes de base du développement d’applications web ASP.NET Core qui utilisent Entity Framework Core.
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 386be395399bf4131e4b6c8cac8221f994e8b7c5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054383"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Didacticiel : en savoir plus sur les scénarios avancés-ASP.NET MVC avec EF Core

Dans le didacticiel précédent, vous avez implémenté l’héritage TPH (table par hiérarchie). Ce didacticiel présente plusieurs rubriques qu’il est utile de connaître lorsque vous allez au-delà des principes de base du développement d’applications web ASP.NET Core qui utilisent Entity Framework Core.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Exécuter des requêtes SQL brutes
> * Appeler une requête pour retourner des entités
> * Appeler une requête pour retourner d’autres types
> * Appeler une requête de mise à jour
> * Examiner les requêtes SQL
> * Créer une couche d’abstraction
> * En savoir plus sur la Détection automatique des modifications
> * En savoir plus sur le code source et les plans de développement EF Core
> * Apprendre à utiliser du code dynamique LINQ pour simplifier le code

## <a name="prerequisites"></a>Prérequis

* [Implémenter l’héritage](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Exécuter des requêtes SQL brutes

L’un des avantages d’utiliser Entity Framework est que cela évite de lier votre code trop étroitement à une méthode particulière de stockage des données. Il le fait en générant des requêtes et des commandes SQL pour vous, ce qui vous évite d’avoir à les écrire vous-même. Mais, dans certains scénarios exceptionnels, vous devez exécuter des requêtes SQL spécifiques que vous créez manuellement. Pour ces scénarios, l’API Entity Framework Code First comprend des méthodes qui vous permettent de transmettre des commandes SQL directement à la base de données. Les options suivantes sont disponibles dans EF Core 1.0 :

* Utilisez la méthode `DbSet.FromSql` pour les requêtes qui renvoient des types d’entités. Les objets renvoyés doivent être du type attendu par l’objet `DbSet` et ils sont automatiquement suivis par le contexte de base de données, sauf si vous [désactivez le suivi](crud.md#no-tracking-queries).

* Utilisez `Database.ExecuteSqlCommand` pour les commandes ne se rapportant pas aux requêtes.

Si vous avez besoin d’exécuter une requête qui renvoie des types qui ne sont pas des entités, vous pouvez utiliser ADO.NET avec la connexion de base de données fournie par EF. Les données renvoyées ne font pas l’objet d’un suivi par le contexte de base de données, même si vous utilisez cette méthode pour récupérer des types d’entités.

Comme c’est toujours le cas lorsque vous exécutez des commandes SQL dans une application web, vous devez prendre des précautions pour protéger votre site contre des attaques par injection de code SQL. Une manière de procéder consiste à utiliser des requêtes paramétrables pour vous assurer que les chaînes soumises par une page web ne peuvent pas être interprétées comme des commandes SQL. Dans ce didacticiel, vous utiliserez des requêtes paramétrables lors de l’intégration de l’entrée utilisateur dans une requête.

## <a name="call-a-query-to-return-entities"></a>Appeler une requête pour retourner des entités

La classe `DbSet<TEntity>` fournit une méthode que vous pouvez utiliser pour exécuter une requête qui renvoie une entité de type `TEntity`. Pour voir comment cela fonctionne vous allez modifier le code dans la méthode `Details` du contrôleur Department.

Dans *DepartmentsController.cs* , dans la méthode `Details`, remplacez le code qui récupère un service par un appel de méthode `FromSql`, comme indiqué dans le code en surbrillance suivant :

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Pour vérifier que le nouveau code fonctionne correctement, sélectionnez l’onglet **Departments** , puis **Details** pour l’un des services.

![Détails du service](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Appeler une requête pour retourner d’autres types

Précédemment, vous avez créé une grille de statistiques des étudiants pour la page About, qui montrait le nombre d’étudiants pour chaque date d’inscription. Vous avez obtenu les données à partir du jeu d’entités Students (`_context.Students`) et utilisé LINQ pour projeter les résultats dans une liste d’objets de modèle de vue `EnrollmentDateGroup`. Supposons que vous voulez écrire le code SQL lui-même plutôt qu’utiliser LINQ. Pour ce faire, vous avez besoin d’exécuter une requête SQL qui renvoie autre chose que des objets d’entité. Dans EF Core 1.0, une manière de procéder consiste à écrire du code ADO.NET et à obtenir la connexion de base de données à partir d’EF.

Dans *HomeController.cs* , remplacez la méthode `About` par le code suivant :

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Ajoutez une instruction using :

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Exécutez l’application et accédez à la page About. Elle affiche les mêmes données qu’auparavant.

![Page About](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Appeler une requête de mise à jour

Supposons que les administrateurs de Contoso University veuillent effectuer des modifications globales dans la base de données, comme par exemple modifier le nombre de crédits pour chaque cours. Si l’université a un grand nombre de cours, il serait inefficace de les récupérer tous sous forme d’entités et de les modifier individuellement. Dans cette section, vous allez implémenter une page web permettant à l’utilisateur de spécifier un facteur selon lequel il convient de modifier le nombre de crédits pour tous les cours, et vous effectuerez la modification en exécutant une instruction SQL UPDATE. La page web ressemblera à l’illustration suivante :

![Page de mise à jour des crédits de cours](advanced/_static/update-credits.png)

Dans *CoursesController.cs* , ajoutez les méthodes UpdateCourseCredits pour HttpGet et HttpPost :

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Lorsque le contrôleur traite une demande HttpGet, rien n’est renvoyé dans `ViewData["RowsAffected"]` et la vue affiche une zone de texte vide et un bouton d’envoi, comme indiqué dans l’illustration précédente.

Lorsque vous cliquez sur le bouton **Update** , la méthode HttpPost est appelée et le multiplicateur a la valeur entrée dans la zone de texte. Le code exécute alors l’instruction SQL qui met à jour les cours et renvoie le nombre de lignes affectées à la vue dans `ViewData`. Lorsque la vue obtient une valeur `RowsAffected`, elle affiche le nombre de lignes mis à jour.

Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Views/Courses* , puis cliquez sur **Ajouter > Nouvel élément** .

Dans la boîte de dialogue **Ajouter un nouvel élément** , cliquez sur **ASP.net Core** sous **installé** dans le volet gauche, cliquez sur **Razor Afficher** , puis nommez la nouvelle vue *UpdateCourseCredits. cshtml* .

Dans *Views/Courses/UpdateCourseCredits.cshtml* , remplacez le code du modèle par le code suivant :

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Exécutez la méthode `UpdateCourseCredits` en sélectionnant l’onglet **Courses** , puis en ajoutant « /UpdateCourseCredits » à la fin de l’URL dans la barre d’adresse du navigateur (par exemple : `http://localhost:5813/Courses/UpdateCourseCredits`). Entrez un nombre dans la zone de texte :

![Page de mise à jour des crédits de cours](advanced/_static/update-credits.png)

Cliquez sur **Update** . Vous voyez le nombre de lignes affectées :

![Page de mise à jour des crédits de cours – lignes affectées](advanced/_static/update-credits-rows-affected.png)

Cliquez sur **Revenir à la liste** pour afficher la liste des cours avec le nombre révisé de crédits.

Notez que le code de production garantit que les mises à jour fourniront toujours des données valides. Le code simplifié indiqué ici peut multiplier le nombre de crédits suffisamment pour générer des nombres supérieurs à 5. (La `Credits` propriété a un `[Range(0, 5)]` attribut.) La requête de mise à jour fonctionne, mais les données non valides peuvent entraîner des résultats inattendus dans d’autres parties du système qui supposent que le nombre de crédits est inférieur ou égal à 5.

Pour plus d’informations sur les requêtes SQL brutes, consultez [Requêtes SQL brutes](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Examiner les requêtes SQL

Il est parfois utile de pouvoir voir les requêtes SQL réelles qui sont envoyées à la base de données. Les fonctionnalité de journalisation intégrées pour ASP.NET Core sont utilisées automatiquement par EF Core pour écrire des journaux qui contiennent le code SQL pour les requêtes et les mises à jour. Dans cette section, vous verrez des exemples de journalisation SQL.

Ouvrez *StudentsController.cs* et dans la méthode `Details`, définissez un point d’arrêt sur l’instruction `if (student == null)`.

Exécutez l’application en mode débogage et accédez à la page Details d’un étudiant.

Accédez à la fenêtre **Output** qui indique la sortie de débogage. Vous voyez la requête :

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

Vous pouvez remarquer ici quelque chose susceptible de vous surprendre : l’instruction SQL sélectionne jusqu’à 2 lignes (`TOP(2)`) à partir de la table Person. La méthode `SingleOrDefaultAsync` ne se résout pas à 1 ligne sur le serveur. Voici pourquoi :

* Si la requête retourne plusieurs lignes, la méthode retourne la valeur Null.
* Pour déterminer si la requête retourne plusieurs lignes, EF doit vérifier s’il retourne au moins 2.

Notez que vous n’êtes pas tenu d’utiliser le mode débogage et de vous arrêter à un point d’arrêt pour obtenir la sortie de journalisation dans la fenêtre **Output** . C’est simplement un moyen pratique d’arrêter la journalisation au stade où vous voulez examiner la sortie. Si vous ne le faites pas, la journalisation se poursuit et vous devez revenir en arrière pour rechercher les parties qui vous intéressent.

## <a name="create-an-abstraction-layer"></a>Créer une couche d’abstraction

De nombreux développeurs écrivent du code pour implémenter les modèles d’unité de travail et de référentiel comme un wrapper autour du code qui fonctionne avec Entity Framework. Ces modèles sont destinés à créer une couche d’abstraction entre la couche d’accès aux données et la couche de logique métier d’une application. L’implémentation de ces modèles peut favoriser l’isolation de votre application face à des modifications dans le magasin de données et peut faciliter le test unitaire automatisé ou le développement piloté par les tests (TDD). Toutefois, l’écriture de code supplémentaire pour implémenter ces modèles n’est pas toujours le meilleur choix pour les applications qui utilisent EF, et ce pour plusieurs raisons :

* La classe de contexte EF elle-même isole votre code face au code spécifique de magasin de données.

* La classe de contexte EF peut agir comme une classe d’unité de travail pour les mises à jour de base de données que vous effectuez à l’aide d’EF.

* EF inclut des fonctionnalités pour implémenter TDD sans écrire de code de référentiel.

Pour plus d’informations sur la façon d’implémenter les modèles d’unité de travail et de référentiel, consultez [la version Entity Framework 5 de cette série de didacticiels](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implémente un fournisseur de base de données en mémoire qui peut être utilisé pour les tests. Pour plus d’informations, consultez [Tester avec InMemory](/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Détection automatique des modifications

Entity Framework détermine la manière dont une entité a changé (et par conséquent les mises à jour qui doivent être envoyées à la base de données) en comparant les valeurs en cours d’une entité avec les valeurs d’origine. Les valeurs d’origine sont stockées lorsque l’entité fait l’objet d’une requête ou d’une jointure. Certaines des méthodes qui provoquent la détection automatique des modifications sont les suivantes :

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

Si vous effectuez le suivi d’un grand nombre d’entités et que vous appelez l’une de ces méthodes de nombreuses fois dans une boucle, vous pouvez obtenir des améliorations significatives des performances en désactivant temporairement la détection automatique des modifications à l’aide de la propriété `ChangeTracker.AutoDetectChangesEnabled`. Exemple :

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>Code source et plans de développement EF Core

La source de Entity Framework Core est [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) . Le dépôt EF Core contient les builds nocturnes, le suivi des problèmes, les spécifications des fonctionnalités, les notes des réunions de conception et [la feuille de route de développement futur](https://github.com/dotnet/efcore/wiki/Roadmap). Vous pouvez signaler ou rechercher des bogues, et apporter votre contribution.

Bien que le code source soit ouvert, Entity Framework Core est entièrement pris en charge comme produit Microsoft. L’équipe Microsoft Entity Framework garde le contrôle sur le choix des contributions qui sont acceptées et teste toutes les modifications du code pour garantir la qualité de chaque version.

## <a name="reverse-engineer-from-existing-database"></a>Ingénierie à rebours à partir de la base de données existante

Pour rétroconcevoir un modèle de données comprenant des classes d’entité issues d’une base de données existante, utilisez la commande [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext). Consultez le [didacticiel de prise en main](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Utiliser du code dynamique LINQ pour simplifier le code

Le [troisième didacticiel de cette série](sort-filter-page.md) montre comment écrire du code LINQ en codant en dur les noms des colonnes dans une instruction `switch`. Avec deux colonnes sélectionnables, cela fonctionne correctement, mais si vous avez de nombreuses colonnes, le code peut devenir très détaillé. Pour résoudre ce problème, vous pouvez utiliser la méthode `EF.Property` pour spécifier le nom de la propriété sous forme de chaîne. Pour tester cette approche, remplacez la méthode `Index` dans `StudentsController` par le code suivant.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Remerciements

Tom Dykstra et Rick Anderson (twitter @RickAndMSFT) ont rédigé ce didacticiel. Rowan Miller, Diego Vega et d’autres membres de l’équipe Entity Framework ont participé à la revue du code et ont aidé à résoudre les problèmes qui se sont posés lorsque nous avons écrit le code pour ces didacticiels. John Parente et Paul Goldman ont travaillé sur la mise à jour de ce tutoriel pour ASP.NET Core 2.2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Résolution des erreurs courantes

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll est utilisé par un autre processus

Message d’erreur :

> Impossible d’ouvrir ’...bin\Debug\netcoreapp1.0\ContosoUniversity.dll’ en écriture -- ’Le processus ne peut pas accéder au fichier ’...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll’, car il est en cours d’utilisation par un autre processus.

Solution :

Arrêtez le site dans IIS Express. Accédez à la barre d’état système de Windows, recherchez IIS Express, cliquez avec le bouton droit sur son icône, sélectionnez le site Contoso University, puis cliquez sur **Arrêter le site** .

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Migration structurée sans code dans les méthodes Up et Down

Cause probable :

Les commandes CLI d’EF ne ferment et n’enregistrent pas automatiquement des fichiers de code. Si vous avez des modifications non enregistrées lorsque vous exécutez la commande `migrations add`, EF ne trouve pas vos modifications.

Solution :

Exécutez la commande `migrations remove`, enregistrez vos modifications de code et réexécutez la commande `migrations add`.

### <a name="errors-while-running-database-update"></a>Erreurs lors de l’exécution de la mise à jour de base de données

Vous pouvez obtenir d’autres erreurs en apportant des modifications au schéma dans une base de données qui comporte déjà des données. Si vous obtenez des erreurs de migration que vous ne pouvez pas résoudre, vous pouvez changer le nom de la base de données dans la chaîne de connexion ou supprimer la base de données. Avec une nouvelle base de données, il n’y a pas de données à migrer et la commande de mise à jour de base de données a beaucoup plus de chances de s’exécuter sans erreur.

L’approche la plus simple consiste à renommer la base de données dans *appsettings.json* . La prochaine fois que vous exécuterez `database update`, une nouvelle base de données sera créée.

Pour supprimer une base de données dans SSOX, cliquez avec le bouton droit sur la base de données, cliquez sur **Supprimer** , puis, dans la boîte de dialogue **Supprimer la base de données** , sélectionnez **Fermer les connexions existantes** et cliquez sur **OK** .

Pour supprimer une base de données à l’aide de l’interface CLI, exécutez la commande CLI `database drop` :

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Erreur lors de la localisation de l’instance SQL Server

Message d’erreur :

> Une erreur liée au réseau ou propre à une instance s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou inaccessible. Vérifiez que le nom de l’instance est correct et que SQL Server est configuré pour autoriser les connexions à distance. (fournisseur : interfaces réseau SQL, erreur : 26 - Erreur lors de la localisation du serveur/de l’instance spécifiés)

Solution :

Vérifiez la chaîne de connexion. Si vous avez supprimé manuellement le fichier de base de données, modifiez le nom de la base de données dans la chaîne de construction pour recommencer avec une nouvelle base de données.

## <a name="get-the-code"></a>Obtenir le code

[Télécharger ou afficher l’application complète.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations sur EF Core, consultez la [documentation sur Entity Framework Core](/ef/core). Un ouvrage est également disponible : [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).

Pour plus d’informations sur le déploiement d’une application web, consultez <xref:host-and-deploy/index>.

Pour plus d’informations sur les autres rubriques associées à ASP.NET Core MVC, par exemple l’authentification et l’autorisation, consultez <xref:index>.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Exécuter des requêtes SQL brutes
> * Appeler une requête pour retourner des entités
> * Appeler une requête pour retourner d’autres types
> * Appeler une requête de mise à jour
> * Examiner les requêtes SQL
> * Créer une couche d’abstraction
> * En savoir plus sur la détection automatique des modifications
> * En savoir plus sur le code source et les plans de développement EF Core
> * Utiliser du code dynamique LINQ pour simplifier le code

Cette étape termine cette série de tutoriels sur l’utilisation d’Entity Framework Core dans une application ASP.NET Core MVC. Cette série a fait appel à une nouvelle base de données ; une alternative consiste à rétroconcevoir un modèle à partir d’une base de données existante.

> [!div class="nextstepaction"]
> [Didacticiel : EF Core avec MVC, base de données existante](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
