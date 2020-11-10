---
title: 'Partie 4 : ajouter un modèle à une application ASP.NET Core MVC'
author: rick-anderson
description: Partie 4 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422732"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a>Partie 4 : ajouter un modèle à une application ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/tdykstra)

Dans cette section, vous allez ajouter des classes pour la gestion des films dans une base de données. Ces classes constituent la partie « **M** odèle » de l’application **M** VC.

Vous utilisez ces classes avec [Entity Framework Core](/ef/core) (EF Core) pour travailler avec une base de données. EF Core est un framework de mappage relationnel d’objets qui simplifie le code d’accès aux données à écrire.

Les classes de modèle que vous créez portent le nom de classes OCT (« **O** bjet **C** LR **T** raditionnel ») **,** car elles n’ont pas de dépendances envers EF Core. Elles définissent simplement les propriétés des données stockées dans la base de données.

Dans ce didacticiel, vous écrivez d’abord les classes du modèle, puis EF Core crée la base de données.

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a>Ajouter une classe de modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**. Nommez le fichier *Movie.cs*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez un fichier nommé *Movie.cs* au dossier *Models*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** une classe  >  **New Class**  >  **vide** Class. Nommez le fichier *Movie.cs*.

---

Mettez le fichier *Movie.cs* à jour avec le contenu suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

La classe `Movie` contient un champ `Id`, qui est nécessaire à la base de données pour la clé primaire.

L' <xref:System.ComponentModel.DataAnnotations.DataType> attribut sur `ReleaseDate` spécifie le type des données ( `Date` ). Avec cet attribut :

* L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
* Seule la date est affichée, pas les informations de temps.

Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.

## <a name="add-nuget-packages"></a>Ajouter des packages NuGet

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).

![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, exécutez la commande suivante :

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

La commande précédente ajoute le fournisseur EF Core SQL Server. Le package du fournisseur installe le package EF Core en tant que dépendance. Plus loin dans ce tutoriel, d’autres packages sont installés automatiquement lors de l’étape de génération de modèles automatique.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Dans le menu **projet** , sélectionnez **gérer les packages NuGet**.

Dans le champ de **recherche** en haut à droite, entrez `Microsoft.EntityFrameworkCore.SQLite` et appuyez sur la touche **retour** pour effectuer la recherche. Sélectionnez le package NuGet correspondant, puis cliquez sur le bouton **Ajouter un package** .

![Ajouter Entity Framework Core package NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

La boîte de dialogue **Sélectionner les projets** s’affiche, avec le `MvcMovie` projet sélectionné. Appuyez sur le bouton **OK** .

Une boîte de dialogue d' **acceptation de licence** s’affiche. Passez en revue les licences comme vous le souhaitez, puis cliquez sur le bouton **accepter** .

Répétez les étapes ci-dessus pour installer les packages NuGet suivants :

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Créer une classe de contexte de base de données

Une classe de contexte de base de données est nécessaire pour coordonner les fonctionnalités d’EF Core (créer, lire, mettre à jour, supprimer) pour le modèle `Movie`. Le contexte de base de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et il spécifie les entités à inclure dans le modèle de données.

Créez un dossier nommé *Data*.

Ajoutez un fichier *Data/MvcMovieContext.cs* avec le code suivant : 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Inscrire le contexte de base de données

ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection). Les services (tels que le contexte de base de données EF Core) doivent être inscrits auprès de l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel. Dans cette section, vous allez inscrire le contexte de base de données auprès du conteneur d’injection de dépendances.

En tête du fichier *Startup.cs* , ajoutez les instructions `using` suivantes :

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au *appsettings.json* fichier :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Générez le projet en tant que vérification des erreurs du compilateur.

## <a name="scaffold-movie-pages"></a>Générer automatiquement des pages de film

Utilisez l’outil de génération de modèles automatique pour créer, lire, mettre à jour et supprimer des pages du modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Contrôleurs* , puis choisissez **Ajouter > Nouvel élément généré automatiquement**.

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

Dans la boîte de dialogue **Ajouter un modèle automatique** , sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

Renseignez la boîte de dialogue **Ajouter un contrôleur**  :

* **Classe de modèle :** *Movie (MvcMovie.Models)*
* **Classe de contexte de données :** *MvcMovieContext (MvcMovie. Data)*

![Ajouter un contexte de données](adding-model/_static/dc5.png)

* **Affichages :** conservez la valeur par défaut de chaque option activée.
* **Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.
* Sélectionnez **Ajouter**

Visual Studio crée :

* Un contrôleur de films ( *Controllers/MoviesController.cs* )
* Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index ( *views/movies/ \* . cshtml* )

La création automatique de ces fichiers est appelée *génération de modèles automatique*.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).

* Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).

* Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Vous ne pouvez pas encore utiliser les pages générées automatiquement, car la base de données n’existe pas. Si vous exécutez l’application et cliquez sur le lien de l' **application de film** , vous recevez un message d’erreur Impossible d' *ouvrir la base de données* ou une *table de ce type :* message d’erreur.

<a name="migration"></a>

## <a name="initial-migration"></a>Migration initiale

Utilisez la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core pour créer la base de données. La fonctionnalité Migrations est un ensemble d’outils qui vous permettent de créer et de mettre à jour une base de données pour qu’elle corresponde à votre modèle de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* . L’argument `InitialCreate` est le nom de la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné. Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données. Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.

* `Update-Database`: Met à jour la base de données avec la dernière migration, créée par la commande précédente. La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs* , ce qui entraîne la création de la base de données.

  La commande « database update » génère l’avertissement suivant : 

  > Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'. Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant ’HasColumnType()’.

  Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Exécutez les commandes CLI .NET Core suivantes :

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* . L’argument `InitialCreate` est le nom de la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné. Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données. Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs* ).

* `ef database update`: Met à jour la base de données avec la dernière migration, créée par la commande précédente. La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs* , ce qui entraîne la création de la base de données.

---

### <a name="the-initialcreate-class"></a>La classe InitialCreate

Examinez le fichier de migration *Migrations/{horodatage}_InitialCreate.cs*  :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

La méthode `Up` crée la table Movie et configure `Id` comme la clé primaire. La méthode `Down` rétablit les modifications de schéma provoquées par la migration `Up`.

<a name="test"></a>

## <a name="test-the-app"></a>Tester l'application

* Exécutez l’application et cliquez sur le lien **Movie App**.

  Si vous obtenez une exception similaire à celle-ci :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Il est probable que vous n’ayez pas effectué l’[étape de migration](#migration).

* Testez la page **Create**. Entrez et envoyez des données.

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez les pages **Edit** , **Details** et **Delete**.

## <a name="dependency-injection-in-the-controller"></a>Injection de dépendances dans le contrôleur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Utiliser SQLite pour le développement, SQL Server pour la production

Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement. Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage. `IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modèles fortement typés et mot clé @model

Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`. Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.

Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue. Cette approche fortement typée permet de vérifier votre code au moment de la compilation. Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et des vues.

Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Le paramètre `id` est généralement passé en tant que données de routage. Par exemple, `https://localhost:5001/movies/details/1` définit :

* Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).
* L’action sur `details` (le deuxième segment de l’URL).
* L’ID sur 1 (le dernier segment de l’URL).

Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :

`https://localhost:5001/movies/details?id=1`

Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.

Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :

```csharp
return View(movie);
```

Examinez le contenu du fichier *Views/Movies/Details.cshtml* :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

L’instruction `@model` située en haut du fichier de la vue spécifie le type d’objet attendu par la vue. Lorsque le contrôleur de film était créé, l’instruction `@model` suivante était incluse :

```cshtml
@model MvcMovie.Models.Movie
```

