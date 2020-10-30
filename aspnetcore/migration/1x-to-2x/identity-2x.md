---
title: Migrer l’authentification et Identity vers ASP.NET Core 2,0
author: scottaddie
description: Cet article décrit les étapes les plus courantes pour la migration de ASP.NET Core authentification 1. x et Identity ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057178"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a>Migrer l’authentification et Identity vers ASP.NET Core 2,0

Par [Scott Addie](https://github.com/scottaddie) et [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2,0 dispose d’un nouveau modèle d’authentification et [Identity](xref:security/authentication/identity) simplifie la configuration à l’aide de services. ASP.NET Core les applications 1. x qui utilisent l’authentification ou Identity peuvent être mises à jour pour utiliser le nouveau modèle comme indiqué ci-dessous.

## <a name="update-namespaces"></a>Mettre à jour les espaces de noms

Dans 1. x, les classes telles `IdentityRole` que et `IdentityUser` ont été trouvées dans l' `Microsoft.AspNetCore.Identity.EntityFrameworkCore` espace de noms.

Dans 2,0, l' <xref:Microsoft.AspNetCore.Identity> espace de noms est devenu la nouvelle page d’hébergement pour plusieurs de ces classes. Avec le code par défaut Identity , les classes affectées incluent `ApplicationUser` et `Startup` . Ajustez vos `using` instructions pour résoudre les références affectées.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Intergiciel et services d’authentification

Dans les projets 1. x, l’authentification est configurée à l’aide d’un intergiciel (middleware). Une méthode d’intergiciel est appelée pour chaque schéma d’authentification que vous souhaitez prendre en charge.

L’exemple 1. x suivant configure l’authentification Facebook avec Identity dans *Startup.cs* :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

Dans les projets 2,0, l’authentification est configurée via les services. Chaque schéma d’authentification est inscrit dans la `ConfigureServices` méthode de *Startup.cs* . La `UseIdentity` méthode est remplacée par `UseAuthentication` .

L’exemple 2,0 suivant configure l’authentification Facebook avec Identity dans *Startup.cs* :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

La `UseAuthentication` méthode ajoute un seul composant d’intergiciel d’authentification, qui est responsable de l’authentification automatique et de la gestion des demandes d’authentification distantes. Il remplace tous les composants de l’intergiciel (middleware) individuels par un seul composant de middleware commun.

Vous trouverez ci-dessous des instructions de migration de 2,0 pour chaque schéma d’authentification principal.

### <a name="no-loccookie-based-authentication"></a>Cookieauthentification basée sur

Sélectionnez l’une des deux options ci-dessous et apportez les modifications nécessaires dans *Startup.cs* :

1. Utiliser cookie s avec Identity
    - Remplacez `UseIdentity` par `UseAuthentication` dans la `Configure` méthode :

        ```csharp
        app.UseAuthentication();
        ```

    - Appelez la `AddIdentity` méthode dans la `ConfigureServices` méthode pour ajouter les cookie services d’authentification.
    - Si vous le souhaitez, appelez la `ConfigureApplicationCookie` `ConfigureExternalCookie` méthode ou dans la `ConfigureServices` méthode pour modifier les Identity cookie paramètres.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Utiliser cookie s sans Identity
    - Remplacez l' `UseCookieAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :

        ```csharp
        app.UseAuthentication();
        ```

    - Appelez les `AddAuthentication` `AddCookie` méthodes et dans la `ConfigureServices` méthode :

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>Authentification du porteur JWT

Apportez les modifications suivantes dans *Startup.cs* :
- Remplacez l' `UseJwtBearerAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Appelez la `AddJwtBearer` méthode dans la `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Cet extrait de code n’utilise pas Identity . par conséquent, le schéma par défaut doit être défini en passant `JwtBearerDefaults.AuthenticationScheme` à la `AddAuthentication` méthode.

### <a name="openid-connect-oidc-authentication"></a>Authentification OpenID Connect (OIDC)

Apportez les modifications suivantes dans *Startup.cs* :

- Remplacez l' `UseOpenIdConnectAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Appelez la `AddOpenIdConnect` méthode dans la `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- Remplacez la `PostLogoutRedirectUri` propriété dans l' `OpenIdConnectOptions` action par `SignedOutRedirectUri` :

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Authentification Facebook

Apportez les modifications suivantes dans *Startup.cs* :
- Remplacez l' `UseFacebookAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Appelez la `AddFacebook` méthode dans la `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Authentification Google

Apportez les modifications suivantes dans *Startup.cs* :
- Remplacez l' `UseGoogleAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Appelez la `AddGoogle` méthode dans la `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Authentification du compte Microsoft

Pour plus d’informations sur l’authentification compte Microsoft, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/14455).

Apportez les modifications suivantes dans *Startup.cs* :
- Remplacez l' `UseMicrosoftAccountAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Appelez la `AddMicrosoftAccount` méthode dans la `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Authentification Twitter

Apportez les modifications suivantes dans *Startup.cs* :
- Remplacez l' `UseTwitterAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Appelez la `AddTwitter` méthode dans la `ConfigureServices` méthode :

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Définition des schémas d’authentification par défaut

Dans 1. x, les `AutomaticAuthenticate` `AutomaticChallenge` Propriétés et de la classe de base [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) étaient destinées à être définies sur un seul schéma d’authentification. Il n’existait pas de bonne méthode pour l’appliquer.

Dans 2,0, ces deux propriétés ont été supprimées en tant que propriétés sur l' `AuthenticationOptions` instance individuelle. Ils peuvent être configurés dans l' `AddAuthentication` appel de méthode au sein `ConfigureServices` de la méthode de *Startup.cs* :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

