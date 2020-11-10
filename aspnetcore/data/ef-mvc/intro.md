---
title: 'Didacticiel : prise en main de EF Core dans une application Web MVC ASP.NET'
description: Cette page est la première d’une série de didacticiels qui expliquent comment créer l’exemple d’application EF/MVC de Contoso University.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: data/ef-mvc/intro
ms.openlocfilehash: a815502bb8aa97c137ea8940c7e5f1dde79e9999
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430885"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Didacticiel : prise en main de EF Core dans une application Web MVC ASP.NET

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

L’exemple de code d’accès Web de l’Université contoso montre comment créer une ASP.NET Core application Web MVC à l’aide d’Entity Framework (EF) Core et de Visual Studio.

L’exemple d’application est un site web pour une université Contoso fictive. Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs. Il s’agit de la première d’une série de didacticiels qui expliquent comment créer l’exemple d’application Contoso University.

## <a name="prerequisites"></a>Prérequis

* Si vous débutez avec ASP.NET Core MVC, suivez la série de didacticiels [prise en main d’ASP.net Core MVC](xref:tutorials/first-mvc-app/start-mvc) avant de commencer celle-ci.

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a>Moteurs de base de données

Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a>Résoudre les problèmes et résoudre les problèmes

Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples). Pour obtenir la liste des erreurs courantes et comment les résoudre, consultez [la section Dépannage du dernier didacticiel de la série](advanced.md#common-errors). Si vous n’y trouvez pas ce dont vous avez besoin, vous pouvez publier une question sur StackOverflow.com pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Il s’agit d’une série de 10 didacticiels, dont chacun s’appuie sur les opérations réalisées dans les précédents. Pensez à enregistrer une copie du projet à la fin de chaque didacticiel réussi. Ainsi, si vous rencontrez des problèmes, vous pouvez recommencer à la fin du didacticiel précédent au lieu de revenir au début de la série entière.

## <a name="contoso-university-web-app"></a>Application web Contoso University

L’application générée dans ces didacticiels est le site web de base d’une université.

Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques-uns des écrans de l’application :

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Créer une application web

1. Démarrez Visual Studio et sélectionnez **Créer un projet**.
1. Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **ASP.net Core application Web** > **suivant**.
1. Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `ContosoUniversity` pour **nom du projet**. Il est important d’utiliser ce nom exact, y compris la mise en majuscules, donc chaque correspond à la `namespace` copie du code.
1. Sélectionnez **Create** (Créer).
1. Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :
    1. **.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.
    1. **ASP.net Core application Web (Model-View-Controller)**.
    1. **Create** 
       Créer ![ Boîte de dialogue Nouveau projet de ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)

## <a name="set-up-the-site-style"></a>Configurer le style du site

Quelques modifications de base configurent le menu site, la disposition et la page d’hébergement.

Ouvrez *Views/Shared/_Layout.cshtml* et apportez les modifications suivantes :

* Remplacez chaque occurrence de `ContosoUniversity` par `Contoso University` . Il y a trois occurrences.
* Ajoutez des entrées de menu pour **About** , **Students** , **Courses** , **Instructors** , et **Departments** , et supprimez l’entrée de menu **Privacy**.

Les modifications précédentes sont mises en surbrillance dans le code suivant :

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

Dans *views/orig/index. cshtml* , remplacez le contenu du fichier par le balisage suivant :

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

Appuyez sur Ctrl+F5 pour exécuter le projet ou choisissez **Déboguer > Exécuter sans débogage** dans le menu. La page d’hébergement s’affiche avec des onglets pour les pages créées dans ce didacticiel.

![Page d’accueil de Contoso University](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a>EF Core les packages NuGet

Ce didacticiel utilise SQL Server et le package de fournisseur est [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).

Le package d’SQL Server EF et ses dépendances, `Microsoft.EntityFrameworkCore` et `Microsoft.EntityFrameworkCore.Relational` fournissent la prise en charge du runtime pour EF.

Ajoutez le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) et le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) . Dans la console du gestionnaire de programmes (PMC), entrez les commandes suivantes pour ajouter les packages NuGet :

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Le `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` package NuGet fournit ASP.net Core intergiciel pour EF Core pages d’erreurs. Cet intergiciel (middleware) permet de détecter et de diagnostiquer les erreurs avec EF Core migrations.

Pour plus d’informations sur les autres fournisseurs de bases de données disponibles pour EF Core, consultez [fournisseurs de bases de données](/ef/core/providers/).

## <a name="create-the-data-model"></a>Créer le modèle de données

Les classes d’entité suivantes sont créées pour cette application :

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Les entités précédentes ont les relations suivantes :

* Relation un-à-plusieurs entre `Student` les `Enrollment` entités et. Un étudiant peut être inscrit dans un nombre quelconque de cours.
* Relation un-à-plusieurs entre `Course` les `Enrollment` entités et. Un cours peut avoir une quantité illimitée d’élèves inscrits.

Dans les sections suivantes, une classe est créée pour chacune de ces entités.

### <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité Student](intro/_static/student-entity.png)

Dans le dossier *Models* , créez la `Student` classe avec le code suivant :

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

