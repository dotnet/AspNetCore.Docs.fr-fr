---
title: ':::no-loc(Identity)::: personnalisation de modèle dans ASP.NET Core'
author: ajcvickers
description: 'Cet article explique comment personnaliser le modèle de données Entity Framework Core sous-jacent pour :::no-loc(ASP.NET Core Identity)::: .'
ms.author: avickers
ms.date: 07/01/2019
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
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052043"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a><span data-ttu-id="1f537-103">:::no-loc(Identity)::: personnalisation de modèle dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f537-103">:::no-loc(Identity)::: model customization in ASP.NET Core</span></span>

<span data-ttu-id="1f537-104">Par [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="1f537-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="1f537-105">:::no-loc(ASP.NET Core Identity)::: fournit une infrastructure pour la gestion et le stockage des comptes d’utilisateur dans les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f537-105">:::no-loc(ASP.NET Core Identity)::: provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="1f537-106">:::no-loc(Identity)::: est ajouté à votre projet lorsque **des comptes d’utilisateur individuels** est sélectionné comme mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1f537-106">:::no-loc(Identity)::: is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="1f537-107">Par défaut, utilise :::no-loc(Identity)::: un modèle de données de base Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="1f537-107">By default, :::no-loc(Identity)::: makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="1f537-108">Cet article explique comment personnaliser le :::no-loc(Identity)::: modèle.</span><span class="sxs-lookup"><span data-stu-id="1f537-108">This article describes how to customize the :::no-loc(Identity)::: model.</span></span>

## <a name="no-locidentity-and-ef-core-migrations"></a><span data-ttu-id="1f537-109">:::no-loc(Identity)::: Migrations et EF Core</span><span class="sxs-lookup"><span data-stu-id="1f537-109">:::no-loc(Identity)::: and EF Core Migrations</span></span>

<span data-ttu-id="1f537-110">Avant d’examiner le modèle, il est utile de comprendre comment :::no-loc(Identity)::: fonctionne avec [EF Core migrations](/ef/core/managing-schemas/migrations/) pour créer et mettre à jour une base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-110">Before examining the model, it's useful to understand how :::no-loc(Identity)::: works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="1f537-111">Au niveau supérieur, le processus est le suivant :</span><span class="sxs-lookup"><span data-stu-id="1f537-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="1f537-112">Définir ou mettre à jour un [modèle de données dans le code](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="1f537-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="1f537-113">Ajoutez une migration pour traduire ce modèle en modifications qui peuvent être appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="1f537-114">Vérifiez que la migration représente correctement vos intentions.</span><span class="sxs-lookup"><span data-stu-id="1f537-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="1f537-115">Appliquez la migration pour mettre à jour la base de données afin qu’elle soit synchronisée avec le modèle.</span><span class="sxs-lookup"><span data-stu-id="1f537-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="1f537-116">Répétez les étapes 1 à 4 pour affiner le modèle et maintenir la synchronisation de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="1f537-117">Utilisez l’une des approches suivantes pour ajouter et appliquer des migrations :</span><span class="sxs-lookup"><span data-stu-id="1f537-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="1f537-118">La fenêtre **console du gestionnaire de package** (PMC) si vous utilisez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1f537-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="1f537-119">Pour plus d’informations, consultez [EF Core les outils PMC](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="1f537-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="1f537-120">CLI .NET Core en cas d’utilisation de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="1f537-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="1f537-121">Pour plus d’informations, consultez [EF Core les outils en ligne de commande .net](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="1f537-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="1f537-122">En cliquant sur le bouton **appliquer des migrations** sur la page d’erreur lors de l’exécution de l’application.</span><span class="sxs-lookup"><span data-stu-id="1f537-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="1f537-123">ASP.NET Core a un gestionnaire de page d’erreurs au moment du développement.</span><span class="sxs-lookup"><span data-stu-id="1f537-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="1f537-124">Le gestionnaire peut appliquer des migrations lorsque l’application est exécutée.</span><span class="sxs-lookup"><span data-stu-id="1f537-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="1f537-125">Les applications de production génèrent généralement des scripts SQL à partir des migrations et déploient des modifications de base de données dans le cadre d’un déploiement d’application contrôlé et de base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="1f537-126">Quand une nouvelle application utilisant :::no-loc(Identity)::: est créée, les étapes 1 et 2 ci-dessus ont déjà été effectuées.</span><span class="sxs-lookup"><span data-stu-id="1f537-126">When a new app using :::no-loc(Identity)::: is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="1f537-127">Autrement dit, le modèle de données initial existe déjà et la migration initiale a été ajoutée au projet.</span><span class="sxs-lookup"><span data-stu-id="1f537-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="1f537-128">La migration initiale doit toujours être appliquée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="1f537-129">La migration initiale peut être appliquée via l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="1f537-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="1f537-130">Exécutez `Update-Database` dans PMC.</span><span class="sxs-lookup"><span data-stu-id="1f537-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="1f537-131">Exécutez `dotnet ef database update` dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="1f537-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="1f537-132">Cliquez sur le bouton **appliquer des migrations** sur la page d’erreur lors de l’exécution de l’application.</span><span class="sxs-lookup"><span data-stu-id="1f537-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="1f537-133">Répétez les étapes précédentes au fur et à mesure que des modifications sont apportées au modèle.</span><span class="sxs-lookup"><span data-stu-id="1f537-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-no-locidentity-model"></a><span data-ttu-id="1f537-134">Le :::no-loc(Identity)::: modèle</span><span class="sxs-lookup"><span data-stu-id="1f537-134">The :::no-loc(Identity)::: model</span></span>

### <a name="entity-types"></a><span data-ttu-id="1f537-135">Types d’entités</span><span class="sxs-lookup"><span data-stu-id="1f537-135">Entity types</span></span>

<span data-ttu-id="1f537-136">Le :::no-loc(Identity)::: modèle se compose des types d’entités suivants.</span><span class="sxs-lookup"><span data-stu-id="1f537-136">The :::no-loc(Identity)::: model consists of the following entity types.</span></span>

|<span data-ttu-id="1f537-137">Type d'entité</span><span class="sxs-lookup"><span data-stu-id="1f537-137">Entity type</span></span>|<span data-ttu-id="1f537-138">Description</span><span class="sxs-lookup"><span data-stu-id="1f537-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="1f537-139">Représente l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1f537-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="1f537-140">Représente un rôle.</span><span class="sxs-lookup"><span data-stu-id="1f537-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="1f537-141">Représente une revendication qu’un utilisateur possède.</span><span class="sxs-lookup"><span data-stu-id="1f537-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="1f537-142">Représente un jeton d’authentification pour un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1f537-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="1f537-143">Associe un utilisateur à une connexion.</span><span class="sxs-lookup"><span data-stu-id="1f537-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="1f537-144">Représente une revendication accordée à tous les utilisateurs d’un rôle.</span><span class="sxs-lookup"><span data-stu-id="1f537-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="1f537-145">Entité de jointure qui associe les utilisateurs et les rôles.</span><span class="sxs-lookup"><span data-stu-id="1f537-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="1f537-146">Relations de type d’entité</span><span class="sxs-lookup"><span data-stu-id="1f537-146">Entity type relationships</span></span>

<span data-ttu-id="1f537-147">Les [types d’entités](#entity-types) sont liés les uns aux autres des manières suivantes :</span><span class="sxs-lookup"><span data-stu-id="1f537-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="1f537-148">Chaque `User` peut avoir plusieurs `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="1f537-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="1f537-149">Chaque `User` peut avoir plusieurs `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="1f537-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="1f537-150">Chaque `User` peut avoir plusieurs `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="1f537-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="1f537-151">Chaque `Role` peut être associé à plusieurs `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="1f537-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="1f537-152">Chaque `User` peut être associé à plusieurs `Roles` , et chaque peut `Role` être associé à plusieurs `Users` .</span><span class="sxs-lookup"><span data-stu-id="1f537-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="1f537-153">Il s’agit d’une relation plusieurs-à-plusieurs qui requiert une table de jointure dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="1f537-154">La table de jointure est représentée par l' `UserRole` entité.</span><span class="sxs-lookup"><span data-stu-id="1f537-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="1f537-155">Configuration de modèle par défaut</span><span class="sxs-lookup"><span data-stu-id="1f537-155">Default model configuration</span></span>

<span data-ttu-id="1f537-156">:::no-loc(Identity)::: définit de nombreuses *classes de contexte* qui héritent de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) pour configurer et utiliser le modèle.</span><span class="sxs-lookup"><span data-stu-id="1f537-156">:::no-loc(Identity)::: defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="1f537-157">Cette configuration s’effectue à l’aide de l' [API EF Core code First Fluent](/ef/core/modeling/) dans la méthode [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) de la classe Context.</span><span class="sxs-lookup"><span data-stu-id="1f537-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="1f537-158">La configuration par défaut est la suivante :</span><span class="sxs-lookup"><span data-stu-id="1f537-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="1f537-159">Types génériques de modèle</span><span class="sxs-lookup"><span data-stu-id="1f537-159">Model generic types</span></span>

<span data-ttu-id="1f537-160">:::no-loc(Identity)::: définit les types CLR ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) par défaut pour chacun des types d’entité listés ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1f537-160">:::no-loc(Identity)::: defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="1f537-161">Le préfixe de ces types est le *:::no-loc(Identity):::* suivant :</span><span class="sxs-lookup"><span data-stu-id="1f537-161">These types are all prefixed with *:::no-loc(Identity):::* :</span></span>

* `:::no-loc(Identity):::User`
* `:::no-loc(Identity):::Role`
* `:::no-loc(Identity):::UserClaim`
* `:::no-loc(Identity):::UserToken`
* `:::no-loc(Identity):::UserLogin`
* `:::no-loc(Identity):::RoleClaim`
* `:::no-loc(Identity):::UserRole`

<span data-ttu-id="1f537-162">Au lieu d’utiliser directement ces types, les types peuvent être utilisés comme classes de base pour les types de l’application.</span><span class="sxs-lookup"><span data-stu-id="1f537-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="1f537-163">Les `DbContext` classes définies par :::no-loc(Identity)::: sont génériques, de sorte que différents types CLR peuvent être utilisés pour un ou plusieurs types d’entité dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="1f537-163">The `DbContext` classes defined by :::no-loc(Identity)::: are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="1f537-164">Ces types génériques permettent également `User` de modifier le type de données de la clé primaire (PK).</span><span class="sxs-lookup"><span data-stu-id="1f537-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="1f537-165">Lors de l’utilisation de :::no-loc(Identity)::: avec la prise en charge des rôles, une <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> classe doit être utilisée.</span><span class="sxs-lookup"><span data-stu-id="1f537-165">When using :::no-loc(Identity)::: with support for roles, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> class should be used.</span></span> <span data-ttu-id="1f537-166">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1f537-166">For example:</span></span>

```csharp
// Uses all the built-in :::no-loc(Identity)::: types
// Uses `string` as the key type
public class :::no-loc(Identity):::DbContext
    : :::no-loc(Identity):::DbContext<:::no-loc(Identity):::User, :::no-loc(Identity):::Role, string>
{
}

// Uses the built-in :::no-loc(Identity)::: types except with a custom User type
// Uses `string` as the key type
public class :::no-loc(Identity):::DbContext<TUser>
    : :::no-loc(Identity):::DbContext<TUser, :::no-loc(Identity):::Role, string>
        where TUser : :::no-loc(Identity):::User
{
}

// Uses the built-in :::no-loc(Identity)::: types except with custom User and Role types
// The key type is defined by TKey
public class :::no-loc(Identity):::DbContext<TUser, TRole, TKey> : :::no-loc(Identity):::DbContext<
    TUser, TRole, TKey, :::no-loc(Identity):::UserClaim<TKey>, :::no-loc(Identity):::UserRole<TKey>,
    :::no-loc(Identity):::UserLogin<TKey>, :::no-loc(Identity):::RoleClaim<TKey>, :::no-loc(Identity):::UserToken<TKey>>
        where TUser : :::no-loc(Identity):::User<TKey>
        where TRole : :::no-loc(Identity):::Role<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in :::no-loc(Identity)::: types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class :::no-loc(Identity):::DbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : :::no-loc(Identity):::UserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : :::no-loc(Identity):::User<TKey>
         where TRole : :::no-loc(Identity):::Role<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : :::no-loc(Identity):::UserClaim<TKey>
         where TUserRole : :::no-loc(Identity):::UserRole<TKey>
         where TUserLogin : :::no-loc(Identity):::UserLogin<TKey>
         where TRoleClaim : :::no-loc(Identity):::RoleClaim<TKey>
         where TUserToken : :::no-loc(Identity):::UserToken<TKey>
```

<span data-ttu-id="1f537-167">Il est également possible d’utiliser :::no-loc(Identity)::: sans rôle (uniquement les revendications), auquel cas une <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::UserContext%601> classe doit être utilisée :</span><span class="sxs-lookup"><span data-stu-id="1f537-167">It's also possible to use :::no-loc(Identity)::: without roles (only claims), in which case an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::UserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role :::no-loc(Identity)::: types except with a custom User type
// Uses `string` as the key type
public class :::no-loc(Identity):::UserContext<TUser>
    : :::no-loc(Identity):::UserContext<TUser, string>
        where TUser : :::no-loc(Identity):::User
{
}

// Uses the built-in non-role :::no-loc(Identity)::: types except with a custom User type
// The key type is defined by TKey
public class :::no-loc(Identity):::UserContext<TUser, TKey> : :::no-loc(Identity):::UserContext<
    TUser, TKey, :::no-loc(Identity):::UserClaim<TKey>, :::no-loc(Identity):::UserLogin<TKey>,
    :::no-loc(Identity):::UserToken<TKey>>
        where TUser : :::no-loc(Identity):::User<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in :::no-loc(Identity)::: types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class :::no-loc(Identity):::UserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : :::no-loc(Identity):::User<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : :::no-loc(Identity):::UserClaim<TKey>
        where TUserLogin : :::no-loc(Identity):::UserLogin<TKey>
        where TUserToken : :::no-loc(Identity):::UserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="1f537-168">Personnaliser le modèle</span><span class="sxs-lookup"><span data-stu-id="1f537-168">Customize the model</span></span>

<span data-ttu-id="1f537-169">Le point de départ pour la personnalisation de modèle consiste à dériver du type de contexte approprié.</span><span class="sxs-lookup"><span data-stu-id="1f537-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="1f537-170">Consultez la section [types génériques de modèle](#model-generic-types) .</span><span class="sxs-lookup"><span data-stu-id="1f537-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="1f537-171">Ce type de contexte est habituellement appelé `ApplicationDbContext` et est créé par les modèles ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="1f537-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="1f537-172">Le contexte est utilisé pour configurer le modèle de deux manières :</span><span class="sxs-lookup"><span data-stu-id="1f537-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="1f537-173">Fourniture des types d’entité et de clé pour les paramètres de type générique.</span><span class="sxs-lookup"><span data-stu-id="1f537-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="1f537-174">Substitution `OnModelCreating` pour modifier le mappage de ces types.</span><span class="sxs-lookup"><span data-stu-id="1f537-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="1f537-175">Lors de la substitution `OnModelCreating` , `base.OnModelCreating` doit être appelé en premier ; la configuration de substitution doit être appelée Next.</span><span class="sxs-lookup"><span data-stu-id="1f537-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="1f537-176">EF Core a généralement une stratégie dernière-un WINS pour la configuration.</span><span class="sxs-lookup"><span data-stu-id="1f537-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="1f537-177">Par exemple, si la `ToTable` méthode d’un type d’entité est appelée en premier avec un nom de table, puis à nouveau ultérieurement avec un nom de table différent, le nom de la table dans le deuxième appel est utilisé.</span><span class="sxs-lookup"><span data-stu-id="1f537-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="1f537-178">Données utilisateur personnalisées</span><span class="sxs-lookup"><span data-stu-id="1f537-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
 -->

<span data-ttu-id="1f537-179">Les [données utilisateur personnalisées](xref:security/authentication/add-user-data) sont prises en charge par l’héritage de `:::no-loc(Identity):::User` .</span><span class="sxs-lookup"><span data-stu-id="1f537-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `:::no-loc(Identity):::User`.</span></span> <span data-ttu-id="1f537-180">Il est personnalisé pour nommer ce type `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="1f537-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="1f537-181">Utilisez le `ApplicationUser` type comme argument générique pour le contexte :</span><span class="sxs-lookup"><span data-stu-id="1f537-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="1f537-182">Il n’est pas nécessaire de remplacer `OnModelCreating` dans la `ApplicationDbContext` classe.</span><span class="sxs-lookup"><span data-stu-id="1f537-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="1f537-183">EF Core mappe la `CustomTag` propriété par Convention.</span><span class="sxs-lookup"><span data-stu-id="1f537-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="1f537-184">Toutefois, la base de données doit être mise à jour pour créer une nouvelle `CustomTag` colonne.</span><span class="sxs-lookup"><span data-stu-id="1f537-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="1f537-185">Pour créer la colonne, ajoutez une migration, puis mettez à jour la base de données comme décrit dans [ :::no-loc(Identity)::: et EF Core migrations](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="1f537-185">To create the column, add a migration, and then update the database as described in [:::no-loc(Identity)::: and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="1f537-186">Mettez à jour *pages/Shared/_LoginPartial. cshtml* et remplacez `:::no-loc(Identity):::User` par `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="1f537-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `:::no-loc(Identity):::User` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.:::no-loc(Identity):::
@using WebApp1.Areas.:::no-loc(Identity):::.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="1f537-187">Mettez à jour *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* ou `Startup.ConfigureServices` et remplacez `:::no-loc(Identity):::User` par `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="1f537-187">Update *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*  or `Startup.ConfigureServices` and replace `:::no-loc(Identity):::User` with `ApplicationUser`.</span></span>

```csharp
services.Add:::no-loc(Identity):::<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="1f537-188">Dans ASP.NET Core 2,1 ou version ultérieure, :::no-loc(Identity)::: est fourni en tant que :::no-loc(Razor)::: bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="1f537-188">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="1f537-189">Pour plus d'informations, consultez <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="1f537-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="1f537-190">Par conséquent, le code précédent requiert un appel à <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="1f537-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="1f537-191">Si le générateur de :::no-loc(Identity)::: modèles automatique a été utilisé pour ajouter des :::no-loc(Identity)::: fichiers au projet, supprimez l’appel à `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="1f537-191">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="1f537-192">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="1f537-192">For more information, see:</span></span>

* [<span data-ttu-id="1f537-193">Destin :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1f537-193">Scaffold :::no-loc(Identity):::</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="1f537-194">Ajouter, télécharger et supprimer des données utilisateur personnalisées :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="1f537-194">Add, download, and delete custom user data to :::no-loc(Identity):::</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="1f537-195">Modifier le type de clé primaire</span><span class="sxs-lookup"><span data-stu-id="1f537-195">Change the primary key type</span></span>

<span data-ttu-id="1f537-196">Une modification du type de données de la colonne de clé primaire après la création de la base de données est problématique sur de nombreux systèmes de base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="1f537-197">La modification de la clé primaire implique généralement la suppression et la recréation de la table.</span><span class="sxs-lookup"><span data-stu-id="1f537-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="1f537-198">Par conséquent, les types de clés doivent être spécifiés lors de la migration initiale lors de la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="1f537-199">Pour modifier le type de clé primaire, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="1f537-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="1f537-200">Si la base de données a été créée avant la modification de la clé primaire, exécutez `Drop-Database` (PMC) ou `dotnet ef database drop` (CLI .net Core) pour la supprimer.</span><span class="sxs-lookup"><span data-stu-id="1f537-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="1f537-201">Après confirmation de la suppression de la base de données, supprimez la migration initiale avec `Remove-Migration` (PMC) ou `dotnet ef migrations remove` (CLI .net Core).</span><span class="sxs-lookup"><span data-stu-id="1f537-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="1f537-202">Mettez à jour la `ApplicationDbContext` classe à partir de laquelle dériver <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext%603> .</span><span class="sxs-lookup"><span data-stu-id="1f537-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext%603>.</span></span> <span data-ttu-id="1f537-203">Spécifiez le nouveau type de clé pour `TKey` .</span><span class="sxs-lookup"><span data-stu-id="1f537-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="1f537-204">Par exemple, pour utiliser un `Guid` type de clé :</span><span class="sxs-lookup"><span data-stu-id="1f537-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : :::no-loc(Identity):::DbContext<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="1f537-205">Dans le code précédent, les classes génériques <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User%601> et <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Role%601> doivent être spécifiées pour utiliser le nouveau type de clé.</span><span class="sxs-lookup"><span data-stu-id="1f537-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Role%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="1f537-206">Dans le code précédent, les classes génériques <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::User%601> et <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::Role%601> doivent être spécifiées pour utiliser le nouveau type de clé.</span><span class="sxs-lookup"><span data-stu-id="1f537-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::User%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::Role%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="1f537-207">`Startup.ConfigureServices` doit être mis à jour pour utiliser l’utilisateur générique :</span><span class="sxs-lookup"><span data-stu-id="1f537-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefault:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.Add:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.Add:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="1f537-208">Si une `ApplicationUser` classe personnalisée est utilisée, mettez à jour la classe pour qu’elle hérite de `:::no-loc(Identity):::User` .</span><span class="sxs-lookup"><span data-stu-id="1f537-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `:::no-loc(Identity):::User`.</span></span> <span data-ttu-id="1f537-209">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1f537-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="1f537-210">Mettez à jour `ApplicationDbContext` pour référencer la `ApplicationUser` classe personnalisée :</span><span class="sxs-lookup"><span data-stu-id="1f537-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : :::no-loc(Identity):::DbContext<ApplicationUser, :::no-loc(Identity):::Role<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="1f537-211">Inscrire la classe de contexte de base de données personnalisée lors de l’ajout du :::no-loc(Identity)::: service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1f537-211">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="1f537-212">Le type de données de la clé primaire est déduit en analysant l’objet [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="1f537-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="1f537-213">Dans ASP.NET Core 2,1 ou version ultérieure, :::no-loc(Identity)::: est fourni en tant que :::no-loc(Razor)::: bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="1f537-213">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="1f537-214">Pour plus d'informations, consultez <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="1f537-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="1f537-215">Par conséquent, le code précédent requiert un appel à <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="1f537-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="1f537-216">Si le générateur de :::no-loc(Identity)::: modèles automatique a été utilisé pour ajouter des :::no-loc(Identity)::: fichiers au projet, supprimez l’appel à `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="1f537-216">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="1f537-217">Le type de données de la clé primaire est déduit en analysant l’objet [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="1f537-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="1f537-218">La <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> méthode accepte un `TKey` type indiquant le type de données de la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="1f537-218">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="1f537-219">Si une `ApplicationRole` classe personnalisée est utilisée, mettez à jour la classe pour qu’elle hérite de `:::no-loc(Identity):::Role<TKey>` .</span><span class="sxs-lookup"><span data-stu-id="1f537-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `:::no-loc(Identity):::Role<TKey>`.</span></span> <span data-ttu-id="1f537-220">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1f537-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="1f537-221">Mettez à jour `ApplicationDbContext` pour référencer la `ApplicationRole` classe personnalisée.</span><span class="sxs-lookup"><span data-stu-id="1f537-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="1f537-222">Par exemple, la classe suivante référence un personnalisé `ApplicationUser` et un personnalisé `ApplicationRole` :</span><span class="sxs-lookup"><span data-stu-id="1f537-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="1f537-223">Inscrire la classe de contexte de base de données personnalisée lors de l’ajout du :::no-loc(Identity)::: service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1f537-223">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="1f537-224">Le type de données de la clé primaire est déduit en analysant l’objet [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="1f537-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="1f537-225">Dans ASP.NET Core 2,1 ou version ultérieure, :::no-loc(Identity)::: est fourni en tant que :::no-loc(Razor)::: bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="1f537-225">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="1f537-226">Pour plus d'informations, consultez <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="1f537-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="1f537-227">Par conséquent, le code précédent requiert un appel à <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="1f537-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="1f537-228">Si le générateur de :::no-loc(Identity)::: modèles automatique a été utilisé pour ajouter des :::no-loc(Identity)::: fichiers au projet, supprimez l’appel à `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="1f537-228">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="1f537-229">Inscrire la classe de contexte de base de données personnalisée lors de l’ajout du :::no-loc(Identity)::: service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1f537-229">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/:::no-loc(Razor):::PagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="1f537-230">Le type de données de la clé primaire est déduit en analysant l’objet [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="1f537-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="1f537-231">Inscrire la classe de contexte de base de données personnalisée lors de l’ajout du :::no-loc(Identity)::: service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1f537-231">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="1f537-232">La <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> méthode accepte un `TKey` type indiquant le type de données de la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="1f537-232">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="1f537-233">Ajouter des propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="1f537-233">Add navigation properties</span></span>

<span data-ttu-id="1f537-234">La modification de la configuration du modèle pour les relations peut être plus difficile que d’apporter d’autres modifications.</span><span class="sxs-lookup"><span data-stu-id="1f537-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="1f537-235">Il faut veiller à remplacer les relations existantes plutôt qu’à créer de nouvelles relations.</span><span class="sxs-lookup"><span data-stu-id="1f537-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="1f537-236">En particulier, la relation modifiée doit spécifier la même propriété de clé étrangère (FK) que la relation existante.</span><span class="sxs-lookup"><span data-stu-id="1f537-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="1f537-237">Par exemple, la relation entre `Users` et `UserClaims` est, par défaut, spécifiée comme suit :</span><span class="sxs-lookup"><span data-stu-id="1f537-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="1f537-238">Le FK pour cette relation est spécifié en tant que `UserClaim.UserId` propriété.</span><span class="sxs-lookup"><span data-stu-id="1f537-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="1f537-239">`HasMany` et `WithOne` sont appelées sans arguments pour créer la relation sans propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="1f537-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="1f537-240">Ajoutez une propriété de navigation à `ApplicationUser` qui permet à d' `UserClaims` être référencée à partir de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="1f537-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="1f537-241">`TKey`Pour `:::no-loc(Identity):::UserClaim<TKey>` est le type spécifié pour le PK des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="1f537-241">The `TKey` for `:::no-loc(Identity):::UserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="1f537-242">Dans ce cas, `TKey` est `string` dû au fait que les valeurs par défaut sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="1f537-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="1f537-243">Il ne s’agit **pas** du type de clé primaire pour le `UserClaim` type d’entité.</span><span class="sxs-lookup"><span data-stu-id="1f537-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="1f537-244">Maintenant que la propriété de navigation existe, elle doit être configurée dans `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="1f537-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="1f537-245">Notez que la relation est configurée exactement telle qu’elle était avant, uniquement avec une propriété de navigation spécifiée dans l’appel à `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="1f537-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="1f537-246">Les propriétés de navigation existent uniquement dans le modèle EF, pas dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="1f537-247">Étant donné que le FK de la relation n’a pas changé, ce type de modification du modèle n’exige pas la mise à jour de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="1f537-248">Vous pouvez vérifier cela en ajoutant une migration après avoir apporté la modification.</span><span class="sxs-lookup"><span data-stu-id="1f537-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="1f537-249">Les `Up` `Down` méthodes et sont vides.</span><span class="sxs-lookup"><span data-stu-id="1f537-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="1f537-250">Ajouter toutes les propriétés de navigation de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="1f537-250">Add all User navigation properties</span></span>

<span data-ttu-id="1f537-251">À l’aide de la section ci-dessus, comme aide, l’exemple suivant configure les propriétés de navigation unidirectionnelles pour toutes les relations sur l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="1f537-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserLogin<string>> Logins { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserToken<string>> Tokens { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="1f537-252">Ajouter des propriétés de navigation utilisateur et de rôle</span><span class="sxs-lookup"><span data-stu-id="1f537-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="1f537-253">À l’aide de la section ci-dessus, en guise d’aide, l’exemple suivant configure les propriétés de navigation pour toutes les relations sur l’utilisateur et le rôle :</span><span class="sxs-lookup"><span data-stu-id="1f537-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserLogin<string>> Logins { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : :::no-loc(Identity):::Role
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : :::no-loc(Identity):::UserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : :::no-loc(Identity):::DbContext<
        ApplicationUser, ApplicationRole, string,
        :::no-loc(Identity):::UserClaim<string>, ApplicationUserRole, :::no-loc(Identity):::UserLogin<string>,
        :::no-loc(Identity):::RoleClaim<string>, :::no-loc(Identity):::UserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="1f537-254">Remarques :</span><span class="sxs-lookup"><span data-stu-id="1f537-254">Notes:</span></span>

* <span data-ttu-id="1f537-255">Cet exemple comprend également l' `UserRole` entité de jointure, qui est nécessaire pour parcourir la relation plusieurs-à-plusieurs des utilisateurs aux rôles.</span><span class="sxs-lookup"><span data-stu-id="1f537-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="1f537-256">N’oubliez pas de modifier les types des propriétés de navigation pour refléter le fait que les `Application{...}` types sont maintenant utilisés à la place des `:::no-loc(Identity):::{...}` types.</span><span class="sxs-lookup"><span data-stu-id="1f537-256">Remember to change the types of the navigation properties to reflect that `Application{...}` types are now being used instead of `:::no-loc(Identity):::{...}` types.</span></span>
* <span data-ttu-id="1f537-257">N’oubliez pas d’utiliser `Application{...}` dans la `ApplicationContext` définition générique.</span><span class="sxs-lookup"><span data-stu-id="1f537-257">Remember to use the `Application{...}` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="1f537-258">Ajouter toutes les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="1f537-258">Add all navigation properties</span></span>

<span data-ttu-id="1f537-259">À l’aide de la section ci-dessus, en guise d’aide, l’exemple suivant configure les propriétés de navigation pour toutes les relations sur tous les types d’entité :</span><span class="sxs-lookup"><span data-stu-id="1f537-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : :::no-loc(Identity):::Role
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : :::no-loc(Identity):::UserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : :::no-loc(Identity):::UserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : :::no-loc(Identity):::UserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : :::no-loc(Identity):::RoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : :::no-loc(Identity):::UserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : :::no-loc(Identity):::DbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="1f537-260">Utiliser des clés composites</span><span class="sxs-lookup"><span data-stu-id="1f537-260">Use composite keys</span></span>

<span data-ttu-id="1f537-261">Les sections précédentes ont démontré la modification du type de clé utilisé dans le :::no-loc(Identity)::: modèle.</span><span class="sxs-lookup"><span data-stu-id="1f537-261">The preceding sections demonstrated changing the type of key used in the :::no-loc(Identity)::: model.</span></span> <span data-ttu-id="1f537-262">La modification du :::no-loc(Identity)::: modèle de clé pour utiliser des clés composites n’est pas prise en charge ou recommandée.</span><span class="sxs-lookup"><span data-stu-id="1f537-262">Changing the :::no-loc(Identity)::: key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="1f537-263">L’utilisation d’une clé composite avec :::no-loc(Identity)::: implique de modifier la façon dont le :::no-loc(Identity)::: Code du gestionnaire interagit avec le modèle.</span><span class="sxs-lookup"><span data-stu-id="1f537-263">Using a composite key with :::no-loc(Identity)::: involves changing how the :::no-loc(Identity)::: manager code interacts with the model.</span></span> <span data-ttu-id="1f537-264">Cette personnalisation n’est pas traitée dans ce document.</span><span class="sxs-lookup"><span data-stu-id="1f537-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="1f537-265">Modifier les facettes et les noms de table/colonne</span><span class="sxs-lookup"><span data-stu-id="1f537-265">Change table/column names and facets</span></span>

<span data-ttu-id="1f537-266">Pour modifier les noms des tables et des colonnes, appelez `base.OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="1f537-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="1f537-267">Ensuite, ajoutez une configuration pour remplacer les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f537-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="1f537-268">Par exemple, pour modifier le nom de toutes les :::no-loc(Identity)::: tables :</span><span class="sxs-lookup"><span data-stu-id="1f537-268">For example, to change the name of all the :::no-loc(Identity)::: tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<:::no-loc(Identity):::Role>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<:::no-loc(Identity):::RoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="1f537-269">Ces exemples utilisent les types par défaut :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="1f537-269">These examples use the default :::no-loc(Identity)::: types.</span></span> <span data-ttu-id="1f537-270">Si vous utilisez un type d’application tel que `ApplicationUser` , configurez ce type à la place du type par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f537-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="1f537-271">L’exemple suivant modifie certains noms de colonne :</span><span class="sxs-lookup"><span data-stu-id="1f537-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="1f537-272">Certains types de colonnes de base de données peuvent être configurés avec certaines *facettes* (par exemple, la `string` longueur maximale autorisée).</span><span class="sxs-lookup"><span data-stu-id="1f537-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="1f537-273">L’exemple suivant définit les longueurs maximales des colonnes pour plusieurs `string` Propriétés dans le modèle :</span><span class="sxs-lookup"><span data-stu-id="1f537-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="1f537-274">Mapper à un schéma différent</span><span class="sxs-lookup"><span data-stu-id="1f537-274">Map to a different schema</span></span>

<span data-ttu-id="1f537-275">Les schémas peuvent se comporter différemment entre les fournisseurs de base de données.</span><span class="sxs-lookup"><span data-stu-id="1f537-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="1f537-276">Par SQL Server, la valeur par défaut consiste à créer toutes les tables dans le schéma *dbo* .</span><span class="sxs-lookup"><span data-stu-id="1f537-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="1f537-277">Les tables peuvent être créées dans un schéma différent.</span><span class="sxs-lookup"><span data-stu-id="1f537-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="1f537-278">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1f537-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="1f537-279">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="1f537-279">Lazy loading</span></span>

<span data-ttu-id="1f537-280">Dans cette section, la prise en charge des proxies de chargement différé dans le :::no-loc(Identity)::: modèle est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="1f537-280">In this section, support for lazy-loading proxies in the :::no-loc(Identity)::: model is added.</span></span> <span data-ttu-id="1f537-281">Le chargement différé est utile, car il permet d’utiliser les propriétés de navigation sans s’assurer d’abord qu’elles sont chargées.</span><span class="sxs-lookup"><span data-stu-id="1f537-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="1f537-282">Les types d’entités peuvent être adaptés au chargement différé de plusieurs façons, comme décrit dans la [documentation EF Core](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="1f537-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1f537-283">Pour plus de simplicité, utilisez des proxies à chargement différé, qui requièrent les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1f537-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="1f537-284">Installation du package [Microsoft. EntityFrameworkCore. proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .</span><span class="sxs-lookup"><span data-stu-id="1f537-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="1f537-285">Appel à <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> l’intérieur [de \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span><span class="sxs-lookup"><span data-stu-id="1f537-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="1f537-286">Types d’entité publics avec des `public virtual` Propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="1f537-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="1f537-287">L’exemple suivant illustre l’appel `UseLazyLoadingProxies` de dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1f537-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefault:::no-loc(Identity):::<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="1f537-288">Reportez-vous aux exemples précédents pour obtenir des conseils sur l’ajout de propriétés de navigation aux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="1f537-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f537-289">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1f537-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
