---
title: 'Partie 4 : travailler avec une base de données'
author: rick-anderson
description: Partie 4 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 09/26/2020
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3e78b5b6dab7145413ae8612bfeb352f328ec86a
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360741"
---
# <a name="part-4-work-with-a-database-and-aspnet-core"></a>Partie 4 : travailler avec une base de données et ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

L’objet `RazorPagesMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données. Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` de *Startup.cs*  :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Le système de [Configuration](xref:fundamentals/configuration/index) ASP.net Core lit la `ConnectionString` clé. Pour le développement local, la configuration obtient la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La chaîne de connexion générée est semblable à ce qui suit :

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Lorsque l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur de base de données de test ou de production. Pour plus d’informations, consultez [configuration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Base de données locale SQL Server Express

LocalDB est une version allégée du moteur de base de données SQL Server Express, qui est ciblée pour le développement de programmes. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, la base de données LocalDB crée des fichiers `*.mdf` dans le répertoire `C:\Users\<user>\`.

<a name="ssox"></a>
1. Dans le menu **Affichage** , ouvrez **l’Explorateur d’objets SQL Server** (SSOX).

   ![Menu Affichage](sql/_static/5/ssox.png)

1. Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **Concepteur de vues** :

   ![Les menus contextuels s’ouvrent dans la table Movie](sql/_static/5/design.png)

   ![Les tables Movie s’ouvrent dans le concepteur](sql/_static/dv.png)

   Notez l’icône de clé en regard de `ID`. Par défaut, EF crée une propriété nommée `ID` pour la clé primaire.

1. Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **afficher les données** :

   ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

Le site web [SQLite](https://www.sqlite.org/) indique :

> SQLite est un moteur de base de données SQL autonome, embarqué, à haute fiabilité, aux fonctionnalités complètes et accessible dans le domaine public. SQLite est le moteur de base de données le plus utilisé au monde.

Vous pouvez télécharger de nombreux outils tiers pour gérer et afficher une base de données SQLite. L’image ci-dessous provient de [DB Browser for SQLite](https://sqlitebrowser.org/). Si vous avez un outil SQLite préféré, laissez un commentaire pour nous dire ce que vous aimez chez lui.

![Explorateur de bases de données pour SQLite avec la base de données Movie](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Pour ce didacticiel, la fonctionnalité de *migrations* Entity Framework Core est utilisée dans la mesure du possible. Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données. Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées. Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification. Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue. En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite. Au lieu de cela, lorsque le schéma est modifié, la base de données est supprimée et recréée.
>
>Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié. La regénération d’un tableau implique :
>
>* La création d’un nouveau tableau.
>* La copie de données de l’ancien tableau vers le nouveau.
>* La suppression de l’ancien tableau.
>* Renommer la nouvelle table.
>
>Pour plus d’informations, consultez les ressources suivantes :
> * [Limites d’un fournisseur de base de données EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Personnaliser le code de migration](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Amorçage des données](/ef/core/modeling/data-seeding)
> * [Instruction SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Amorcer la base de données

Create une nouvelle classe nommée `SeedData` dans le dossier *Models* avec le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Si la base de données contient des films, l’initialiseur de valeur initiale retourne et aucun film n’est ajouté.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Ajouter l’initialiseur de valeur initiale

Remplacez le contenu du fichier *Program.cs* par le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

Dans le code précédent, la `Main` méthode a été modifiée pour effectuer les opérations suivantes :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.
* Appelez la `seedData.Initialize` méthode, en lui transmettant l’instance de contexte de base de données.
* Supprimer le contexte une fois la méthode seed terminée. L' [instruction using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantit que le contexte est supprimé.

L’exception suivante se produit lorsque `Update-Database` n’a pas été exécuté :

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Tester l'application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Delete tous les enregistrements de la base de données. Utilisez les liens supprimer dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox)

1. Force l’initialisation de l’application en appelant les méthodes de la `Startup` classe, de sorte que la méthode Seed s’exécute. Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré. Arrêtez et redémarrez IIS avec l’une des approches suivantes :

   1. Cliquez avec le bouton droit sur l’icône IIS Express de la barre d’état système dans la zone de notification et sélectionnez **quitter** ou **arrêter le site** :

      ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menu contextuel](sql/_static/stopIIS.png)

   1. Si l’application s’exécute en mode sans débogage, appuyez sur <kbd>F5</kbd> pour l’exécuter en mode débogage.
   1. Si l’application est en mode débogage, arrêtez le débogueur et appuyez sur <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Delete tous les enregistrements de la base de données, la méthode Seed est donc exécutée. Arrêtez et démarrez l’application pour amorcer la base de données.

---

L’application affiche les données de départ :

![Application vidéo ouverte dans le navigateur pour afficher les données de film](sql/_static/5/m55.png)

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page) 
>  [suivant : mettre à jour les pages](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

L’objet `RazorPagesMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données. Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` de *Startup.cs*  :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Le système de [Configuration](xref:fundamentals/configuration/index) ASP.net Core lit la `ConnectionString` clé. Pour le développement local, la configuration obtient la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La chaîne de connexion générée est semblable à ce qui suit :

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Lorsque l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur de base de données de test ou de production. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Base de données locale SQL Server Express

LocalDB est une version allégée du moteur de base de données SQL Server Express, qui est ciblée pour le développement de programmes. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, la base de données LocalDB crée des fichiers `*.mdf` dans le répertoire `C:\Users\<user>\`.

<a name="ssox"></a>
* Dans le menu **Affichage** , ouvrez **l’Explorateur d’objets SQL Server** (SSOX).

  ![Menu Affichage](sql/_static/ssox.png)

* Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **Concepteur de vues** :

  ![Les menus contextuels s’ouvrent dans la table Movie](sql/_static/design.png)

  ![Les tables Movie s’ouvrent dans le concepteur](sql/_static/dv.png)

Notez l’icône de clé en regard de `ID`. Par défaut, EF crée une propriété nommée `ID` pour la clé primaire.

* Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **afficher les données** :

  ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

Le site web [SQLite](https://www.sqlite.org/) indique :

> SQLite est un moteur de base de données SQL autonome, embarqué, à haute fiabilité, aux fonctionnalités complètes et accessible dans le domaine public. SQLite est le moteur de base de données le plus utilisé au monde.

Vous pouvez télécharger de nombreux outils tiers pour gérer et afficher une base de données SQLite. L’image ci-dessous provient de [DB Browser for SQLite](https://sqlitebrowser.org/). Si vous avez un outil SQLite préféré, laissez un commentaire pour nous dire ce que vous aimez chez lui.

![Explorateur de bases de données pour SQLite avec la base de données Movie](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Pour ce didacticiel, la fonctionnalité de *migrations* Entity Framework Core est utilisée dans la mesure du possible. Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données. Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées. Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification. Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue. En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite. Au lieu de cela, lorsque le schéma est modifié, la base de données est supprimée et recréée.
>
>Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié. La regénération d’un tableau implique :
>
>* La création d’un nouveau tableau.
>* La copie de données de l’ancien tableau vers le nouveau.
>* La suppression de l’ancien tableau.
>* Renommer la nouvelle table.
>
>Pour plus d’informations, consultez les ressources suivantes :
> * [Limites d’un fournisseur de base de données EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Personnaliser le code de migration](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Amorçage des données](/ef/core/modeling/data-seeding)
> * [Instruction SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Amorcer la base de données

Create une nouvelle classe nommée `SeedData` dans le dossier *Models* avec le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Si la base de données contient des films, l’initialiseur de valeur initiale retourne et aucun film n’est ajouté.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Ajouter l’initialiseur de valeur initiale

Remplacez le contenu du fichier *Program.cs* par le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Dans le code précédent, la `Main` méthode a été modifiée pour effectuer les opérations suivantes :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.
* Appelez la `seedData.Initialize` méthode, en lui transmettant l’instance de contexte de base de données.
* Supprimer le contexte une fois la méthode seed terminée. L' [instruction using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantit que le contexte est supprimé.

L’exception suivante se produit lorsque `Update-Database` n’a pas été exécuté :

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Tester l'application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Delete tous les enregistrements de la base de données. Utilisez les liens supprimer dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Force l’initialisation de l’application en appelant les méthodes de la `Startup` classe, de sorte que la méthode Seed s’exécute. Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré. Arrêtez et redémarrez IIS avec l’une des approches suivantes :

  * Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site** :

    ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](sql/_static/stopIIS.png)

    * Si l’application s’exécute en mode sans débogage, appuyez sur <kbd>F5</kbd> pour l’exécuter en mode débogage.
    * Si l’application est en mode débogage, arrêtez le débogueur et appuyez sur <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Delete tous les enregistrements de la base de données, la méthode Seed est donc exécutée. Arrêtez et démarrez l’application pour amorcer la base de données.

---

L’application affiche les données de départ :

![Application Movie ouverte dans Chrome, affichant les données relatives aux films](sql/_static/m55https.png)

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page) 
>  [suivant : mettre à jour les pages](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

L’objet `RazorPagesMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données. Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` de *Startup.cs*  :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Pour plus d’informations sur les méthodes utilisées dans `ConfigureServices`, consultez :

* [Prise en charge du règlement général sur la protection des données (RGPD) de l’Union Européenne dans ASP.NET Core](xref:security/gdpr) pour `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

Le système de [Configuration](xref:fundamentals/configuration/index) ASP.net Core lit la `ConnectionString` clé. Pour le développement local, la configuration obtient la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La chaîne de connexion générée est semblable à ce qui suit :

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Lorsque l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur de base de données de test ou de production. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Base de données locale SQL Server Express

LocalDB est une version allégée du moteur de base de données SQL Server Express, qui est ciblée pour le développement de programmes. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, la base de données LocalDB crée des fichiers `*.mdf` dans le répertoire `C:/Users/<user/>`.

<a name="ssox"></a>
* Dans le menu **Affichage** , ouvrez **l’Explorateur d’objets SQL Server** (SSOX).

  ![Menu Affichage](sql/_static/ssox.png)

* Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **Concepteur de vues** :

  ![Menu contextuel ouvert sur la table Movie](sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](sql/_static/dv.png)

Notez l’icône de clé en regard de `ID`. Par défaut, EF crée une propriété nommée `ID` pour la clé primaire.

* Cliquez avec le bouton droit sur la `Movie` table et sélectionnez **afficher les données** :

  ![Table Movie ouverte, affichant des données de table](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Amorcer la base de données

Create une nouvelle classe nommée `SeedData` dans le dossier *Models* avec le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Si la base de données contient des films, l’initialiseur de valeur initiale retourne et aucun film n’est ajouté.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Ajouter l’initialiseur de valeur initiale

Remplacez le contenu du fichier *Program.cs* par le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Dans le code précédent, la `Main` méthode a été modifiée pour effectuer les opérations suivantes :

* Obtenir une instance de contexte de base de données à partir du conteneur d’injection de dépendance.
* Appelez la `seedData.Initialize` méthode, en lui transmettant l’instance de contexte de base de données.
* Supprimer le contexte une fois la méthode seed terminée. L' [instruction using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantit que le contexte est supprimé.

Une application de production n’appelle pas `Database.Migrate`. Il est ajouté au code précédent afin d’éviter l’exception suivante quand `Update-Database` n’a pas été exécutée :

SqlException : impossible d’ouvrir la base de données « Razor PagesMovieContext-21 » demandée par la connexion. La connexion a échoué.
Échec de la connexion de l’utilisateur 'nom utilisateur'.

### <a name="test-the-app"></a>Tester l'application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Delete tous les enregistrements de la base de données. Vous pouvez le faire avec les liens supprimer dans le navigateur ou à partir de [SSOX](xref:tutorials/razor-pages/new-field#ssox)
* Force l’initialisation de l’application en appelant les méthodes de la `Startup` classe, de sorte que la méthode Seed s’exécute. Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré. Pour cela, adoptez l’une des approches suivantes :

  * Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site** :

    ![Icône de la barre d’état système IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](sql/_static/stopIIS.png)

    * Si l’application s’exécute en mode sans débogage, appuyez sur <kbd>F5</kbd> pour l’exécuter en mode débogage.
    * Si l’application est en mode débogage, arrêtez le débogueur et appuyez sur <kbd>F5</kbd>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Delete tous les enregistrements de la base de données, la méthode Seed est donc exécutée. Arrêtez et démarrez l’application pour amorcer la base de données.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Delete tous les enregistrements de la base de données, la méthode Seed est donc exécutée. Arrêtez et démarrez l’application pour amorcer la base de données.

---

L’application affiche les données de départ :

![Application Movie ouverte dans Chrome, affichant les données relatives aux films](sql/_static/m55https.png)

Le didacticiel suivant nettoie la présentation des données.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Précédent : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page) 
>  [suivant : mettre à jour les pages](xref:tutorials/razor-pages/da1)

::: moniker-end
