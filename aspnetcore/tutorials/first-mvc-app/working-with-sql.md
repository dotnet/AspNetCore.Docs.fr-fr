---
title: 'Partie 5 : travailler avec une base de données dans une application ASP.NET Core MVC'
author: rick-anderson
description: 'Partie 5 : ajout d’un modèle à une application ASP.NET Core MVC'
ms.author: riande
ms.date: 11/10/2020
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
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: c082457258346637499b8ea8000dd8792931112f
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422650"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a>Partie 5 : travailler avec une base de données dans une application ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

L’objet `MvcMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données. Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` du fichier *Startup.cs* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`. Pour le développement local, elle obtient la chaîne de connexion à partir du *appsettings.json* fichier :

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`. Pour le développement local, elle obtient la chaîne de connexion à partir du *appsettings.json* fichier :

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Quand l’application est déployée sur un serveur de test ou de production, une variable d’environnement peut être utilisée pour définir la chaîne de connexion à un serveur SQL Server de production. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Base de données locale SQL Server Express

LocalDB est une version allégée du moteur de base de données SQL Server Express qui est ciblée pour le développement de programmes. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, la base de données LocalDB crée des fichiers *.mdf* dans le répertoire *C:/Users/{utilisateur}*.

* Dans le menu **Affichage** , ouvrez **l’Explorateur d’objets SQL Server** (SSOX).

  ![Menu Affichage](working-with-sql/_static/ssox.png)

* Cliquez avec le bouton droit sur la table `Movie`**> Concepteur de vue**.

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](working-with-sql/_static/dv.png)

Notez l’icône de clé en regard de `ID`. Par défaut, EF fait d’une propriété nommée `ID` la clé primaire.

* Cliquez avec le bouton droit sur la table `Movie`**> Afficher les données**

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/ssox2.png)

  ![Table Movie ouverte, affichant des données de table](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Amorcer la base de données

Créez une classe nommée `SeedData` dans l’espace de noms *Modèles*. Remplacez le code généré par ce qui suit :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

Si la base de données contient des films, l’initialiseur de valeur initiale retourne une valeur et aucun film n’est ajouté.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Ajouter l’initialiseur de valeur initiale

Remplacez le contenu du fichier *Program.cs* par le code suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

Tester l'application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Supprimez tous les enregistrements de la base de données. Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de SSOX.
* Forcez l’application à s’initialiser (appelez les méthodes de la classe `Startup`) pour que la méthode seed s’exécute. Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré. Pour cela, adoptez l’une des approches suivantes :

  * Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site**.

    ![Icône de la barre d’état système IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](working-with-sql/_static/stopIIS.png)

    * Si vous exécutiez Visual Studio en mode non-débogage, appuyez sur F5 pour l’exécuter en mode débogage.
    * Si vous exécutiez Visual Studio en mode débogage, arrêtez le débogueur et appuyez sur F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute). Arrêtez et démarrez l’application pour amorcer la base de données.

---

L’application affiche les données de départ.

![Application MVC Movie ouverte dans Microsoft Edge, affichant les données relatives aux films](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [Précédent](adding-model.md) 
>  [Suivant](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

L’objet `MvcMovieContext` gère la tâche de connexion à la base de données et de mappage d’objets `Movie` à des enregistrements de la base de données. Le contexte de base de données est inscrit auprès du conteneur [Injection de dépendances](xref:fundamentals/dependency-injection) dans la méthode `ConfigureServices` du fichier *Startup.cs* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`. Pour le développement local, elle obtient la chaîne de connexion à partir du *appsettings.json* fichier :

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Le système de [configuration](xref:fundamentals/configuration/index) d’ASP.NET Core lit `ConnectionString`. Pour le développement local, elle obtient la chaîne de connexion à partir du *appsettings.json* fichier :

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Quand vous déployez l’application sur un serveur de test ou de production, vous pouvez utiliser une variable d’environnement ou une autre approche pour définir un serveur SQL Server réel comme chaîne de connexion. Pour plus d’informations, consultez [Configuration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>Base de données locale SQL Server Express

LocalDB est une version allégée du moteur de base de données SQL Server Express qui est ciblée pour le développement de programmes. LocalDB démarre à la demande et s’exécute en mode utilisateur, ce qui n’implique aucune configuration complexe. Par défaut, la base de données LocalDB crée des fichiers *.mdf* dans le répertoire *C:/Users/{utilisateur}*.

* Dans le menu **Affichage** , ouvrez **l’Explorateur d’objets SQL Server** (SSOX).

  ![Menu Affichage](working-with-sql/_static/ssox.png)

* Cliquez avec le bouton droit sur la table `Movie`**> Concepteur de vue**.

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/design.png)

  ![Table Movie ouverte dans le Concepteur](working-with-sql/_static/dv.png)

Notez l’icône de clé en regard de `ID`. Par défaut, EF fait d’une propriété nommée `ID` la clé primaire.

* Cliquez avec le bouton droit sur la table `Movie`**> Afficher les données**

  ![Menu contextuel ouvert sur la table Movie](working-with-sql/_static/ssox2.png)

  ![Table Movie ouverte, affichant des données de table](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Amorcer la base de données

Créez une classe nommée `SeedData` dans l’espace de noms *Modèles*. Remplacez le code généré par ce qui suit :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

Si la base de données contient des films, l’initialiseur de valeur initiale retourne une valeur et aucun film n’est ajouté.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Ajouter l’initialiseur de valeur initiale

Remplacez le contenu du fichier *Program.cs* par le code suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

Tester l'application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Supprimez tous les enregistrements de la base de données. Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de SSOX.
* Forcez l’application à s’initialiser (appelez les méthodes de la classe `Startup`) pour que la méthode seed s’exécute. Pour forcer l’initialisation, IIS Express doit être arrêté et redémarré. Pour cela, adoptez l’une des approches suivantes :

  * Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la zone de notification, puis appuyez sur **Quitter** ou sur **Arrêter le site**.

    ![Icône de la barre d’état système IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menu contextuel](working-with-sql/_static/stopIIS.png)

    * Si vous exécutiez Visual Studio en mode non-débogage, appuyez sur F5 pour l’exécuter en mode débogage.
    * Si vous exécutiez Visual Studio en mode débogage, arrêtez le débogueur et appuyez sur F5.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Supprimez tous les enregistrements dans la base de données (pour que la méthode seed s’exécute). Arrêtez et démarrez l’application pour amorcer la base de données.

---

L’application affiche les données de départ.

![Application MVC Movie ouverte dans Microsoft Edge, affichant les données relatives aux films](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [Précédent](adding-model.md) 
>  [Suivant](controller-methods-views.md)

::: moniker-end
