---
title: 'Didacticiel : utilisation de la fonctionnalité migrations-ASP.NET MVC avec EF Core'
description: Dans ce didacticiel, vous utilisez la fonctionnalité Migrations d’EF Core pour gérer les modifications du modèle de données dans une application ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-mvc/migrations
ms.openlocfilehash: 070c18db55956d79560904f53395b5001c7bce6d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054032"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="68ef7-103">Didacticiel : utilisation de la fonctionnalité migrations-ASP.NET MVC avec EF Core</span><span class="sxs-lookup"><span data-stu-id="68ef7-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="68ef7-104">Dans ce didacticiel, vous utilisez la fonctionnalité Migrations d’EF Core pour gérer les modifications du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="68ef7-105">Dans les didacticiels suivants, vous allez ajouter d’autres migrations au fil de la modification du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="68ef7-106">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="68ef7-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="68ef7-107">En savoir plus sur les migrations</span><span class="sxs-lookup"><span data-stu-id="68ef7-107">Learn about migrations</span></span>
> * <span data-ttu-id="68ef7-108">Changer la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="68ef7-108">Change the connection string</span></span>
> * <span data-ttu-id="68ef7-109">Créer une migration initiale</span><span class="sxs-lookup"><span data-stu-id="68ef7-109">Create an initial migration</span></span>
> * <span data-ttu-id="68ef7-110">Examiner les méthodes Up et Down</span><span class="sxs-lookup"><span data-stu-id="68ef7-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="68ef7-111">En savoir plus sur la capture instantanée du modèle de données</span><span class="sxs-lookup"><span data-stu-id="68ef7-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="68ef7-112">Appliquer la migration</span><span class="sxs-lookup"><span data-stu-id="68ef7-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68ef7-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="68ef7-113">Prerequisites</span></span>

