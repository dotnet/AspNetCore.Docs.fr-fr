---
title: 'Didacticiel : prise en main de EF Core dans une application Web MVC ASP.NET'
description: Ce document est le premier d’une série de didacticiels qui expliquent comment générer à partir de zéro l’exemple d’application Contoso University.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
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
ms.openlocfilehash: 36d72e037087399c8893d5ecb4a6fffdca3a3608
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054240"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Didacticiel : prise en main de EF Core dans une application Web MVC ASP.NET

Ce tutoriel n’a **pas** été mis à jour vers ASP.NET Core 3.0. La [ Razor version des pages](xref:data/ef-rp/intro) a été mise à jour. La plupart des modifications de code pour le ASP.NET Core 3,0 et la version ultérieure de ce didacticiel :

* Se trouvent dans les fichiers *Startup.cs* et *Program.cs* .
* Se trouve dans la [ Razor version des pages](xref:data/ef-rp/intro). 

Pour plus d’informations sur le moment où cette mise à jour pourrait avoir lieu, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/13920).

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

L’exemple d’application web Contoso University montre comment créer des applications web ASP.NET Core 2.2 MVC à l’aide d’Entity Framework (EF) Core 2.2 et de Visual Studio 2017 ou 2019.

L’exemple d’application est un site web pour une université Contoso fictive. Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs. Ce document est le premier d’une série de didacticiels qui expliquent comment générer à partir de zéro l’exemple d’application Contoso University.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer une application web ASP.NET Core MVC
> * Configurer le style du site
> * En savoir plus sur les packages NuGet EF Core
> * Créer le modèle de données
> * Créer le contexte de base de données
> * Inscrire le contexte pour l’injection de dépendance
> * Initialiser la base de données avec des données de test
> * Créer un contrôleur et des vues
> * Afficher la base de données

## <a name="prerequisites"></a>Prérequis

