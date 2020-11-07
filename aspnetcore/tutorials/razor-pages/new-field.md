---
title: Partie 7, ajouter un nouveau champ
author: rick-anderson
description: Partie 7 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2dca5a9552dd2800212f8cd78ace0578b3d38cdb
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360877"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a>Partie 7, ajouter un nouveau champ à une Razor page dans ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :

* Ajouter un nouveau champ au modèle.
* Migrer la nouvelle modification du schéma des champs vers la base de données.

Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :

* Ajoute une [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.
* Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.

Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Ajout d’une propriété Rating au modèle Movie

1. Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. Générez l'application.

1. Modifiez *pages/movies/ Index . cshtml* et ajoutez un `Rating` champ :

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. Mettez à jour les pages suivantes :
   1. Ajoutez le `Rating` champ aux Delete pages détails et.
   1. Mettez à jour [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) avec un `Rating` champ.
   1. Ajoutez le champ `Rating` à la Page Edit.

L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ. L’exécution de l’application sans mise à jour de la base de données lève une exception `SqlException` :

`SqlException: Invalid column name 'Rating'.`

L' `SqlException` exception est due au fait que la classe de modèle Movie mise à jour est différente du schéma de la table Movie de la base de données. Il n’y a aucune `Rating` colonne dans la table de base de données.

Plusieurs approches sont possibles pour résoudre l’erreur :

1. Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle. Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle. L’inconvénient est que vous perdez les données existantes dans la base de données. N’utilisez pas cette approche sur une base de données de production ! La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.

2. Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle. L’avantage de cette approche est de conserver les données. Effectuez cette modification manuellement ou en créant un script de modification de la base de données.

3. Utilisez les migrations Code First pour mettre à jour le schéma de base de données.

Pour ce didacticiel, nous allons utiliser les migrations Code First.

Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne. Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).

Générez la solution.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Ajouter une migration pour le champ d’évaluation

1. Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.
2. Dans la console du gestionnaire de package, entrez les commandes suivantes :

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

La commande `Add-Migration` indique au framework qu’il doit :

* Comparez le `Movie` modèle au `Movie` schéma de la base de données.
* Create code pour migrer le schéma de base de données vers le nouveau modèle.

Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration. Il est utile d’utiliser un nom explicite pour le fichier de migration.

La `Update-Database` commande indique à l’infrastructure d’appliquer les modifications de schéma à la base de données et de conserver les données existantes.

<a name="ssox"></a>

Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le `Rating` champ. Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).

Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données. Pour supprimer la base de données dans SSOX :

1. Sélectionnez la base de données dans SSOX.
1. Cliquez avec le bouton droit sur la base de données, puis sélectionnez **Delete** .
1. Cochez **Fermer les connexions existantes**.
1. Sélectionnez **OK**.
1. Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Supprimer et recréer la base de données

