---
title: 'Migrer de l’authentification d’appartenance ASP.NET vers ASP.NET Core 2,0 :::no-loc(Identity):::'
author: isaac2004
description: 'Découvrez comment migrer des applications ASP.NET existantes à l’aide de l’authentification d’appartenance vers ASP.NET Core 2,0 :::no-loc(Identity)::: .'
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: d981c424fd2d6cad95b9164420f093672325c347
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051354"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a><span data-ttu-id="87ffc-103">Migrer de l’authentification d’appartenance ASP.NET vers ASP.NET Core 2,0 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="87ffc-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 :::no-loc(Identity):::</span></span>

<span data-ttu-id="87ffc-104">De [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="87ffc-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="87ffc-105">Cet article explique comment migrer le schéma de base de données pour les applications ASP.NET à l’aide de l’authentification d’appartenance vers ASP.NET Core 2,0 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="87ffc-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 :::no-loc(Identity):::.</span></span>

> [!NOTE]
> <span data-ttu-id="87ffc-106">Ce document décrit les étapes nécessaires à la migration du schéma de base de données pour les applications basées sur l’appartenance ASP.NET vers le schéma de base de données utilisé pour :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="87ffc-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="87ffc-107">Pour plus d’informations sur la migration de l’authentification basée sur l’appartenance ASP.NET vers ASP.NET :::no-loc(Identity)::: , consultez [migrer une application existante :::no-loc(Identity)::: de l’appartenance SQL vers ASP.net ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="87ffc-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET :::no-loc(Identity):::, see [Migrate an existing app from SQL Membership to ASP.NET :::no-loc(Identity):::](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="87ffc-108">Pour plus d’informations sur :::no-loc(ASP.NET Core Identity)::: , consultez [Introduction à :::no-loc(Identity)::: sur ASP.net Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="87ffc-108">For more information about :::no-loc(ASP.NET Core Identity):::, see [Introduction to :::no-loc(Identity)::: on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="87ffc-109">Examen du schéma d’appartenance</span><span class="sxs-lookup"><span data-stu-id="87ffc-109">Review of Membership schema</span></span>

<span data-ttu-id="87ffc-110">Avant ASP.NET 2,0, les développeurs devaient créer l’intégralité du processus d’authentification et d’autorisation pour leurs applications.</span><span class="sxs-lookup"><span data-stu-id="87ffc-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="87ffc-111">Avec ASP.NET 2,0, l’appartenance a été introduite et fournit une solution réutilisable pour gérer la sécurité dans les applications ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="87ffc-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="87ffc-112">Les développeurs pouvaient désormais démarrer un schéma dans une base de données SQL Server à l’aide de la <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> commande.</span><span class="sxs-lookup"><span data-stu-id="87ffc-112">Developers were now able to bootstrap a schema into a SQL Server database with the <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> command.</span></span> <span data-ttu-id="87ffc-113">Après l’exécution de cette commande, les tables suivantes ont été créées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="87ffc-113">After running this command, the following tables were created in the database.</span></span>

  ![Tables d’appartenance](identity/_static/membership-tables.png)

<span data-ttu-id="87ffc-115">Pour migrer des applications existantes vers ASP.NET Core 2,0 :::no-loc(Identity)::: , les données de ces tables doivent être migrées vers les tables utilisées par le nouveau :::no-loc(Identity)::: schéma.</span><span class="sxs-lookup"><span data-stu-id="87ffc-115">To migrate existing apps to ASP.NET Core 2.0 :::no-loc(Identity):::, the data in these tables needs to be migrated to the tables used by the new :::no-loc(Identity)::: schema.</span></span>

## <a name="no-locaspnet-core-identity-20-schema"></a><span data-ttu-id="87ffc-116">:::no-loc(ASP.NET Core Identity)::: schéma 2,0</span><span class="sxs-lookup"><span data-stu-id="87ffc-116">:::no-loc(ASP.NET Core Identity)::: 2.0 schema</span></span>

<span data-ttu-id="87ffc-117">ASP.NET Core 2,0 suit le [:::no-loc(Identity):::](/aspnet/identity/index) principe introduit dans ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="87ffc-117">ASP.NET Core 2.0 follows the [:::no-loc(Identity):::](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="87ffc-118">Bien que le principe soit partagé, l’implémentation entre les frameworks est différente, même entre les versions de ASP.NET Core (consultez [migrer l’authentification et :::no-loc(Identity)::: vers ASP.net Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="87ffc-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="87ffc-119">La façon la plus rapide d’afficher le schéma pour ASP.NET Core 2,0 :::no-loc(Identity)::: consiste à créer une nouvelle application ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="87ffc-119">The fastest way to view the schema for ASP.NET Core 2.0 :::no-loc(Identity)::: is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="87ffc-120">Procédez comme suit dans Visual Studio 2017 :</span><span class="sxs-lookup"><span data-stu-id="87ffc-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="87ffc-121">Sélectionnez **Fichier** > **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="87ffc-121">Select **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="87ffc-122">Créez un projet d' **application Web ASP.net Core** nommé *Core :::no-loc(Identity)::: Sample* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-122">Create a new **ASP.NET Core Web Application** project named *Core:::no-loc(Identity):::Sample* .</span></span>
1. <span data-ttu-id="87ffc-123">Sélectionnez **ASP.NET Core 2,0** dans la liste déroulante, puis sélectionnez **application Web** .</span><span class="sxs-lookup"><span data-stu-id="87ffc-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application** .</span></span> <span data-ttu-id="87ffc-124">Ce modèle produit une application [ :::no-loc(Razor)::: pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="87ffc-124">This template produces a [:::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="87ffc-125">Avant de cliquer sur **OK** , cliquez sur **modifier l’authentification** .</span><span class="sxs-lookup"><span data-stu-id="87ffc-125">Before clicking **OK** , click **Change Authentication** .</span></span>
1. <span data-ttu-id="87ffc-126">Choisissez des **comptes d’utilisateur individuels** pour les :::no-loc(Identity)::: modèles.</span><span class="sxs-lookup"><span data-stu-id="87ffc-126">Choose **Individual User Accounts** for the :::no-loc(Identity)::: templates.</span></span> <span data-ttu-id="87ffc-127">Enfin, cliquez sur **OK** , puis sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="87ffc-127">Finally, click **OK** , then **OK** .</span></span> <span data-ttu-id="87ffc-128">Visual Studio crée un projet à l’aide du :::no-loc(ASP.NET Core Identity)::: modèle.</span><span class="sxs-lookup"><span data-stu-id="87ffc-128">Visual Studio creates a project using the :::no-loc(ASP.NET Core Identity)::: template.</span></span>
1. <span data-ttu-id="87ffc-129">Sélectionnez **Outils**  >  **Gestionnaire de package NuGet**  >  **console du gestionnaire** de package pour ouvrir la fenêtre **console du gestionnaire de package** (PMC).</span><span class="sxs-lookup"><span data-stu-id="87ffc-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="87ffc-130">Accédez à la racine du projet dans PMC, puis exécutez la commande [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="87ffc-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="87ffc-131">ASP.NET Core 2,0 :::no-loc(Identity)::: utilise EF Core pour interagir avec la base de données qui stocke les données d’authentification.</span><span class="sxs-lookup"><span data-stu-id="87ffc-131">ASP.NET Core 2.0 :::no-loc(Identity)::: uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="87ffc-132">Pour que l’application nouvellement créée fonctionne, vous devez disposer d’une base de données pour stocker ces données.</span><span class="sxs-lookup"><span data-stu-id="87ffc-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="87ffc-133">Après la création d’une nouvelle application, la méthode la plus rapide pour inspecter le schéma dans un environnement de base de données consiste à créer la base de données à l’aide de [EF Core migrations](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="87ffc-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="87ffc-134">Ce processus crée une base de données, localement ou ailleurs, qui imite ce schéma.</span><span class="sxs-lookup"><span data-stu-id="87ffc-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="87ffc-135">Pour plus d’informations, consultez la documentation précédente.</span><span class="sxs-lookup"><span data-stu-id="87ffc-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="87ffc-136">Les commandes EF Core utilisent la chaîne de connexion pour la base de données spécifiée dans *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-136">EF Core commands use the connection string for the database specified in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="87ffc-137">La chaîne de connexion suivante cible une base de données sur *localhost* nommée *asp-net-Core-Identity* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-137">The following connection string targets a database on *localhost* named *asp-net-core-identity* .</span></span> <span data-ttu-id="87ffc-138">Dans ce paramètre, EF Core est configurée pour utiliser la `DefaultConnection` chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="87ffc-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="87ffc-139">Sélectionnez **Afficher**  >  **Explorateur d’objets SQL Server** .</span><span class="sxs-lookup"><span data-stu-id="87ffc-139">Select **View** > **SQL Server Object Explorer** .</span></span> <span data-ttu-id="87ffc-140">Développez le nœud correspondant au nom de la base de données spécifié dans la `ConnectionStrings:DefaultConnection` propriété de *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *:::no-loc(appsettings.json):::* .</span></span>

    <span data-ttu-id="87ffc-141">La `Update-Database` commande a créé la base de données spécifiée avec le schéma et toutes les données nécessaires à l’initialisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="87ffc-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="87ffc-142">L’image suivante représente la structure de table créée à l’aide des étapes précédentes.</span><span class="sxs-lookup"><span data-stu-id="87ffc-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![::: No-Loc (Identity) ::: tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="87ffc-144">Migrer le schéma</span><span class="sxs-lookup"><span data-stu-id="87ffc-144">Migrate the schema</span></span>

<span data-ttu-id="87ffc-145">Il existe des différences subtiles entre les structures et les champs de table pour l’appartenance et :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="87ffc-145">There are subtle differences in the table structures and fields for both Membership and :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="87ffc-146">Le modèle a changé de manière substantielle pour l’authentification/l’autorisation avec les applications ASP.NET et ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87ffc-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="87ffc-147">Les objets clés qui sont toujours utilisés avec :::no-loc(Identity)::: sont *les utilisateurs et les* *rôles* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-147">The key objects that are still used with :::no-loc(Identity)::: are *Users* and *Roles* .</span></span> <span data-ttu-id="87ffc-148">Voici les tables de mappage pour *les utilisateurs, les* *rôles* et les *UserRoles* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-148">Here are mapping tables for *Users* , *Roles* , and *UserRoles* .</span></span>

### <a name="users"></a><span data-ttu-id="87ffc-149">Utilisateurs</span><span class="sxs-lookup"><span data-stu-id="87ffc-149">Users</span></span>

|:::no-loc(Identity):::<br><span data-ttu-id="87ffc-150">`dbo.AspNetUsers`colonne ()</span><span class="sxs-lookup"><span data-stu-id="87ffc-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="87ffc-151">Type</span><span class="sxs-lookup"><span data-stu-id="87ffc-151">Type</span></span>     |<span data-ttu-id="87ffc-152">Adhésion</span><span class="sxs-lookup"><span data-stu-id="87ffc-152">Membership</span></span><br><span data-ttu-id="87ffc-153">`dbo.aspnet_Users`  /  `dbo.aspnet_Membership` colonne ()</span><span class="sxs-lookup"><span data-stu-id="87ffc-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="87ffc-154">Type</span><span class="sxs-lookup"><span data-stu-id="87ffc-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="87ffc-155">Tous les mappages de champs ne ressemblent pas aux relations un-à-un de l’appartenance à :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="87ffc-155">Not all the field mappings resemble one-to-one relationships from Membership to :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="87ffc-156">Le tableau précédent prend le schéma de l’utilisateur d’appartenance par défaut et le mappe au :::no-loc(ASP.NET Core Identity)::: schéma.</span><span class="sxs-lookup"><span data-stu-id="87ffc-156">The preceding table takes the default Membership User schema and maps it to the :::no-loc(ASP.NET Core Identity)::: schema.</span></span> <span data-ttu-id="87ffc-157">Tous les autres champs personnalisés qui ont été utilisés pour l’appartenance doivent être mappés manuellement.</span><span class="sxs-lookup"><span data-stu-id="87ffc-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="87ffc-158">Dans ce mappage, il n’existe pas de mappage pour les mots de passe, car les critères de mot de passe et les sels de mot de passe ne migrent pas entre les deux.</span><span class="sxs-lookup"><span data-stu-id="87ffc-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="87ffc-159">**Il est recommandé de conserver la valeur null pour le mot de passe et de demander aux utilisateurs de réinitialiser leur mot de passe.**</span><span class="sxs-lookup"><span data-stu-id="87ffc-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="87ffc-160">Dans :::no-loc(ASP.NET Core Identity)::: , `LockoutEnd` doit être défini à une date ultérieure si l’utilisateur est verrouillé. Cela est illustré dans le script de migration.</span><span class="sxs-lookup"><span data-stu-id="87ffc-160">In :::no-loc(ASP.NET Core Identity):::, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="87ffc-161">Rôles</span><span class="sxs-lookup"><span data-stu-id="87ffc-161">Roles</span></span>

|:::no-loc(Identity):::<br><span data-ttu-id="87ffc-162">`dbo.AspNetRoles`colonne ()</span><span class="sxs-lookup"><span data-stu-id="87ffc-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="87ffc-163">Type</span><span class="sxs-lookup"><span data-stu-id="87ffc-163">Type</span></span>|<span data-ttu-id="87ffc-164">Adhésion</span><span class="sxs-lookup"><span data-stu-id="87ffc-164">Membership</span></span><br><span data-ttu-id="87ffc-165">`dbo.aspnet_Roles`colonne ()</span><span class="sxs-lookup"><span data-stu-id="87ffc-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="87ffc-166">Type</span><span class="sxs-lookup"><span data-stu-id="87ffc-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="87ffc-167">Rôles d'utilisateur</span><span class="sxs-lookup"><span data-stu-id="87ffc-167">User Roles</span></span>

|:::no-loc(Identity):::<br><span data-ttu-id="87ffc-168">`dbo.AspNetUserRoles`colonne ()</span><span class="sxs-lookup"><span data-stu-id="87ffc-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="87ffc-169">Type</span><span class="sxs-lookup"><span data-stu-id="87ffc-169">Type</span></span>|<span data-ttu-id="87ffc-170">Adhésion</span><span class="sxs-lookup"><span data-stu-id="87ffc-170">Membership</span></span><br><span data-ttu-id="87ffc-171">`dbo.aspnet_UsersInRoles`colonne ()</span><span class="sxs-lookup"><span data-stu-id="87ffc-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="87ffc-172">Type</span><span class="sxs-lookup"><span data-stu-id="87ffc-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="87ffc-173">Référencez les tables de mappage précédentes lors de la création d’un script de migration pour *les utilisateurs et les* *rôles* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles* .</span></span> <span data-ttu-id="87ffc-174">L’exemple suivant suppose que vous disposez de deux bases de données sur un serveur de base de données.</span><span class="sxs-lookup"><span data-stu-id="87ffc-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="87ffc-175">Une base de données contient le schéma et les données d’appartenance ASP.NET existants.</span><span class="sxs-lookup"><span data-stu-id="87ffc-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="87ffc-176">L’autre *:::no-loc(Identity)::: exemple* de base de données de base a été créé à l’aide des étapes décrites précédemment.</span><span class="sxs-lookup"><span data-stu-id="87ffc-176">The other *Core:::no-loc(Identity):::Sample* database was created using steps described earlier.</span></span> <span data-ttu-id="87ffc-177">Les commentaires sont inclus dans Inline pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="87ffc-177">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO Core:::no-loc(Identity):::Sample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in :::no-loc(ASP.NET Core Identity):::
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in :::no-loc(ASP.NET Core Identity):::
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN Core:::no-loc(Identity):::Sample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO Core:::no-loc(Identity):::Sample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO Core:::no-loc(Identity):::Sample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="87ffc-178">Une fois le script précédent terminé, l' :::no-loc(ASP.NET Core Identity)::: application créée précédemment est remplie avec les utilisateurs d’appartenance.</span><span class="sxs-lookup"><span data-stu-id="87ffc-178">After completion of the preceding script, the :::no-loc(ASP.NET Core Identity)::: app created earlier is populated with Membership users.</span></span> <span data-ttu-id="87ffc-179">Les utilisateurs doivent modifier leur mot de passe avant de se connecter.</span><span class="sxs-lookup"><span data-stu-id="87ffc-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="87ffc-180">Si le système d’appartenance avait des utilisateurs dont les noms d’utilisateur ne correspondaient pas à leur adresse de messagerie, les modifications sont requises pour l’application créée précédemment pour s’adapter à cette opération.</span><span class="sxs-lookup"><span data-stu-id="87ffc-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="87ffc-181">Le modèle par défaut `UserName` est attendu et `Email` doit être le même.</span><span class="sxs-lookup"><span data-stu-id="87ffc-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="87ffc-182">Dans les situations où elles sont différentes, le processus de connexion doit être modifié pour utiliser `UserName` au lieu de `Email` .</span><span class="sxs-lookup"><span data-stu-id="87ffc-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="87ffc-183">Dans le `PageModel` de la page de connexion, situé dans *Pages\Account\Login.cshtml.cs* , supprimez l' `[EmailAddress]` attribut de la propriété *e-mail* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs* , remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="87ffc-184">Renommez-le nom d' *utilisateur* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-184">Rename it to *UserName* .</span></span> <span data-ttu-id="87ffc-185">Pour ce faire, vous devez modifier chaque fois `EmailAddress` qu’il est mentionné dans la *vue* et *PageModel* .</span><span class="sxs-lookup"><span data-stu-id="87ffc-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel* .</span></span> <span data-ttu-id="87ffc-186">Le résultat se présente comme suit :</span><span class="sxs-lookup"><span data-stu-id="87ffc-186">The result looks like the following:</span></span>

 ![Connexion fixe](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="87ffc-188">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="87ffc-188">Next steps</span></span>

<span data-ttu-id="87ffc-189">Dans ce didacticiel, vous avez appris comment porter les utilisateurs de l’appartenance SQL vers ASP.NET Core 2,0 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="87ffc-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 :::no-loc(Identity):::.</span></span> <span data-ttu-id="87ffc-190">Pour plus d’informations sur :::no-loc(ASP.NET Core Identity)::: , consultez [présentation :::no-loc(Identity)::: de ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="87ffc-190">For more information regarding :::no-loc(ASP.NET Core Identity):::, see [Introduction to :::no-loc(Identity):::](xref:security/authentication/identity).</span></span>
