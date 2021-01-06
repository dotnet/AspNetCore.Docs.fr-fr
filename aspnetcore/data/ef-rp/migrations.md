---
title: 'Partie 4 : Razor pages avec EF Core dans ASP.net Core migrations'
author: rick-anderson
description: Partie 4 des Razor pages et Entity Framework de la série de didacticiels.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/migrations
ms.openlocfilehash: e6d1b9f041e892aaa37840c28fdb3153bf098b0d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061104"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a>Partie 4 : Razor pages avec EF Core migrations dans ASP.net Core

Par [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Ce tutoriel présente la fonctionnalité de migrations EF Core pour gérer les modifications du modèle de données.

Quand une nouvelle application est développée, le modèle de données change fréquemment. Chaque fois que le modèle change, il est désynchronisé avec la base de données. Cette série de tutoriels a commencé par la configuration d’Entity Framework pour créer la base de données si elle n’existait pas. Chaque fois que le modèle de données change, vous devez supprimer la base de données. À l’exécution suivante de l’application, l’appel à `EnsureCreated` a pour effet de recréer la base de données en fonction du nouveau modèle de données. La classe `DbInitializer` s’exécute ensuite pour amorcer la nouvelle base de données.

Cette approche consistant à maintenir la base de données synchronisée avec le modèle de données fonctionne bien tant que vous ne déployez pas l’application en production. Quand l’application s’exécute en production, elle stocke généralement des données qui doivent être tenues à jour. L’application ne peut pas démarrer avec une base de données de test chaque fois qu’une modification est apportée (comme l’ajout d’une nouvelle colonne). La fonctionnalité Migrations d’EF Core résout ce problème en permettant à EF Core de mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.

Au lieu de supprimer et de recréer la base de données quand le modèle de données change, les migrations mettent à jour le schéma et conservent les données existantes.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Supprimer la base de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utilisez l’**Explorateur d’objets SQL Server** (SSOX) pour supprimer la base de données ou exécutez la commande suivante dans la **console du Gestionnaire de package** (PMC) :

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Exécutez la commande suivante à l’invite de commandes pour installer l’interface de ligne de commande EF :

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* Dans l’invite de commandes, accédez au dossier du projet. Le dossier du projet contient le fichier *ContosoUniversity.csproj*.

* Supprimez le fichier *CU.db* ou exécutez la commande suivante :

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Créer une migration initiale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Exécutez les commandes suivantes dans PMC :

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vérifiez que l’invite de commandes se trouve dans le dossier du projet et exécutez les commandes suivantes :

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Méthodes Up et Down

La commande EF Core `migrations add` a généré du code pour créer la base de données. Ce code de migration se trouve dans le fichier *migrations \<timestamp> _InitialCreate. cs* . La méthode `Up` de la classe `InitialCreate` crée les tables de base de données qui correspondent aux jeux d’entités du modèle de données. La méthode `Down` les supprime, comme indiqué dans l’exemple suivant :

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Le code précédent concerne la migration initiale. Le code :

* A été généré par la commande `migrations add InitialCreate`. 
* Est exécuté par la commande `database update`.
* Crée une base de données pour le modèle de données spécifié par la classe du contexte de base de données.

Le paramètre de nom de migration (« InitialCreate » dans l’exemple) est utilisé comme nom de fichier. Le nom de la migration peut être n’importe quel nom de fichier valide. Nous vous conseillons néanmoins de choisir un mot ou une expression qui résume ce qui est effectué dans la migration. Par exemple, une migration ajoutant une table de département pourrait se nommer « TableAjoutDépartement ».

## <a name="the-migrations-history-table"></a>Table d’historique des migrations

* Utilisez SSOX ou votre outil SQLite pour inspecter la base de données.
* Notez l’ajout d’une table `__EFMigrationsHistory`. La table `__EFMigrationsHistory` effectue le suivi des migrations qui ont été appliquées à la base de données.
* Examinez les données contenues dans la table `__EFMigrationsHistory`. Elle présente une ligne pour la première migration.

## <a name="the-data-model-snapshot"></a>Capture instantanée du modèle de données

Les migrations créent une *capture instantanée* du modèle de données actif dans *Migrations/SchoolContextModelSnapshot.cs*. Quand vous ajoutez une migration, EF détermine ce qui a changé en comparant le modèle de données actif au fichier de capture instantanée.

Comme le fichier de capture instantané suit l’état du modèle de données, vous ne pouvez pas supprimer une migration en supprimant le fichier `<timestamp>_<migrationname>.cs`. Pour annuler la migration la plus récente, vous devez utiliser la commande `migrations remove`. Cette commande supprime la migration et vérifie que la capture instantanée est correctement réinitialisée. Pour plus d’informations, voir [migrations dotnet EF supprimer](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Supprimer EnsureCreated

Cette série de tutoriels a commencé en utilisant `EnsureCreated`. La méthode `EnsureCreated` ne crée pas de table d’historique des migrations et ne peut donc pas être utilisée avec les migrations. Elle est destinée à effectuer des tests et un prototypage rapide, où la base de données est supprimée et recréée fréquemment.

À partir de là, les tutoriels utilisent des migrations.

Dans *Data/DBInitializer. cs*, commentez la ligne suivante :

```csharp
context.Database.EnsureCreated();
```
Exécutez l’application et vérifiez que la base de données est amorcée.

## <a name="applying-migrations-in-production"></a>Application de migrations en production

Nous **déconseillons** l’appel de [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) dans les applications de production pendant leur démarrage. `Migrate` ne doit pas être appelé à partir d’une application déployée sur une batterie de serveurs. Si un scale-out de plusieurs instances de serveur a lieu sur l’application, il est difficile de vérifier que les mises à jour du schéma de base de données ne se produisent pas à partir de plusieurs serveurs ou qu’elles ne sont pas en conflit avec un accès en lecture/écriture.

La migration de base de données doit être effectuée dans le cadre du déploiement et de manière contrôlée. Parmi les approches de migration de base de données de production, citons :

* L’utilisation de migrations pour créer des scripts SQL et l’utilisation de scripts SQL dans le déploiement
* L’exécution de `dotnet ef database update` à partir d’un environnement contrôlé

## <a name="troubleshooting"></a>Dépannage

Si l’application utilise la Base de données locale SQL Server et affiche l’exception suivante :

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

La solution peut consister à exécuter `dotnet ef database update` à partir d’une invite de commandes.

### <a name="additional-resources"></a>Ressources supplémentaires

* [CLI EF Core](/ef/core/miscellaneous/cli/dotnet)
* [Console du Gestionnaire de package (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Étapes suivantes

Le tutoriel suivant crée le modèle de données en ajoutant des propriétés d’entité et de nouvelles entités.

> [!div class="step-by-step"]
> [Didacticiel précédent](xref:data/ef-rp/sort-filter-page) 
>  [Didacticiel suivant](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans ce didacticiel, nous allons utiliser la fonctionnalité de migrations EF Core pour gérer les modifications du modèle de données.

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez [l’application terminée](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

Quand une nouvelle application est développée, le modèle de données change fréquemment. Chaque fois que le modèle change, il est désynchronisé avec la base de données. Ce didacticiel commence par configurer Entity Framework pour créer la base de données si elle n’existe pas. Chaque fois que le modèle de données change :

* La base de données est supprimée
* EF crée une nouvelle base de données qui correspond au modèle
* L’application amorce la base de données avec des données de test

Cette approche pour conserver la synchronisation de la base de données avec le modèle de données fonctionne bien jusqu’à ce que vous déployiez l’application en production. Quand l’application s’exécute en production, elle stocke généralement des données qui doivent être tenues à jour. L’application ne peut pas commencer avec une base de données de test chaque fois qu’une modification est apportée (par exemple en cas d’ajout d’une nouvelle colonne). La fonctionnalité Migrations d’EF Core résout ce problème en permettant à EF Core de mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.

Plutôt que de supprimer et de recréer la base de données quand le modèle de données change, les migrations mettent à jour le schéma et conservent les données existantes.

## <a name="drop-the-database"></a>Supprimer la base de données

Utilisez **l’Explorateur d’objets SQL Server** (SSOX) ou la commande `database drop` :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans la **console du Gestionnaire de package**, exécutez la commande suivante :

```powershell
Drop-Database
```

Exécutez `Get-Help about_EntityFrameworkCore` à partir de la console du Gestionnaire de package pour obtenir des informations d’aide.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ouvrez une fenêtre de commande et accédez au dossier du projet. Le dossier du projet contient le fichier *Startup.cs*.

Entrez ce qui suit dans la fenêtre de commande :

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Créer une migration initiale et mettre à jour la base de données

Générez le projet et créez la première migration.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Examiner les méthodes Up et Down

La commande EF Core `migrations add` a généré du code pour créer la base de données. Ce code de migration se trouve dans le fichier *migrations \<timestamp> _InitialCreate. cs* . La méthode `Up` de la classe `InitialCreate` crée les tables de base de données qui correspondent aux jeux d’entités du modèle de données. La méthode `Down` les supprime, comme indiqué dans l’exemple suivant :

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

La fonctionnalité Migrations appelle la méthode `Up` pour implémenter les modifications du modèle de données pour une migration. Quand vous entrez une commande pour restaurer la mise à jour, les migrations appellent la méthode `Down`.

Le code précédent concerne la migration initiale. Ce code a été créé quand la commande `migrations add InitialCreate` a été exécutée. Le paramètre de nom de migration (« InitialCreate » dans l’exemple) est utilisé comme nom de fichier. Le nom de la migration peut être n’importe quel nom de fichier valide. Nous vous conseillons néanmoins de choisir un mot ou une expression qui résume ce qui est effectué dans la migration. Par exemple, une migration ajoutant une table de département pourrait se nommer « TableAjoutDépartement ».

Si la migration initiale est créée et que la base de données existe :

* Le code de création de base de données est généré
* Le code de création de base de données n’a pas besoin de s’exécuter, car la base de données correspond déjà au modèle de données. Si le code de création de base de données est exécuté, il n’apporte aucune modification, car la base de données correspond déjà au modèle de données.

Quand l’application est déployée sur un nouvel environnement, vous devez exécuter le code de création de base de données pour créer la base de données.

Comme la base de données a été supprimée et n’existe pas, les migrations créent une autre base de données.

### <a name="the-data-model-snapshot"></a>Capture instantanée du modèle de données

Les migrations créent un *instantané* du schéma de base de données actuel dans *Migrations/SchoolContextModelSnapshot.cs*. Quand vous ajoutez une migration, EF détermine ce qui a changé en comparant le modèle de données au fichier de capture instantanée.

Pour supprimer une migration, utilisez la commande suivante :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Remove-Migration

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Pour plus d’informations, voir [migrations dotnet EF supprimer](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

Pour supprimer les migrations, la commande supprime la migration et garantit que l’instantané est correctement réinitialisé.

### <a name="remove-ensurecreated-and-test-the-app"></a>Supprimer EnsureCreated et tester l’application

Dans les phases initiales de développement, nous avons utilisé `EnsureCreated`. Dans ce tutoriel, nous utilisons des migrations. La commande `EnsureCreated` a les limitations suivantes :

* Elle ignore les migrations et crée la base de données et le schéma
* Elle ne crée pas de table de migrations
* Elle ne peut *pas* être utilisée avec des migrations
* Elle est conçue pour effectuer des tests et un prototypage rapide, où la base de données est supprimée et recréée fréquemment.

Supprimez `EnsureCreated` :

```csharp
context.Database.EnsureCreated();
```

Exécutez l’application et vérifiez que la base de données est amorcée.

### <a name="inspect-the-database"></a>Inspecter la base de données

Utilisez **l’Explorateur d’objets SQL Server** pour inspecter la base de données. Notez l’ajout d’une table `__EFMigrationsHistory`. La table `__EFMigrationsHistory` effectue le suivi des migrations qui ont été appliquées à la base de données. Visualisez les données dans la table `__EFMigrationsHistory` ; elle affiche une ligne pour la première migration. Le dernier journal dans l’exemple de sortie CLI précédent montre l’instruction INSERT qui crée cette ligne.

Exécutez l’application et vérifiez que tout fonctionne.

## <a name="applying-migrations-in-production"></a>Application de migrations en production

Nous vous recommandons de faire en sorte que les applications de production n’appellent **pas**[Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) au démarrage de l’application. `Migrate` ne doit pas être appelée à partir d’une application dans la batterie de serveurs, par exemple si l’application a été déployée dans le cloud avec montée en puissance parallèle (plusieurs instances de l’application sont en cours d’exécution).

La migration de base de données doit être effectuée dans le cadre du déploiement et de manière contrôlée. Parmi les approches de migration de base de données de production, citons :

* L’utilisation de migrations pour créer des scripts SQL et l’utilisation de scripts SQL dans le déploiement
* L’exécution de `dotnet ef database update` à partir d’un environnement contrôlé

EF Core utilise la table `__MigrationsHistory` pour voir si des migrations doivent s’exécuter. Si la base de données est à jour, aucune migration n’est exécutée.

## <a name="troubleshooting"></a>Dépannage

Téléchargez [l’application terminée](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).

L’application génère l’exception suivante :

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Solution : Exécutez `dotnet ef database update`.

### <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [CLI .net Core](/ef/core/miscellaneous/cli/dotnet).
* [Console du Gestionnaire de package (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Précédent](xref:data/ef-rp/sort-filter-page) 
>  [Suivant](xref:data/ef-rp/complex-data-model)

::: moniker-end