La `ID` propriété est la colonne de clé primaire ( **PK** ) de la table de base de données qui correspond à cette classe. Par défaut, EF interprète une propriété nommée `ID` ou `classnameID` comme clé primaire. Par exemple, la clé PK peut être nommée `StudentID` plutôt que `ID` .

La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships). Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité. La `Enrollments` propriété d’une `Student` entité :

* Contient toutes les `Enrollment` entités qui sont associées à cette `Student` entité.
* Si une `Student` ligne spécifique de la base de données a deux `Enrollment` lignes associées :
  * `Student`La propriété de navigation de cette entité `Enrollments` contient ces deux `Enrollment` entités.
  
`Enrollment` les lignes contiennent la valeur PK d’un étudiant dans la `StudentID` colonne de clé étrangère ( **FK** ).

Si une propriété de navigation peut contenir plusieurs entités :

 * Le type doit être une liste, telle que `ICollection<T>` , `List<T>` ou `HashSet<T>` .
 * Les entités peuvent être ajoutées, supprimées et mises à jour.

Les relations de navigation plusieurs-à-plusieurs et un-à-plusieurs peuvent contenir plusieurs entités. Lorsque `ICollection<T>` est utilisé, EF crée une `HashSet<T>` collection par défaut.

### <a name="the-enrollment-entity"></a>L’entité Enrollment

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

Dans le dossier *Models* , créez la `Enrollment` classe avec le code suivant :

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

La `EnrollmentID` propriété est la clé primaire. Cette entité utilise le `classnameID` modèle au lieu de `ID` lui-même. L' `Student` entité a utilisé le `ID` modèle. Certains développeurs préfèrent utiliser un modèle dans le modèle de données. Dans ce didacticiel, la variation illustre que l’un ou l’autre modèle peut être utilisé. Un [didacticiel ultérieur](inheritance.md) montre comment l’utilisation `ID` de sans ClassName simplifie l’implémentation de l’héritage dans le modèle de données.

La propriété `Grade` est un `enum`. Le `?` après la `Grade` déclaration de type indique que la `Grade` propriété a la valeur [null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types). Un grade qui `null` est différent d’un niveau zéro. `null` signifie qu’une note n’est pas connue ou n’a pas encore été affectée.

La `StudentID` propriété est une clé étrangère (FK) et la propriété de navigation correspondante est `Student` . Une `Enrollment` entité est associée à une `Student` entité, donc la propriété ne peut contenir qu’une seule `Student` entité. Cela diffère de la `Student.Enrollments` propriété de navigation, qui peut contenir plusieurs `Enrollment` entités.

La `CourseID` propriété est un FK et la propriété de navigation correspondante est `Course` . Une entité `Enrollment` est associée à une entité `Course`.

Entity Framework interprète une propriété comme une propriété FK si elle porte le nom de la propriété de la clé primaire nom de la propriété de `<` navigation `><` `>` . Par exemple, `StudentID` pour la `Student` propriété de navigation, puisque la `Student` clé primaire de l’entité est `ID` . Les propriétés FK peuvent également être nommées nom de la  `<` propriété de clé primaire `>` . Par exemple, `CourseID` car la `Course` clé primaire de l’entité est `CourseID` .

### <a name="the-course-entity"></a>L’entité Course

![Diagramme de l’entité Course](intro/_static/course-entity.png)

Dans le dossier *Models* , créez la `Course` classe avec le code suivant :

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

La propriété `Enrollments` est une propriété de navigation. Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.

L’attribut [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) est expliqué dans un [didacticiel ultérieur](complex-data-model.md). Cet attribut permet d’entrer le PK du cours plutôt que de le générer.

## <a name="create-the-database-context"></a>Créer le contexte de base de données

