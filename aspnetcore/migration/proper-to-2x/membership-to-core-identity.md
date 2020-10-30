---
title: Migrer de l’authentification d’appartenance ASP.NET vers ASP.NET Core 2,0 Identity
author: isaac2004
description: Découvrez comment migrer des applications ASP.NET existantes à l’aide de l’authentification d’appartenance vers ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: d981c424fd2d6cad95b9164420f093672325c347
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051354"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a>Migrer de l’authentification d’appartenance ASP.NET vers ASP.NET Core 2,0 Identity

De [Isaac Levin](https://isaaclevin.com)

Cet article explique comment migrer le schéma de base de données pour les applications ASP.NET à l’aide de l’authentification d’appartenance vers ASP.NET Core 2,0 Identity .

> [!NOTE]
> Ce document décrit les étapes nécessaires à la migration du schéma de base de données pour les applications basées sur l’appartenance ASP.NET vers le schéma de base de données utilisé pour ASP.NET Core Identity . Pour plus d’informations sur la migration de l’authentification basée sur l’appartenance ASP.NET vers ASP.NET Identity , consultez [migrer une application existante Identity de l’appartenance SQL vers ASP.net ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Pour plus d’informations sur ASP.NET Core Identity , consultez [Introduction à Identity sur ASP.net Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Examen du schéma d’appartenance

Avant ASP.NET 2,0, les développeurs devaient créer l’intégralité du processus d’authentification et d’autorisation pour leurs applications. Avec ASP.NET 2,0, l’appartenance a été introduite et fournit une solution réutilisable pour gérer la sécurité dans les applications ASP.NET. Les développeurs pouvaient désormais démarrer un schéma dans une base de données SQL Server à l’aide de la <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> commande. Après l’exécution de cette commande, les tables suivantes ont été créées dans la base de données.

  ![Tables d’appartenance](identity/_static/membership-tables.png)

Pour migrer des applications existantes vers ASP.NET Core 2,0 Identity , les données de ces tables doivent être migrées vers les tables utilisées par le nouveau Identity schéma.

## <a name="no-locaspnet-core-identity-20-schema"></a>ASP.NET Core Identity schéma 2,0

ASP.NET Core 2,0 suit le [Identity](/aspnet/identity/index) principe introduit dans ASP.NET 4,5. Bien que le principe soit partagé, l’implémentation entre les frameworks est différente, même entre les versions de ASP.NET Core (consultez [migrer l’authentification et Identity vers ASP.net Core 2,0](xref:migration/1x-to-2x/index)).

La façon la plus rapide d’afficher le schéma pour ASP.NET Core 2,0 Identity consiste à créer une nouvelle application ASP.NET Core 2,0. Procédez comme suit dans Visual Studio 2017 :

1. Sélectionnez **Fichier** > **Nouveau** > **Projet** .
1. Créez un projet d' **application Web ASP.net Core** nommé *Core Identity Sample* .
1. Sélectionnez **ASP.NET Core 2,0** dans la liste déroulante, puis sélectionnez **application Web** . Ce modèle produit une application [ Razor pages](xref:razor-pages/index) . Avant de cliquer sur **OK** , cliquez sur **modifier l’authentification** .
1. Choisissez des **comptes d’utilisateur individuels** pour les Identity modèles. Enfin, cliquez sur **OK** , puis sur **OK** . Visual Studio crée un projet à l’aide du ASP.NET Core Identity modèle.
1. Sélectionnez **Outils**  >  **Gestionnaire de package NuGet**  >  **console du gestionnaire** de package pour ouvrir la fenêtre **console du gestionnaire de package** (PMC).
1. Accédez à la racine du projet dans PMC, puis exécutez la commande [Entity Framework (EF) Core](/ef/core) `Update-Database` .

    ASP.NET Core 2,0 Identity utilise EF Core pour interagir avec la base de données qui stocke les données d’authentification. Pour que l’application nouvellement créée fonctionne, vous devez disposer d’une base de données pour stocker ces données. Après la création d’une nouvelle application, la méthode la plus rapide pour inspecter le schéma dans un environnement de base de données consiste à créer la base de données à l’aide de [EF Core migrations](/ef/core/managing-schemas/migrations/). Ce processus crée une base de données, localement ou ailleurs, qui imite ce schéma. Pour plus d’informations, consultez la documentation précédente.

    Les commandes EF Core utilisent la chaîne de connexion pour la base de données spécifiée dans *appsettings.json* . La chaîne de connexion suivante cible une base de données sur *localhost* nommée *asp-net-Core-Identity* . Dans ce paramètre, EF Core est configurée pour utiliser la `DefaultConnection` chaîne de connexion.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Sélectionnez **Afficher**  >  **Explorateur d’objets SQL Server** . Développez le nœud correspondant au nom de la base de données spécifié dans la `ConnectionStrings:DefaultConnection` propriété de *appsettings.json* .

    La `Update-Database` commande a créé la base de données spécifiée avec le schéma et toutes les données nécessaires à l’initialisation de l’application. L’image suivante représente la structure de table créée à l’aide des étapes précédentes.

    ![::: No-Loc (Identity) ::: tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrer le schéma

Il existe des différences subtiles entre les structures et les champs de table pour l’appartenance et ASP.NET Core Identity . Le modèle a changé de manière substantielle pour l’authentification/l’autorisation avec les applications ASP.NET et ASP.NET Core. Les objets clés qui sont toujours utilisés avec Identity sont *les utilisateurs et les* *rôles* . Voici les tables de mappage pour *les utilisateurs, les* *rôles* et les *UserRoles* .

### <a name="users"></a>Utilisateurs

|Identity<br>`dbo.AspNetUsers`colonne ()  |Type     |Adhésion<br>`dbo.aspnet_Users`  /  `dbo.aspnet_Membership` colonne ()|Type      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> Tous les mappages de champs ne ressemblent pas aux relations un-à-un de l’appartenance à ASP.NET Core Identity . Le tableau précédent prend le schéma de l’utilisateur d’appartenance par défaut et le mappe au ASP.NET Core Identity schéma. Tous les autres champs personnalisés qui ont été utilisés pour l’appartenance doivent être mappés manuellement. Dans ce mappage, il n’existe pas de mappage pour les mots de passe, car les critères de mot de passe et les sels de mot de passe ne migrent pas entre les deux. **Il est recommandé de conserver la valeur null pour le mot de passe et de demander aux utilisateurs de réinitialiser leur mot de passe.** Dans ASP.NET Core Identity , `LockoutEnd` doit être défini à une date ultérieure si l’utilisateur est verrouillé. Cela est illustré dans le script de migration.

### <a name="roles"></a>Rôles

|Identity<br>`dbo.AspNetRoles`colonne ()|Type|Adhésion<br>`dbo.aspnet_Roles`colonne ()|Type|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a>Rôles d'utilisateur

|Identity<br>`dbo.AspNetUserRoles`colonne ()|Type|Adhésion<br>`dbo.aspnet_UsersInRoles`colonne ()|Type|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

Référencez les tables de mappage précédentes lors de la création d’un script de migration pour *les utilisateurs et les* *rôles* . L’exemple suivant suppose que vous disposez de deux bases de données sur un serveur de base de données. Une base de données contient le schéma et les données d’appartenance ASP.NET existants. L’autre *Identity exemple* de base de données de base a été créé à l’aide des étapes décrites précédemment. Les commentaires sont inclus dans Inline pour plus d’informations.

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
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
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
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
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

Une fois le script précédent terminé, l' ASP.NET Core Identity application créée précédemment est remplie avec les utilisateurs d’appartenance. Les utilisateurs doivent modifier leur mot de passe avant de se connecter.

> [!NOTE]
> Si le système d’appartenance avait des utilisateurs dont les noms d’utilisateur ne correspondaient pas à leur adresse de messagerie, les modifications sont requises pour l’application créée précédemment pour s’adapter à cette opération. Le modèle par défaut `UserName` est attendu et `Email` doit être le même. Dans les situations où elles sont différentes, le processus de connexion doit être modifié pour utiliser `UserName` au lieu de `Email` .

Dans le `PageModel` de la page de connexion, situé dans *Pages\Account\Login.cshtml.cs* , supprimez l' `[EmailAddress]` attribut de la propriété *e-mail* . Renommez-le nom d' *utilisateur* . Pour ce faire, vous devez modifier chaque fois `EmailAddress` qu’il est mentionné dans la *vue* et *PageModel* . Le résultat se présente comme suit :

 ![Connexion fixe](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment porter les utilisateurs de l’appartenance SQL vers ASP.NET Core 2,0 Identity . Pour plus d’informations sur ASP.NET Core Identity , consultez [présentation Identity de ](xref:security/authentication/identity).