* [<span data-ttu-id="68ef7-114">Tri, filtrage et pagination</span><span class="sxs-lookup"><span data-stu-id="68ef7-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="68ef7-115">À propos des migrations</span><span class="sxs-lookup"><span data-stu-id="68ef7-115">About migrations</span></span>

<span data-ttu-id="68ef7-116">Quand vous développez une nouvelle application, votre modèle de données change fréquemment et, chaque fois que le modèle change, il n’est plus en synchronisation avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="68ef7-117">Vous avez démarré ce didacticiel en configurant Entity Framework pour créer la base de données si elle n’existait pas.</span><span class="sxs-lookup"><span data-stu-id="68ef7-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="68ef7-118">Ensuite, chaque fois que vous modifiez le modèle de données, en ajoutant, supprimant ou changeant des classes d’entité ou votre classe DbContext, vous pouvez supprimer la base de données : EF en crée alors une nouvelle qui correspond au modèle et l’alimente avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="68ef7-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="68ef7-119">Cette méthode pour conserver la base de données en synchronisation avec le modèle de données fonctionne bien jusqu’au déploiement de l’application en production.</span><span class="sxs-lookup"><span data-stu-id="68ef7-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="68ef7-120">Quand l’application s’exécute en production, elle stocke généralement les données que vous voulez conserver, et vous ne voulez pas tout perdre chaque fois que vous apportez une modification, comme ajouter une nouvelle colonne.</span><span class="sxs-lookup"><span data-stu-id="68ef7-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="68ef7-121">La fonctionnalité Migrations d’EF Core résout ce problème en permettant à EF de mettre à jour le schéma de base de données au lieu de créer une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="68ef7-122">Pour travailler avec des migrations, vous pouvez utiliser la **console du gestionnaire de package** (PMC) ou l’interface CLI.</span><span class="sxs-lookup"><span data-stu-id="68ef7-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="68ef7-123">Ces didacticiels montrent comment utiliser des commandes CLI.</span><span class="sxs-lookup"><span data-stu-id="68ef7-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="68ef7-124">Vous trouverez des informations sur la console du Gestionnaire de package [à la fin de ce didacticiel](#pmc).</span><span class="sxs-lookup"><span data-stu-id="68ef7-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="68ef7-125">Changer la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="68ef7-125">Change the connection string</span></span>

<span data-ttu-id="68ef7-126">Dans le *:::no-loc(appsettings.json):::* fichier, remplacez le nom de la base de données dans la chaîne de connexion par par contosouniversity2 ou un autre nom que vous n’avez pas utilisé sur l’ordinateur que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="68ef7-126">In the *:::no-loc(appsettings.json):::* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="68ef7-127">Cette modification configure le projet de façon à ce que la première migration crée une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="68ef7-128">Ce n’est pas obligatoire pour commencer à utiliser les migrations, mais vous verrez plus tard pourquoi c’est judicieux.</span><span class="sxs-lookup"><span data-stu-id="68ef7-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="68ef7-129">Comme alternative au changement de nom de la base de données, vous pouvez supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="68ef7-130">Utilisez **SQL Server Object Explorer** (SSOX) ou la commande CLI `database drop` :</span><span class="sxs-lookup"><span data-stu-id="68ef7-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="68ef7-131">La section suivante explique comment exécuter des commandes CLI.</span><span class="sxs-lookup"><span data-stu-id="68ef7-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="68ef7-132">Créer une migration initiale</span><span class="sxs-lookup"><span data-stu-id="68ef7-132">Create an initial migration</span></span>

<span data-ttu-id="68ef7-133">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="68ef7-133">Save your changes and build the project.</span></span> <span data-ttu-id="68ef7-134">Ouvrez ensuite une fenêtre Commande et accédez au dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="68ef7-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="68ef7-135">Voici un moyen rapide pour le faire :</span><span class="sxs-lookup"><span data-stu-id="68ef7-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="68ef7-136">Dans **l’Explorateur de solutions** , cliquez sur le projet et choisissez **Ouvrir le dossier dans l’Explorateur de fichiers** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="68ef7-136">In **Solution Explorer** , right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Élément de menu Ouvrir dans l’Explorateur de fichiers](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="68ef7-138">Entrez « cmd » dans la barre d’adresses et appuyez sur Entrée.</span><span class="sxs-lookup"><span data-stu-id="68ef7-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Ouvrir une fenêtre Commande](migrations/_static/open-command-window.png)

<span data-ttu-id="68ef7-140">Entrez la commande suivante dans la fenêtre Commande :</span><span class="sxs-lookup"><span data-stu-id="68ef7-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="68ef7-141">`dotnet tool install --global dotnet-ef` s’installe `dotnet ef` en tant qu' [outil Global](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="68ef7-141">`dotnet tool install --global dotnet-ef` installs `dotnet ef` as a [global tool](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="68ef7-142">Dans les commandes précédentes, une sortie similaire à ce qui suit s’affiche :</span><span class="sxs-lookup"><span data-stu-id="68ef7-142">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="68ef7-143">Si vous voyez un message d’erreur « *Impossible d’accéder au fichier... ContosoUniversity.dll parce qu’il est utilisé par un autre processus.* », recherchez l’icône de IIS Express dans la barre d’état système Windows, cliquez dessus avec le bouton droit, puis cliquez sur **ContosoUniversity > arrêter le site** .</span><span class="sxs-lookup"><span data-stu-id="68ef7-143">If you see an error message " *cannot access the file ... ContosoUniversity.dll because it is being used by another process.* ", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site** .</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="68ef7-144">Examiner les méthodes Up et Down</span><span class="sxs-lookup"><span data-stu-id="68ef7-144">Examine Up and Down methods</span></span>

<span data-ttu-id="68ef7-145">Quand vous avez exécuté la commande `migrations add`, EF a généré le code qui crée la base de données à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="68ef7-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="68ef7-146">Ce code se trouve dans le dossier *migrations* , dans le fichier nommé *\<timestamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="68ef7-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs* .</span></span> <span data-ttu-id="68ef7-147">La méthode `Up` de la classe `InitialCreate` crée les tables de base de données qui correspondent aux jeux d’entités du modèle de données, et la méthode `Down` les supprime, comme indiqué dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="68ef7-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="68ef7-148">La fonctionnalité Migrations appelle la méthode `Up` pour implémenter les modifications du modèle de données pour une migration.</span><span class="sxs-lookup"><span data-stu-id="68ef7-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="68ef7-149">Quand vous entrez une commande pour annuler la mise à jour, Migrations appelle la méthode `Down`.</span><span class="sxs-lookup"><span data-stu-id="68ef7-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="68ef7-150">Ce code est celui de la migration initiale qui a été créé quand vous avez entré la commande `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="68ef7-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="68ef7-151">Le paramètre de nom de la migration (« InitialCreate » dans l’exemple) est utilisé comme nom de fichier ; vous pouvez le choisir librement.</span><span class="sxs-lookup"><span data-stu-id="68ef7-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="68ef7-152">Nous vous conseillons néanmoins de choisir un mot ou une expression qui résume ce qui est effectué dans la migration.</span><span class="sxs-lookup"><span data-stu-id="68ef7-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="68ef7-153">Par exemple, vous pouvez nommer une migration ultérieure « AjouterTableDépartement ».</span><span class="sxs-lookup"><span data-stu-id="68ef7-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="68ef7-154">Si vous avez créé la migration initiale alors que la base de données existait déjà, le code de création de la base de données est généré, mais il n’est pas nécessaire de l’exécuter, car la base de données correspond déjà au modèle de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="68ef7-155">Quand vous déployez l’application sur un autre environnement où la base de données n’existe pas encore, ce code est exécuté pour créer votre base de données : il est donc judicieux de le tester au préalable.</span><span class="sxs-lookup"><span data-stu-id="68ef7-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="68ef7-156">C’est la raison pour laquelle vous avez précédemment changé le nom de la base de données dans la chaîne de connexion : les migrations doivent pouvoir créer une base de données à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="68ef7-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="68ef7-157">Capture instantanée du modèle de données</span><span class="sxs-lookup"><span data-stu-id="68ef7-157">The data model snapshot</span></span>

<span data-ttu-id="68ef7-158">Migrations crée une *capture instantanée* du schéma de base de données actuel dans *Migrations/SchoolContextModelSnapshot.cs* .</span><span class="sxs-lookup"><span data-stu-id="68ef7-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs* .</span></span> <span data-ttu-id="68ef7-159">Quand vous ajoutez une migration, EF détermine ce qui a changé en comparant le modèle de données au fichier de capture instantanée.</span><span class="sxs-lookup"><span data-stu-id="68ef7-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="68ef7-160">Utilisez la commande [dotnet EF migrations Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) pour supprimer une migration.</span><span class="sxs-lookup"><span data-stu-id="68ef7-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="68ef7-161">`dotnet ef migrations remove` supprime la migration et garantit que la capture instantanée est correctement réinitialisée.</span><span class="sxs-lookup"><span data-stu-id="68ef7-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="68ef7-162">En cas `dotnet ef migrations remove` d’échec, utilisez `dotnet ef migrations remove -v` pour obtenir plus d’informations sur l’échec.</span><span class="sxs-lookup"><span data-stu-id="68ef7-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="68ef7-163">Pour plus d’informations sur l’utilisation du fichier de capture instantanée, consultez [Migrations EF Core dans les environnements d’équipe](/ef/core/managing-schemas/migrations/teams).</span><span class="sxs-lookup"><span data-stu-id="68ef7-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="68ef7-164">Appliquer la migration</span><span class="sxs-lookup"><span data-stu-id="68ef7-164">Apply the migration</span></span>

<span data-ttu-id="68ef7-165">Dans la fenêtre Commande, entrez la commande suivante pour créer la base de données et ses tables.</span><span class="sxs-lookup"><span data-stu-id="68ef7-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="68ef7-166">La sortie de la commande est similaire à la commande `migrations add`, à ceci près que vous voyez des journaux pour les commandes SQL qui configurent la base de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="68ef7-167">La plupart des journaux sont omis dans l’exemple de sortie suivant.</span><span class="sxs-lookup"><span data-stu-id="68ef7-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="68ef7-168">Si vous préférez ne pas voir ce niveau de détail dans les messages des journaux, vous pouvez changer le niveau de journalisation dans le fichier *appsettings.Development.json* .</span><span class="sxs-lookup"><span data-stu-id="68ef7-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="68ef7-169">Pour plus d'informations, consultez <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="68ef7-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
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
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="68ef7-170">Utilisez **l’Explorateur d’objets SQL Server** pour inspecter la base de données, comme vous l’avez fait dans le premier didacticiel.</span><span class="sxs-lookup"><span data-stu-id="68ef7-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="68ef7-171">Vous pouvez noter l’ajout d’une table \_\_EFMigrationsHistory, qui fait le suivi des migrations qui ont été appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="68ef7-172">Visualisez les données de cette table : vous y voyez une ligne pour la première migration.</span><span class="sxs-lookup"><span data-stu-id="68ef7-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="68ef7-173">(Le dernier journal dans l’exemple de sortie CLI précédent montre l’instruction INSERT qui crée cette ligne.)</span><span class="sxs-lookup"><span data-stu-id="68ef7-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="68ef7-174">Exécutez l’application pour vérifier que tout fonctionne toujours comme avant.</span><span class="sxs-lookup"><span data-stu-id="68ef7-174">Run the application to verify that everything still works the same as before.</span></span>

![Page d’index des étudiants](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="68ef7-176">Comparer l’interface CLI et PMC</span><span class="sxs-lookup"><span data-stu-id="68ef7-176">Compare CLI and PMC</span></span>

<span data-ttu-id="68ef7-177">Les outils EF pour la gestion des migrations sont disponibles à partir de commandes CLI .NET Core ou d’applets de commande PowerShell dans la fenêtre **Console du Gestionnaire de package** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68ef7-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="68ef7-178">Ce didacticiel montre comment utiliser l’interface CLI, mais vous pouvez utiliser la console du Gestionnaire de package si vous préférez.</span><span class="sxs-lookup"><span data-stu-id="68ef7-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="68ef7-179">Les commandes EF pour la console du Gestionnaire de package se trouvent dans le package [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools).</span><span class="sxs-lookup"><span data-stu-id="68ef7-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="68ef7-180">Ce package étant inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), vous n’avez pas besoin d’ajouter une référence de package si votre application en comporte une pour `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="68ef7-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="68ef7-181">**Important :** Il ne s’agit pas du même package que celui que vous installez pour l’interface CLI en modifiant le fichier *.csproj* .</span><span class="sxs-lookup"><span data-stu-id="68ef7-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="68ef7-182">Le nom de celui-ci se termine par `Tools`, contrairement au nom du package CLI qui se termine par `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="68ef7-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="68ef7-183">Pour plus d’informations sur les commandes CLI, consultez [Interface CLI .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="68ef7-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="68ef7-184">Pour plus d’informations sur les commandes de la console du Gestionnaire de package, consultez [Console du Gestionnaire de package (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="68ef7-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="68ef7-185">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="68ef7-185">Get the code</span></span>

[<span data-ttu-id="68ef7-186">Télécharger ou afficher l’application complète.</span><span class="sxs-lookup"><span data-stu-id="68ef7-186">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="68ef7-187">Étape suivante</span><span class="sxs-lookup"><span data-stu-id="68ef7-187">Next step</span></span>

<span data-ttu-id="68ef7-188">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="68ef7-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="68ef7-189">Découvert des informations sur les migrations"</span><span class="sxs-lookup"><span data-stu-id="68ef7-189">Learned about migrations</span></span>
> * <span data-ttu-id="68ef7-190">Découvert des informations sur les packages de migration NuGet</span><span class="sxs-lookup"><span data-stu-id="68ef7-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="68ef7-191">Changer la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="68ef7-191">Changed the connection string</span></span>
> * <span data-ttu-id="68ef7-192">Créer une migration initiale</span><span class="sxs-lookup"><span data-stu-id="68ef7-192">Created an initial migration</span></span>
> * <span data-ttu-id="68ef7-193">Examiner les méthodes Up et Down</span><span class="sxs-lookup"><span data-stu-id="68ef7-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="68ef7-194">Découvrir des informations sur la capture instantanée du modèle de données</span><span class="sxs-lookup"><span data-stu-id="68ef7-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="68ef7-195">Migration appliquée</span><span class="sxs-lookup"><span data-stu-id="68ef7-195">Applied the migration</span></span>

<span data-ttu-id="68ef7-196">Passez au tutoriel suivant pour aborder des sujets plus avancés sur le développement du modèle de données.</span><span class="sxs-lookup"><span data-stu-id="68ef7-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="68ef7-197">Au cour de ce processus, vous allez créer et appliquer d’autres migrations.</span><span class="sxs-lookup"><span data-stu-id="68ef7-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="68ef7-198">Créer et appliquer d’autres migrations</span><span class="sxs-lookup"><span data-stu-id="68ef7-198">Create and apply additional migrations</span></span>](complex-data-model.md)