La classe principale qui coordonne la fonctionnalité EF pour un modèle de données donné est la <xref:Microsoft.EntityFrameworkCore.DbContext> classe de contexte de base de données. Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`. La `DbContext` classe dérivée spécifie les entités qui sont incluses dans le modèle de données. Certains comportements EF peuvent être personnalisés. Dans ce projet, la classe est nommée `SchoolContext`.

Dans le dossier du projet, créez un dossier nommé `Data` .

Dans le dossier *Data* , créez une `SchoolContext` classe avec le code suivant :

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Le code précédent crée une `DbSet` propriété pour chaque jeu d’entités. Dans la terminologie EF :

* Un jeu d’entités correspond généralement à une table de base de données.
* Une entité correspond à une ligne dans la table.

Les `DbSet<Enrollment>` `DbSet<Course>` instructions et peuvent être omises et elles fonctionnent de la même façon. EF les inclurait implicitement pour les raisons suivantes :

* L' `Student` entité référence l' `Enrollment` entité.
* L' `Enrollment` entité référence l' `Course` entité.

Quand la base de données est créée, EF crée des tables dont les noms sont identiques aux noms de propriété `DbSet`. Les noms de propriété pour les collections sont généralement au pluriel. Par exemple, `Students` plutôt que `Student` . Les développeurs ne sont pas tous d’accord sur la nécessité d’utiliser des noms de table au pluriel. Pour ces didacticiels, le comportement par défaut est remplacé en spécifiant des noms de tables singuliers dans le `DbContext` . Pour ce faire, ajoutez le code en surbrillance suivant après la dernière propriété DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Inscrire la `SchoolContext`

ASP.NET Core comprend [l’injection de dépendances](../../fundamentals/dependency-injection.md). Les services, tels que le contexte de base de données EF, sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services, tels que les contrôleurs MVC, par le biais de paramètres de constructeur. Le code du constructeur de contrôleur qui obtient une instance de contexte est indiqué plus loin dans ce didacticiel.

Pour inscrire `SchoolContext` en tant que service, ouvrez *Startup.cs* et ajoutez les lignes en surbrillance à la méthode `ConfigureServices`.

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet `DbContextOptionsBuilder`. Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

Ouvrez le *appsettings.json* fichier et ajoutez une chaîne de connexion comme indiqué dans le balisage suivant :

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a>Ajouter le filtre d’exception de base de données

Ajoutez <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> à `ConfigureServices` , comme indiqué dans le code suivant :

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

Le `AddDatabaseDeveloperPageExceptionFilter` fournit des informations d’erreur utiles dans l' [environnement de développement](xref:fundamentals/environments).

### <a name="sql-server-express-localdb"></a>Base de données locale SQL Server Express

La chaîne de connexion spécifie [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.

## <a name="initialize-db-with-test-data"></a>Initialiser la base de données avec des données de test

EF crée une base de données vide. Dans cette section, vous ajoutez une méthode appelée après la création de la base de données afin de la remplir avec des données de test.

La `EnsureCreated` méthode est utilisée pour créer automatiquement la base de données. Dans un [didacticiel ultérieur](migrations.md), vous verrez comment gérer les modifications de modèle à l’aide de migrations code First pour modifier le schéma de base de données au lieu de supprimer et de recréer la base de données.

Dans le dossier *Data* , créez une nouvelle classe nommée `DbInitializer` avec le code suivant :

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

Le code précédent vérifie si la base de données existe :

* Si la base de données est introuvable ;
  * Il est créé et chargé avec les données de test. Il charge les données de test dans des tableaux plutôt que des collections `List<T>` afin d’optimiser les performances.
* Si la base de données est trouvée, elle n’effectue aucune action.

Mettez à jour *Program.cs* avec le code suivant :

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

*Program.cs* effectue les opérations suivantes au démarrage de l’application :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.
* Appelez la méthode `DbInitializer.Initialize` .
* Supprimez le contexte lorsque la `Initialize` méthode se termine, comme indiqué dans le code suivant :

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

La première fois que l’application est exécutée, la base de données est créée et chargée avec les données de test. Chaque fois que le modèle de données change :

* Supprimez la base de données.
* Mettez à jour la méthode Seed et recommencez avec une nouvelle base de données.

 Dans les didacticiels ultérieurs, la base de données est modifiée lorsque le modèle de données change, sans la supprimer et la recréer. Aucune donnée n’est perdue lorsque le modèle de données change.

## <a name="create-controller-and-views"></a>Créer un contrôleur et des vues

Utilisez le moteur de génération de modèles automatique dans Visual Studio pour ajouter un contrôleur MVC et des vues qui utiliseront EF pour interroger et enregistrer des données.

La création automatique de méthodes et de vues d’action [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) porte le nom de génération de modèles automatique.

* Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le `Controllers` dossier et sélectionnez **Ajouter > nouvel élément de génération de modèles** automatique.
* Dans la boîte de dialogue **Ajouter un modèle automatique** :
  * Sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework**.
  * Cliquez sur **Add**. La boîte de dialogue **Ajouter un contrôleur MVC avec des vues, à l’aide de Entity Framework** s’affiche : ![ structure Student](intro/_static/scaffold-student2.png)
  * Dans **classe de modèle** , sélectionnez **Student**.
  * Dans la **classe de contexte de données** , sélectionnez **SchoolContext**.
  * Acceptez la valeur par défaut **StudentsController** comme nom.
  * Cliquez sur **Add**.

Le moteur de génération de modèles automatique de Visual Studio crée un `StudentsController.cs` fichier et un ensemble de vues ( `*.cshtml` fichiers) qui fonctionnent avec le contrôleur.

Notez que le contrôleur prend un `SchoolContext` comme paramètre de constructeur.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

L’injection de dépendance ASP.NET Core s’occupe de la transmission d’une instance de `SchoolContext` dans le contrôleur. Vous l’avez configurée dans la `Startup` classe.

Le contrôleur contient une méthode d’action `Index`, qui affiche tous les étudiants dans la base de données. La méthode obtient la liste des étudiants du jeu d’entités Students en lisant la propriété `Students` de l’instance de contexte de base de données :

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Les éléments de programmation asynchrone dans ce code sont expliqués plus loin dans ce didacticiel.

La vue *Views/Students/Index.cshtml* affiche cette liste dans une table :

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Appuyez sur Ctrl+F5 pour exécuter le projet ou choisissez **Déboguer > Exécuter sans débogage** dans le menu.

Cliquez sur l’onglet Students pour afficher les données de test que la méthode `DbInitializer.Initialize` a insérées. Selon l’étroitesse de votre fenêtre de navigateur, vous verrez le lien de l’onglet `Students` en haut de la page ou vous devrez cliquer sur l’icône de navigation dans le coin supérieur droit pour afficher le lien.

![Page d’accueil étroite de Contoso University](intro/_static/home-page-narrow.png)

![Page d’index des étudiants](intro/_static/students-index.png)

## <a name="view-the-database"></a>Afficher la base de données

Lorsque l’application est démarrée, la `DbInitializer.Initialize` méthode appelle `EnsureCreated` . EF a vu qu’il n’existait aucune base de données :

* Il a donc créé une base de données.
* Le `Initialize` Code de méthode a rempli la base de données avec des données.

Utilisez **Explorateur d’objets SQL Server** (SSOX) pour afficher la base de données dans Visual Studio :

* Sélectionnez **Explorateur d’objets SQL Server** dans le menu **affichage** de Visual Studio.
* Dans SSOX, sélectionnez **(base de données locale) \MSSQLLocalDB > bases de données**.
* Sélectionnez `ContosoUniversity1` , l’entrée correspondant au nom de la base de données qui se trouve dans la chaîne de connexion dans le *appsettings.json* fichier.
* Développez le nœud **tables** pour afficher les tables dans la base de données.

![Tables dans SSOX](intro/_static/ssox-tables.png)

Cliquez avec le bouton droit sur la table **Student** et cliquez sur **afficher les données** pour afficher les données dans la table.

![Table Student dans SSOX](intro/_static/ssox-student-table.png)

Les `*.mdf` `*.ldf` fichiers de base de données et se trouvent dans le dossier *C:\Users \\ \<username>* .

Étant donné que `EnsureCreated` est appelé dans la méthode d’initialiseur qui s’exécute sur le démarrage de l’application, vous pouvez :

* Apportez une modification à la `Student` classe.
* Supprimez la base de données.
* Arrêtez, puis démarrez l’application. La base de données est automatiquement recréée pour correspondre à la modification.

Par exemple, si une `EmailAddress` propriété est ajoutée à la `Student` classe, une nouvelle `EmailAddress` colonne dans la table recréée. La vue classée n’affiche pas la nouvelle `EmailAddress` propriété.

## <a name="conventions"></a>Conventions

La quantité de code écrite permettant à EF de créer une base de données complète est minime en raison de l’utilisation des conventions EF :

* Les noms des propriétés `DbSet` sont utilisés comme noms de tables. Pour les entités non référencées par une propriété `DbSet`, les noms des classes d’entités sont utilisés comme noms de tables.
* Les noms des propriétés d’entités sont utilisés comme noms de colonnes.
* Les propriétés d’entité nommées `ID` ou `classnameID` sont reconnues comme propriétés PK.
* Une propriété est interprétée comme une propriété FK si elle est nommée nom de la propriété de `<` navigation `><` PK `>` . Par exemple, `StudentID` pour la `Student` propriété de navigation, puisque la `Student` clé primaire de l’entité est `ID` . Les propriétés FK peuvent également être nommées nom de la `<` propriété de clé primaire `>` . Par exemple, `EnrollmentID` puisque la `Enrollment` clé primaire de l’entité est `EnrollmentID` .

Le comportement conventionnel peut être remplacé. Par exemple, les noms de table peuvent être spécifiés explicitement, comme indiqué plus haut dans ce didacticiel. Les noms de colonne et toute propriété peuvent être définis en tant que PK ou FK.

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés. Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés. Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent. Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes. Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.

Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution, mais dans les situations de faible trafic, la baisse de performances est négligeable, alors qu’en cas de trafic élevé, l’amélioration potentielle des performances est importante.

Dans le code suivant,,,, `async` `Task<T>` `await` et `ToListAsync` permettent au code de s’exécuter de façon asynchrone.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Le mot clé `async` indique au compilateur de générer des rappels pour les parties du corps de la méthode et pour créer automatiquement l’objet `Task<IActionResult>` qui est renvoyé.
* Le type de retour `Task<IActionResult>` représente le travail en cours avec un résultat de type `IActionResult`.
* Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties. La première partie se termine par l’opération qui est démarrée de façon asynchrone. La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.
* `ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.

