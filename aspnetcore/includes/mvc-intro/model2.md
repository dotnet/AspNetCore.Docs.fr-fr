::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Créez un dossier nommé *Data*.

Ajoutez la classe `MvcMovieContext` suivante au dossier *Data* :  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Le code précédent crée une propriété `DbSet` pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au *appsettings.jssur* le fichier :

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Ajouter des packages NuGet des outils EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Inscrire le contexte de base de données

En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Générez le projet en tant que vérification des erreurs du compilateur.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ajoutez la classe `MvcMovieContext` suivante au dossier *Models* :  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

Le code précédent crée une propriété `DbSet` pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données, et une entité correspond à une ligne dans la table.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Ajouter une chaîne de connexion de base de données

Ajoutez une chaîne de connexion au *appsettings.jssur* le fichier :

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Ajouter les packages NuGet exigés

Exécutez la commande CLI .NET Core suivante pour ajouter SQLite et CodeGeneration.Design au projet :

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

Le package `Microsoft.VisualStudio.Web.CodeGeneration.Design` est nécessaire à la génération de modèles automatique.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Inscrire le contexte de base de données

En tête du fichier *Startup.cs*, ajoutez les instructions `using` suivantes :

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Inscrivez le contexte de base de données auprès du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) dans `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Générez le projet en tant que vérification des erreurs.
::: moniker-end