---
title: Multi-Factor Authentication dans ASP.NET Core
author: damienbod
description: Découvrez comment configurer Multi-Factor Authentication (MFA) dans une application ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060389"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>Multi-Factor Authentication dans ASP.NET Core

Par [Damien Bowden](https://github.com/damienbod)

[Afficher ou télécharger l’exemple de code (référentiel GitHub damienbod/AspNetCoreHybridFlowWithApi)](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

Multi-Factor Authentication (MFA) est un processus dans lequel un utilisateur est demandé pendant un événement de connexion pour des formes d’identification supplémentaires. Vous pouvez entrer un code à partir d’un téléphone portable, utiliser une clé FIDO2 ou fournir une analyse par empreinte digitale. Lorsque vous avez besoin d’une deuxième forme d’authentification, la sécurité est améliorée. Le facteur supplémentaire n’est pas facilement obtenu ou dupliqué par une personne malveillante.

Cet article aborde les sujets suivants :

* Qu’est-ce que MFA et quels flux MFA sont recommandés
* Configurer l’authentification MFA pour les pages d’administration à l’aide de ASP.NET Core Identity
* Envoyer une exigence de connexion MFA au serveur OpenID Connect
* Forcer ASP.NET Core client OpenID Connect à exiger l’authentification MFA

## <a name="mfa-2fa"></a>MFA, 2FA

L’authentification multifacteur nécessite au moins deux types de preuves pour une identité comme un nom que vous connaissez, un nom que vous possédez ou une validation biométrique pour l’utilisateur à authentifier.

L’authentification à deux facteurs (2FA) est similaire à un sous-ensemble de l’authentification MFA, mais la différence est que l’authentification multifacteur peut nécessiter au moins deux facteurs pour prouver l’identité.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (algorithme de mot de passe à usage unique basé sur le temps)

L’authentification MFA avec TOTP est une implémentation prise en charge à l’aide de ASP.NET Core Identity . Cela peut être utilisé avec n’importe quelle application d’authentificateur conforme, y compris :

* Application Microsoft Authenticator
* Application Google Authenticator

Pour plus d’informations sur l’implémentation, consultez le lien suivant :

[Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>MFA FIDO2 ou mot de passe

FIDO2 est actuellement :

* Le moyen le plus sûr d’obtenir l’authentification multifacteur.
* Le seul circuit MFA qui protège contre les attaques par hameçonnage.

À l’heure actuelle, ASP.NET Core ne prend pas en charge directement FIDO2. FIDO2 peut être utilisé pour l’authentification MFA ou les flux avec mot de passe.

Azure Active Directory prend en charge les FIDO2 et les flux avec mot de passe. Pour plus d’informations, consultez [options d’authentification par mot de passe pour Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).

### <a name="mfa-sms"></a>SMS MFA

L’authentification MFA avec SMS augmente la sécurité massivement comparée à l’authentification par mot de passe (facteur unique). Toutefois, l’utilisation de SMS comme second facteur n’est plus recommandée. Un trop grand nombre de vecteurs d’attaque connus existent pour ce type d’implémentation.

[Instructions du NIST](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a>Configurer l’authentification MFA pour les pages d’administration à l’aide de ASP.NET Core Identity

L’authentification multifacteur peut être forcée sur les utilisateurs qui accèdent à des pages sensibles au sein d’une ASP.NET Core Identity application. Cela peut être utile pour les applications où différents niveaux d’accès existent pour les différentes identités. Par exemple, les utilisateurs peuvent être en mesure d’afficher les données de profil à l’aide d’une connexion de mot de passe, mais un administrateur doit utiliser l’authentification multifacteur pour accéder aux pages d’administration.

### <a name="extend-the-login-with-an-mfa-claim"></a>Étendre la connexion avec une revendication MFA

Le code de démonstration est configuré à l’aide de ASP.NET Core avec les Identity Razor pages et. La `AddIdentity` méthode est utilisée au lieu d' `AddDefaultIdentity` une, donc une `IUserClaimsPrincipalFactory` implémentation peut être utilisée pour ajouter des revendications à l’identité après une connexion réussie.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

La `AdditionalUserClaimsPrincipalFactory` classe ajoute la `amr` revendication aux revendications de l’utilisateur uniquement après une connexion réussie. La valeur de la revendication est lue à partir de la base de données. La revendication est ajoutée ici, car l’utilisateur ne doit accéder qu’à l’affichage protégé le plus élevé si l’identité s’est connectée avec MFA. Si la vue de base de données est lue directement à partir de la base de données au lieu d’utiliser la revendication, il est possible d’accéder à la vue sans MFA directement après l’activation de l’authentification multifacteur.

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

Étant donné que l' Identity installation du service a changé dans la `Startup` classe, les dispositions du Identity doivent être mises à jour. Générez l’échafaudage des Identity pages dans l’application. Définissez la disposition dans le fichier *Identity /Account/Manage/_Layout. cshtml* .

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

Affectez également la disposition de toutes les pages de gestion à partir des Identity pages :

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>Valider les conditions d’authentification multifacteur dans la page d’administration

La Razor page d’administration vérifie que l’utilisateur s’est connecté à l’aide de l’authentification multifacteur. Dans la `OnGet` méthode, l’identité est utilisée pour accéder aux revendications de l’utilisateur. La `amr` valeur est vérifiée pour la revendication `mfa` . Si cette revendication ne figure pas dans l’identité ou si a la valeur `false` , la page est redirigée vers la page activer mfa. Cela est possible parce que l’utilisateur s’est déjà connecté, mais sans MFA.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>Logique d’interface utilisateur pour activer/désactiver les informations de connexion de l’utilisateur

Une stratégie d’autorisation a été ajoutée au démarrage. La stratégie requiert la `amr` revendication avec la valeur `mfa` .

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

Cette stratégie peut ensuite être utilisée dans la `_Layout` vue pour afficher ou masquer le menu d' **administration** avec l’avertissement suivant :

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Si l’identité s’est connectée à l’aide de l’authentification multifacteur, le menu **admin** s’affiche sans l’avertissement d’info-bulle. Lorsque l’utilisateur s’est connecté sans MFA, le menu **admin (non activé)** s’affiche avec l’info-bulle qui informe l’utilisateur (ce qui explique l’avertissement).

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

Si l’utilisateur se connecte sans MFA, l’avertissement s’affiche :

![Authentification MFA administrateur](mfa/_static/identitystandalonemfa_01.png)

L’utilisateur est redirigé vers la vue d’activation de l’authentification MFA quand vous cliquez sur le lien d' **administration** :

![L’administrateur Active l’authentification MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>Envoyer une exigence de connexion MFA au serveur OpenID Connect 

Le `acr_values` paramètre peut être utilisé pour transmettre la `mfa` valeur requise du client au serveur dans une demande d’authentification.

> [!NOTE]
> Le `acr_values` paramètre doit être géré sur le serveur OpenID Connect pour que cela fonctionne.

### <a name="openid-connect-aspnet-core-client"></a>OpenID Connect ASP.NET Core client

ASP.NET Core l' Razor application cliente OpenID pages Connect utilise la `AddOpenIdConnect` méthode pour se connecter au serveur OpenID Connect. Le `acr_values` paramètre est défini avec la `mfa` valeur et est envoyé avec la demande d’authentification. `OpenIdConnectEvents`Est utilisé pour ajouter ce.

Pour connaître les valeurs de paramètre recommandées `acr_values` , consultez valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a>Exemple OpenID Connect Identity Server 4 Server avec ASP.NET Core Identity

Sur le serveur OpenID Connect, qui est implémenté à l’aide ASP.NET Core Identity de avec les vues MVC, une nouvelle vue nommée *ErrorEnable2FA. cshtml* est créée. La vue :

* Indique si le Identity provient d’une application qui requiert l’authentification MFA, mais que l’utilisateur n’a pas activé cette fonctionnalité dans Identity .
* Informe l’utilisateur et ajoute un lien pour activer cette.

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

Dans la `Login` méthode, l' `IIdentityServerInteractionService` implémentation de l’interface `_interaction` est utilisée pour accéder aux paramètres de la demande de connexion OpenID. Le `acr_values` paramètre est accessible à l’aide de la `AcrValues` propriété. À mesure que le client l' `mfa` a envoyé avec set, cette valeur peut ensuite être vérifiée.

Si l’authentification multifacteur est requise et que l’authentification MFA est activée pour l’utilisateur dans ASP.NET Core Identity , la connexion se poursuit. Lorsque l’option MFA n’est pas activée pour l’utilisateur, l’utilisateur est redirigé vers la vue personnalisée *ErrorEnable2FA. cshtml* . ASP.NET Core IdentityConnecte ensuite l’utilisateur.

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

La `ExternalLoginCallback` méthode fonctionne comme la Identity connexion locale. La `AcrValues` valeur est recherchée dans la propriété `mfa` . Si la `mfa` valeur est présente, l’authentification MFA est forcée avant la fin de la connexion (par exemple, redirigé vers la `ErrorEnable2FA` vue).

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

Si l’utilisateur est déjà connecté, l’application cliente :

* Valide toujours la `amr` revendication.
* Peut configurer l’authentification multifacteur avec un lien vers la ASP.NET Core Identity vue.

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>Forcer ASP.NET Core client OpenID Connect à exiger l’authentification MFA

Cet exemple montre comment une Razor application de Page ASP.net Core, qui utilise OpenID Connect pour se connecter, peut exiger que les utilisateurs soient authentifiés à l’aide de l’authentification multifacteur.

Pour valider l’exigence d’authentification multifacteur, une `IAuthorizationRequirement` spécification est créée. Il sera ajouté aux pages à l’aide d’une stratégie qui requiert l’authentification MFA.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

Un `AuthorizationHandler` est implémenté qui utilisera la `amr` revendication et vérifiera la valeur `mfa` . `amr`Est retourné dans le `id_token` d’une authentification réussie et peut avoir de nombreuses valeurs différentes comme défini dans la spécification des valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

La valeur retournée dépend de la manière dont l’identité a été authentifiée et de l’implémentation du serveur OpenID Connect.

Le `AuthorizationHandler` utilise la `RequireMfa` spécification et valide la `amr` revendication. Le serveur OpenID Connect peut être implémenté à l’aide Identity de 4 avec ASP.NET Core Identity . Lorsqu’un utilisateur se connecte à l’aide de TOTP, la `amr` revendication est retournée avec une valeur mfa. Si vous utilisez une autre implémentation de serveur OpenID Connect ou un autre type d’authentification multifacteur, la `amr` revendication peut avoir une valeur différente. Le code doit être étendu pour accepter cela également.

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

Dans la `Startup.ConfigureServices` méthode, la `AddOpenIdConnect` méthode est utilisée comme schéma de stimulation par défaut. Le gestionnaire d’autorisations, qui est utilisé pour vérifier la `amr` revendication, est ajouté à l’inversion du conteneur de contrôle. Une stratégie est ensuite créée, ce qui ajoute la `RequireMfa` spécification.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

Cette stratégie est ensuite utilisée dans la Razor page, selon les besoins. La stratégie peut également être ajoutée globalement pour l’ensemble de l’application.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

Si l’utilisateur s’authentifie sans MFA, la `amr` revendication aura probablement une `pwd` valeur. La demande n’est pas autorisée à accéder à la page. À l’aide des valeurs par défaut, l’utilisateur est redirigé vers la page *compte/AccessDenied* . Ce comportement peut être modifié ou vous pouvez implémenter votre propre logique personnalisée ici. Dans cet exemple, un lien est ajouté afin que l’utilisateur valide puisse configurer MFA pour son compte.

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

Désormais, seuls les utilisateurs qui s’authentifient avec MFA peuvent accéder à la page ou au site Web. Si différents types d’authentification multifacteur sont utilisés ou si 2FA est OK, la `amr` revendication aura des valeurs différentes et doit être traitée correctement. Différents serveurs OpenID Connect retournent également des valeurs différentes pour cette revendication et peuvent ne pas suivre la spécification des valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

Lors de la connexion sans authentification multifacteur (par exemple, à l’aide d’un mot de passe uniquement) :

* `amr`A la `pwd` valeur :

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* Accès refusé :

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

Vous pouvez également ouvrir une session avec un mot de passe à usage unique avec Identity :

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Options d’authentification sans mot de passe pour Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless)
* [FIDO2 bibliothèque .NET pour l’attestation et l’assertion FIDO2/webauthn à l’aide de .NET](https://github.com/abergs/fido2-net-lib)
* [Authentification Isard](https://github.com/herrjemand/awesome-webauthn)