Voici quelques éléments à prendre en compte lors de l’écriture de code asynchrone utilisant EF :

* Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone. Cela inclut, par exemple, `ToListAsync`, `SingleOrDefaultAsync` et `SaveChangesAsync`, mais pas les instructions qui ne font, par exemple, que changer `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexte EF n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle. Lorsque vous appelez une méthode EF asynchrone quelconque, utilisez toujours le mot clé `await`.
* Pour tirer parti des avantages en termes de performances du code asynchrone, assurez-vous que tous les packages de bibliothèque utilisés utilisent également Async s’ils appellent des méthodes EF qui entraînent l’envoi de requêtes à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble du code asynchrone](/dotnet/articles/standard/async).

## <a name="limit-entities-fetched"></a>Limiter les entités extraites

Pour plus d’informations sur la limitation du nombre ou des entités retournées à partir d’une requête, consultez Considérations sur les [performances](xref:data/ef-rp/intro) .

Passez au tutoriel suivant pour découvrir comment effectuer des opérations CRUD de base (créer, lire, mettre à jour, supprimer).

> [!div class="nextstepaction"]
> [Implémenter la fonctionnalité CRUD de base](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET Core 2.2 MVC à l’aide d’Entity Framework (EF) Core 2.2 et de Visual Studio 2017 ou 2019.

Ce didacticiel n’a pas été mis à jour pour ASP.NET Core 3,1. Il a été mis à jour pour [ASP.NET Core 5,0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).

L’exemple d’application est un site web pour une université Contoso fictive. Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs. Ce document est le premier d’une série de didacticiels qui expliquent comment générer à partir de zéro l’exemple d’application Contoso University.

## <a name="prerequisites"></a>Prérequis

* [Kit SDK .NET Core 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec les charges de travail suivantes :
  * Charge de travail **Développement web et ASP.NET**
  * Charge de travail **Développement multiplateforme .NET Core**

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples). Pour obtenir la liste des erreurs courantes et comment les résoudre, consultez [la section Dépannage du dernier didacticiel de la série](advanced.md#common-errors). Si vous n’y trouvez pas ce dont vous avez besoin, vous pouvez publier une question sur StackOverflow.com pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Il s’agit d’une série de 10 didacticiels, dont chacun s’appuie sur les opérations réalisées dans les précédents. Pensez à enregistrer une copie du projet à la fin de chaque didacticiel réussi. Ainsi, si vous rencontrez des problèmes, vous pouvez recommencer à la fin du didacticiel précédent au lieu de revenir au début de la série entière.

## <a name="contoso-university-web-app"></a>Application web Contoso University

L’application que vous allez générer dans ces didacticiels est un site web simple d’université.

Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques écrans que vous allez créer.

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Créer une application web

* Ouvrez Visual Studio.

* Dans le menu **Fichier** , sélectionnez **Nouveau > Projet**.

* Dans le volet gauche, sélectionnez **Installé > Visual C# > Web**.

* Sélectionnez le modèle de projet **Application web ASP.NET Core**.

* Entrez **ContosoUniversity** comme nom et cliquez sur **OK**.

  ![Boîte de dialogue Nouveau projet](intro/_static/new-project2.png)

* Patientez jusqu’à l’affichage de la boîte de dialogue **Nouvelle application web ASP.NET Core**.

* Sélectionnez **.NET Core** , **ASP.NET Core 2.2** et le modèle **Application web (Model-View-Controller)**.

* Assurez-vous que **l’authentification** est définie sur **aucune authentification**.

* Sélectionnez **OK**.

  ![Boîte de dialogue Nouveau projet ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Configurer le style du site

Quelques changements simples configureront le menu, la disposition et la page d’accueil du site.

Ouvrez *Views/Shared/_Layout.cshtml* et apportez les modifications suivantes :

* Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ». Il y a trois occurrences.

* Ajoutez des entrées de menu pour **About** , **Students** , **Courses** , **Instructors** , et **Departments** , et supprimez l’entrée de menu **Privacy**.

Les modifications sont mises en surbrillance.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

Dans *Views/Home/Index.cshtml* , remplacez le contenu du fichier par le code suivant, afin de remplacer le texte relatif à ASP.NET et MVC par le texte relatif à cette application :

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Appuyez sur Ctrl+F5 pour exécuter le projet ou choisissez **Déboguer > Exécuter sans débogage** dans le menu. Vous voyez la page d’accueil avec des onglets pour les pages que vous allez créer dans ces didacticiels.

![Page d’accueil de Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>À propos des packages NuGet EF Core

Pour ajouter la prise en charge d’EF Core à un projet, installez le fournisseur de bases de données que vous souhaitez cibler. Ce didacticiel utilise SQL Server et le package de fournisseur est [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Ce package étant inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), vous n’avez pas besoin de le référencer.

Le package EF SQL Server et ses dépendances (`Microsoft.EntityFrameworkCore` et `Microsoft.EntityFrameworkCore.Relational`) fournissent la prise en charge du runtime pour EF. Vous ajouterez un package d’outils ultérieurement, dans le didacticiel [Migrations](migrations.md).

Pour obtenir des informations sur les autres fournisseurs de bases de données qui sont disponibles pour Entity Framework Core, consultez [Fournisseurs de bases de données](/ef/core/providers/).

## <a name="create-the-data-model"></a>Créer le modèle de données

Ensuite, vous allez créer des classes d’entités pour l’application Contoso University. Vous commencerez avec les trois entités suivantes.

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Il existe une relation un-à-plusieurs entre les entités `Student` et `Enrollment`, et une relation un-à-plusieurs entre les entités `Course` et `Enrollment`. En d’autres termes, un étudiant peut être inscrit dans un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.

Dans les sections suivantes, vous allez créer une classe pour chacune de ces entités.

### <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité Student](intro/_static/student-entity.png)

Dans le dossier *Models* , créez un fichier de classe nommé *Student.cs* et remplacez le code du modèle par le code suivant.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, Entity Framework interprète une propriété nommée `ID` ou `classnameID` comme étant la clé primaire.

La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships). Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité. Dans ce cas, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à l’entité `Student`. En d’autres termes, si une `Student` ligne de la base de données a deux `Enrollment` lignes associées (lignes qui contiennent la valeur de clé primaire de cet étudiant dans sa colonne de clé étrangère StudentID), `Student` la propriété de navigation de cette entité `Enrollments` contiendra ces deux `Enrollment` entités.

Si une propriété de navigation peut contenir plusieurs entités (comme dans des relations plusieurs à plusieurs ou un -à-plusieurs), son type doit être une liste dans laquelle les entrées peuvent être ajoutées, supprimées et mises à jour, telle que `ICollection<T>`. Vous pouvez spécifier `ICollection<T>` ou un type tel que `List<T>` ou `HashSet<T>`. Si vous spécifiez `ICollection<T>`, EF crée une collection `HashSet<T>` par défaut.

### <a name="the-enrollment-entity"></a>L’entité Enrollment

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

Dans le dossier *Models* , créez *Enrollment.cs* et remplacez le code existant par le code suivant :

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

La propriété `EnrollmentID` sera la clé primaire. Cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même, comme vous l’avez vu dans l’entité `Student`. En général, vous choisissez un modèle et l’utilisez dans tout votre modèle de données. Ici, la variante illustre que vous pouvez utiliser l’un ou l’autre modèle. Dans un [prochain didacticiel](inheritance.md), vous verrez comment l’utilisation de l’ID sans classname simplifie l’implémentation de l’héritage dans le modèle de données.

La propriété `Grade` est un `enum`. Le point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` est nullable. Une note (Grade) qui a la valeur Null est différente d’une note égale à zéro : la valeur Null signifie qu’une note n’est pas connue ou n’a pas encore été affectée.

