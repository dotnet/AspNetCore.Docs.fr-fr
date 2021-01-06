---
title: Didacticiel, partie 5, appliquer des migrations à l’exemple Contoso University
description: Partie 5 de la série de didacticiels sur Contoso University. Utilisez la fonctionnalité migrations EF Core pour gérer les modifications de modèle de données dans une application ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: 7c8f562bcf0b7e2672f2f1ac244e0d9278e4c204
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485925"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a>Didacticiel : partie 5, appliquer des migrations à l’exemple Contoso University

Dans ce didacticiel, vous utilisez la fonctionnalité Migrations d’EF Core pour gérer les modifications du modèle de données. Dans les didacticiels suivants, vous allez ajouter d’autres migrations au fil de la modification du modèle de données.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * En savoir plus sur les migrations
> * Créer une migration initiale
> * Examiner les méthodes Up et Down
> * En savoir plus sur la capture instantanée du modèle de données
> * Appliquer la migration

## <a name="prerequisites"></a>Conditions préalables requises

* [Tri, filtrage et pagination](sort-filter-page.md)

## <a name="about-migrations"></a>À propos des migrations

Quand vous développez une nouvelle application, votre modèle de données change fréquemment et, chaque fois que le modèle change, il n’est plus en synchronisation avec la base de données. Vous avez démarré ce didacticiel en configurant Entity Framework pour créer la base de données si elle n’existait pas. Ensuite, chaque fois que vous modifiez le modèle de données, en ajoutant, supprimant ou changeant des classes d’entité ou votre classe DbContext, vous pouvez supprimer la base de données : EF en crée alors une nouvelle qui correspond au modèle et l’alimente avec des données de test.

Cette méthode pour conserver la base de données en synchronisation avec le modèle de données fonctionne bien jusqu’au déploiement de l’application en production. Quand l’application s’exécute en production, elle stocke généralement les données que vous voulez conserver, et vous ne voulez pas tout perdre chaque fois que vous apportez une modification, comme ajouter une nouvelle colonne. La fonctionnalité Migrations d’EF Core résout ce problème en permettant à EF de mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.

