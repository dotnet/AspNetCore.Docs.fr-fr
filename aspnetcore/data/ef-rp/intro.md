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
ms.openlocfilehash: 5849f4bfb9d0355177ceb5c2f4236c6d39dd9e92
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340034"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Razor Pages avec Entity Framework Core dans ASP.NET Core-didacticiel 1 sur 8

Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

Il s’agit de la première d’une série de didacticiels qui montrent comment utiliser Entity Framework (EF) Core dans une application [ASP.net Core Razor pages](xref:razor-pages/index) . Dans ces tutoriels, un site web est créé pour une université fictive nommée Contoso. Le site comprend des fonctionnalités comme l’admission des étudiants, la création de cours et les affectations des formateurs. Le didacticiel utilise l’approche code First. Pour plus d’informations sur ce didacticiel avec la première approche basée sur la base de données, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Télécharger ou afficher l’application terminée.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instructions de téléchargement](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prérequis

* Si vous Razor débutez avec des pages, consultez la série de didacticiels [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) avant de commencer celle-ci.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a>Moteurs de base de données

Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.

Les instructions Visual Studio Code utilisent [SQLite](https://www.sqlite.org/), moteur de base de données multiplateforme.

Si vous choisissez d’utiliser SQLite, téléchargez et installez un outil tiers pour la gestion et l’affichage d’une base de données SQLite, comme [Browser for SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez un problème que vous ne pouvez pas résoudre, comparez votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Un bon moyen d’obtenir de l’aide est de poster une question sur StackOverflow.com en utilisant le [mot-clé ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou le [mot-clé EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Exemple d’application

L’application générée dans ces didacticiels est le site web de base d’une université. Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques-uns des écrans créés dans le didacticiel.

![Page d’index des étudiants](intro/_static/students-index30.png)

![Page de modification des étudiants](intro/_static/student-edit30.png)

Le style de l’interface utilisateur de ce site repose sur les modèles de projet intégrés. Ce didacticiel porte sur l’utilisation de EF Core avec ASP.NET Core, et non sur la personnalisation de l’interface utilisateur.

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

## <a name="create-the-web-app-project"></a>Créer le projet d’application web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Démarrez Visual Studio et sélectionnez **Créer un projet**.
1. Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **ASP.net Core application Web** > **suivant**.
1. Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `ContosoUniversity` pour **nom du projet**. Il est important d’utiliser ce nom exact, y compris la mise en majuscules, donc chaque correspond à la `namespace` copie du code.
1. Sélectionnez **Create** (Créer).
1. Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :
    1. **.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.
    1. **ASP.net Core application Web (Model-View-Controller)**.
    1. **Create** 
       Créer ![ Boîte de dialogue Nouveau projet de ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)
    
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dans un terminal, accédez au dossier dans lequel le dossier de projet doit être créé.
* Exécutez les commandes suivantes pour créer un Razor projet de pages et `cd` dans le dossier du nouveau projet :

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a>Configurer le style du site

Copiez et collez le code suivant dans le fichier *pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Le fichier de disposition définit l’en-tête, le pied de page et le menu du site. Le code précédent apporte les modifications suivantes :

* Chaque occurrence de « ContosoUniversity » à « Contoso University ». Il y a trois occurrences.
* Les entrées du menu d' **hébergement** et de **confidentialité** sont supprimées.
* Des entrées sont ajoutées pour **à propos** de, **étudiants** , **cours** , **instructeurs** et **services**.

Dans *pages/index. cshtml* , remplacez le contenu du fichier par le code suivant :

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

Le code précédent remplace le texte relatif à ASP.NET Core par le texte de cette application.

Exécutez l’application pour vérifier que la page d’accueil (« Home ») s’affiche.

## <a name="the-data-model"></a>Modèle de données

Les sections suivantes créent un modèle de données :

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Un étudiant peut s’inscrire à un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.

## <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité Student](intro/_static/student-entity.png)

* Créez un dossier *Models* dans le dossier de projet. 

* Créez *Models/Student.cs* avec le code suivant :

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire. L’autre nom reconnu automatiquement de la clé primaire de classe `Student` est `StudentID`. Pour plus d’informations, consultez [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).

La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships). Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité. Dans ce cas, la propriété `Enrollments` d’une entité `Student` contient toutes les entités `Enrollment` associées à cet étudiant. Par exemple, si une ligne Student dans la base de données est associée à deux lignes Enrollment, la propriété de navigation `Enrollments` contient ces deux entités Enrollment. 

Dans la base de données, une ligne Enrollment est associée à une ligne Student si sa colonne StudentID contient la valeur d’ID de l’étudiant. Par exemple, supposez qu’une ligne Student présente un ID égal à 1. Les lignes Enrollment associées auront un StudentID égal à 1. StudentID est une *clé étrangère* dans la table Enrollment. 

La propriété `Enrollments` est définie en tant que `ICollection<Enrollment>`, car plusieurs entités Enrollment associées peuvent exister. Vous pouvez utiliser d’autres types de collection, comme `List<Enrollment>` ou `HashSet<Enrollment>`. Quand vous utilisez `ICollection<Enrollment>`, EF Core crée une collection `HashSet<Enrollment>` par défaut.

## <a name="the-enrollment-entity"></a>L’entité Enrollment

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

Créez *Models/Enrollment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

La propriété `EnrollmentID` est la clé primaire ; cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même. Pour un modèle de données de production, choisissez un modèle et utilisez-le systématiquement. Ce tutoriel utilise les deux pour montrer qu’ils fonctionnent tous les deux. L’utilisation de `ID` sans `classname` facilite l’implémentation de certaines modifications du modèle de données.

La propriété `Grade` est un `enum`. La présence du point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade`[accepte les valeurs Null](/dotnet/csharp/programming-guide/nullable-types/). Une note (Grade) de valeur Null est différente d’une note zéro : la valeur Null signifie que la note n’est pas connue ou qu’elle n’a pas encore été attribuée.

La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`. Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.

La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`. Une entité `Enrollment` est associée à une entité `Course`.

EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`. Par exemple, `StudentID` est la clé étrangère pour la propriété de navigation `Student`, car la clé primaire de l’entité `Student` est `ID`. Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`. Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.

## <a name="the-course-entity"></a>L’entité Course

![Diagramme de l’entité Course](intro/_static/course-entity.png)

Créez *Models/Course.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

La propriété `Enrollments` est une propriété de navigation. Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.

L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.

Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.

## <a name="scaffold-student-pages"></a>Générer automatiquement des modèles de pages Student

Dans cette section, vous allez utiliser l’outil de génération de modèles automatique ASP.NET Core pour générer :

* Classe EF Core `DbContext` . Le contexte est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données déterminé. Il dérive de la classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.
* Razor les pages qui gèrent les opérations de création, lecture, mise à jour et suppression (CRUD) pour l' `Student` entité.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Créez un dossier *Pages/Students*.
* Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Pages/Students* , puis sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.
* Dans la boîte de dialogue **Ajouter un nouvel élément de structure** :
  * Dans l’onglet de gauche, sélectionnez **installé > Razor pages > communes**
  * Sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.
* Dans la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :
  * Dans la liste déroulante **Classe de modèle** , sélectionnez **Student (ContosoUniversity.Models)**.
  * Dans la ligne **Classe du contexte de données** , sélectionnez le signe **+** (plus).
    * Modifiez le nom du contexte de données pour qu’il se termine par `SchoolContext` plutôt que `ContosoUniversityContext` . Nom du contexte mis à jour : `ContosoUniversity.Data.SchoolContext`
   * Sélectionnez **Ajouter**.

Les packages suivants sont automatiquement installés :

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Exécutez les commandes CLI .NET Core suivantes pour installer les packages NuGet nécessaires :

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   Le package Microsoft.VisualStudio.Web.CodeGeneration.Design est requis pour la génération de modèles automatique. Bien que l’application ne soit pas appelée à utiliser SQL Server, l’outil de génération de modèles automatique a besoin du package SQL Server.

* Créez un dossier *Pages/Students*.

* Exécutez la commande suivante pour installer l’[outil de génération de modèles automatique aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* Exécutez la commande suivante pour générer automatiquement des modèles de pages Student.

  **Sur Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  **Sur macOS ou Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

Si l’étape précédente échoue, générez le projet et recommencez l’étape de génération de modèles automatique.

Le processus de génération de modèles automatique :

* Crée Razor des pages dans le dossier *pages/élèves* :
  * *Create.cshtml* et *Create.cshtml.cs*
  * *Delete.cshtml* et *Delete.cshtml.cs*
  * *Details.cshtml* et *Details.cshtml.cs*
  * *Edit.cshtml* et *Edit.cshtml.cs*
  * *Index.cshtml* et *Index.cshtml.cs*
* Crée *Data/SchoolContext. cs*.
* Ajoute le contexte à l’injection de dépendances dans *Startup.cs*.
* Ajoute une chaîne de connexion de base de données à *appsettings.json* .

## <a name="database-connection-string"></a>Chaîne de connexion de base de données

L’outil de génération de modèles automatique génère une chaîne de connexion dans le *appsettings.json* fichier.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La chaîne de connexion spécifie [SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)base de données locale :

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production. Par défaut, la Base de données locale crée des fichiers *.mdf* dans le répertoire `C:/Users/<user>`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Raccourcissez la chaîne de connexion SQLite à *cu. db* :

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Mettre à jour la classe du contexte de base de données

La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données déterminé est la classe du contexte de base de données. Le contexte est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données. Dans ce projet, la classe est nommée `SchoolContext`.

Mettez à jour *Data/SchoolContext.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Le code précédent passe du singulier `DbSet<Student> Student` au pluriel `DbSet<Student> Students` . Pour que le Razor Code des pages corresponde au nouveau `DBSet` nom, effectuez une modification globale à partir de : `_context.Student.`
À: `_context.Students.`

Il y a 8 occurrences.

Un jeu d’entités contenant plusieurs entités, de nombreux développeurs préfèrent que les `DBSet` noms de propriété soient au pluriel.

Code mis en surbrillance :

* Crée une [propriété \<TEntity> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités. Dans la terminologie d’EF Core :
  * Un jeu d’entités correspond généralement à une table de base de données.
  * Une entité correspond à une ligne dans la table.
* Appelle <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>. `OnModelCreating`:
  * Est appelé quand `SchoolContext` a été initialisé, mais avant que le modèle ait été verrouillé et utilisé pour initialiser le contexte.
  * Est requis, car ultérieurement dans le didacticiel, l' `Student` entité aura des références aux autres entités.
  <!-- Review, OnModelCreating needs review -->

Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Les services tels que le `SchoolContext` sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services, tels que les Razor pages, via des paramètres de constructeur. Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

L’outil de génération de modèles automatique a inscrit automatiquement la classe du contexte dans le conteneur d’injection de dépendances.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Les lignes en surbrillance suivantes ont été ajoutées par le générateur de modèles :

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vérifiez le code ajouté par les appels de l’échafaudage `UseSqlite` .

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

Pour plus d’informations sur l’utilisation d’une base de données de production [, consultez utiliser SQLite pour le développement, SQL Server pour la production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .

---

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

### <a name="add-the-database-exception-filter"></a>Ajouter le filtre d’exception de base de données

Ajoutez <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> à `ConfigureServices` , comme indiqué dans le code suivant :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

Ajoutez le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .

Dans le PMC, entrez ce qui suit pour ajouter le package NuGet :

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

Le `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` package NuGet fournit ASP.net Core intergiciel pour Entity Framework Core pages d’erreurs. Cet intergiciel (middleware) permet de détecter et de diagnostiquer les erreurs avec Entity Framework Core migrations.

Le `AddDatabaseDeveloperPageExceptionFilter` fournit des informations d’erreur utiles dans l' [environnement de développement](xref:fundamentals/environments).

## <a name="create-the-database"></a>Création de la base de données

Mettez à jour *Program.cs* pour créer la base de données si elle n’existe pas :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

La méthode [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) n’effectue aucune action s’il existe une base de données pour le contexte. S’il n’existe pas de base de données, elle crée la base de données et le schéma. `EnsureCreated` active le workflow suivant pour gérer les modifications du modèle de données :

* Supprimez la base de données. Toutes les données existantes sont perdues.
* Modifiez le modèle de données. Par exemple, ajoutez un champ `EmailAddress`.
* Exécutez l’application.
* `EnsureCreated` crée une base de données avec le nouveau schéma.

Ce workflow fonctionne bien à un stade précoce du développement, quand le schéma évolue rapidement, aussi longtemps que vous n’avez pas besoin de conserver les données. La situation est différente quand les données qui ont été entrées dans la base de données doivent être conservées. Dans ce cas, procédez à des migrations.

Plus tard dans cette série de tutoriels, vous supprimerez la base de données créée par `EnsureCreated` et procéderez à des migrations. Une base de données créée par `EnsureCreated` ne peut pas être mise à jour via des migrations.

### <a name="test-the-app"></a>Tester l'application

* Exécutez l’application.
* Sélectionnez le lien **Students** , puis **Créer nouveau**.
* Testez les liens Edit, Details et Delete.

## <a name="seed-the-database"></a>Amorcer la base de données

La méthode `EnsureCreated` crée une base de données vide. Cette section ajoute du code qui remplit la base de données avec des données de test.

Créez *Data/DbInitializer.cs* avec le code suivant :
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Le code vérifie si des étudiants figurent dans la base de données. S’il n’y a pas d’étudiants, il ajoute des données de test à la base de données. Il crée les données de test dans des tableaux et non dans des collections `List<T>` afin d’optimiser les performances.

Dans *Program.cs* , remplacez l’appel `EnsureCreated` par un appel `DbInitializer.Initialize` :

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package**  :

```powershell
Drop-Database -Confirm
```

Répondre avec `Y` pour supprimer la base de données.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.

---

* Redémarrez l’application.
* Sélectionnez la page Students pour examiner les données amorcées.

## <a name="view-the-database"></a>Afficher la base de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.
* Dans SSOX, sélectionnez **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**. Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.
* Développez le nœud **Tables**.
* Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.
* Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher le code** pour voir comment le modèle `Student` est mappé au schéma de la table `Student`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Utilisez votre outil SQLite pour examiner le schéma de base de données et les données amorcées. Le fichier de base de données est nommé *CU.db* et se trouve dans le dossier du projet.

---

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés. Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés. Avec le code synchrone, de nombreux threads peuvent être associés alors qu’ils ne fonctionnent pas car ils sont en attente de la fin des e/s. Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes. Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.

Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution. Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.

Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Le mot clé `async` fait en sorte que le compilateur :
  * Génère des rappels pour les parties du corps de méthode.
  * Crée l’objet [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) qui est retourné.
* Le type de retour `Task<T>` représente le travail en cours.
* Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties. La première partie se termine par l’opération qui est démarrée de façon asynchrone. La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.
* `ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.

Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :

* Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone. Cela inclut `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`, mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.
* Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a>Considérations relatives aux performances

En général, une page Web ne doit pas charger un nombre arbitraire de lignes. Une requête doit utiliser la pagination ou une approche de limitation. Par exemple, la requête ci-dessus peut utiliser `Take` pour limiter les lignes retournées :

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

L’énumération d’une table volumineuse dans une vue peut retourner une réponse HTTP 200 partiellement construite si une exception de base de données se produit de façon partielle via l’énumération.

<xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> la valeur par défaut est 1024. Les jeux de codes suivants `MaxModelBindingCollectionSize` :

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

La pagination est traitée plus loin dans le didacticiel.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Didacticiel suivant](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Il s’agit de la première d’une série de didacticiels qui montrent comment utiliser Entity Framework (EF) Core dans une application [ASP.net Core Razor pages](xref:razor-pages/index) . Dans ces tutoriels, un site web est créé pour une université fictive nommée Contoso. Le site comprend des fonctionnalités comme l’admission des étudiants, la création de cours et les affectations des formateurs. Le didacticiel utilise l’approche code First. Pour plus d’informations sur ce didacticiel avec la première approche basée sur la base de données, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Télécharger ou afficher l’application terminée.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instructions de téléchargement](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prérequis

* Si vous Razor débutez avec des pages, consultez la série de didacticiels [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) avant de commencer celle-ci.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Moteurs de base de données

Les instructions Visual Studio utilisent la [Base de données locale SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), version de SQL Server Express qui s’exécute uniquement sur Windows.

Les instructions Visual Studio Code utilisent [SQLite](https://www.sqlite.org/), moteur de base de données multiplateforme.

Si vous choisissez d’utiliser SQLite, téléchargez et installez un outil tiers pour la gestion et l’affichage d’une base de données SQLite, comme [Browser for SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez un problème que vous ne pouvez pas résoudre, comparez votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Un bon moyen d’obtenir de l’aide est de poster une question sur StackOverflow.com en utilisant le [mot-clé ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou le [mot-clé EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Exemple d’application

L’application générée dans ces didacticiels est le site web de base d’une université. Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques-uns des écrans créés dans le didacticiel.

![Page d’index des étudiants](intro/_static/students-index30.png)

![Page de modification des étudiants](intro/_static/student-edit30.png)

Le style de l’interface utilisateur de ce site repose sur les modèles de projet intégrés. Le tutoriel traite essentiellement de l’utilisation d’EF Core, et non de la façon de personnaliser l’interface utilisateur.

Suivez le lien en haut de la page pour obtenir le code source du projet terminé. Le dossier *cu30* contient le code de la version 3.0 d’ASP.NET Core. Les fichiers qui reflètent l’état du code pour les tutoriels 1-7 se trouvent dans le dossier *cu30snapshots*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour exécuter l’application après avoir téléchargé le projet terminé :

* Créez le projet.
* Dans la console du Gestionnaire de package (PMC), exécutez la commande suivante :

  ```powershell
  Update-Database
  ```

* Exécutez le projet pour amorcer la base de données.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pour exécuter l’application après avoir téléchargé le projet terminé :

* Supprimez *ContosoUniversity.csproj* , puis renommez *ContosoUniversitySQLite.csproj* en *ContosoUniversity.csproj*.
* Dans *Program.cs* , commentez-le `#define Startup` pour qu’il `StartupSQLite` soit utilisé.
* Supprimez *appSettings.json* et renommez *appSettingsSQLite.json* en *appSettings.json*.
* Supprimez le dossier *Migrations* et renommez *MigrationsSQL* en *Migrations*.
* Effectuez une recherche globale `#if SQLiteVersion` et supprimez `#if SQLiteVersion` et l' `#endif` instruction associée.
* Créez le projet.
* Dans une invite de commandes, exécutez la commande suivante dans le dossier du projet :

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* Dans votre outil SQLite, exécutez l’instruction SQL suivante :

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* Exécutez le projet pour amorcer la base de données.

---

## <a name="create-the-web-app-project"></a>Créer le projet d’application web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet**.
* Sélectionnez **Application web ASP.NET Core**.
* Nommez le projet *ContosoUniversity*. Il est important d’utiliser ce nom exact, en respectant l’utilisation des majuscules, de sorte que les espaces de noms correspondent au moment où le code est copié et collé.
* Sélectionnez **.NET Core** et **ASP.NET Core 3.0** dans les listes déroulantes, puis **Application web**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dans un terminal, accédez au dossier dans lequel le dossier de projet doit être créé.

* Exécutez les commandes suivantes pour créer un Razor projet de pages et `cd` dans le dossier du nouveau projet :

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Configurer le style du site

Configurez l’en-tête, le pied de page et le menu du site en mettant à jour *Pages/Shared/_Layout.cshtml*  :

* Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ». Il y a trois occurrences.

* Supprimez les entrées de menu **Home** et **Privacy** et ajoutez les entrées **About** , **Students** , **Courses** , **Instructors** et **Departments**.

Les modifications sont mises en surbrillance.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Dans *Pages/Index.cshtml* , remplacez le contenu du fichier par le code suivant de façon à remplacer le texte relatif à ASP.NET Core par le texte se rapportant à cette application :

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Exécutez l’application pour vérifier que la page d’accueil (« Home ») s’affiche.

## <a name="the-data-model"></a>Modèle de données

Les sections suivantes créent un modèle de données :

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Un étudiant peut s’inscrire à un nombre quelconque de cours et un cours peut avoir un nombre quelconque d’élèves inscrits.

## <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité Student](intro/_static/student-entity.png)

* Créez un dossier *Models* dans le dossier de projet.
* Créez *Models/Student.cs* avec le code suivant :

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire. L’autre nom reconnu automatiquement de la clé primaire de classe `Student` est `StudentID`. Pour plus d’informations, consultez [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).

La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships). Les propriétés de navigation contiennent d’autres entités qui sont associées à cette entité. Dans ce cas, la propriété `Enrollments` d’une entité `Student` contient toutes les entités `Enrollment` associées à cet étudiant. Par exemple, si une ligne Student dans la base de données est associée à deux lignes Enrollment, la propriété de navigation `Enrollments` contient ces deux entités Enrollment. 

Dans la base de données, une ligne Enrollment est associée à une ligne Student si sa colonne StudentID contient la valeur d’ID de l’étudiant. Par exemple, supposez qu’une ligne Student présente un ID égal à 1. Les lignes Enrollment associées auront un StudentID égal à 1. StudentID est une *clé étrangère* dans la table Enrollment. 

La propriété `Enrollments` est définie en tant que `ICollection<Enrollment>`, car plusieurs entités Enrollment associées peuvent exister. Vous pouvez utiliser d’autres types de collection, comme `List<Enrollment>` ou `HashSet<Enrollment>`. Quand vous utilisez `ICollection<Enrollment>`, EF Core crée une collection `HashSet<Enrollment>` par défaut.

## <a name="the-enrollment-entity"></a>L’entité Enrollment

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

Créez *Models/Enrollment.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

La propriété `EnrollmentID` est la clé primaire ; cette entité utilise le modèle `classnameID` à la place de `ID` par lui-même. Pour un modèle de données de production, choisissez un modèle et utilisez-le systématiquement. Ce tutoriel utilise les deux pour montrer qu’ils fonctionnent tous les deux. L’utilisation de `ID` sans `classname` facilite l’implémentation de certaines modifications du modèle de données.

La propriété `Grade` est un `enum`. La présence du point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade`[accepte les valeurs Null](/dotnet/csharp/programming-guide/nullable-types/). Une note (Grade) de valeur Null est différente d’une note zéro : la valeur Null signifie que la note n’est pas connue ou qu’elle n’a pas encore été attribuée.

La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`. Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`.

La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`. Une entité `Enrollment` est associée à une entité `Course`.

EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`. Par exemple, `StudentID` est la clé étrangère pour la propriété de navigation `Student`, car la clé primaire de l’entité `Student` est `ID`. Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`. Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.

## <a name="the-course-entity"></a>L’entité Course

![Diagramme de l’entité Course](intro/_static/course-entity.png)

Créez *Models/Course.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

La propriété `Enrollments` est une propriété de navigation. Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.

L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.

Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.

## <a name="scaffold-student-pages"></a>Générer automatiquement des modèles de pages Student

Dans cette section, vous allez utiliser l’outil de génération de modèles automatique ASP.NET Core pour générer :

* Une classe de *contexte* EF Core. Le contexte est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données déterminé. Il dérive de la classe `Microsoft.EntityFrameworkCore.DbContext`.
* Razor les pages qui gèrent les opérations de création, lecture, mise à jour et suppression (CRUD) pour l' `Student` entité.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Créez un dossier *Students* dans le dossier *Pages*.
* Dans l’ **Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Pages/Students* , puis sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.
* Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.
* Dans la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :
  * Dans la liste déroulante **Classe de modèle** , sélectionnez **Student (ContosoUniversity.Models)**.
  * Dans la ligne **Classe du contexte de données** , sélectionnez le signe **+** (plus).
  * Remplacez le nom du contexte de données *ContosoUniversity.Models.ContosoUniversityContext* par *ContosoUniversity.Data.SchoolContext*.
  * Sélectionnez **Ajouter**.

Les packages suivants sont automatiquement installés :

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Exécutez les commandes CLI .NET Core suivantes pour installer les packages NuGet nécessaires :
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

  Le package Microsoft.VisualStudio.Web.CodeGeneration.Design est requis pour la génération de modèles automatique. Bien que l’application ne soit pas appelée à utiliser SQL Server, l’outil de génération de modèles automatique a besoin du package SQL Server.

* Créez un dossier *Pages/Students*.

* Exécutez la commande suivante pour installer l’[outil de génération de modèles automatique aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Exécutez la commande suivante pour générer automatiquement des modèles de pages Student.

  **Sur Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **Sur macOS ou Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Si vous rencontrez un problème à l’étape précédente, générez le projet et recommencez l’étape de génération de modèles automatique.

Le processus de génération de modèles automatique :

* Crée Razor des pages dans le dossier *pages/élèves* :
  * *Create.cshtml* et *Create.cshtml.cs*
  * *Delete.cshtml* et *Delete.cshtml.cs*
  * *Details.cshtml* et *Details.cshtml.cs*
  * *Edit.cshtml* et *Edit.cshtml.cs*
  * *Index.cshtml* et *Index.cshtml.cs*
* Crée *Data/SchoolContext. cs*.
* Ajoute le contexte à l’injection de dépendances dans *Startup.cs*.
* Ajoute une chaîne de connexion de base de données à *appsettings.json* .

## <a name="database-connection-string"></a>Chaîne de connexion de base de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Le *appsettings.json* fichier spécifie la chaîne de connexion [SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)la base de données locale.

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production. Par défaut, la Base de données locale crée des fichiers *.mdf* dans le répertoire `C:/Users/<user>`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Modifiez la chaîne de connexion de sorte qu’elle pointe vers un fichier de base de données SQLite nommé *CU.db*  :

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Mettre à jour la classe du contexte de base de données

La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données déterminé est la classe du contexte de base de données. Le contexte est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données. Dans ce projet, la classe est nommée `SchoolContext`.

Mettez à jour *Data/SchoolContext.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Le code en surbrillance crée une propriété [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités. Dans la terminologie d’EF Core :

* Un jeu d’entités correspond généralement à une table de base de données.
* Une entité correspond à une ligne dans la table.

Comme un jeu d’entités contient plusieurs entités, les propriétés DBSet doivent être des noms au pluriel. Comme l’outil de génération de modèles automatique a créé un DBSet `Student`, cette étape le remplace par le pluriel `Students`. 

Pour que le Razor Code des pages corresponde au nouveau nom de DBSet, effectuez une modification globale sur l’ensemble du projet de `_context.Student` à `_context.Students` .  Il y a 8 occurrences.

Générez le projet pour vérifier qu’il n’y a pas d’erreurs du compilateur.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Certains services (comme le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

L’outil de génération de modèles automatique a inscrit automatiquement la classe du contexte dans le conteneur d’injection de dépendances.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans `ConfigureServices`, les lignes en surbrillance ont été ajoutées par l’outil de génération de modèles automatique :

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dans `ConfigureServices`, vérifiez que le code ajouté par l’outil de génération de modèles automatique appelle `UseSqlite`.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

## <a name="create-the-database"></a>Création de la base de données

Mettez à jour *Program.cs* pour créer la base de données si elle n’existe pas :

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

La méthode [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) n’effectue aucune action s’il existe une base de données pour le contexte. S’il n’existe pas de base de données, elle crée la base de données et le schéma. `EnsureCreated` active le workflow suivant pour gérer les modifications du modèle de données :

* Supprimez la base de données. Toutes les données existantes sont perdues.
* Modifiez le modèle de données. Par exemple, ajoutez un champ `EmailAddress`.
* Exécutez l’application.
* `EnsureCreated` crée une base de données avec le nouveau schéma.

Ce workflow fonctionne bien à un stade précoce du développement, quand le schéma évolue rapidement, aussi longtemps que vous n’avez pas besoin de conserver les données. La situation est différente quand les données qui ont été entrées dans la base de données doivent être conservées. Dans ce cas, procédez à des migrations.

Plus tard dans cette série de tutoriels, vous supprimerez la base de données créée par `EnsureCreated` et procéderez à des migrations. Une base de données créée par `EnsureCreated` ne peut pas être mise à jour via des migrations.

### <a name="test-the-app"></a>Tester l'application

* Exécutez l’application.
* Sélectionnez le lien **Students** , puis **Créer nouveau**.
* Testez les liens Edit, Details et Delete.

## <a name="seed-the-database"></a>Amorcer la base de données

La méthode `EnsureCreated` crée une base de données vide. Cette section ajoute du code qui remplit la base de données avec des données de test.

Créez *Data/DbInitializer.cs* avec le code suivant :
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Le code vérifie si des étudiants figurent dans la base de données. S’il n’y a pas d’étudiants, il ajoute des données de test à la base de données. Il crée les données de test dans des tableaux et non dans des collections `List<T>` afin d’optimiser les performances.

* Dans *Program.cs* , remplacez l’appel `EnsureCreated` par un appel `DbInitializer.Initialize` :

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package**  :

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.

---

* Redémarrez l’application.

* Sélectionnez la page Students pour examiner les données amorcées.

## <a name="view-the-database"></a>Afficher la base de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.
* Dans SSOX, sélectionnez **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**. Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID.
* Développez le nœud **Tables**.
* Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.
* Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher le code** pour voir comment le modèle `Student` est mappé au schéma de la table `Student`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Utilisez votre outil SQLite pour examiner le schéma de base de données et les données amorcées. Le fichier de base de données est nommé *CU.db* et se trouve dans le dossier du projet.

---

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés. Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés. Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent. Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes. Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.

Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution. Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.

Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Le mot clé `async` fait en sorte que le compilateur :
  * Génère des rappels pour les parties du corps de méthode.
  * Crée l’objet [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) qui est retourné.
* Le type de retour `Task<T>` représente le travail en cours.
* Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties. La première partie se termine par l’opération qui est démarrée de façon asynchrone. La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.
* `ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.

Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :

* Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone. Cela inclut `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`, mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.
* Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Didacticiel suivant](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

L’exemple d’application Web Contoso University montre comment créer une Razor application ASP.net Core pages à l’aide d’Entity Framework (EF) Core.

L’exemple d’application est un site web pour une université Contoso fictive. Il comprend des fonctionnalités telles que l’admission des étudiants, la création des cours et les affectations des formateurs. Cette page est la première d’une série de didacticiels qui expliquent comment générer l’exemple d’application Contoso University.

[Télécharger ou afficher l’application terminée.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instructions de téléchargement](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Vous êtes familiarisé avec les [ Razor pages](xref:razor-pages/index). Les nouveaux programmeurs doivent effectuer la [prise en main des Razor pages avant de](xref:tutorials/razor-pages/razor-pages-start) commencer cette série.

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez un problème que vous ne pouvez pas résoudre, vous pouvez généralement trouver la solution en comparant votre code au [projet terminé](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Un bon moyen d’obtenir de l’aide est de publier une question sur [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pour [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>L’application web Contoso University

L’application générée dans ces didacticiels est le site web de base d’une université.

Les utilisateurs peuvent afficher et mettre à jour les informations relatives aux étudiants, aux cours et aux formateurs. Voici quelques-uns des écrans créés dans le didacticiel.

![Page d’index des étudiants](intro/_static/students-index.png)

![Page de modification des étudiants](intro/_static/student-edit.png)

Le style de l’interface utilisateur de ce site est proche de ce qui est généré par les modèles prédéfinis. Ce didacticiel se concentre sur EF Core avec Razor les pages, et non sur l’interface utilisateur.

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a>Créer l' Razor application Web ContosoUniversity pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet**.
* Créez une application web ASP.NET Core. Nommez le projet **ContosoUniversity**. Il est important de nommer le projet *ContosoUniversity* afin que les espaces de noms correspondent quand le code est copié/collé.
* Sélectionnez **ASP.NET Core 2.1** dans la liste déroulante, puis sélectionnez **Application web**.

Pour obtenir des images des étapes précédentes, consultez [créer une Razor application Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Exécutez l’application.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Configurer le style du site

Quelques changements permettent de définir le menu, la disposition et la page d’accueil du site. Mettez à jour *Pages/Shared/_Layout.cshtml* avec les changements suivants :

* Remplacez chaque occurrence de « ContosoUniversity » par « Contoso University ». Il y a trois occurrences.

* Ajoutez des entrées de menu pour **Students** , **Courses** , **Instructors** et **Departments** , et supprimez l’entrée de menu **Contact**.

Les modifications sont mises en surbrillance. (Tout le balisage n’est *pas* affiché.)

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

Dans *Pages/Index.cshtml* , remplacez le contenu du fichier par le code suivant afin de remplacer le texte sur ASP.NET et MVC par le texte concernant cette application :

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Créer le modèle de données

Créez des classes d’entités pour l’application Contoso University. Commencez par les trois entités suivantes :

![Diagramme du modèle de données Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Il existe une relation un-à-plusieurs entre les entités `Student` et `Enrollment`. Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`. Un étudiant peut s’inscrire à autant de cours qu’il le souhaite. Un cours peut avoir une quantité illimitée d’élèves inscrits.

Dans les sections suivantes, une classe pour chacune de ces entités est créée.

### <a name="the-student-entity"></a>L’entité Student

![Diagramme de l’entité Student](intro/_static/student-entity.png)

Créez un dossier *Models*. Dans le dossier *Models* , créez un fichier de classe nommé *Student.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

La propriété `ID` devient la colonne de clé primaire de la table de base de données qui correspond à cette classe. Par défaut, EF Core interprète une propriété nommée `ID` ou `classnameID` comme clé primaire. Dans `classnameID`, `classname` est le nom de la classe. L’autre clé primaire reconnue automatiquement est `StudentID` dans l’exemple précédent.

La `Enrollments` propriété est une [propriété de navigation](/ef/core/modeling/relationships). Les propriétés de navigation établissent des liaisons à d’autres entités qui sont associées à cette entité. Ici, la propriété `Enrollments` d’un `Student entity` contient toutes les entités `Enrollment` associées à ce `Student`. Par exemple, si une ligne Student dans la base de données a deux lignes Enrollment associées, la propriété de navigation `Enrollments` contient ces deux entités `Enrollment`. Une ligne `Enrollment` associée est une ligne qui contient la valeur de clé primaire de cette étudiant dans la colonne `StudentID`. Par exemple, supposez que l’étudiant avec ID=1 a deux lignes dans la table `Enrollment`. La table `Enrollment` a deux lignes avec `StudentID` = 1. `StudentID` est une clé étrangère dans la table `Enrollment` qui spécifie l’étudiant dans la table `Student`.

Si une propriété de navigation peut contenir plusieurs entités, la propriété de navigation doit être un type de liste, tel que `ICollection<T>`. Vous pouvez spécifier `ICollection<T>`, ou un type tel que `List<T>` ou `HashSet<T>`. Quand vous utilisez `ICollection<T>`, EF Core crée une collection `HashSet<T>` par défaut. Les propriétés de navigation qui contiennent plusieurs entités proviennent de relations plusieurs-à-plusieurs et un-à-plusieurs.

### <a name="the-enrollment-entity"></a>L’entité Enrollment

![Diagramme de l’entité Enrollment](intro/_static/enrollment-entity.png)

Dans le dossier *Models* , créez un fichier *Enrollment.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

La propriété `EnrollmentID` est la clé primaire. Cette entité utilise le modèle `classnameID` au lieu de `ID` comme l’entité `Student`. En général, les développeurs choisissent un modèle et l’utilisent dans tout le modèle de données. Dans un prochain didacticiel, nous verrons qu’utiliser ID sans classname simplifie l’implémentation de l’héritage dans le modèle de données.

La propriété `Grade` est un `enum`. Le point d’interrogation après la déclaration de type `Grade` indique que la propriété `Grade` est nullable. Une note (Grade) qui a la valeur Null est différente d’une note égale à zéro : la valeur Null signifie qu’une note n’est pas connue ou n’a pas encore été affectée.

La propriété `StudentID` est une clé étrangère, et la propriété de navigation correspondante est `Student`. Une entité `Enrollment` est associée à une entité `Student`. Par conséquent, la propriété contient une seule entité `Student`. L’entité `Student` diffère de la propriété de navigation `Student.Enrollments`, qui contient plusieurs entités `Enrollment`.

La propriété `CourseID` est une clé étrangère, et la propriété de navigation correspondante est `Course`. Une entité `Enrollment` est associée à une entité `Course`.

EF Core interprète une propriété en tant que clé étrangère si elle se nomme `<navigation property name><primary key property name>`. Par exemple, `StudentID` pour la propriété de navigation `Student`, puisque la clé primaire de l’entité `Student` est `ID`. Les propriétés de clé étrangère peuvent également se nommer `<primary key property name>`. Par exemple, `CourseID` puisque la clé primaire de l’entité `Course` est `CourseID`.

### <a name="the-course-entity"></a>L’entité Course

![Diagramme de l’entité Course](intro/_static/course-entity.png)

Dans le dossier *Models* , créez un fichier *Course.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

La propriété `Enrollments` est une propriété de navigation. Une entité `Course` peut être associée à un nombre quelconque d’entités `Enrollment`.

L’attribut `DatabaseGenerated` permet à l’application de spécifier la clé primaire, plutôt que de la faire générer par la base de données.

## <a name="scaffold-the-student-model"></a>Générer automatiquement le modèle d’étudiant

Dans cette section, le modèle d’étudiant est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création (Create), de lecture (Read), de mise à jour (Update) et de suppression (Delete) (CRUD) pour le modèle d’étudiant.

* Créez le projet.
* Créez le dossier *Pages/Students*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Pages/Students* > **Ajouter** > **Nouvel élément généré automatiquement**.
* Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :

* Dans la liste déroulante **Classe de modèle** , sélectionnez **Student (ContosoUniversity.Models)**.
* Dans la ligne **Classe de contexte de données** , sélectionnez le signe **+** (plus) et remplacez le nom généré par **ContosoUniversity.Models.SchoolContext**.
* Dans la liste déroulante **Classe de contexte de données** , sélectionnez **ContosoUniversity.Models.SchoolContext**
* Sélectionnez **Ajouter**.

![Boîte de dialogue CRUD](intro/_static/s1.png)

Consultez [Générer automatiquement le modèle de film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) si vous rencontrez un problème à l’étape précédente.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez les commandes suivantes pour générer automatiquement le modèle d’étudiant.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Le processus de génération de modèles automatique a créé et changé les fichiers suivants :

### <a name="files-created"></a>Fichiers créés

* *Pages/Students* Create, Delete, Details, Edit, Index.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Mises à jour du fichier

* *Startup.cs*  : Les changements apportés à ce fichier sont détaillés dans la section suivante.
* *appsettings.json* : La chaîne de connexion utilisée pour se connecter à une base de données locale est ajoutée.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le didacticiel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.

Examinez la méthode `ConfigureServices` dans *Startup.cs*. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

## <a name="update-main"></a>Mettre à jour la méthode Main

Dans *Program.cs* , modifiez la méthode `Main` pour effectuer les opérations suivantes :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendances.
* Appelez [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Supprimez le contexte une fois la méthode `EnsureCreated` exécutée.

Le code suivant montre le fichier *Program.cs* mis à jour.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` garantit que la base de données existe pour le contexte. Si elle existe, aucune action n’est effectuée. Si elle n’existe pas, la base de données et tous ses schéma sont créés. `EnsureCreated` n’utilise pas de migrations pour créer la base de données. Une base de données créée avec `EnsureCreated` ne peut pas être mise à jour à l’aide de migrations par la suite.

`EnsureCreated` est appelée au démarrage de l’application, ce qui active le flux de travail suivant :

* Supprimez la base de données.
* Modification du schéma de base de données (par exemple, ajout d’un champ `EmailAddress`).
* Exécutez l’application.
* `EnsureCreated` crée une base de données avec la colonne `EmailAddress`.

`EnsureCreated` est pratique au début du développement quand le schéma évolue rapidement. Plus loin dans le tutoriel, la base de données est supprimée et les migrations sont utilisées.

### <a name="test-the-app"></a>Tester l'application

Exécutez l’application et acceptez la cookie stratégie. Cette application ne conserve pas les informations personnelles. Pour en savoir plus sur la cookie stratégie au niveau de la [prise en charge de l’union européenne règlement général sur la protection des données (RGPD)](xref:security/gdpr).

* Sélectionnez le lien **Students** , puis **Créer nouveau**.
* Testez les liens Edit, Details et Delete.

## <a name="examine-the-schoolcontext-db-context"></a>Examiner le contexte de base de données SchoolContext

La classe principale qui coordonne les fonctionnalités d’EF Core pour un modèle de données spécifié est la classe de contexte de base de données. Le contexte de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données. Dans ce projet, la classe est nommée `SchoolContext`.

Mettez à jour *SchoolContext.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Le code en surbrillance crée une propriété [DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour chaque jeu d’entités. Dans la terminologie d’EF Core :

* Un jeu d’entités correspond généralement à une table de base de données.
* Une entité correspond à une ligne dans la table.

`DbSet<Enrollment>` et `DbSet<Course>` peuvent être omis. EF Core les inclut implicitement, car l’entité `Student` référence l’entité `Enrollment`, et l’entité `Enrollment` référence l’entité `Course`. Pour ce didacticiel, conservez `DbSet<Enrollment>` et `DbSet<Course>` dans le `SchoolContext`.

### <a name="sql-server-express-localdb"></a>Base de données locale SQL Server Express

La chaîne de connexion spécifie [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB est une version allégée du moteur de base de données SQL Server Express. Elle est destinée au développement d’applications, et non à une utilisation en production. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, LocalDB crée des fichiers de base de données *.mdf* dans le répertoire `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Ajouter du code pour initialiser la base de données avec des données de test

EF Core crée une base de données vide. Dans cette section, une méthode `Initialize` est écrite pour la remplir avec des données de test.

Dans le dossier *Data* , créez un fichier de classe nommé *DbInitializer.cs* et ajoutez le code suivant :

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Remarque : le code précédent utilise `Models` pour l’espace de noms ( `namespace ContosoUniversity.Models` ) au lieu de `Data` . `Models` est cohérent avec le code généré par l’outil de génération de modèles automatique. Pour plus d’informations, consultez [ce problème de génération de modèles automatique GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Le code vérifie s’il existe des étudiants dans la base de données. S’il n’y en a pas, la base de données est initialisée avec des données de test. Il charge les données de test dans des tableaux plutôt que des collections `List<T>` afin d’optimiser les performances.

La méthode `EnsureCreated` crée automatiquement la base de données pour le contexte de base de données. Si la base de données existe, `EnsureCreated` retourne sans modifier la base de données.

Dans *Program.cs* , modifiez la méthode `Main` pour appeler `Initialize` :

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Arrêtez l’application si elle est en cours d’exécution et exécutez la commande suivante dans la **Console du gestionnaire de package**  :

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Arrêtez l’application si elle est en cours d’exécution, puis supprimez le fichier *CU.db*.

---

## <a name="view-the-db"></a>Afficher la base de données

Le nom de la base de données est généré à partir du nom de contexte indiqué précédemment, ainsi que d’un tiret et d’un GUID. Il sera donc « SchoolContext-{GUID} ». Le GUID est différent pour chaque utilisateur.
Ouvrez **l’Explorateur d’objets SQL Server** (SSOX) à partir du menu **Affichage** de Visual Studio.
Dans SSOX, cliquez sur **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.

Développez le nœud **Tables**.

Cliquez avec le bouton droit sur la table **Student** et cliquez sur **Afficher les données** pour voir les colonnes créées et les lignes insérées dans la table.

## <a name="asynchronous-code"></a>Code asynchrone

La programmation asynchrone est le mode par défaut pour ASP.NET Core et EF Core.

Un serveur web a un nombre limité de threads disponibles et, dans les situations de forte charge, tous les threads disponibles peuvent être utilisés. Quand cela se produit, le serveur ne peut pas traiter de nouvelle requête tant que les threads ne sont pas libérés. Avec le code synchrone, plusieurs threads peuvent être bloqués alors qu’ils n’effectuent en fait aucun travail, car ils attendent que des E/S se terminent. Avec le code asynchrone, quand un processus attend que des E/S se terminent, son thread est libéré afin d’être utilisé par le serveur pour traiter d’autres demandes. Il permet ainsi d’utiliser les ressources serveur plus efficacement, et le serveur peut gérer plus de trafic sans retard.

Le code asynchrone introduit néanmoins une petite surcharge au moment de l’exécution. Dans les situations de faible trafic, le gain de performances est négligeable, tandis qu’en cas de trafic élevé l’amélioration potentielle des performances est importante.

Dans le code suivant, le mot clé [async](/dotnet/csharp/language-reference/keywords/async), la valeur renvoyée `Task<T>`, le mot clé `await` et la méthode `ToListAsync` déclenchent l’exécution asynchrone du code.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Le mot clé `async` fait en sorte que le compilateur :
  * Génère des rappels pour les parties du corps de méthode.
  * Crée automatiquement l’objet [Task](/dotnet/api/system.threading.tasks.task) qui est retourné. Pour plus d’informations, consultez [Type de retour Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Le type de retour implicite `Task` représente le travail en cours.
* Le mot clé `await` indique au compilateur de fractionner la méthode en deux parties. La première partie se termine par l’opération qui est démarrée de façon asynchrone. La seconde partie est placée dans une méthode de rappel qui est appelée quand l’opération se termine.
* `ToListAsync` est la version asynchrone de la méthode d’extension `ToList`.

Voici quelques éléments à connaître lors de l’écriture de code asynchrone qui utilise EF Core :

* Seules les instructions qui provoquent l’envoi de requêtes ou de commandes vers la base de données sont exécutées de façon asynchrone. Cela comprend `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` et `SaveChangesAsync`, mais pas les instructions qui ne font que changer un `IQueryable`, telles que `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contexte EF Core n’est pas thread-safe : n’essayez pas d’effectuer plusieurs opérations en parallèle.
* Pour tirer parti des avantages de performances du code asynchrone, vérifiez que les packages de bibliothèque (par exemple pour la pagination) utilisent le mode asynchrone s’ils appellent des méthodes EF Core qui envoient des requêtes à la base de données.

Pour plus d’informations sur la programmation asynchrone dans .NET, consultez [Vue d’ensemble d’Async](/dotnet/standard/async) et [Programmation asynchrone avec async et await](/dotnet/csharp/programming-guide/concepts/async/).

Dans le didacticiel suivant, nous allons examiner les opérations CRUD de base (créer, lire, mettre à jour, supprimer).



## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Next](xref:data/ef-rp/crud)

::: moniker-end