La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`. Une entité `Enrollment` est associée à une entité `Student`, donc la propriété peut contenir uniquement une entité `Student` unique (contrairement à la propriété de navigation `Student.Enrollments` que vous avez vue précédemment, qui peut contenir plusieurs entités `Enrollment`).

La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`. Une entité `Enrollment` est associée à une entité `Course`.

Entity Framework interprète une propriété comme une propriété de clé étrangère si elle est nommée `<navigation property name><primary key property name>` (par exemple, `StudentID` pour la propriété de navigation `Student`, puisque la clé primaire de l’entité `Student` est `ID`). Les propriétés de clé étrangère peuvent également être nommées simplement `<primary key property name>` (par exemple, `CourseID`, puisque la clé primaire de l’entité `Course` est `CourseID`).

### <a name="the-course-entity"></a>L’entité Course

![Diagramme de l’entité Course](intro/_static/course-entity.png)

Dans le dossier *Models* , créez *cs* et remplacez le code existant par le code suivant :

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

La propriété `Enrollments` est une propriété de navigation. Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.

Nous fournirons plus de détails sur l’attribut `DatabaseGenerated` dans un [didacticiel ultérieur](complex-data-model.md) de cette série. En fait, cet attribut vous permet d’entrer la clé primaire pour le cours plutôt que de laisser la base de données la générer.

