---
title: Fournisseurs de stockage personnalisés pour ASP.NET Core Identity
author: ardalis
description: Découvrez comment configurer des fournisseurs de stockage personnalisés pour ASP.NET Core Identity .
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
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
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: f1c4366e4e4afa3dd86a816a649ad0a8b2ce817b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588625"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Fournisseurs de stockage personnalisés pour ASP.NET Core Identity

Par [Steve Smith](https://ardalis.com/)

ASP.NET Core Identity est un système extensible qui vous permet de créer un fournisseur de stockage personnalisé et de le connecter à votre application. Cette rubrique explique comment créer un fournisseur de stockage personnalisé pour ASP.NET Core Identity . Il couvre les concepts importants pour la création de votre propre fournisseur de stockage, mais n’est pas une procédure pas à pas.

[Affichez ou téléchargez un exemple depuis GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample).

## <a name="introduction"></a>Introduction

Par défaut, le ASP.NET Core Identity système stocke les informations utilisateur dans une base de données SQL Server à l’aide de Entity Framework Core. Pour de nombreuses applications, cette approche fonctionne bien. Toutefois, vous préférerez peut-être utiliser un mécanisme de persistance ou un schéma de données différent. Par exemple :

* Vous utilisez le [stockage table Azure](/azure/storage/) ou un autre magasin de données.
* Les tables de votre base de données ont une structure différente. 
* Vous souhaiterez peut-être utiliser une autre approche d’accès aux données, telle que [dapper](https://github.com/StackExchange/Dapper). 

Dans chacun de ces cas, vous pouvez écrire un fournisseur personnalisé pour votre mécanisme de stockage et connecter ce fournisseur à votre application.

ASP.NET Core Identity est inclus dans les modèles de projet dans Visual Studio avec l’option « comptes d’utilisateur individuels ».

Lorsque vous utilisez l’CLI .NET Core, ajoutez `-au Individual` :

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>L' ASP.NET Core Identity architecture

ASP.NET Core Identity se compose de classes appelées gestionnaires et magasins. Les *gestionnaires* sont des classes de haut niveau qu’un développeur d’applications utilise pour effectuer des opérations, telles que la création d’un Identity utilisateur. Les *magasins* sont des classes de niveau inférieur qui spécifient la façon dont les entités, telles que les utilisateurs et les rôles, sont conservées. Les magasins suivent le modèle de référentiel et sont étroitement couplés avec le mécanisme de persistance. Les gestionnaires sont dissociés des magasins, ce qui signifie que vous pouvez remplacer le mécanisme de persistance sans modifier le code de votre application (à l’exception de la configuration).

Le diagramme suivant montre comment une application Web interagit avec les gestionnaires, tandis que les magasins interagissent avec la couche d’accès aux données.

![ASP.NET Core applications fonctionnent avec des gestionnaires (par exemple, « UserManager », « RoleManager »). Les gestionnaires travaillent avec les magasins (par exemple, « UserStore ») qui communiquent avec une source de données à l’aide d’une bibliothèque comme Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Pour créer un fournisseur de stockage personnalisé, créez la source de données, la couche d’accès aux données et les classes de magasin qui interagissent avec cette couche d’accès aux données (zones vertes et grises dans le diagramme ci-dessus). Vous n’avez pas besoin de personnaliser les gestionnaires ou le code d’application qui interagit avec eux (les zones bleues ci-dessus).

Lorsque vous créez une nouvelle instance de `UserManager` ou `RoleManager` que vous fournissez le type de la classe utilisateur et que vous transmettez une instance de la classe Store en tant qu’argument. Cette approche vous permet de connecter vos classes personnalisées dans ASP.NET Core. 

[Reconfigurer l’application pour utiliser le nouveau fournisseur de stockage](#reconfigure-app-to-use-a-new-storage-provider) montre comment instancier `UserManager` et `RoleManager` avec un magasin personnalisé.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity stocke les types de données

[ASP.NET Core Identity](https://github.com/aspnet/identity) les types de données sont détaillés dans les sections suivantes :

### <a name="users"></a>Utilisateurs

Utilisateurs inscrits de votre site Web. Le type d' [ Identity utilisateur](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) peut être étendu ou utilisé comme exemple pour votre propre type personnalisé. Vous n’avez pas besoin d’hériter d’un type particulier pour implémenter votre propre solution de stockage d’identité personnalisée.

### <a name="user-claims"></a>Revendications utilisateur

Ensemble d’instructions (ou de [revendications](/dotnet/api/system.security.claims.claim)) sur l’utilisateur qui représente l’identité de l’utilisateur. Peut permettre une plus grande expression de l’identité de l’utilisateur que le permet d’obtenir des rôles.

### <a name="user-logins"></a>Connexions utilisateur

Informations sur le fournisseur d’authentification externe (par exemple, Facebook ou compte Microsoft) à utiliser lors de la connexion à un utilisateur. [Exemple](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Rôles

Groupes d’autorisations pour votre site. Comprend l’ID de rôle et le nom de rôle (par exemple, « admin » ou « Employee »). [Exemple](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Couche d’accès aux données

Cette rubrique suppose que vous êtes familiarisé avec le mécanisme de persistance que vous allez utiliser et comment créer des entités pour ce mécanisme. Cette rubrique ne fournit pas de détails sur la création des référentiels ou des classes d’accès aux données. Il fournit des suggestions sur les décisions de conception lors de l’utilisation de ASP.NET Core Identity .

Vous avez beaucoup de liberté lors de la conception de la couche d’accès aux données pour un fournisseur de magasin personnalisé. Vous devez uniquement créer des mécanismes de persistance pour les fonctionnalités que vous envisagez d’utiliser dans votre application. Par exemple, si vous n’utilisez pas de rôles dans votre application, vous n’avez pas besoin de créer de stockage pour les rôles ou les associations de rôles d’utilisateur. Votre technologie et votre infrastructure existante peuvent nécessiter une structure très différente de l’implémentation par défaut de ASP.NET Core Identity . Dans votre couche d’accès aux données, vous fournissez la logique nécessaire pour utiliser la structure de votre implémentation de stockage.

La couche d’accès aux données fournit la logique pour enregistrer les données de ASP.NET Core Identity dans une source de données. La couche d’accès aux données de votre fournisseur de stockage personnalisé peut inclure les classes suivantes pour stocker les informations d’utilisateur et de rôle.

### <a name="context-class"></a>Context (classe)

Encapsule les informations pour se connecter à votre mécanisme de persistance et exécuter des requêtes. Plusieurs classes de données requièrent une instance de cette classe, généralement fournie par le biais de l’injection de dépendances. [Exemple](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Stockage utilisateur

Stocke et récupère les informations utilisateur (telles que le nom d’utilisateur et le hachage de mot de passe). [Exemple](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Stockage de rôle

Stocke et récupère les informations de rôle (par exemple, le nom du rôle). [Exemple](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Stockage UserClaims

Stocke et récupère les informations de revendication de l’utilisateur (par exemple, le type et la valeur de la revendication). [Exemple](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Stockage UserLogins

Stocke et récupère les informations de connexion de l’utilisateur (par exemple, un fournisseur d’authentification externe). [Exemple](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Stockage UserRole

Stocke et récupère les rôles attribués aux utilisateurs. [Exemple](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**Conseil :** Implémentez uniquement les classes que vous envisagez d’utiliser dans votre application.

Dans les classes d’accès aux données, fournissez du code pour effectuer des opérations de données pour votre mécanisme de persistance. Par exemple, dans un fournisseur personnalisé, vous pouvez avoir le code suivant pour créer un nouvel utilisateur dans la classe *Store* :

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

La logique d’implémentation pour créer l’utilisateur se trouve dans la `_usersTable.CreateAsync` méthode, comme indiqué ci-dessous.

## <a name="customize-the-user-class"></a>Personnaliser la classe utilisateur

Lors de l’implémentation d’un fournisseur de stockage, créez une classe d’utilisateur qui est équivalente à la [ Identity classe utilisateur](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

Au minimum, votre classe d’utilisateur doit inclure un `Id` et une `UserName` propriété.

La `IdentityUser` classe définit les propriétés que le `UserManager` appelle lors de l’exécution des opérations demandées. Le type par défaut de la `Id` propriété est une chaîne, mais vous pouvez hériter de `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` et spécifier un type différent. L’infrastructure s’attend à ce que l’implémentation du stockage gère les conversions de types de données.

## <a name="customize-the-user-store"></a>Personnaliser le magasin de l’utilisateur

Créez une `UserStore` classe qui fournit les méthodes pour toutes les opérations de données sur l’utilisateur. Cette classe est équivalente à la classe [UserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) . Dans votre `UserStore` classe, implémentez `IUserStore<TUser>` et les interfaces facultatives requises. Vous sélectionnez les interfaces facultatives à implémenter en fonction des fonctionnalités fournies dans votre application.

### <a name="optional-interfaces"></a>Interfaces facultatives

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

Les interfaces facultatives héritent de `IUserStore<TUser>` . Vous pouvez voir un exemple de magasin d’utilisateurs partiellement implémenté dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).

Dans la `UserStore` classe, vous utilisez les classes d’accès aux données que vous avez créées pour effectuer des opérations. Ils sont passés à l’aide de l’injection de dépendances. Par exemple, dans le SQL Server avec l’implémentation de dapper, la `UserStore` classe a la `CreateAsync` méthode qui utilise une instance de `DapperUsersTable` pour insérer un nouvel enregistrement :

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Interfaces à implémenter lors de la personnalisation du magasin d’utilisateurs

* **IUserStore**  
 L' [interface &lt; IUserStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) est la seule interface que vous devez implémenter dans le magasin de l’utilisateur. Il définit des méthodes pour la création, la mise à jour, la suppression et la récupération des utilisateurs.
* **IUserClaimStore**  
 L' [interface &lt; IUserClaimStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) définit les méthodes que vous implémentez pour activer les revendications d’utilisateur. Il contient des méthodes pour l’ajout, la suppression et la récupération de revendications d’utilisateur.
* **IUserLoginStore**  
 Le [ &lt; &gt; TUser IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) définit les méthodes que vous implémentez pour activer les fournisseurs d’authentification externes. Il contient des méthodes permettant d’ajouter, de supprimer et de récupérer des connexions utilisateur, ainsi qu’une méthode pour récupérer un utilisateur en fonction des informations de connexion.
* **IUserRoleStore**  
 L' [interface &lt; IUserRoleStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) définit les méthodes que vous implémentez pour mapper un utilisateur à un rôle. Elle contient des méthodes permettant d’ajouter, de supprimer et de récupérer les rôles d’un utilisateur, ainsi qu’une méthode permettant de vérifier si un utilisateur est affecté à un rôle.
* **IUserPasswordStore**  
 L' [interface &lt; IUserPasswordStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) définit les méthodes que vous implémentez pour rendre les mots de passe hachés persistants. Elle contient des méthodes permettant d’obtenir et de définir le mot de passe haché, ainsi qu’une méthode qui indique si l’utilisateur a défini un mot de passe.
* **IUserSecurityStampStore**  
 L' [interface &lt; IUserSecurityStampStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) définit les méthodes que vous implémentez pour utiliser un tampon de sécurité pour indiquer si les informations de compte de l’utilisateur ont été modifiées. Ce marquage est mis à jour lorsqu’un utilisateur modifie le mot de passe, ou ajoute ou supprime des connexions. Elle contient des méthodes permettant d’obtenir et de définir le tampon de sécurité.
* **IUserTwoFactorStore**  
 L' [interface &lt; IUserTwoFactorStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) définit les méthodes que vous implémentez pour prendre en charge l’authentification à deux facteurs. Elle contient des méthodes permettant d’obtenir et de définir si l’authentification à deux facteurs est activée pour un utilisateur.
* **IUserPhoneNumberStore**  
 L' [interface &lt; IUserPhoneNumberStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) définit les méthodes que vous implémentez pour stocker les numéros de téléphone des utilisateurs. Il contient des méthodes permettant d’obtenir et de définir le numéro de téléphone et si le numéro de téléphone est confirmé.
* **IUserEmailStore**  
 L' [interface &lt; IUserEmailStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) définit les méthodes que vous implémentez pour stocker les adresses de messagerie des utilisateurs. Il contient des méthodes permettant d’obtenir et de définir l’adresse de messagerie et si l’e-mail est confirmé.
* **IUserLockoutStore**  
 L' [interface &lt; IUserLockoutStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) définit les méthodes que vous implémentez pour stocker des informations sur le verrouillage d’un compte. Elle contient des méthodes de suivi des échecs et des échecs de tentatives d’accès.
* **IQueryableUserStore**  
 L' [interface &lt; IQueryableUserStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) définit les membres que vous implémentez pour fournir un magasin d’utilisateurs interrogeable.

Vous implémentez uniquement les interfaces qui sont nécessaires dans votre application. Par exemple :

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, Identity Userlogin et Identity userrole

L' `Microsoft.AspNet.Identity.EntityFramework` espace de noms contient des implémentations des classes [ Identity UserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [ Identity Userlogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)et [ Identity userrole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) . Si vous utilisez ces fonctionnalités, vous souhaiterez peut-être créer vos propres versions de ces classes et définir les propriétés de votre application. Toutefois, il est parfois plus efficace de ne pas charger ces entités dans la mémoire lors de l’exécution d’opérations de base (telles que l’ajout ou la suppression d’une revendication d’utilisateur). Au lieu de cela, les classes du magasin principal peuvent exécuter ces opérations directement sur la source de données. Par exemple, la `UserStore.GetClaimsAsync` méthode peut appeler la `userClaimTable.FindByUserId(user.Id)` méthode pour exécuter directement une requête sur cette table et retourner une liste de revendications.

## <a name="customize-the-role-class"></a>Personnaliser la classe de rôle

Lorsque vous implémentez un fournisseur de stockage de rôles, vous pouvez créer un type de rôle personnalisé. Elle n’a pas besoin d’implémenter une interface particulière, mais elle doit avoir un `Id` et, en général, elle aura une `Name` propriété.

Voici un exemple de classe de rôle :

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Personnaliser le magasin de rôles

Vous pouvez créer une `RoleStore` classe qui fournit les méthodes pour toutes les opérations de données sur les rôles. Cette classe est équivalente à la classe [au rolestore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) . Dans la `RoleStore` classe, vous implémentez le `IRoleStore<TRole>` et éventuellement l' `IQueryableRoleStore<TRole>` interface.

* **IRoleStore &lt; TRole&gt;**  
 L' [interface &lt; IRoleStore &gt; TRole](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) définit les méthodes à implémenter dans la classe du magasin de rôles. Elle contient des méthodes pour la création, la mise à jour, la suppression et la récupération de rôles.
* **Au rolestore &lt; TRole&gt;**  
 Pour personnaliser `RoleStore` , créez une classe qui implémente l' `IRoleStore<TRole>` interface. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Reconfigurer l’application pour utiliser un nouveau fournisseur de stockage

Une fois que vous avez implémenté un fournisseur de stockage, vous configurez votre application pour l’utiliser. Si votre application a utilisé le fournisseur par défaut, remplacez-le par votre fournisseur personnalisé.

1. Supprimez le `Microsoft.AspNetCore.EntityFramework.Identity` package NuGet.
1. Si le fournisseur de stockage réside dans un autre projet ou package, ajoutez une référence à ce dernier.
1. Remplacez toutes les références à `Microsoft.AspNetCore.EntityFramework.Identity` par une instruction using pour l’espace de noms de votre fournisseur de stockage.
1. Dans la `ConfigureServices` méthode, modifiez la `AddIdentity` méthode pour utiliser vos types personnalisés. Vous pouvez créer vos propres méthodes d’extension à cet effet. Pour obtenir un exemple, consultez [ Identity ServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) .
1. Si vous utilisez des rôles, mettez à jour `RoleManager` pour utiliser votre `RoleStore` classe.
1. Mettez à jour la chaîne de connexion et les informations d’identification dans la configuration de votre application.

Exemple :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>Références

* [Fournisseurs de stockage personnalisés pour ASP.NET 4. x Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/main/src/Identity): Ce référentiel contient des liens vers des fournisseurs de magasins gérés par la communauté.
