---
title: Identity personnalisation de modèle dans ASP.NET Core
author: ajcvickers
description: Cet article explique comment personnaliser le modèle de données Entity Framework Core sous-jacent pour ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
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
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052043"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a>Identity personnalisation de modèle dans ASP.NET Core

Par [Arthur Vickers](https://github.com/ajcvickers)

ASP.NET Core Identity fournit une infrastructure pour la gestion et le stockage des comptes d’utilisateur dans les applications ASP.NET Core. Identity est ajouté à votre projet lorsque **des comptes d’utilisateur individuels** est sélectionné comme mécanisme d’authentification. Par défaut, utilise Identity un modèle de données de base Entity Framework (EF). Cet article explique comment personnaliser le Identity modèle.

## <a name="no-locidentity-and-ef-core-migrations"></a>Identity Migrations et EF Core

Avant d’examiner le modèle, il est utile de comprendre comment Identity fonctionne avec [EF Core migrations](/ef/core/managing-schemas/migrations/) pour créer et mettre à jour une base de données. Au niveau supérieur, le processus est le suivant :

1. Définir ou mettre à jour un [modèle de données dans le code](/ef/core/modeling/).
1. Ajoutez une migration pour traduire ce modèle en modifications qui peuvent être appliquées à la base de données.
1. Vérifiez que la migration représente correctement vos intentions.
1. Appliquez la migration pour mettre à jour la base de données afin qu’elle soit synchronisée avec le modèle.
1. Répétez les étapes 1 à 4 pour affiner le modèle et maintenir la synchronisation de la base de données.

Utilisez l’une des approches suivantes pour ajouter et appliquer des migrations :

* La fenêtre **console du gestionnaire de package** (PMC) si vous utilisez Visual Studio. Pour plus d’informations, consultez [EF Core les outils PMC](/ef/core/miscellaneous/cli/powershell).
* CLI .NET Core en cas d’utilisation de la ligne de commande. Pour plus d’informations, consultez [EF Core les outils en ligne de commande .net](/ef/core/miscellaneous/cli/dotnet).
* En cliquant sur le bouton **appliquer des migrations** sur la page d’erreur lors de l’exécution de l’application.

ASP.NET Core a un gestionnaire de page d’erreurs au moment du développement. Le gestionnaire peut appliquer des migrations lorsque l’application est exécutée. Les applications de production génèrent généralement des scripts SQL à partir des migrations et déploient des modifications de base de données dans le cadre d’un déploiement d’application contrôlé et de base de données.

Quand une nouvelle application utilisant Identity est créée, les étapes 1 et 2 ci-dessus ont déjà été effectuées. Autrement dit, le modèle de données initial existe déjà et la migration initiale a été ajoutée au projet. La migration initiale doit toujours être appliquée à la base de données. La migration initiale peut être appliquée via l’une des approches suivantes :

* Exécutez `Update-Database` dans PMC.
* Exécutez `dotnet ef database update` dans un interpréteur de commandes.
* Cliquez sur le bouton **appliquer des migrations** sur la page d’erreur lors de l’exécution de l’application.

Répétez les étapes précédentes au fur et à mesure que des modifications sont apportées au modèle.

## <a name="the-no-locidentity-model"></a>Le Identity modèle

### <a name="entity-types"></a>Types d’entités

Le Identity modèle se compose des types d’entités suivants.

|Type d'entité|Description                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Représente l’utilisateur.                                         |
|`Role`     |Représente un rôle.                                           |
|`UserClaim`|Représente une revendication qu’un utilisateur possède.                    |
|`UserToken`|Représente un jeton d’authentification pour un utilisateur.               |
|`UserLogin`|Associe un utilisateur à une connexion.                              |
|`RoleClaim`|Représente une revendication accordée à tous les utilisateurs d’un rôle.|
|`UserRole` |Entité de jointure qui associe les utilisateurs et les rôles.               |

### <a name="entity-type-relationships"></a>Relations de type d’entité

Les [types d’entités](#entity-types) sont liés les uns aux autres des manières suivantes :

* Chaque `User` peut avoir plusieurs `UserClaims` .
* Chaque `User` peut avoir plusieurs `UserLogins` .
* Chaque `User` peut avoir plusieurs `UserTokens` .
* Chaque `Role` peut être associé à plusieurs `RoleClaims` .
* Chaque `User` peut être associé à plusieurs `Roles` , et chaque peut `Role` être associé à plusieurs `Users` . Il s’agit d’une relation plusieurs-à-plusieurs qui requiert une table de jointure dans la base de données. La table de jointure est représentée par l' `UserRole` entité.

### <a name="default-model-configuration"></a>Configuration de modèle par défaut

Identity définit de nombreuses *classes de contexte* qui héritent de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) pour configurer et utiliser le modèle. Cette configuration s’effectue à l’aide de l' [API EF Core code First Fluent](/ef/core/modeling/) dans la méthode [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) de la classe Context. La configuration par défaut est la suivante :

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

### <a name="model-generic-types"></a>Types génériques de modèle

Identity définit les types CLR ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) par défaut pour chacun des types d’entité listés ci-dessus. Le préfixe de ces types est le *Identity* suivant :

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Au lieu d’utiliser directement ces types, les types peuvent être utilisés comme classes de base pour les types de l’application. Les `DbContext` classes définies par Identity sont génériques, de sorte que différents types CLR peuvent être utilisés pour un ou plusieurs types d’entité dans le modèle. Ces types génériques permettent également `User` de modifier le type de données de la clé primaire (PK).