## <a name="create-the-database-context"></a>Créer le contexte de base de données

La classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données spécifié est la classe de contexte de base de données. Vous créez cette classe en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`. Dans votre code, vous spécifiez les entités qui sont incluses dans le modèle de données. Vous pouvez également personnaliser un certain comportement d’Entity Framework. Dans ce projet, la classe est nommée `SchoolContext`.

Dans le dossier du projet, créez un dossier nommé *Data*.

Dans ce dossier *Data* , créez un nouveau fichier de classe nommé *SchoolContext.cs* et remplacez le code du modèle par le code suivant :

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Ce code crée une propriété `DbSet` pour chaque jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.

Vous pourriez avoir omis les instructions `DbSet<Enrollment>` et `DbSet<Course>`, et cela fonctionnerait de la même façon. Entity Framework les inclurait implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`.

Quand la base de données est créée, EF crée des tables dont les noms sont identiques aux noms de propriété `DbSet`. Les noms de propriété pour les collections sont généralement pluriels (Students plutôt que Student), mais les développeurs ne sont pas tous d’accord sur la nécessité d’utiliser des noms de tables au pluriel. Pour ces didacticiels, vous remplacerez le comportement par défaut en spécifiant des noms de tables au singulier dans le contexte DbContext. Pour ce faire, ajoutez le code en surbrillance suivant après la dernière propriété DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

Générez le projet en tant que vérification des erreurs du compilateur.

## <a name="register-the-schoolcontext"></a>Inscrire SchoolContext

ASP.NET Core implémente l’[injection de dépendance](../../fundamentals/dependency-injection.md) par défaut. Des services (tels que le contexte de base de données EF) sont inscrits avec l’injection de dépendance au démarrage de l’application. Ces services sont affectés aux composants qui les nécessitent (tels que les contrôleurs MVC) par le biais de paramètres de constructeur. Vous verrez le code de constructeur de contrôleur qui obtient une instance de contexte plus loin dans ce didacticiel.