Pour travailler avec des migrations, vous pouvez utiliser la **console du gestionnaire de package** (PMC) ou l’interface CLI.  Ces didacticiels montrent comment utiliser des commandes CLI. Vous trouverez des informations sur la console du Gestionnaire de package [à la fin de ce didacticiel](#pmc).

## <a name="drop-the-database"></a>Supprimer la base de données

Installez EF Core Tools en tant qu' [outil Global](/ef/core/miscellaneous/cli/dotnet) et supprimez la base de données :

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

La section suivante explique comment exécuter des commandes CLI.

## <a name="create-an-initial-migration"></a>Créer une migration initiale

Enregistrez vos modifications et générez le projet. Ouvrez ensuite une fenêtre Commande et accédez au dossier du projet. Voici un moyen rapide pour le faire :

* Dans **l’Explorateur de solutions**, cliquez sur le projet et choisissez **Ouvrir le dossier dans l’Explorateur de fichiers** dans le menu contextuel.

  ![Élément de menu Ouvrir dans l’Explorateur de fichiers](migrations/_static/open-in-file-explorer.png)

* Entrez « cmd » dans la barre d’adresses et appuyez sur Entrée.

  ![Ouvrir une fenêtre Commande](migrations/_static/open-command-window.png)

Entrez la commande suivante dans la fenêtre Commande :

```dotnetcli
dotnet ef migrations add InitialCreate
```

Dans les commandes précédentes, une sortie similaire à ce qui suit s’affiche :

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

Si vous voyez un message d’erreur «*Impossible d’accéder au fichier... ContosoUniversity.dll parce qu’il est utilisé par un autre processus.*», recherchez l’icône de IIS Express dans la barre d’état système Windows, cliquez dessus avec le bouton droit, puis cliquez sur **ContosoUniversity > arrêter le site**.

## <a name="examine-up-and-down-methods"></a>Examiner les méthodes Up et Down

Quand vous avez exécuté la commande `migrations add`, EF a généré le code qui crée la base de données à partir de zéro. Ce code se trouve dans le dossier *migrations* , dans le fichier nommé *\<timestamp> _InitialCreate. cs*. La méthode `Up` de la classe `InitialCreate` crée les tables de base de données qui correspondent aux jeux d’entités du modèle de données, et la méthode `Down` les supprime, comme indiqué dans l’exemple suivant.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

La fonctionnalité Migrations appelle la méthode `Up` pour implémenter les modifications du modèle de données pour une migration. Quand vous entrez une commande pour annuler la mise à jour, Migrations appelle la méthode `Down`.

Ce code est celui de la migration initiale qui a été créé quand vous avez entré la commande `migrations add InitialCreate`. Le paramètre de nom de la migration (« InitialCreate » dans l’exemple) est utilisé comme nom de fichier ; vous pouvez le choisir librement. Nous vous conseillons néanmoins de choisir un mot ou une expression qui résume ce qui est effectué dans la migration. Par exemple, vous pouvez nommer une migration ultérieure « AjouterTableDépartement ».

Si vous avez créé la migration initiale alors que la base de données existait déjà, le code de création de la base de données est généré, mais il n’est pas nécessaire de l’exécuter, car la base de données correspond déjà au modèle de données. Quand vous déployez l’application sur un autre environnement où la base de données n’existe pas encore, ce code est exécuté pour créer votre base de données : il est donc judicieux de le tester au préalable. C’est la raison pour laquelle vous avez précédemment changé le nom de la base de données dans la chaîne de connexion : les migrations doivent pouvoir créer une base de données à partir de zéro.

## <a name="the-data-model-snapshot"></a>Capture instantanée du modèle de données

Migrations crée une *capture instantanée* du schéma de base de données actuel dans *Migrations/SchoolContextModelSnapshot.cs*. Quand vous ajoutez une migration, EF détermine ce qui a changé en comparant le modèle de données au fichier de capture instantanée.

Utilisez la commande [dotnet EF migrations Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) pour supprimer une migration. `dotnet ef migrations remove` supprime la migration et garantit que la capture instantanée est correctement réinitialisée. En cas `dotnet ef migrations remove` d’échec, utilisez `dotnet ef migrations remove -v` pour obtenir plus d’informations sur l’échec.

Pour plus d’informations sur l’utilisation du fichier de capture instantanée, consultez [Migrations EF Core dans les environnements d’équipe](/ef/core/managing-schemas/migrations/teams).

## <a name="apply-the-migration"></a>Appliquer la migration

Dans la fenêtre Commande, entrez la commande suivante pour créer la base de données et ses tables.

```dotnetcli
dotnet ef database update
```

La sortie de la commande est similaire à la commande `migrations add`, à ceci près que vous voyez des journaux pour les commandes SQL qui configurent la base de données. La plupart des journaux sont omis dans l’exemple de sortie suivant. Si vous préférez ne pas voir ce niveau de détail dans les messages des journaux, vous pouvez changer le niveau de journalisation dans le fichier *appsettings.Development.json*. Pour plus d’informations, consultez <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

Utilisez **l’Explorateur d’objets SQL Server** pour inspecter la base de données, comme vous l’avez fait dans le premier didacticiel.  Vous pouvez noter l’ajout d’une table \_\_EFMigrationsHistory, qui fait le suivi des migrations qui ont été appliquées à la base de données. Visualisez les données de cette table : vous y voyez une ligne pour la première migration. (Le dernier journal dans l’exemple de sortie CLI précédent montre l’instruction INSERT qui crée cette ligne.)

Exécutez l’application pour vérifier que tout fonctionne toujours comme avant.

![Page d’index des étudiants](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Comparer l’interface CLI et PMC

Les outils EF pour la gestion des migrations sont disponibles à partir de commandes CLI .NET Core ou d’applets de commande PowerShell dans la fenêtre **Console du Gestionnaire de package** de Visual Studio. Ce didacticiel montre comment utiliser l’interface CLI, mais vous pouvez utiliser la console du Gestionnaire de package si vous préférez.

Les commandes EF pour la console du Gestionnaire de package se trouvent dans le package [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools). Ce package étant inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), vous n’avez pas besoin d’ajouter une référence de package si votre application en comporte une pour `Microsoft.AspNetCore.App`.

**Important :** Il ne s’agit pas du même package que celui que vous installez pour l’interface CLI en modifiant le fichier *.csproj*. Le nom de celui-ci se termine par `Tools`, contrairement au nom du package CLI qui se termine par `Tools.DotNet`.

Pour plus d’informations sur les commandes CLI, consultez [Interface CLI .NET Core](/ef/core/miscellaneous/cli/dotnet).

Pour plus d’informations sur les commandes de la console du Gestionnaire de package, consultez [Console du Gestionnaire de package (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Obtenir le code

[Télécharger ou afficher l’application complète.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a>Étape suivante

Passez au tutoriel suivant pour aborder des sujets plus avancés sur le développement du modèle de données. Au cour de ce processus, vous allez créer et appliquer d’autres migrations.

> [!div class="nextstepaction"]
> [Créer et appliquer d’autres migrations](complex-data-model.md)