Cette directive `@model` autorise l’accès au film que le contrôleur a passé à la vue. L’objet `Model` est fortement typé. Par exemple, dans la vue *Details.cshtml* , le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé. Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.

Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies. Notez comment le code crée un objet `List` quand il appelle la méthode `View`. Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Lors de la création du contrôleur du film, la génération de modèles automatique a inclus l’instruction `@model` suivante en haut du fichier *Index.cshtml*  :

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé. Par exemple, dans la vue *Index.cshtml* , le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`. Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
* [Globalisation et localisation](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation de SQL](working-with-sql.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a>Ajouter une classe de modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**. Nommez le fichier *Movie.cs*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez un fichier nommé *Movie.cs* au dossier *Models*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** une classe  >  **New Class**  >  **vide** Class. Nommez le fichier *Movie.cs*.

---

Mettez le fichier *Movie.cs* à jour avec le contenu suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

La classe `Movie` contient un champ `Id`, qui est nécessaire à la base de données pour la clé primaire.

L' <xref:System.ComponentModel.DataAnnotations.DataType> attribut sur `ReleaseDate` spécifie le type des données ( `Date` ). Avec cet attribut :

* L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.
* Seule la date est affichée, pas les informations de temps.

Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.

## <a name="add-nuget-packages"></a>Ajouter des packages NuGet

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).

![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, exécutez la commande suivante :

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

La commande précédente ajoute le fournisseur EF Core SQL Server. Le package du fournisseur installe le package EF Core en tant que dépendance. Plus loin dans ce tutoriel, d’autres packages sont installés automatiquement lors de l’étape de génération de modèles automatique.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Dans le menu **projet** , sélectionnez **gérer les packages NuGet**.

Dans le champ de **recherche** en haut à droite, entrez `Microsoft.EntityFrameworkCore.SQLite` et appuyez sur la touche **retour** pour effectuer la recherche. Sélectionnez le package NuGet correspondant, puis cliquez sur le bouton **Ajouter un package** .

![Ajouter Entity Framework Core package NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

La boîte de dialogue **Sélectionner les projets** s’affiche, avec le `MvcMovie` projet sélectionné. Appuyez sur le bouton **OK** .

Une boîte de dialogue d' **acceptation de licence** s’affiche. Passez en revue les licences comme vous le souhaitez, puis cliquez sur le bouton **accepter** .

Répétez les étapes ci-dessus pour installer les packages NuGet suivants :

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Créer une classe de contexte de base de données

Une classe de contexte de base de données est nécessaire pour coordonner les fonctionnalités d’EF Core (créer, lire, mettre à jour, supprimer) pour le modèle `Movie`. Le contexte de base de données est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et il spécifie les entités à inclure dans le modèle de données.

Créez un dossier nommé *Data*.

Ajoutez un fichier *Data/MvcMovieContext.cs* avec le code suivant : 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Inscrire le contexte de base de données

ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection). Les services (tels que le contexte de base de données EF Core) doivent être inscrits auprès de l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel. Dans cette section, vous allez inscrire le contexte de base de données auprès du conteneur d’injection de dépendances.

En tête du fichier *Startup.cs* , ajoutez les instructions `using` suivantes :

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au *appsettings.json* fichier :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Générez le projet en tant que vérification des erreurs du compilateur.

## <a name="scaffold-movie-pages"></a>Générer automatiquement des pages de film

Utilisez l’outil de génération de modèles automatique pour créer, lire, mettre à jour et supprimer des pages du modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Contrôleurs* , puis choisissez **Ajouter > Nouvel élément généré automatiquement**.

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

Dans la boîte de dialogue **Ajouter un modèle automatique** , sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

Renseignez la boîte de dialogue **Ajouter un contrôleur**  :

* **Classe de modèle :** *Movie (MvcMovie.Models)*
* **Classe de contexte de données :** *MvcMovieContext (MvcMovie. Data)*

![Ajouter un contexte de données](adding-model/_static/dc3.png)

* **Affichages :** conservez la valeur par défaut de chaque option activée.
* **Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.
* Sélectionnez **Ajouter**

Visual Studio crée :

* Un contrôleur de films ( *Controllers/MoviesController.cs* )
* Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index ( *views/movies/ \* . cshtml* )

La création automatique de ces fichiers est appelée *génération de modèles automatique*.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).

* Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).

* Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Vous ne pouvez pas encore utiliser les pages générées automatiquement, car la base de données n’existe pas. Si vous exécutez l’application et cliquez sur le lien de l' **application de film** , vous recevez un message d’erreur Impossible d' *ouvrir la base de données* ou une *table de ce type :* message d’erreur.

<a name="migration"></a>

## <a name="initial-migration"></a>Migration initiale

Utilisez la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core pour créer la base de données. La fonctionnalité Migrations est un ensemble d’outils qui vous permettent de créer et de mettre à jour une base de données pour qu’elle corresponde à votre modèle de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* . L’argument `InitialCreate` est le nom de la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné. Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données. Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`.