Pour inscrire `SchoolContext` en tant que service, ouvrez *Startup.cs* et ajoutez les lignes en surbrillance à la méthode `ConfigureServices`.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet `DbContextOptionsBuilder`. Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

Ajoutez des instructions `using` pour les espaces de noms `ContosoUniversity.Data` et `Microsoft.EntityFrameworkCore`, puis générez le projet.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Ouvrez le *appsettings.json* fichier et ajoutez une chaîne de connexion comme indiqué dans l’exemple suivant.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>Base de données locale SQL Server Express

La chaîne de connexion spécifie une base de données SQL Server LocalDB. LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.

## <a name="initialize-db-with-test-data"></a>Initialiser la base de données avec des données de test

Entity Framework créera une base de données vide pour vous. Dans cette section, vous écrivez une méthode qui est appelée après la création de la base de données pour la remplir avec des données de test.

Là, vous allez utiliser la méthode `EnsureCreated` pour créer automatiquement la base de données. Dans un [didacticiel ultérieur](migrations.md), vous verrez comment traiter les modifications des modèles à l’aide des migrations Code First pour modifier le schéma de base de données au lieu de supprimer et de recréer la base de données.

Dans le dossier *Data* , créez un nouveau fichier de classe nommé *DbInitializer.cs* et remplacez le code de modèle par le code suivant, qui entraîne la création d’une base de données, si nécessaire, et charge les données de test dans la nouvelle base de données.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Le code vérifie si des étudiants figurent dans la base de données et, dans la négative, il suppose que la base de données est nouvelle et doit être initialement peuplée avec des données de test. Il charge les données de test dans des tableaux plutôt que des collections `List<T>` afin d’optimiser les performances.

Dans *Program.cs* , modifiez la méthode `Main` pour effectuer les opérations suivantes au démarrage de l’application :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.
* Appeler la méthode de remplissage initial, en lui transmettant le contexte.
* Supprimer le contexte une fois l’exécution de la méthode de peuplement initial terminée.

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

La première fois que vous exécutez l’application, la base de données est créée et amorcée avec des données de test. Chaque fois que vous modifiez le modèle de données :

 * Supprimez la base de données.
 * Mettez à jour la méthode Seed et recommencez avec une nouvelle base de données de la même façon.
 
Dans les didacticiels suivants, vous verrez comment modifier la base de données quand le modèle de données change, sans supprimer et recréer la base de données.

## <a name="create-controller-and-views"></a>Créer un contrôleur et des vues

Dans cette section, le moteur de génération de modèles automatique de Visual Studio est utilisé pour ajouter un contrôleur MVC et des vues qui utiliseront EF pour interroger et enregistrer des données.

La création automatique de vues et de méthodes d’action CRUD porte le nom de génération de modèles automatique. La génération de modèles automatique diffère de la génération de code dans la mesure où le code obtenu par génération de modèles automatique est un point de départ que vous pouvez modifier pour prendre en compte vos propres exigences, tandis qu’en général vous ne modifiez pas le code généré. Lorsque vous avez besoin de personnaliser le code généré, vous utilisez des classes partielles ou vous regénérez le code en cas de changements.

* Cliquez avec le bouton droit sur le dossier **Contrôleurs** dans l’ **Explorateur de solutions** , puis sélectionnez **Ajouter > Nouvel élément généré automatiquement**.
* Dans la boîte de dialogue **Ajouter un modèle automatique** :
  * Sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework**.
  * Cliquez sur **Add**. La boîte de dialogue **Ajouter un contrôleur MVC avec des vues, à l’aide de Entity Framework** s’affiche : ![ structure Student](intro/_static/scaffold-student2.png)
  * Dans **Classe de modèle** , sélectionnez **Student**.
  * Dans **Classe du contexte de données** , sélectionnez **SchoolContext**.
  * Acceptez la valeur par défaut **StudentsController** comme nom.
  * Cliquez sur **Add**.

Le moteur de génération de modèles automatique de Visual Studio crée un fichier *StudentsController.cs* et un ensemble de vues (fichiers *. cshtml* ) qui fonctionnent avec le contrôleur.

Notez que le contrôleur prend un `SchoolContext` comme paramètre de constructeur.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

L’injection de dépendance ASP.NET Core s’occupe de la transmission d’une instance de `SchoolContext` dans le contrôleur. Qui a été configuré dans le fichier *Startup.cs* .

Le contrôleur contient une méthode d’action `Index`, qui affiche tous les étudiants dans la base de données. La méthode obtient la liste des étudiants du jeu d’entités Students en lisant la propriété `Students` de l’instance de contexte de base de données :

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Vous en saurez plus sur les éléments de programmation asynchrone dans ce code plus loin dans ce didacticiel.

La vue *Views/Students/Index.cshtml* affiche cette liste dans une table :

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Appuyez sur Ctrl+F5 pour exécuter le projet ou choisissez **Déboguer > Exécuter sans débogage** dans le menu.

Cliquez sur l’onglet Students pour afficher les données de test que la méthode `DbInitializer.Initialize` a insérées. Selon l’étroitesse de votre fenêtre de navigateur, vous verrez le lien de l’onglet `Students` en haut de la page ou vous devrez cliquer sur l’icône de navigation dans le coin supérieur droit pour afficher le lien.