Dans l’extrait de code précédent, le schéma par défaut est défini sur `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

Vous pouvez également utiliser une version surchargée de la `AddAuthentication` méthode pour définir plusieurs propriétés. Dans l’exemple de méthode surchargée suivant, le schéma par défaut est défini sur `CookieAuthenticationDefaults.AuthenticationScheme` . Le schéma d’authentification peut également être spécifié dans vos propres `[Authorize]` attributs ou stratégies d’autorisation.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Définissez un schéma par défaut dans 2,0 si l’une des conditions suivantes est remplie :
- Vous souhaitez que l’utilisateur soit automatiquement connecté
- Vous utilisez les `[Authorize]` stratégies d’attribut ou d’autorisation sans spécifier de schémas

La méthode est une exception à cette règle `AddIdentity` . Cette méthode ajoute cookie des s pour vous et définit les schémas d’authentification et de stimulation par défaut pour l’application cookie `IdentityConstants.ApplicationScheme` . En outre, il définit le schéma de connexion par défaut sur l’externe cookie `IdentityConstants.ExternalScheme` .

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Utiliser les extensions d’authentification HttpContext

L' `IAuthenticationManager` interface est le point d’entrée principal dans le système d’authentification 1. x. Il a été remplacé par un nouvel ensemble de `HttpContext` méthodes d’extension dans l' `Microsoft.AspNetCore.Authentication` espace de noms.

Par exemple, les projets 1. x référencent une `Authentication` propriété :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Dans les projets 2,0, importez l' `Microsoft.AspNetCore.Authentication` espace de noms et supprimez les `Authentication` références de propriété :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Authentification Windows (HTTP.sys/IISIntegration)

Il existe deux variantes de l’authentification Windows :

* L’hôte autorise uniquement les utilisateurs authentifiés. Cette variation n’est pas affectée par les modifications apportées à 2,0.
* L’hôte autorise les utilisateurs anonymes et authentifiés. Cette variation est affectée par les modifications apportées à 2,0. Par exemple, l’application doit autoriser les utilisateurs anonymes au niveau de la couche [IIS](xref:host-and-deploy/iis/index) ou [HTTP.sys](xref:fundamentals/servers/httpsys) , mais autoriser les utilisateurs au niveau du contrôleur. Dans ce scénario, définissez le schéma par défaut dans la `Startup.ConfigureServices` méthode.

  Pour [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), définissez le schéma par défaut sur `IISDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Pour [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), définissez le schéma par défaut sur `HttpSysDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Si vous ne définissez pas le schéma par défaut, la demande Authorize (Challenge) ne pourra pas fonctionner avec l’exception suivante :

  > `System.InvalidOperationException`: Aucun authenticationScheme n’a été spécifié, et aucun DefaultChallengeScheme n’a été trouvé.

Pour plus d'informations, consultez <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a>IdentityCookieInstances d’options

Un effet secondaire des modifications 2,0 est le passage à l’utilisation des options nommées à la place des cookie instances d’options. La possibilité de personnaliser les Identity cookie noms de schéma est supprimée.

Par exemple, les projets 1. x utilisent l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) pour passer un `IdentityCookieOptions` paramètre dans *AccountController.cs* et *ManageController.cs* . Le cookie schéma d’authentification externe est accessible à partir de l’instance fournie :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

L’injection de constructeur susmentionnée devient inutile dans les projets 2,0 et le `_externalCookieScheme` champ peut être supprimé :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

les projets 1. x utilisaient le `_externalCookieScheme` champ comme suit :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Dans les projets 2,0, remplacez le code précédent par ce qui suit. La `IdentityConstants.ExternalScheme` constante peut être utilisée directement.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Pour résoudre l’appel récemment ajouté `SignOutAsync` , importez l’espace de noms suivant :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a>Ajouter des Identity Propriétés de navigation poco utilisateur

Les propriétés de navigation principales de l’Entity Framework (EF) de l' `IdentityUser` objet POCO (Plain Old CLR Object) de base ont été supprimées. Si votre projet 1. x a utilisé ces propriétés, rajoutez-les manuellement au projet 2,0 :

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

Pour éviter les doublons de clé étrangère lors de l’exécution de EF Core migrations, ajoutez le code suivant à la méthode de votre `IdentityDbContext` classe `OnModelCreating` (après l' `base.OnModelCreating();` appel) :

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>Remplacer GetExternalAuthenticationSchemes

La méthode synchrone `GetExternalAuthenticationSchemes` a été supprimée en faveur d’une version asynchrone. les projets 1. x ont le code suivant dans *Controllers/ManageController. cs* :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Cette méthode apparaît également dans *views/Account/login. cshtml* :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

Dans les projets 2,0, utilisez la <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> méthode. La modification dans *ManageController.cs* ressemble au code suivant :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

Dans *login. cshtml* , la `AuthenticationScheme` propriété accessible dans la `foreach` boucle devient `Name` :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Modification de la propriété ManageLoginsViewModel

Un `ManageLoginsViewModel` objet est utilisé dans l' `ManageLogins` action de *ManageController.cs* . Dans les projets 1. x, le type de retour de la propriété de l’objet `OtherLogins` est `IList<AuthenticationDescription>` . Ce type de retour nécessite une importation de `Microsoft.AspNetCore.Http.Authentication` :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

Dans les projets 2,0, le type de retour devient `IList<AuthenticationScheme>` . Ce nouveau type de retour nécessite le remplacement de l' `Microsoft.AspNetCore.Http.Authentication` importation par une `Microsoft.AspNetCore.Authentication` importation.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez la discussion sur le problème [Auth 2,0](https://github.com/aspnet/Security/issues/1338) sur GitHub.