* `Update-Database`: Met à jour la base de données avec la dernière migration, créée par la commande précédente. La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs* , ce qui entraîne la création de la base de données.

  La commande « database update » génère l’avertissement suivant : 

  > Aucun type n’a été spécifié pour la colonne décimale 'Price' sur le type d’entité 'Movie'. Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant ’HasColumnType()’.

  Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Exécutez les commandes CLI .NET Core suivantes :

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Génère un fichier de migration *_InitialCreate. cs migrations/{timestamp}* . L’argument `InitialCreate` est le nom de la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné. Étant donné qu’il s’agit de la première migration, la classe générée contient du code permettant de créer le schéma de la base de données. Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs* ).

* `ef database update`: Met à jour la base de données avec la dernière migration, créée par la commande précédente. La commande exécute la méthode `Up` dans le fichier *Migrations/{horodatage}_InitialCreate.cs* , ce qui entraîne la création de la base de données.

---

### <a name="the-initialcreate-class"></a>La classe InitialCreate

Examinez le fichier de migration *Migrations/{horodatage}_InitialCreate.cs*  :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

La méthode `Up` crée la table Movie et configure `Id` comme la clé primaire. La méthode `Down` rétablit les modifications de schéma provoquées par la migration `Up`.

<a name="test"></a>

## <a name="test-the-app"></a>Tester l'application

* Exécutez l’application et cliquez sur le lien **Movie App**.

  Si vous obtenez une exception similaire à celle-ci :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Il est probable que vous n’ayez pas effectué l’[étape de migration](#migration).

* Testez la page **Create**. Entrez et envoyez des données.

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez les pages **Edit** , **Details** et **Delete**.

## <a name="dependency-injection-in-the-controller"></a>Injection de dépendances dans le contrôleur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Utiliser SQLite pour le développement, SQL Server pour la production

Lorsque SQLite est sélectionné, le code généré par le modèle est prêt pour le développement. Le code suivant montre comment injecter au <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> démarrage. `IWebHostEnvironment` est injecté afin de `ConfigureServices` pouvoir utiliser SQLite dans le développement et SQL Server en production.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modèles fortement typés et mot clé @model

Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`. Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.

Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue. Cette approche fortement typée permet de vérifier votre code au moment de la compilation. Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et des vues.

Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Le paramètre `id` est généralement passé en tant que données de routage. Par exemple, `https://localhost:5001/movies/details/1` définit :

* Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).
* L’action sur `details` (le deuxième segment de l’URL).
* L’ID sur 1 (le dernier segment de l’URL).

Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :

`https://localhost:5001/movies/details?id=1`

Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.

Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :

```csharp
return View(movie);
```

Examinez le contenu du fichier *Views/Movies/Details.cshtml* :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

L’instruction `@model` située en haut du fichier de la vue spécifie le type d’objet attendu par la vue. Lorsque le contrôleur de film était créé, l’instruction `@model` suivante était incluse :

```cshtml
@model MvcMovie.Models.Movie
```

