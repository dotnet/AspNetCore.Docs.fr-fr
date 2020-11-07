---
title: Partie 2, ajouter un modèle
author: rick-anderson
description: Partie 2 de la série de didacticiels sur les Razor pages. Dans cette section, des classes de modèle sont ajoutées.
ms.author: riande
ms.date: 09/30/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: ee1b7d77d8d209a11669ba29c8a951c59368180e
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361024"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Partie 2, ajouter un modèle à une Razor application pages dans ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Dans cette section, des classes sont ajoutées pour la gestion des films dans une base de données. Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données. EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données. Vous écrivez d’abord les classes de modèle, et EF Core crée la base de données.

Les classes de modèle sont appelées classes POCO (à partir de « **P** Lain- **O** LD **C** LR **O** bjets »), car elles n’ont pas de dépendance sur EF Core. Elles définissent les propriétés des données stockées dans la base de données.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Ajouter un modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet *Razor PagesMovie* > **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *modèles*.
1. Cliquez avec le bouton droit sur le dossier *modèles* . Sélectionnez **Ajouter** une  >  **classe**. Nommez la classe *Movie*.
1. Ajoutez les propriétés suivantes à la classe `Movie` :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer des informations d’heure dans le champ date.
  * Seule la date est affichée, pas les informations de temps.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Ajoutez un dossier nommé *Models*.
1. Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.

Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
  * Seule la date est affichée, pas les informations de temps.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Ajouter des packages NuGet des outils EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>Ajouter une classe de contexte de base de données

1. Dans le projet *Razor PagesMovie* , créez un dossier nommé *Data*.
1. Dans le dossier *Data* , ajoutez un fichier nommé *Razor PagesMovieContext.cs* avec le code suivant :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   Le code précédent crée une propriété `DbSet` pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table. Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Inscrire le contexte de base de données

1. En tête du fichier *Startup.cs* , ajoutez les instructions `using` suivantes :

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. Inscrivez le contexte de base de données avec le conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices` :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la **fenêtre outil de solution** , cliquez avec le contrôle sur le projet *Razor PagesMovie* , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.
1. Cliquez avec le contrôle sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.
1. Dans la boîte de dialogue **Nouveau fichier** :
   1. Dans le volet gauche, sélectionnez **Général**.
   1. Dans le volet central, sélectionnez **Classe vide**.
   1. Nommez la classe **Movie** , puis sélectionnez **Nouveau**.

1. Ajoutez les propriétés suivantes à la classe `Movie` :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
  * Seule la date est affichée, pas les informations de temps.

---

Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.

Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.

## <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle de film

Dans cette section, le modèle de film est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique produit des pages pour Create les opérations, lire, mettre à jour et Delete (CRUD) pour le modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Create un dossier *pages/movies* :
   1. Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.
   1. Nommez le dossier *films*.

1. Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.

   ![Image illustrant les instructions précédentes.](model/_static/5/sca.png)

1. Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.

   ![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

1. Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :
   1. Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.
   1. Dans la ligne **Classe du contexte de données** , sélectionnez le signe **+** (plus).
      1. Dans la boîte de dialogue **Ajouter un contexte de données** , nommez la classe *Razor PagesMovie. Data. Razor PagesMovieContext* est généré.
   1. Sélectionnez **Ajouter**.

   ![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Ouvrez une interface de commande dans le répertoire du projet, qui contient les fichiers *Program.cs* , *Startup.cs* et *. csproj* .

* **Pour Windows** : exécutez la commande suivante :

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pour macOS et Linux** , exécutez la commande suivante :

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> Le tableau suivant détaille les options du générateur de code ASP.NET Core.

| Option               | Description|
| ----------------- | ------------ |
| `-m`  | Nom du modèle. |
| `-dc`  | Classe `DbContext` à utiliser. |
| `-udl` | Utiliser la disposition par défaut. |
| `-outDir` | Chemin du dossier de sortie relatif pour créer les vues. |
| `--referenceScriptLibraries` | Ajoute `_ValidationScriptsPartial` à la modification et aux Create pages |

Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Utiliser SQLite pour le développement, SQL Server pour la production

Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement. Le code suivant montre comment injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` . `IWebHostEnvironment` est injecté afin que l’application puisse utiliser SQLite dans le développement et SQL Server en production.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Create un dossier *pages/movies* :
   1. Cliquez avec le contrôle sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.
   1. Nommez le dossier *films*.