Lors de l’utilisation de Identity avec la prise en charge des rôles, une <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> classe doit être utilisée. Exemple :

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

Il est également possible d’utiliser Identity sans rôle (uniquement les revendications), auquel cas une <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> classe doit être utilisée :

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a>Personnaliser le modèle

Le point de départ pour la personnalisation de modèle consiste à dériver du type de contexte approprié. Consultez la section [types génériques de modèle](#model-generic-types) . Ce type de contexte est habituellement appelé `ApplicationDbContext` et est créé par les modèles ASP.net core.

Le contexte est utilisé pour configurer le modèle de deux manières :

* Fourniture des types d’entité et de clé pour les paramètres de type générique.
* Substitution `OnModelCreating` pour modifier le mappage de ces types.

Lors de la substitution `OnModelCreating` , `base.OnModelCreating` doit être appelé en premier ; la configuration de substitution doit être appelée Next. EF Core a généralement une stratégie dernière-un WINS pour la configuration. Par exemple, si la `ToTable` méthode d’un type d’entité est appelée en premier avec un nom de table, puis à nouveau ultérieurement avec un nom de table différent, le nom de la table dans le deuxième appel est utilisé.

### <a name="custom-user-data"></a>Données utilisateur personnalisées

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

Les [données utilisateur personnalisées](xref:security/authentication/add-user-data) sont prises en charge par l’héritage de `IdentityUser` . Il est personnalisé pour nommer ce type `ApplicationUser` :

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Utilisez le `ApplicationUser` type comme argument générique pour le contexte :

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
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

Il n’est pas nécessaire de remplacer `OnModelCreating` dans la `ApplicationDbContext` classe. EF Core mappe la `CustomTag` propriété par Convention. Toutefois, la base de données doit être mise à jour pour créer une nouvelle `CustomTag` colonne. Pour créer la colonne, ajoutez une migration, puis mettez à jour la base de données comme décrit dans [ Identity et EF Core migrations](#identity-and-ef-core-migrations).

Mettez à jour *pages/Shared/_LoginPartial. cshtml* et remplacez `IdentityUser` par `ApplicationUser` :

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Mettez à jour *Areas/ Identity / Identity HostingStartup.cs* ou `Startup.ConfigureServices` et remplacez `IdentityUser` par `ApplicationUser` .

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

Dans ASP.NET Core 2,1 ou version ultérieure, Identity est fourni en tant que Razor bibliothèque de classes. Pour plus d'informations, consultez <xref:security/authentication/scaffold-identity>. Par conséquent, le code précédent requiert un appel à <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Si le générateur de Identity modèles automatique a été utilisé pour ajouter des Identity fichiers au projet, supprimez l’appel à `AddDefaultUI` . Pour plus d'informations, consultez les pages suivantes :

* [Destin Identity](xref:security/authentication/scaffold-identity)
* [Ajouter, télécharger et supprimer des données utilisateur personnalisées Identity](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Modifier le type de clé primaire

Une modification du type de données de la colonne de clé primaire après la création de la base de données est problématique sur de nombreux systèmes de base de données. La modification de la clé primaire implique généralement la suppression et la recréation de la table. Par conséquent, les types de clés doivent être spécifiés lors de la migration initiale lors de la création de la base de données.

Pour modifier le type de clé primaire, procédez comme suit :

1. Si la base de données a été créée avant la modification de la clé primaire, exécutez `Drop-Database` (PMC) ou `dotnet ef database drop` (CLI .net Core) pour la supprimer.
2. Après confirmation de la suppression de la base de données, supprimez la migration initiale avec `Remove-Migration` (PMC) ou `dotnet ef migrations remove` (CLI .net Core).
3. Mettez à jour la `ApplicationDbContext` classe à partir de laquelle dériver <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> . Spécifiez le nouveau type de clé pour `TKey` . Par exemple, pour utiliser un `Guid` type de clé :

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    Dans le code précédent, les classes génériques <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> et <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> doivent être spécifiées pour utiliser le nouveau type de clé.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    Dans le code précédent, les classes génériques <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> et <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> doivent être spécifiées pour utiliser le nouveau type de clé.

    ::: moniker-end

    `Startup.ConfigureServices` doit être mis à jour pour utiliser l’utilisateur générique :

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. Si une `ApplicationUser` classe personnalisée est utilisée, mettez à jour la classe pour qu’elle hérite de `IdentityUser` . Exemple :

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Mettez à jour `ApplicationDbContext` pour référencer la `ApplicationUser` classe personnalisée :

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    Inscrire la classe de contexte de base de données personnalisée lors de l’ajout du Identity service dans `Startup.ConfigureServices` :

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Le type de données de la clé primaire est déduit en analysant l’objet [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    Dans ASP.NET Core 2,1 ou version ultérieure, Identity est fourni en tant que Razor bibliothèque de classes. Pour plus d'informations, consultez <xref:security/authentication/scaffold-identity>. Par conséquent, le code précédent requiert un appel à <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Si le générateur de Identity modèles automatique a été utilisé pour ajouter des Identity fichiers au projet, supprimez l’appel à `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Le type de données de la clé primaire est déduit en analysant l’objet [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    La <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> méthode accepte un `TKey` type indiquant le type de données de la clé primaire.

    ::: moniker-end

5. Si une `ApplicationRole` classe personnalisée est utilisée, mettez à jour la classe pour qu’elle hérite de `IdentityRole<TKey>` . Exemple :

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Mettez à jour `ApplicationDbContext` pour référencer la `ApplicationRole` classe personnalisée. Par exemple, la classe suivante référence un personnalisé `ApplicationUser` et un personnalisé `ApplicationRole` :

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Inscrire la classe de contexte de base de données personnalisée lors de l’ajout du Identity service dans `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Le type de données de la clé primaire est déduit en analysant l’objet [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    Dans ASP.NET Core 2,1 ou version ultérieure, Identity est fourni en tant que Razor bibliothèque de classes. Pour plus d'informations, consultez <xref:security/authentication/scaffold-identity>. Par conséquent, le code précédent requiert un appel à <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Si le générateur de Identity modèles automatique a été utilisé pour ajouter des Identity fichiers au projet, supprimez l’appel à `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Inscrire la classe de contexte de base de données personnalisée lors de l’ajout du Identity service dans `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Le type de données de la clé primaire est déduit en analysant l’objet [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Inscrire la classe de contexte de base de données personnalisée lors de l’ajout du Identity service dans `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    La <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> méthode accepte un `TKey` type indiquant le type de données de la clé primaire.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Ajouter des propriétés de navigation

La modification de la configuration du modèle pour les relations peut être plus difficile que d’apporter d’autres modifications. Il faut veiller à remplacer les relations existantes plutôt qu’à créer de nouvelles relations. En particulier, la relation modifiée doit spécifier la même propriété de clé étrangère (FK) que la relation existante. Par exemple, la relation entre `Users` et `UserClaims` est, par défaut, spécifiée comme suit :

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

Le FK pour cette relation est spécifié en tant que `UserClaim.UserId` propriété. `HasMany` et `WithOne` sont appelées sans arguments pour créer la relation sans propriétés de navigation.

Ajoutez une propriété de navigation à `ApplicationUser` qui permet à d' `UserClaims` être référencée à partir de l’utilisateur :

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

`TKey`Pour `IdentityUserClaim<TKey>` est le type spécifié pour le PK des utilisateurs. Dans ce cas, `TKey` est `string` dû au fait que les valeurs par défaut sont utilisées. Il ne s’agit **pas** du type de clé primaire pour le `UserClaim` type d’entité.

Maintenant que la propriété de navigation existe, elle doit être configurée dans `OnModelCreating` :

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
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

Notez que la relation est configurée exactement telle qu’elle était avant, uniquement avec une propriété de navigation spécifiée dans l’appel à `HasMany` .

Les propriétés de navigation existent uniquement dans le modèle EF, pas dans la base de données. Étant donné que le FK de la relation n’a pas changé, ce type de modification du modèle n’exige pas la mise à jour de la base de données. Vous pouvez vérifier cela en ajoutant une migration après avoir apporté la modification. Les `Up` `Down` méthodes et sont vides.

### <a name="add-all-user-navigation-properties"></a>Ajouter toutes les propriétés de navigation de l’utilisateur

À l’aide de la section ci-dessus, comme aide, l’exemple suivant configure les propriétés de navigation unidirectionnelles pour toutes les relations sur l’utilisateur :

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
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

### <a name="add-user-and-role-navigation-properties"></a>Ajouter des propriétés de navigation utilisateur et de rôle

À l’aide de la section ci-dessus, en guise d’aide, l’exemple suivant configure les propriétés de navigation pour toutes les relations sur l’utilisateur et le rôle :

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
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

Remarques :

* Cet exemple comprend également l' `UserRole` entité de jointure, qui est nécessaire pour parcourir la relation plusieurs-à-plusieurs des utilisateurs aux rôles.
* N’oubliez pas de modifier les types des propriétés de navigation pour refléter le fait que les `Application{...}` types sont maintenant utilisés à la place des `Identity{...}` types.
* N’oubliez pas d’utiliser `Application{...}` dans la `ApplicationContext` définition générique.

### <a name="add-all-navigation-properties"></a>Ajouter toutes les propriétés de navigation

À l’aide de la section ci-dessus, en guise d’aide, l’exemple suivant configure les propriétés de navigation pour toutes les relations sur tous les types d’entité :

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
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

### <a name="use-composite-keys"></a>Utiliser des clés composites

Les sections précédentes ont démontré la modification du type de clé utilisé dans le Identity modèle. La modification du Identity modèle de clé pour utiliser des clés composites n’est pas prise en charge ou recommandée. L’utilisation d’une clé composite avec Identity implique de modifier la façon dont le Identity Code du gestionnaire interagit avec le modèle. Cette personnalisation n’est pas traitée dans ce document.

### <a name="change-tablecolumn-names-and-facets"></a>Modifier les facettes et les noms de table/colonne

Pour modifier les noms des tables et des colonnes, appelez `base.OnModelCreating` . Ensuite, ajoutez une configuration pour remplacer les valeurs par défaut. Par exemple, pour modifier le nom de toutes les Identity tables :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

Ces exemples utilisent les types par défaut Identity . Si vous utilisez un type d’application tel que `ApplicationUser` , configurez ce type à la place du type par défaut.

L’exemple suivant modifie certains noms de colonne :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

Certains types de colonnes de base de données peuvent être configurés avec certaines *facettes* (par exemple, la `string` longueur maximale autorisée). L’exemple suivant définit les longueurs maximales des colonnes pour plusieurs `string` Propriétés dans le modèle :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a>Mapper à un schéma différent

Les schémas peuvent se comporter différemment entre les fournisseurs de base de données. Par SQL Server, la valeur par défaut consiste à créer toutes les tables dans le schéma *dbo* . Les tables peuvent être créées dans un schéma différent. Exemple :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Chargement différé

Dans cette section, la prise en charge des proxies de chargement différé dans le Identity modèle est ajoutée. Le chargement différé est utile, car il permet d’utiliser les propriétés de navigation sans s’assurer d’abord qu’elles sont chargées.

Les types d’entités peuvent être adaptés au chargement différé de plusieurs façons, comme décrit dans la [documentation EF Core](/ef/core/querying/related-data#lazy-loading). Pour plus de simplicité, utilisez des proxies à chargement différé, qui requièrent les éléments suivants :

* Installation du package [Microsoft. EntityFrameworkCore. proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .
* Appel à <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> l’intérieur [de \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).
* Types d’entité publics avec des `public virtual` Propriétés de navigation.

L’exemple suivant illustre l’appel `UseLazyLoadingProxies` de dans `Startup.ConfigureServices` :

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Reportez-vous aux exemples précédents pour obtenir des conseils sur l’ajout de propriétés de navigation aux types d’entités.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authentication/scaffold-identity>

::: moniker-end