Cette directive `@model` autorise l’accès au film que le contrôleur a passé à la vue. L’objet `Model` est fortement typé. Par exemple, dans la vue *Details.cshtml* , le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé. Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.

Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies. Notez comment le code crée un objet `List` quand il appelle la méthode `View`. Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Lors de la création du contrôleur du film, la génération de modèles automatique a inclus l’instruction `@model` suivante en haut du fichier *Index.cshtml*  :

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé. Par exemple, dans la vue *Index.cshtml* , le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`. Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
* [Globalisation et localisation](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation de SQL](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Ajouter une classe de modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le dossier *Models* > **Ajouter** > **Classe**. Nommez la classe **Movie**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle de film

Dans cette section, le modèle de film est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur le dossier *Contrôleurs* , puis choisissez **Ajouter > Nouvel élément généré automatiquement**.

![affichage de l’étape ci-dessus](adding-model/_static/add_controller21.png)

Dans la boîte de dialogue **Ajouter un modèle automatique** , sélectionnez **Contrôleur MVC avec vues, utilisant Entity Framework > Ajouter**.

![Boîte de dialogue Ajouter un modèle automatique](adding-model/_static/add_scaffold21.png)

Renseignez la boîte de dialogue **Ajouter un contrôleur**  :

* **Classe de modèle :** *Movie (MvcMovie.Models)*
* **Classe de contexte de données :** sélectionnez l’icône **+** et ajoutez le **MvcMovie.Models.MvcMovieContext** par défaut.

![Ajouter un contexte de données](adding-model/_static/dc.png)

* **Affichages :** conservez la valeur par défaut de chaque option activée.
* **Nom du contrôleur :** conservez la valeur par défaut *MoviesController*.
* Sélectionnez **Ajouter**

![Boîte de dialogue Ajouter un contrôleur](adding-model/_static/add_controller2.png)

Visual Studio crée :

* Une [classe de contexte de base de données](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core ( *Data/MvcMovieContext.cs* )
* Un contrôleur de films ( *Controllers/MoviesController.cs* )
* Razor afficher des fichiers pour les pages Create, Delete, Details, Edit et index ( *views/movies/ \* . cshtml* )

La création automatique du contexte de base de données et de méthodes d’action et de vues [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (créer, lire, mettre à jour et supprimer) porte le nom de *génération de modèles automatique*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).
* Installez l’outil de génération de modèles automatique :

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Sur Linux, exportez le chemin de l’outil de génération de modèles automatique :

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Exécutez la commande suivante :

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).
* Installez l’outil de génération de modèles automatique :

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Exécutez la commande suivante :

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Si vous exécutez l’application et que vous cliquez sur le lien **Mvc Movie** , vous recevez une erreur semblable à la suivante :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Vous devez créer la base de données, et vous utilisez pour cela la fonctionnalité [Migrations](xref:data/ef-mvc/migrations) d’EF Core. Les migrations permettent de créer une base de données qui correspond à votre modèle de données, et de mettre à jour le schéma de base de données quand votre modèle de données change.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migration initiale

Dans cette section, vous devez effectuer les tâches suivantes :

* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package** (PMC).

   ![Menu Console du Gestionnaire de package](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. Dans la console du gestionnaire de package, entrez les commandes suivantes :

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   La commande `Add-Migration` génère du code pour créer le schéma de base de données initial.

   Le schéma de base de données est basé sur le modèle spécifié dans la classe `MvcMovieContext`. L’argument `Initial` est le nom de la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé. Pour plus d'informations, consultez <xref:data/ef-mvc/migrations>.

   La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/{Time-horodatage} _InitialCreate. cs* , qui crée la base de données.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

La commande `ef migrations add InitialCreate` génère du code pour créer le schéma de base de données initial.

Le schéma de base de donénes est basé sur le modèle spécifié dans la classe `MvcMovieContext` (dans *Data/MvcMovieContext.cs* ). L’argument `InitialCreate` est le nom de la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances (DI)](xref:fundamentals/dependency-injection). Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.

Examinez la méthode `Startup.ConfigureServices` suivante. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

`MvcMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`. Le contexte de données (`MvcMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Vous avez créé un contexte de base de données et vous l’avez inscrit dans le conteneur d’injection de dépendances.

---

<a name="test"></a>

### <a name="test-the-app"></a>Tester l'application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).