![Page d’accueil étroite de Contoso University](intro/_static/home-page-narrow.png)

![Page d’index des étudiants](intro/_static/students-index.png)

## <a name="view-the-database"></a>Afficher la base de données

Lorsque vous avez démarré l’application, la méthode `DbInitializer.Initialize` appelle `EnsureCreated`. EF a vu qu’il n’y avait pas de base de données et en a donc créé une, puis le reste du code de la méthode `Initialize` a rempli la base de données avec des données. Vous pouvez utiliser l’ **Explorateur d’objets SQL Server** (SSOX) pour afficher la base de données dans Visual Studio.

Fermez le navigateur.

Si la fenêtre SSOX n’est pas déjà ouverte, sélectionnez-la dans le menu **Affichage** de Visual Studio.

Dans SSOX, cliquez sur **\MSSQLLocalDB > bases de données** , puis cliquez sur l’entrée correspondant au nom de la base de données qui se trouve dans la chaîne de connexion dans le *appsettings.json* fichier.

Développez le nœud **tables** pour afficher les tables dans la base de données.

![Tables dans SSOX](intro/_static/ssox-tables.png)

Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes qui ont été créées et les lignes qui ont été insérées dans la table.

![Table Student dans SSOX](intro/_static/ssox-student-table.png)

Les fichiers de base de données *.mdf* et *.ldf* se trouvent dans le dossier *C:\Utilisateurs\\\<username>*.

Étant donné que vous appelez `EnsureCreated` dans la méthode d’initialiseur qui s’exécute au démarrage de l’application, vous pouvez maintenant apporter une modification à la classe `Student`, supprimer la base de données ou réexécuter l’application, et la base de données serait automatiquement recréée conformément à votre modification. Par exemple, si vous ajoutez une propriété `EmailAddress` à la classe `Student`, vous voyez une nouvelle colonne `EmailAddress` dans la table recréée.

## <a name="conventions"></a>Conventions

La quantité de code que vous deviez écrire pour qu’Entity Framework puisse créer une base de données complète pour vous est minimale en raison de l’utilisation de conventions ou d’hypothèses effectuées par Entity Framework.

* Les noms des propriétés `DbSet` sont utilisés comme noms de tables. Pour les entités non référencées par une propriété `DbSet`, les noms des classes d’entités sont utilisés comme noms de tables.
* Les noms des propriétés d’entités sont utilisés comme noms de colonnes.
* Les propriétés d’entité nommées ID ou classnameID sont reconnues comme propriétés de clé primaire.
* Une propriété est interprétée comme une propriété de clé étrangère si elle est nommée *\<navigation property name>\<primary key property name>* (par exemple, `StudentID` pour la `Student` propriété de navigation, puisque la `Student` clé primaire de l’entité est `ID` ). Les propriétés de clé étrangère peuvent également être nommées simplement *\<primary key property name>* (par exemple, `EnrollmentID` puisque la `Enrollment` clé primaire de l’entité est `EnrollmentID` ).

Le comportement conventionnel peut être remplacé. Par exemple, vous pouvez spécifier explicitement les noms de tables, comme vous l’avez vu précédemment dans ce didacticiel. De plus, vous pouvez définir des noms de colonne et définir une propriété quelconque en tant que clé primaire ou clé étrangère, comme vous le verrez dans un [didacticiel ultérieur](complex-data-model.md) dans cette série.

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés. Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés. Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent. Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes. Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.

Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution, mais dans les situations de faible trafic, la baisse de performances est négligeable, alors qu’en cas de trafic élevé, l’amélioration potentielle des performances est importante.

Dans le code suivant, le mot clé `async`, la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` provoquent l’exécution asynchrone du code.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Le mot clé `async` indique au compilateur de générer des rappels pour les parties du corps de la méthode et pour créer automatiquement l’objet `Task<IActionResult>` qui est renvoyé.
* Le type de retour `Task<IActionResult>` représente le travail en cours avec un résultat de type `IActionResult`.
* Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties. La première partie se termine par l’opération qui est démarrée de façon asynchrone. La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.
* `ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.

Voici quelques éléments à connaître lorsque vous écrivez un code asynchrone qui utilise Entity Framework :

* Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone. Cela inclut, par exemple, `ToListAsync`, `SingleOrDefaultAsync` et `SaveChangesAsync`, mais pas les instructions qui ne font, par exemple, que changer `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexte EF n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle. Lorsque vous appelez une méthode EF asynchrone quelconque, utilisez toujours le mot clé `await`.
* Si vous souhaitez tirer profit des meilleures performances du code asynchrone, assurez-vous que tous les packages de bibliothèque que vous utilisez (par exemple pour changer de page) utilisent également du code asynchrone s’ils appellent des méthodes Entity Framework qui provoquent l’envoi des requêtes à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble du code asynchrone](/dotnet/articles/standard/async).

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour découvrir comment effectuer des opérations CRUD de base (créer, lire, mettre à jour, supprimer).

> [!div class="nextstepaction"]
> [Implémenter la fonctionnalité CRUD de base](crud.md)

::: moniker-end