> [!NOTE]
> Pour ce didacticiel, vous utilisez la fonctionnalité *migrations* Entity Framework Core dans la mesure du possible. Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données. Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées. Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification. Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue. En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite. À la place, vous supprimez puis recréez la base de données quand le schéma change.
>
>Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié. La regénération d’un tableau implique :
>
>* La création d’un nouveau tableau.
>* La copie de données de l’ancien tableau vers le nouveau.
>* La suppression de l’ancien tableau.
>* Renommer la nouvelle table.
>
>Pour plus d’informations, consultez les ressources suivantes :
>
> * [Limites d’un fournisseur de base de données EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Personnaliser le code de migration](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Amorçage des données](/ef/core/modeling/data-seeding)
> * [Instruction SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

1. Delete dossier de migration.  

1. Utilisez les commandes suivantes pour recréer la base de données.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`. Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :

* Ajouter un nouveau champ au modèle.
* Migrer la nouvelle modification du schéma des champs vers la base de données.

Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :

* Ajoute une [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.
* Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.

Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Ajout d’une propriété Rating au modèle Movie

1. Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. Générez l'application.

1. Modifiez *pages/movies/ Index . cshtml* et ajoutez un `Rating` champ :

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. Mettez à jour les pages suivantes :
   1. Ajoutez le `Rating` champ aux Delete pages détails et.
   1. Mettez à jour [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) avec un `Rating` champ.
   1. Ajoutez le champ `Rating` à la Page Edit.

L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ. L’exécution de l’application sans mise à jour de la base de données lève une exception `SqlException` :

`SqlException: Invalid column name 'Rating'.`

L' `SqlException` exception est due au fait que la classe de modèle Movie mise à jour est différente du schéma de la table Movie de la base de données. Il n’y a aucune `Rating` colonne dans la table de base de données.

Plusieurs approches sont possibles pour résoudre l’erreur :

1. Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle. Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle. L’inconvénient est que vous perdez les données existantes dans la base de données. N’utilisez pas cette approche sur une base de données de production ! La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.

2. Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle. L’avantage de cette approche est de conserver les données. Effectuez cette modification manuellement ou en créant un script de modification de la base de données.

3. Utilisez les migrations Code First pour mettre à jour le schéma de base de données.

Pour ce didacticiel, nous allons utiliser les migrations Code First.

Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne. Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).

Générez la solution.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Ajouter une migration pour le champ d’évaluation

1. Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.
2. Dans la console du gestionnaire de package, entrez les commandes suivantes :

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

La commande `Add-Migration` indique au framework qu’il doit :

* Comparez le `Movie` modèle au `Movie` schéma de la base de données.
* Create code pour migrer le schéma de base de données vers le nouveau modèle.

Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration. Il est utile d’utiliser un nom explicite pour le fichier de migration.

La `Update-Database` commande indique à l’infrastructure d’appliquer les modifications de schéma à la base de données et de conserver les données existantes.

<a name="ssox"></a>

Si vous supprimez tous les enregistrements de la base de données, l’initialiseur amorce la base de données et inclut le `Rating` champ. Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).

Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données. Pour supprimer la base de données dans SSOX :

* Sélectionnez la base de données dans SSOX.
* Cliquez avec le bouton droit sur la base de données, puis sélectionnez **Delete** .
* Cochez **Fermer les connexions existantes**.
* Sélectionnez **OK**.
* Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Supprimer et recréer la base de données

> [!NOTE]
> Pour ce didacticiel, vous utilisez la fonctionnalité de *migrations* Entity Framework Core dans la mesure du possible. Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données. Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées. Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification. Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue. En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite. À la place, vous supprimez puis recréez la base de données quand le schéma change.
>
>Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié. La regénération d’un tableau implique :
>
>* La création d’un nouveau tableau.
>* La copie de données de l’ancien tableau vers le nouveau.
>* La suppression de l’ancien tableau.
>* Renommer la nouvelle table.
>
>Pour plus d’informations, consultez les ressources suivantes :
>
> * [Limites d’un fournisseur de base de données EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Personnaliser le code de migration](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Amorçage des données](/ef/core/modeling/data-seeding)
> * [Instruction SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

1. Delete dossier de migration.  

1. Utilisez les commandes suivantes pour recréer la base de données.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`. Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans cette section, Migrations [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First est utilisé pour :

* Ajouter un nouveau champ au modèle.
* Migrer la nouvelle modification du schéma des champs vers la base de données.

Quand vous utilisez EF Code First pour créer automatiquement une base de données, Code First :

* Ajoute une [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table à la base de données pour déterminer si le schéma de la base de données est synchronisé avec les classes de modèle à partir desquelles il a été généré.
* Si les classes de modèle ne sont pas synchronisées avec la base de données, EF lève une exception.

Vérification automatique que le schéma et le modèle sont synchronisés facilite la recherche de problèmes de code de base de données incohérents.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Ajout d’une propriété Rating au modèle Movie

Ouvrez le fichier *Models/Movie.cs* et ajoutez une propriété `Rating` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Générez l'application.

Modifiez *pages/movies/ Index . cshtml* et ajoutez un `Rating` champ :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Mettez à jour les pages suivantes :

* Ajoutez le `Rating` champ aux Delete pages détails et.
* Mettez à jour [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) avec un `Rating` champ.
* Ajoutez le champ `Rating` à la Page Edit.

L’application ne fonctionne pas tant que la base de données n’a pas été mise à jour pour inclure le nouveau champ. Si l’application est exécutée maintenant, l’application lève une exception `SqlException` :

`SqlException: Invalid column name 'Rating'.`

Cette erreur est due au fait que la classe du modèle Movie mise à jour est différente du schéma de la table Movie de la base de données. Il n’y a aucune `Rating` colonne dans la table de base de données.

Plusieurs approches sont possibles pour résoudre l’erreur :

1. Laisser Entity Framework supprimer et recréer automatiquement la base de données avec le nouveau schéma de classes du modèle. Cette approche est pratique au début du cycle de développement, ce qui vous permet de faire évoluer rapidement le schéma de base de données et le modèle. L’inconvénient est que vous perdez les données existantes dans la base de données. N’utilisez pas cette approche sur une base de données de production ! La suppression de la base de données sur les modifications de schéma et l’utilisation d’un initialiseur pour amorcer automatiquement la base de données avec des données de test est souvent un moyen productif de développer une application.

2. Modifier explicitement le schéma de la base de données existante pour le faire correspondre aux classes du modèle. L’avantage de cette approche est de conserver les données. Effectuez cette modification manuellement ou en créant un script de modification de la base de données.

3. Utilisez les migrations Code First pour mettre à jour le schéma de base de données.

Pour ce didacticiel, nous allons utiliser les migrations Code First.

Mettez à jour la classe `SeedData` pour qu’elle fournisse une valeur pour la nouvelle colonne. Un exemple de modification est présenté ci-dessous, mais apporte cette modification pour chaque `new Movie` bloc.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Consultez le [fichier SeedData.cs complet](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Générez la solution.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Ajouter une migration pour le champ d’évaluation

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.
Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration Rating
Update-Database
```

La commande `Add-Migration` indique au framework qu’il doit :

* Comparez le `Movie` modèle au `Movie` schéma de la base de données.
* Create code pour migrer le schéma de base de données vers le nouveau modèle.

Le nom « Rating » est arbitraire et utilisé pour nommer le fichier de migration. Il est utile d’utiliser un nom explicite pour le fichier de migration.

La commande `Update-Database` demande à l’infrastructure d’appliquer les modifications de schéma à la base de données.

<a name="ssox"></a>

Si vous supprimez tous les enregistrements dans DdatabaseB, l’initialiseur amorce le DdatabaseB et inclut le `Rating` champ. Pour ce faire, utilisez les liens de suppression disponibles dans le navigateur ou à partir de [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox).

Une autre option consiste à supprimer la base de données et à utiliser des migrations pour recréer la base de données. Pour supprimer la base de données dans SSOX :

* Sélectionnez la base de données dans SSOX.
* Cliquez avec le bouton droit sur la base de données, puis sélectionnez **Delete** .
* Cochez **Fermer les connexions existantes**.
* Sélectionnez **OK**.
* Dans le [PMC](xref:tutorials/razor-pages/new-field#pmc), mettez à jour la base de données :

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Supprimer et recréer la base de données

> [!NOTE]
> Pour ce didacticiel, vous utilisez la fonctionnalité de *migrations* Entity Framework Core dans la mesure du possible. Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données. Toutefois, les migrations ne peuvent effectuer que les modifications prises en charge par le fournisseur EF Core, et les capacités du fournisseur SQLite sont limitées. Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression ou sa modification. Si vous créez une migration pour supprimer ou modifier une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue. En raison de ces limitations, ce tutoriel n’utilise pas les migrations pour les modifications de schéma SQLite. À la place, vous supprimez puis recréez la base de données quand le schéma change.
>
>Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié. La regénération d’un tableau implique :
>
>* La création d’un nouveau tableau.
>* La copie de données de l’ancien tableau vers le nouveau.
>* La suppression de l’ancien tableau.
>* Renommer la nouvelle table.
>
>Pour plus d’informations, consultez les ressources suivantes :
>
> * [Limites d’un fournisseur de base de données EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Personnaliser le code de migration](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Amorçage des données](/ef/core/modeling/data-seeding)
> * [Instruction SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)

Delete la base de données et utilisent des migrations pour recréer la base de données. Pour supprimer la base de données, supprimez le fichier de base de données ( *MvcMovie.db* ). Ensuite, exécutez la commande `ef database update` :

```dotnetcli
dotnet ef database update
```

---

Exécutez l’application et vérifiez que vous pouvez créer/modifier/afficher des films avec un champ `Rating`. Si la base de données n’est pas amorcée, définissez un point d’arrêt dans la méthode `SeedData.Initialize`.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Précédent : ajouter une recherche](xref:tutorials/razor-pages/search) 
>  [Suivant : ajouter une validation](xref:tutorials/razor-pages/validation)

::: moniker-end