1. Cliquez avec le contrôle sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.

   ![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

1. Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant**.

   ![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

1. Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :
   1. Dans la **classe DbContext à utiliser :** Row, nommez la classe *Razor PagesMovie. Data. Razor PagesMovieContext*.
   1. Sélectionnez **Terminer**.

   ![Image illustrant les instructions précédentes.](model/_static/5/arpMac.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Utiliser SQLite pour le développement, SQL Server pour la production

Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement. Le code suivant montre comment injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` . `IWebHostEnvironment` est injecté afin que l’application puisse utiliser SQLite dans le développement et SQL Server en production.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Fichiers créés

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

* *Pages/films* : Create , Delete , détails, modifier et Index .
* *Données/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Le processus de génération de modèles automatique crée les fichiers suivants :

* *Pages/films* : Create , Delete , détails, modifier et Index .

Les fichiers créés sont expliqués dans la section suivante.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

* *Pages/films* : Create , Delete , détails, modifier et Index .
* *Données/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a>Create schéma de base de données initial à l’aide de la fonctionnalité de migration d’EF

La fonctionnalité migrations de Entity Framework Core offre un moyen d’effectuer les opérations suivantes :

* Create schéma de base de données initial.
* Mettez à jour de façon incrémentielle le schéma de base de données pour le maintenir synchronisé avec le modèle de données de l’application.  Les données existantes de la base de données sont conservées.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans cette section, la fenêtre **console du gestionnaire de package** (PMC) permet d’effectuer les opérations suivantes :

* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

1. Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.

   ![Menu Console du Gestionnaire de package](model/_static/5/pmc.png)

1. Dans la console du gestionnaire de package, entrez les commandes suivantes :

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* Exécutez les commandes CLI .NET suivantes :

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ». Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »

Ignorez l’avertissement, car il sera traité dans une étape ultérieure.

La commande `migrations` génère du code pour créer le schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans `DbContext` . L’argument `InitialCreate` est utilisé pour nommer les migrations. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.

La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées. Dans ce cas, `update` exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _Initial Create . cs* , qui crée la base de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Les services, tels que le contexte de base de données EF Core, sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code de constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.

Examinez la méthode `Startup.ConfigureServices`. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que Create , lire, mettre à jour et Delete , pour le `Movie` modèle. Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Il spécifie les entités qui sont incluses dans le modèle de données.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une [propriété \<Movie> DbSet](xref:Microsoft.EntityFrameworkCore.DbSet%601) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Examinez la méthode `Up`.

---

<a name="test"></a>

## <a name="test-the-app"></a>Tester l'application

1. Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).

   Si vous recevez l’erreur suivante :

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   Vous avez manqué [l’étape des migrations](#pmc).

1. Testez le **Create** lien.

   ![::: No-Loc (créer) ::: page](model/_static/conan5.png)

   > [!NOTE]
   > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

1. Testez la **modification** , les **Détails** et les **Delete** liens.

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Dans cette section, des classes sont ajoutées pour la gestion des films. Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données. EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.

Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core. Elles définissent les propriétés des données stockées dans la base de données.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Ajouter un modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *modèles*.

Cliquez avec le bouton droit sur le dossier *modèles* . Sélectionnez **Ajouter** une  >  **classe**. Nommez la classe **Movie**.

Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
  * Seule la date est affichée, pas les informations de temps.

Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models*.
* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.

Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
  * Seule la date est affichée, pas les informations de temps.

Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Ajouter des packages NuGet des outils EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Ajouter une classe de contexte de base de données

* Dans le projet *Razor PagesMovie* , créez un dossier nommé *Data*.
* Ajoutez la classe `RazorPagesMovieContext` suivante au dossier *Data*  :

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une propriété `DbSet` pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table. Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Inscrire le contexte de base de données

En tête du fichier *Startup.cs* , ajoutez les instructions `using` suivantes :

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans la **fenêtre outil de solution** , cliquez avec le contrôle sur le projet **Razor PagesMovie** , puis sélectionnez **Ajouter** > **un nouveau dossier...**. Nommez le dossier *modèles*.
* Cliquez avec le bouton droit sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...**.
* Dans la boîte de dialogue **Nouveau fichier** :

  * Dans le volet gauche, sélectionnez **Général**.
  * Dans le volet central, sélectionnez **Classe vide**.
  * Nommez la classe **Movie** , puis sélectionnez **Nouveau**.

Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
  * Seule la date est affichée, pas les informations de temps.

---

Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.

Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.

## <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle de film

Dans cette section, le modèle de film est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique produit des pages pour Create les opérations, lire, mettre à jour et Delete (CRUD) pour le modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Create un dossier *pages/movies* :

* Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.
* Nommez le dossier *films*.

Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.

![Image illustrant les instructions précédentes.](model/_static/sca.png)

Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :

* Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.
* Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus), puis modifiez le nom généré à partir de Razor PagesMovie. **Modèles**. Razor PagesMovieContext à Razor PagesMovie. **Données**. Razor PagesMovieContext. [Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise. Elle crée la classe de contexte de base de données avec l’espace de noms correct.
* Sélectionnez **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Ouvrez une fenêtre de commande dans le répertoire du projet, qui contient les fichiers *Program.cs* , *Startup.cs* et *. csproj* .

* **Pour Windows** : exécutez la commande suivante :

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pour macOS et Linux** , exécutez la commande suivante :

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> Le tableau suivant détaille les options du générateur de code ASP.NET Core :

| Option               | Description|
| ----------------- | ------------ |
| `-m`  | Nom du modèle. |
| `-dc`  | Classe `DbContext` à utiliser. |
| `-udl` | Utiliser la disposition par défaut. |
| `-outDir` | Chemin du dossier de sortie relatif pour créer les vues. |
| `--referenceScriptLibraries` | Ajoute `_ValidationScriptsPartial` à la modification et aux Create pages |

Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Utiliser SQLite pour le développement, SQL Server pour la production

Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement. Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage. `IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Create un dossier *pages/movies* :

* Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.
* Nommez le dossier *films*.

Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...**.

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant**.

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :

* Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie ( Razor PagesMovie. Models)**.
* Dans la ligne de la **classe de contexte de données** , tapez le nom de la nouvelle classe, Razor PagesMovie. **Données**. Razor PagesMovieContext. [Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise. Elle crée la classe de contexte de base de données avec l’espace de noms correct.
* Sélectionnez **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

### <a name="add-ef-tools"></a>Ajouter des outils EF

Exécutez la commande CLI .NET Core suivante :

```dotnetcli
dotnet tool install --global dotnet-ef
```

La commande précédente ajoute les outils de Entity Framework Core pour le CLI .NET Core. Pour plus d’informations, consultez [référence des outils de Entity Framework Core-CLI .net Core](/ef/core/miscellaneous/cli/dotnet).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Utiliser SQLite pour le développement, SQL Server pour la production

Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement. Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage. `IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Fichiers créés

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

* *Pages/films* : Create , Delete , détails, modifier et Index .
* *Données/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

* *Pages/films* : Create , Delete , détails, modifier et Index .
* *Données/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Le processus de génération de modèles automatique crée les fichiers suivants :

* *Pages/films* : Create , Delete , détails, modifier et Index .

Les fichiers créés sont expliqués dans la section suivante.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migration initiale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans cette section, la console du gestionnaire de package est utilisée pour :

* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Exécutez les commandes CLI .NET Core suivantes :

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ». Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »

Ignorez l’avertissement, car il sera traité dans une étape ultérieure.

La commande migrations génère du code pour créer le schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans `DbContext` . L’argument `InitialCreate` est utilisé pour nommer les migrations. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.

La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées. Dans ce cas, `update` exécute la `Up` méthode dans le fichier  *migrations/ \<time-stamp> _Initial Create . cs* , qui crée la base de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Les services, tels que le contexte de contexte de la base de données EF Core, sont inscrits avec l’injection de dépendance pendant le démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services, tels que les Razor pages, via des paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de contexte de base de données est indiqué plus loin dans ce didacticiel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.

Examinez la méthode `Startup.ConfigureServices`. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalité, telles que Create , lire, mettre à jour et Delete , pour le `Movie` modèle. Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Il spécifie les entités qui sont incluses dans le modèle de données.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Examinez la méthode `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Tester l'application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).

Si vous obtenez cette erreur :

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Vous avez manqué [l’étape des migrations](#pmc).

* Testez le **Create** lien.

  ![::: No-Loc (créer) ::: page](model/_static/conan5.png)

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez la **modification** , les **Détails** et les **Delete** liens.

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

Dans cette section, des classes sont ajoutées pour la gestion des films dans une [base de données SQLite](https://www.sqlite.org/index.html)multiplateforme. Les applications créées à partir d’un modèle de ASP.NET Core utilisent une base de données SQLite. Les classes de modèle de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([fournisseur de base de données SQLite EF Core](/ef/core/providers/sqlite)) pour fonctionner avec la base de données. EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.

Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core. Elles définissent les propriétés des données stockées dans la base de données.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Ajouter un modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *modèles*.

Cliquez avec le bouton droit sur le dossier *modèles* . Sélectionnez **Ajouter** une  >  **classe**. Nommez la classe **Movie**.

Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
  * Seule la date est affichée, pas les informations de temps.

Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models*.
* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.

Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
  * Seule la date est affichée, pas les informations de temps.

Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Ajouter des packages NuGet des outils EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Ajouter une classe de contexte de base de données

Dans le Razor projet PagesMovie, créez un dossier nommé *Data*. 
Ajoutez la classe `RazorPagesMovieContext` suivante au dossier *Data*  :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une propriété `DbSet` pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table. Le code n’est pas compilé tant que les dépendances n’ont pas été ajoutées dans une étape ultérieure.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au *appsettings.json* fichier comme indiqué dans le code en surbrillance suivant :

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a>Ajouter les packages NuGet exigés

Exécutez la commande CLI .NET Core suivante pour ajouter SQLite et CodeGeneration. Design au projet :

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

Le package `Microsoft.VisualStudio.Web.CodeGeneration.Design` est nécessaire à la génération de modèles automatique.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Inscrire le contexte de base de données

En tête du fichier *Startup.cs* , ajoutez les instructions `using` suivantes :

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Générez le projet en tant que vérification des erreurs.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans la **fenêtre outil** de la solution, cliquez avec le contrôle sur le projet *Razor PagesMovie* , puis sélectionnez **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *modèles*.
* Cliquez avec le contrôle sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier**.
* Dans la boîte de dialogue **Nouveau fichier** :

  * Dans le volet gauche, sélectionnez **Général**.
  * Dans le volet central, sélectionnez **Classe vide**.
  * Nommez la classe **Movie** , puis sélectionnez **Nouveau**.

Ajoutez les propriétés suivantes à la classe `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

La classe `Movie` contient :

* Le champ `ID` est requis par la base de données pour la clé primaire.
* `[DataType(DataType.Date)]`: L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Avec cet attribut :

  * L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
  * Seule la date est affichée, pas les informations de temps.

Les [DataAnnotations](xref:System.ComponentModel.DataAnnotations) sont traitées dans un prochain didacticiel.

---

Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.

## <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle de film

Dans cette section, le modèle de film est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique produit des pages pour Create les opérations, lire, mettre à jour et Delete (CRUD) pour le modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Create un dossier *pages/movies* :

* Cliquez avec le bouton droit sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.
* Nommez le dossier *films*.

Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.

![Image illustrant les instructions précédentes.](model/_static/sca.png)

Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)**.
* Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus) et acceptez le nom généré **Razor PagesMovie. Models. Razor PagesMovieContext**.
* Sélectionnez **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/arp.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Ouvrez une fenêtre de commande dans le répertoire du projet, qui contient les fichiers *Program.cs* , *Startup.cs* et *. csproj* .

* **Pour Windows** : exécutez la commande suivante :

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pour macOS et Linux** , exécutez la commande suivante :

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> Le tableau suivant détaille les options du générateur de code ASP.NET Core :

| Option               | Description|
| ----------------- | ------------ |
| `-m`  | Nom du modèle. |
| `-dc`  | Classe `DbContext` à utiliser. |
| `-udl` | Utiliser la disposition par défaut. |
| `-outDir` | Chemin du dossier de sortie relatif pour créer les vues. |
| `--referenceScriptLibraries` | Ajoute `_ValidationScriptsPartial` à la modification et aux Create pages |

Utilisez l' `-h` option pour obtenir de l’aide sur la `aspnet-codegenerator razorpage` commande :

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Pour plus d’informations, consultez [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Create un dossier *pages/movies* :

* Cliquez avec le contrôle sur le dossier *pages* > **Ajouter** > **un nouveau dossier**.
* Nommez le dossier *films*.

Cliquez avec le contrôle sur le dossier *pages/movies* > **Ajouter** > **un nouvel élément de génération de modèles** automatique.

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

Dans la boîte de dialogue **Ajouter une nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :

* Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie**.
* Dans la ligne de la **classe de contexte de données** , tapez Select the **Razor PagesMovieContext** This crée une nouvelle classe de contexte de base de données avec l’espace de noms correct. Dans ce cas, il s’agit de **Razor PagesMovie. Models. Razor PagesMovieContext**.
* Sélectionnez **Ajouter**.

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

---

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

### <a name="files-created"></a>Fichiers créés

* *Pages/films* : Create , Delete , détails, modifier et Index .
* *Données/ Razor PagesMovieContext.cs*

### <a name="file-updated"></a>Fichier mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migration initiale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans cette section, la console du gestionnaire de package est utilisée pour :

* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration Initial
Update-Database
```

La commande `Add-Migration` génère du code pour créer le schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans le `DbContext` , dans le fichier *Razor PagesMovieContext.cs* . L' `InitialCreate` argument est utilisé pour nommer la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé. Pour plus d'informations, consultez <xref:data/ef-mvc/migrations>.

La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _Initial Create . cs* . La méthode `Up` crée la base de données.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Exécutez les commandes CLI .NET Core suivantes :

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ». Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »

Ignorez l’avertissement, car il sera traité dans une étape ultérieure.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Les services (tels que le contexte de contexte de la base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de contextB du contexte de base de données est indiqué plus loin dans ce didacticiel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de contexte de base de données et l’a inscrit auprès du conteneur d’injection de dépendance.

Examinez la méthode `Startup.ConfigureServices`. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

Les `RazorPagesMovieContext` coordonnées EF Core fonctionnalités, telles que Create , lire, mettre à jour et Delete , pour le `Movie` modèle. Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext). Il spécifie les entités qui sont incluses dans le modèle de données.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions). Pour le développement local, le [système de configuration](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Examinez la méthode `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Tester l'application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).

Si vous obtenez cette erreur :

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Vous avez manqué [l’étape des migrations](#pmc).

* Testez le **Create** lien.

  ![::: No-Loc (créer) ::: page](model/_static/conan.png)

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez la **modification** , les **Détails** et les **Delete** liens.

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)

::: moniker-end