Si vous obtenez une exception de base de données similaire à ce qui suit :

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Vous avez manqué [l’étape des migrations](#pmc).

* Testez le lien **Créer**. Entrez et envoyez des données.

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez les liens **Edit** , **Details** et **Delete**.

Examiner la classe `Startup` :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Le code précédent mis en surbrillance montre le contexte de la base de données des films qui est ajouté au conteneur [d’injection de dépendance](xref:fundamentals/dependency-injection) :

* `services.AddDbContext<MvcMovieContext>(options =>` spécifie la base de données à utiliser et la chaîne de connexion.
* `=>` est un [opérateur lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

Ouvrez le fichier *Controllers/MoviesController.cs* et examinez le constructeur :

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Le constructeur utilise une [injection de dépendance](xref:fundamentals/dependency-injection) pour injecter le contexte de base de données (`MvcMovieContext`) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modèles fortement typés et mot clé @model

Plus tôt dans ce didacticiel, vous avez vu comment un contrôleur peut passer des données ou des objets à une vue en utilisant le dictionnaire `ViewData`. Le dictionnaire `ViewData` est un objet dynamique qui fournit un moyen pratique d’effectuer une liaison tardive pour passer des informations à une vue.

Le modèle MVC fournit également la possibilité de passer des objets de modèle fortement typés à une vue. Cette approche fortement typée permet une meilleure vérification de votre code au moment de la compilation. Le mécanisme de génération de modèles automatique a utilisé cette approche (c’est-à-dire passer un modèle fortement typé) avec la classe `MoviesController` et les vues quand il a créé les méthodes et les vues.

Examinez la méthode `Details` générée dans le fichier *Controllers/MoviesController.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Le paramètre `id` est généralement passé en tant que données de routage. Par exemple, `https://localhost:5001/movies/details/1` définit :

* Le contrôleur sur le contrôleur `movies` (le premier segment de l’URL).
* L’action sur `details` (le deuxième segment de l’URL).
* L’ID sur 1 (le dernier segment de l’URL).

Vous pouvez aussi passer `id` avec une requête de chaîne, comme suit :

`https://localhost:5001/movies/details?id=1`

Le `id` paramètre est défini en tant que [type Nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) au cas où une valeur d’ID n’est pas fournie.

Une [expression lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) est passée à `FirstOrDefaultAsync` pour sélectionner les entités de film qui correspondent aux données de routage ou à la valeur de la chaîne de requête.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Si un film est trouvé, une instance du modèle `Movie` est passée à la vue `Details` :

```csharp
return View(movie);
   ```

Examinez le contenu du fichier *Views/Movies/Details.cshtml* :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

En incluant une instruction `@model` en haut du fichier de la vue, vous pouvez spécifier le type d’objet attendu par la vue. Quand vous avez créé le contrôleur pour les films, l’instruction `@model` suivante a été incluse automatiquement en haut du fichier *Details.cshtml* :

```cshtml
@model MvcMovie.Models.Movie
```

Cette directive `@model` vous permet d’accéder au film que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé. Par exemple, dans la vue *Details.cshtml* , le code passe chaque champ du film aux Helpers HTML `DisplayNameFor` et `DisplayFor` avec l’objet `Model` fortement typé. Les méthodes et les vues `Create` et `Edit` passent aussi un objet du modèle `Movie`.

Examinez la vue *Index.cshtml* et la méthode `Index` dans le contrôleur Movies. Notez comment le code crée un objet `List` quand il appelle la méthode `View`. Le code passe cette liste `Movies` de la méthode d’action `Index` à la vue :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Quand vous avez créé le contrôleur pour les films, la génération de modèles automatique a inclus automatiquement l’instruction `@model` suivante en haut du fichier *Index.cshtml* :

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

La directive `@model` vous permet d’accéder à la liste des films que le contrôleur a passé à la vue en utilisant un objet `Model` qui est fortement typé. Par exemple, dans la vue *Index.cshtml* , le code boucle dans les films avec une instruction `foreach` sur l’objet `Model` fortement typé :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Comme l’objet `Model` est fortement typé (en tant qu’objet `IEnumerable<Movie>`), chaque élément de la boucle est typé en tant que `Movie`. Entre autres avantages, cela signifie que votre code est vérifié au moment de la compilation :

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
* [Globalisation et localisation](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Ajout d’une vue précédente](adding-view.md) 
>  [Utilisation d’une base de données](working-with-sql.md)

::: moniker-end
