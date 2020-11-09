---
title: 'Migrer l’authentification et Identity vers ASP.NET Core 2,0'
author: scottaddie
description: 'Cet article décrit les étapes les plus courantes pour la migration de ASP.NET Core authentification 1. x et Identity ASP.NET Core 2,0.'
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057178"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="aa9b8-103">Migrer l’authentification et Identity vers ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="aa9b8-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="aa9b8-104">Par [Scott Addie](https://github.com/scottaddie) et [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="aa9b8-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="aa9b8-105">ASP.NET Core 2,0 dispose d’un nouveau modèle d’authentification et [Identity](xref:security/authentication/identity) simplifie la configuration à l’aide de services.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="aa9b8-106">ASP.NET Core les applications 1. x qui utilisent l’authentification ou Identity peuvent être mises à jour pour utiliser le nouveau modèle comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="aa9b8-107">Mettre à jour les espaces de noms</span><span class="sxs-lookup"><span data-stu-id="aa9b8-107">Update namespaces</span></span>

<span data-ttu-id="aa9b8-108">Dans 1. x, les classes telles `IdentityRole` que et `IdentityUser` ont été trouvées dans l' `Microsoft.AspNetCore.Identity.EntityFrameworkCore` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="aa9b8-109">Dans 2,0, l' <xref:Microsoft.AspNetCore.Identity> espace de noms est devenu la nouvelle page d’hébergement pour plusieurs de ces classes.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="aa9b8-110">Avec le code par défaut Identity , les classes affectées incluent `ApplicationUser` et `Startup` .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="aa9b8-111">Ajustez vos `using` instructions pour résoudre les références affectées.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="aa9b8-112">Intergiciel et services d’authentification</span><span class="sxs-lookup"><span data-stu-id="aa9b8-112">Authentication Middleware and services</span></span>

<span data-ttu-id="aa9b8-113">Dans les projets 1. x, l’authentification est configurée à l’aide d’un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="aa9b8-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="aa9b8-114">Une méthode d’intergiciel est appelée pour chaque schéma d’authentification que vous souhaitez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="aa9b8-115">L’exemple 1. x suivant configure l’authentification Facebook avec Identity dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs* :</span></span>

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

<span data-ttu-id="aa9b8-116">Dans les projets 2,0, l’authentification est configurée via les services.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="aa9b8-117">Chaque schéma d’authentification est inscrit dans la `ConfigureServices` méthode de *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs* .</span></span> <span data-ttu-id="aa9b8-118">La `UseIdentity` méthode est remplacée par `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="aa9b8-119">L’exemple 2,0 suivant configure l’authentification Facebook avec Identity dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs* :</span></span>

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

<span data-ttu-id="aa9b8-120">La `UseAuthentication` méthode ajoute un seul composant d’intergiciel d’authentification, qui est responsable de l’authentification automatique et de la gestion des demandes d’authentification distantes.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="aa9b8-121">Il remplace tous les composants de l’intergiciel (middleware) individuels par un seul composant de middleware commun.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="aa9b8-122">Vous trouverez ci-dessous des instructions de migration de 2,0 pour chaque schéma d’authentification principal.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="aa9b8-123">Cookieauthentification basée sur</span><span class="sxs-lookup"><span data-stu-id="aa9b8-123">Cookie-based authentication</span></span>

<span data-ttu-id="aa9b8-124">Sélectionnez l’une des deux options ci-dessous et apportez les modifications nécessaires dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-124">Select one of the two options below, and make the necessary changes in *Startup.cs* :</span></span>

1. <span data-ttu-id="aa9b8-125">Utiliser cookie s avec Identity</span><span class="sxs-lookup"><span data-stu-id="aa9b8-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="aa9b8-126">Remplacez `UseIdentity` par `UseAuthentication` dans la `Configure` méthode :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="aa9b8-127">Appelez la `AddIdentity` méthode dans la `ConfigureServices` méthode pour ajouter les cookie services d’authentification.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="aa9b8-128">Si vous le souhaitez, appelez la `ConfigureApplicationCookie` `ConfigureExternalCookie` méthode ou dans la `ConfigureServices` méthode pour modifier les Identity cookie paramètres.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="aa9b8-129">Utiliser cookie s sans Identity</span><span class="sxs-lookup"><span data-stu-id="aa9b8-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="aa9b8-130">Remplacez l' `UseCookieAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="aa9b8-131">Appelez les `AddAuthentication` `AddCookie` méthodes et dans la `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="aa9b8-132">Authentification du porteur JWT</span><span class="sxs-lookup"><span data-stu-id="aa9b8-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="aa9b8-133">Apportez les modifications suivantes dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-133">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="aa9b8-134">Remplacez l' `UseJwtBearerAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="aa9b8-135">Appelez la `AddJwtBearer` méthode dans la `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="aa9b8-136">Cet extrait de code n’utilise pas Identity . par conséquent, le schéma par défaut doit être défini en passant `JwtBearerDefaults.AuthenticationScheme` à la `AddAuthentication` méthode.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="aa9b8-137">Authentification OpenID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="aa9b8-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="aa9b8-138">Apportez les modifications suivantes dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-138">Make the following changes in *Startup.cs* :</span></span>

- <span data-ttu-id="aa9b8-139">Remplacez l' `UseOpenIdConnectAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="aa9b8-140">Appelez la `AddOpenIdConnect` méthode dans la `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="aa9b8-141">Remplacez la `PostLogoutRedirectUri` propriété dans l' `OpenIdConnectOptions` action par `SignedOutRedirectUri` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="aa9b8-142">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="aa9b8-142">Facebook authentication</span></span>

<span data-ttu-id="aa9b8-143">Apportez les modifications suivantes dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-143">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="aa9b8-144">Remplacez l' `UseFacebookAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="aa9b8-145">Appelez la `AddFacebook` méthode dans la `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="aa9b8-146">Authentification Google</span><span class="sxs-lookup"><span data-stu-id="aa9b8-146">Google authentication</span></span>

<span data-ttu-id="aa9b8-147">Apportez les modifications suivantes dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-147">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="aa9b8-148">Remplacez l' `UseGoogleAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="aa9b8-149">Appelez la `AddGoogle` méthode dans la `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="aa9b8-150">Authentification du compte Microsoft</span><span class="sxs-lookup"><span data-stu-id="aa9b8-150">Microsoft Account authentication</span></span>

<span data-ttu-id="aa9b8-151">Pour plus d’informations sur l’authentification compte Microsoft, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span><span class="sxs-lookup"><span data-stu-id="aa9b8-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="aa9b8-152">Apportez les modifications suivantes dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-152">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="aa9b8-153">Remplacez l' `UseMicrosoftAccountAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="aa9b8-154">Appelez la `AddMicrosoftAccount` méthode dans la `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="aa9b8-155">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="aa9b8-155">Twitter authentication</span></span>

<span data-ttu-id="aa9b8-156">Apportez les modifications suivantes dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-156">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="aa9b8-157">Remplacez l' `UseTwitterAuthentication` appel de méthode dans la `Configure` méthode par `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="aa9b8-158">Appelez la `AddTwitter` méthode dans la `ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="aa9b8-159">Définition des schémas d’authentification par défaut</span><span class="sxs-lookup"><span data-stu-id="aa9b8-159">Setting default authentication schemes</span></span>

<span data-ttu-id="aa9b8-160">Dans 1. x, les `AutomaticAuthenticate` `AutomaticChallenge` Propriétés et de la classe de base [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) étaient destinées à être définies sur un seul schéma d’authentification.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="aa9b8-161">Il n’existait pas de bonne méthode pour l’appliquer.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="aa9b8-162">Dans 2,0, ces deux propriétés ont été supprimées en tant que propriétés sur l' `AuthenticationOptions` instance individuelle.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="aa9b8-163">Ils peuvent être configurés dans l' `AddAuthentication` appel de méthode au sein `ConfigureServices` de la méthode de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs* :</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="aa9b8-164">Dans l’extrait de code précédent, le schéma par défaut est défini sur `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="aa9b8-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="aa9b8-165">Vous pouvez également utiliser une version surchargée de la `AddAuthentication` méthode pour définir plusieurs propriétés.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="aa9b8-166">Dans l’exemple de méthode surchargée suivant, le schéma par défaut est défini sur `CookieAuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="aa9b8-167">Le schéma d’authentification peut également être spécifié dans vos propres `[Authorize]` attributs ou stratégies d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="aa9b8-168">Définissez un schéma par défaut dans 2,0 si l’une des conditions suivantes est remplie :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="aa9b8-169">Vous souhaitez que l’utilisateur soit automatiquement connecté</span><span class="sxs-lookup"><span data-stu-id="aa9b8-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="aa9b8-170">Vous utilisez les `[Authorize]` stratégies d’attribut ou d’autorisation sans spécifier de schémas</span><span class="sxs-lookup"><span data-stu-id="aa9b8-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="aa9b8-171">La méthode est une exception à cette règle `AddIdentity` .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="aa9b8-172">Cette méthode ajoute cookie des s pour vous et définit les schémas d’authentification et de stimulation par défaut pour l’application cookie `IdentityConstants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="aa9b8-173">En outre, il définit le schéma de connexion par défaut sur l’externe cookie `IdentityConstants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="aa9b8-174">Utiliser les extensions d’authentification HttpContext</span><span class="sxs-lookup"><span data-stu-id="aa9b8-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="aa9b8-175">L' `IAuthenticationManager` interface est le point d’entrée principal dans le système d’authentification 1. x.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="aa9b8-176">Il a été remplacé par un nouvel ensemble de `HttpContext` méthodes d’extension dans l' `Microsoft.AspNetCore.Authentication` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="aa9b8-177">Par exemple, les projets 1. x référencent une `Authentication` propriété :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="aa9b8-178">Dans les projets 2,0, importez l' `Microsoft.AspNetCore.Authentication` espace de noms et supprimez les `Authentication` références de propriété :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="aa9b8-179">Authentification Windows (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="aa9b8-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="aa9b8-180">Il existe deux variantes de l’authentification Windows :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="aa9b8-181">L’hôte autorise uniquement les utilisateurs authentifiés.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-181">The host only allows authenticated users.</span></span> <span data-ttu-id="aa9b8-182">Cette variation n’est pas affectée par les modifications apportées à 2,0.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="aa9b8-183">L’hôte autorise les utilisateurs anonymes et authentifiés.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="aa9b8-184">Cette variation est affectée par les modifications apportées à 2,0.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="aa9b8-185">Par exemple, l’application doit autoriser les utilisateurs anonymes au niveau de la couche [IIS](xref:host-and-deploy/iis/index) ou [HTTP.sys](xref:fundamentals/servers/httpsys) , mais autoriser les utilisateurs au niveau du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="aa9b8-186">Dans ce scénario, définissez le schéma par défaut dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="aa9b8-187">Pour [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), définissez le schéma par défaut sur `IISDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="aa9b8-188">Pour [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), définissez le schéma par défaut sur `HttpSysDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="aa9b8-189">Si vous ne définissez pas le schéma par défaut, la demande Authorize (Challenge) ne pourra pas fonctionner avec l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="aa9b8-190">`System.InvalidOperationException`: Aucun authenticationScheme n’a été spécifié, et aucun DefaultChallengeScheme n’a été trouvé.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="aa9b8-191">Pour plus d'informations, consultez <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="aa9b8-192">IdentityCookieInstances d’options</span><span class="sxs-lookup"><span data-stu-id="aa9b8-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="aa9b8-193">Un effet secondaire des modifications 2,0 est le passage à l’utilisation des options nommées à la place des cookie instances d’options.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="aa9b8-194">La possibilité de personnaliser les Identity cookie noms de schéma est supprimée.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="aa9b8-195">Par exemple, les projets 1. x utilisent l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) pour passer un `IdentityCookieOptions` paramètre dans *AccountController.cs* et *ManageController.cs* .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs* .</span></span> <span data-ttu-id="aa9b8-196">Le cookie schéma d’authentification externe est accessible à partir de l’instance fournie :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="aa9b8-197">L’injection de constructeur susmentionnée devient inutile dans les projets 2,0 et le `_externalCookieScheme` champ peut être supprimé :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="aa9b8-198">les projets 1. x utilisaient le `_externalCookieScheme` champ comme suit :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="aa9b8-199">Dans les projets 2,0, remplacez le code précédent par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="aa9b8-200">La `IdentityConstants.ExternalScheme` constante peut être utilisée directement.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="aa9b8-201">Pour résoudre l’appel récemment ajouté `SignOutAsync` , importez l’espace de noms suivant :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="aa9b8-202">Ajouter des Identity Propriétés de navigation poco utilisateur</span><span class="sxs-lookup"><span data-stu-id="aa9b8-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="aa9b8-203">Les propriétés de navigation principales de l’Entity Framework (EF) de l' `IdentityUser` objet POCO (Plain Old CLR Object) de base ont été supprimées.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="aa9b8-204">Si votre projet 1. x a utilisé ces propriétés, rajoutez-les manuellement au projet 2,0 :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="aa9b8-205">Pour éviter les doublons de clé étrangère lors de l’exécution de EF Core migrations, ajoutez le code suivant à la méthode de votre `IdentityDbContext` classe `OnModelCreating` (après l' `base.OnModelCreating();` appel) :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="aa9b8-206">Remplacer GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="aa9b8-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="aa9b8-207">La méthode synchrone `GetExternalAuthenticationSchemes` a été supprimée en faveur d’une version asynchrone.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="aa9b8-208">les projets 1. x ont le code suivant dans *Controllers/ManageController. cs* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-208">1.x projects have the following code in *Controllers/ManageController.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="aa9b8-209">Cette méthode apparaît également dans *views/Account/login. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="aa9b8-210">Dans les projets 2,0, utilisez la <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> méthode.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="aa9b8-211">La modification dans *ManageController.cs* ressemble au code suivant :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="aa9b8-212">Dans *login. cshtml* , la `AuthenticationScheme` propriété accessible dans la `foreach` boucle devient `Name` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-212">In *Login.cshtml* , the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="aa9b8-213">Modification de la propriété ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="aa9b8-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="aa9b8-214">Un `ManageLoginsViewModel` objet est utilisé dans l' `ManageLogins` action de *ManageController.cs* .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs* .</span></span> <span data-ttu-id="aa9b8-215">Dans les projets 1. x, le type de retour de la propriété de l’objet `OtherLogins` est `IList<AuthenticationDescription>` .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="aa9b8-216">Ce type de retour nécessite une importation de `Microsoft.AspNetCore.Http.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="aa9b8-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="aa9b8-217">Dans les projets 2,0, le type de retour devient `IList<AuthenticationScheme>` .</span><span class="sxs-lookup"><span data-stu-id="aa9b8-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="aa9b8-218">Ce nouveau type de retour nécessite le remplacement de l' `Microsoft.AspNetCore.Http.Authentication` importation par une `Microsoft.AspNetCore.Authentication` importation.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="aa9b8-219">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="aa9b8-219">Additional resources</span></span>

<span data-ttu-id="aa9b8-220">Pour plus d’informations, consultez la discussion sur le problème [Auth 2,0](https://github.com/aspnet/Security/issues/1338) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="aa9b8-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