* [Kit SDK .NET Core 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec les charges de travail suivantes :
  * Charge de travail **Développement web et ASP.NET**
  * Charge de travail **Développement multiplateforme .NET Core**

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Pour obtenir la liste des erreurs courantes et comment les résoudre, consultez [la section Dépannage du dernier didacticiel de la série](advanced.md#common-errors). Si vous n’y trouvez pas ce dont vous avez besoin, vous pouvez publier une question sur StackOverflow.com pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Il s’agit d’une série de 10 didacticiels, dont chacun s’appuie sur les opérations réalisées dans les précédents. Pensez à enregistrer une copie du projet à la fin de chaque didacticiel réussi. Ainsi, si vous rencontrez des problèmes, vous pouvez recommencer à la fin du didacticiel précédent au lieu de revenir au début de la série entière.

## <a name="contoso-university-web-app"></a>Application web Contoso University

L’application que vous allez générer dans ces didacticiels est un site web simple d’université.

Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques écrans que vous allez créer.

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Créer une application web

* Ouvrez Visual Studio.

* Dans le menu **Fichier** , sélectionnez **Nouveau > Projet** .

* Dans le volet gauche, sélectionnez **Installé > Visual C# > Web** .

* Sélectionnez le modèle de projet **Application web ASP.NET Core** .

* Entrez **ContosoUniversity** comme nom et cliquez sur **OK** .

  ![Boîte de dialogue Nouveau projet](intro/_static/new-project2.png)

* Patientez jusqu’à l’affichage de la boîte de dialogue **Nouvelle application web ASP.NET Core** .

* Sélectionnez **.NET Core** , **ASP.NET Core 2.2** et le modèle **Application web (Model-View-Controller)** .

* Assurez-vous que **l’authentification** est définie sur **aucune authentification** .

* Sélectionnez **OK** .

  ![Boîte de dialogue Nouveau projet ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Configurer le style du site

Quelques changements simples configureront le menu, la disposition et la page d’accueil du site.

Ouvrez *Views/Shared/_Layout.cshtml* et apportez les modifications suivantes :

* Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ». Il y a trois occurrences.

* Ajoutez des entrées de menu pour **About** , **Students** , **Courses** , **Instructors** , et **Departments** , et supprimez l’entrée de menu **Privacy** .

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

La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships). Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité. Dans ce cas, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à l’entité `Student`. En d’autres termes, si une ligne Student donnée dans la base de données a deux lignes Enrollment associées (lignes qui contiennent la valeur de clé primaire de cet étudiant dans la colonne de clé étrangère StudentID), la propriété de navigation `Enrollments` de cette entité `Student` contiendra ces deux entités `Enrollment`.

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

Dans le dossier du projet, créez un dossier nommé *Data* .

Dans ce dossier *Data* , créez un nouveau fichier de classe nommé *SchoolContext.cs* et remplacez le code du modèle par le code suivant :

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Ce code crée une propriété `DbSet` pour chaque jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.

Vous pourriez avoir omis les instructions `DbSet<Enrollment>` et `DbSet<Course>`, et cela fonctionnerait de la même façon. Entity Framework les inclurait implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`.

Quand la base de données est créée, EF crée des tables dont les noms sont identiques aux noms de propriété `DbSet`. Les noms de propriété pour les collections sont généralement pluriels (Students plutôt que Student), mais les développeurs ne sont pas tous d’accord sur la nécessité d’utiliser des noms de tables au pluriel. Pour ces didacticiels, vous remplacerez le comportement par défaut en spécifiant des noms de tables au singulier dans le contexte DbContext. Pour ce faire, ajoutez le code en surbrillance suivant après la dernière propriété DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

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

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Ajoutez des instructions `using` :

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

Dans les didacticiels plus anciens, vous pouvez voir un code similaire dans la méthode `Configure`, dans *Startup.cs* . Nous vous recommandons d’utiliser la méthode `Configure` uniquement pour configurer le pipeline de demande. Le code de démarrage d’application doit figurer dans la méthode `Main`.

À présent, la première fois que vous exécutez l’application, la base de données est créée et initialement peuplée avec les données de test. Chaque fois que vous changez votre modèle de données, vous pouvez supprimer la base de données, mettre à jour votre méthode de peuplement initial et repartir de la même façon avec une nouvelle base de données. Dans les didacticiels suivants, vous verrez comment modifier la base de données quand le modèle de données change, sans supprimer et recréer la base de données.

## <a name="create-controller-and-views"></a>Créer un contrôleur et des vues

Ensuite, vous utiliserez le moteur de génération de modèles automatique dans Visual Studio pour ajouter un contrôleur MVC et les vues qu’utilisera EF pour exécuter des requêtes de données et enregistrer les données.

La création automatique de vues et de méthodes d’action CRUD porte le nom de génération de modèles automatique. La génération de modèles automatique diffère de la génération de code dans la mesure où le code obtenu par génération de modèles automatique est un point de départ que vous pouvez modifier pour prendre en compte vos propres exigences, tandis qu’en général vous ne modifiez pas le code généré. Lorsque vous avez besoin de personnaliser le code généré, vous utilisez des classes partielles ou vous regénérez le code en cas de changements.

* Cliquez avec le bouton droit sur le dossier **Contrôleurs** dans l’ **Explorateur de solutions** , puis sélectionnez **Ajouter > Nouvel élément généré automatiquement** .

* Dans la boîte de dialogue **Ajouter un modèle automatique** :

  * Sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework** .

  * Cliquez sur **Add** . La boîte de dialogue **Ajouter un contrôleur MVC avec vues, utilisant Entity Framework** s’affiche.

    ![Génération de modèles automatique – Étudiant](intro/_static/scaffold-student2.png)

  * Dans **Classe de modèle** , sélectionnez **Student** .

  * Dans **Classe du contexte de données** , sélectionnez **SchoolContext** .

  * Acceptez la valeur par défaut **StudentsController** comme nom.

  * Cliquez sur **Add** .

  Lorsque vous cliquez sur **Ajouter** , le moteur de génération de modèles automatique de Visual Studio crée un fichier *StudentsController.cs* et un ensemble de vues (fichiers *.cshtml* ) qui fonctionnent avec le contrôleur.

(Le moteur de génération de modèles automatique peut également créer le contexte de base de données pour vous, si vous ne l’avez pas déjà créé manuellement, comme vous l’avez fait précédemment pour ce didacticiel. Vous pouvez spécifier une nouvelle classe de contexte dans la zone **Ajouter un contrôleur** en cliquant sur le signe plus à droite de **Classe du contexte de données** .  Visual Studio crée ensuite votre classe `DbContext` ainsi que le contrôleur et les vues.)

Vous pouvez remarquer que le contrôleur accepte un `SchoolContext` comme paramètre de constructeur.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

L’injection de dépendance ASP.NET Core s’occupe de la transmission d’une instance de `SchoolContext` dans le contrôleur. Vous avez configuré cela dans le fichier *Startup.cs* précédemment.

Le contrôleur contient une méthode d’action `Index`, qui affiche tous les étudiants dans la base de données. La méthode obtient la liste des étudiants du jeu d’entités Students en lisant la propriété `Students` de l’instance de contexte de base de données :

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Vous découvrirez les éléments de programmation asynchrones dans ce code, plus loin dans ce didacticiel.

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

Dans SSOX, cliquez sur **\MSSQLLocalDB > bases de données** , puis cliquez sur l’entrée correspondant au nom de la base de données qui se trouve dans la chaîne de connexion de votre *appsettings.json* fichier.

Développez le nœud **Tables** pour afficher les tables de votre base de données.

![Tables dans SSOX](intro/_static/ssox-tables.png)

Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes qui ont été créées et les lignes qui ont été insérées dans la table.

![Table Student dans SSOX](intro/_static/ssox-student-table.png)

Les fichiers de base de données *.mdf* et *.ldf* se trouvent dans le dossier *C:\Utilisateurs\\\<yourusername>* .

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

## <a name="get-the-code"></a>Obtenir le code

[Télécharger ou afficher l’application complète.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer une application web ASP.NET Core MVC
> * Configurer le style du site
> * En savoir plus sur les packages NuGet EF Core
> * Créer le modèle de données
> * Créer le contexte de base de données
> * Inscrire le contexte SchoolContext
> * Initialiser la base de données avec des données de test
> * Créer un contrôleur et des vues
> * Afficher la base de données

Dans le prochain didacticiel, vous apprendrez à effectuer des opérations CRUD de base (créer, lire, mettre à jour, supprimer).

Passez au tutoriel suivant pour découvrir comment effectuer des opérations CRUD de base (créer, lire, mettre à jour, supprimer).

> [!div class="nextstepaction"]
> [Implémenter la fonctionnalité CRUD de base](crud.md)

