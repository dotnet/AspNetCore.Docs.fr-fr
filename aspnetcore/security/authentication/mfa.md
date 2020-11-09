---
title: Multi-Factor Authentication dans ASP.NET Core
author: damienbod
description: Découvrez comment configurer Multi-Factor Authentication (MFA) dans une application ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060389"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="4cffc-103">Multi-Factor Authentication dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4cffc-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="4cffc-104">Par [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="4cffc-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="4cffc-105">Afficher ou télécharger l’exemple de code (référentiel GitHub damienbod/AspNetCoreHybridFlowWithApi)</span><span class="sxs-lookup"><span data-stu-id="4cffc-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="4cffc-106">Multi-Factor Authentication (MFA) est un processus dans lequel un utilisateur est demandé pendant un événement de connexion pour des formes d’identification supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="4cffc-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="4cffc-107">Vous pouvez entrer un code à partir d’un téléphone portable, utiliser une clé FIDO2 ou fournir une analyse par empreinte digitale.</span><span class="sxs-lookup"><span data-stu-id="4cffc-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="4cffc-108">Lorsque vous avez besoin d’une deuxième forme d’authentification, la sécurité est améliorée.</span><span class="sxs-lookup"><span data-stu-id="4cffc-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="4cffc-109">Le facteur supplémentaire n’est pas facilement obtenu ou dupliqué par une personne malveillante.</span><span class="sxs-lookup"><span data-stu-id="4cffc-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="4cffc-110">Cet article aborde les sujets suivants :</span><span class="sxs-lookup"><span data-stu-id="4cffc-110">This article covers the following areas:</span></span>

* <span data-ttu-id="4cffc-111">Qu’est-ce que MFA et quels flux MFA sont recommandés</span><span class="sxs-lookup"><span data-stu-id="4cffc-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="4cffc-112">Configurer l’authentification MFA pour les pages d’administration à l’aide de ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="4cffc-112">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="4cffc-113">Envoyer une exigence de connexion MFA au serveur OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="4cffc-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="4cffc-114">Forcer ASP.NET Core client OpenID Connect à exiger l’authentification MFA</span><span class="sxs-lookup"><span data-stu-id="4cffc-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="4cffc-115">MFA, 2FA</span><span class="sxs-lookup"><span data-stu-id="4cffc-115">MFA, 2FA</span></span>

<span data-ttu-id="4cffc-116">L’authentification multifacteur nécessite au moins deux types de preuves pour une identité comme un nom que vous connaissez, un nom que vous possédez ou une validation biométrique pour l’utilisateur à authentifier.</span><span class="sxs-lookup"><span data-stu-id="4cffc-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="4cffc-117">L’authentification à deux facteurs (2FA) est similaire à un sous-ensemble de l’authentification MFA, mais la différence est que l’authentification multifacteur peut nécessiter au moins deux facteurs pour prouver l’identité.</span><span class="sxs-lookup"><span data-stu-id="4cffc-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="4cffc-118">MFA TOTP (algorithme de mot de passe à usage unique basé sur le temps)</span><span class="sxs-lookup"><span data-stu-id="4cffc-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="4cffc-119">L’authentification MFA avec TOTP est une implémentation prise en charge à l’aide de ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="4cffc-119">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="4cffc-120">Cela peut être utilisé avec n’importe quelle application d’authentificateur conforme, y compris :</span><span class="sxs-lookup"><span data-stu-id="4cffc-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="4cffc-121">Application Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="4cffc-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="4cffc-122">Application Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="4cffc-122">Google Authenticator App</span></span>

<span data-ttu-id="4cffc-123">Pour plus d’informations sur l’implémentation, consultez le lien suivant :</span><span class="sxs-lookup"><span data-stu-id="4cffc-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="4cffc-124">Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4cffc-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="4cffc-125">MFA FIDO2 ou mot de passe</span><span class="sxs-lookup"><span data-stu-id="4cffc-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="4cffc-126">FIDO2 est actuellement :</span><span class="sxs-lookup"><span data-stu-id="4cffc-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="4cffc-127">Le moyen le plus sûr d’obtenir l’authentification multifacteur.</span><span class="sxs-lookup"><span data-stu-id="4cffc-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="4cffc-128">Le seul circuit MFA qui protège contre les attaques par hameçonnage.</span><span class="sxs-lookup"><span data-stu-id="4cffc-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="4cffc-129">À l’heure actuelle, ASP.NET Core ne prend pas en charge directement FIDO2.</span><span class="sxs-lookup"><span data-stu-id="4cffc-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="4cffc-130">FIDO2 peut être utilisé pour l’authentification MFA ou les flux avec mot de passe.</span><span class="sxs-lookup"><span data-stu-id="4cffc-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="4cffc-131">Azure Active Directory prend en charge les FIDO2 et les flux avec mot de passe.</span><span class="sxs-lookup"><span data-stu-id="4cffc-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="4cffc-132">Pour plus d’informations, consultez [options d’authentification par mot de passe pour Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="4cffc-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="4cffc-133">SMS MFA</span><span class="sxs-lookup"><span data-stu-id="4cffc-133">MFA SMS</span></span>

<span data-ttu-id="4cffc-134">L’authentification MFA avec SMS augmente la sécurité massivement comparée à l’authentification par mot de passe (facteur unique).</span><span class="sxs-lookup"><span data-stu-id="4cffc-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="4cffc-135">Toutefois, l’utilisation de SMS comme second facteur n’est plus recommandée.</span><span class="sxs-lookup"><span data-stu-id="4cffc-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="4cffc-136">Un trop grand nombre de vecteurs d’attaque connus existent pour ce type d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="4cffc-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="4cffc-137">Instructions du NIST</span><span class="sxs-lookup"><span data-stu-id="4cffc-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="4cffc-138">Configurer l’authentification MFA pour les pages d’administration à l’aide de ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="4cffc-138">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="4cffc-139">L’authentification multifacteur peut être forcée sur les utilisateurs qui accèdent à des pages sensibles au sein d’une ASP.NET Core Identity application.</span><span class="sxs-lookup"><span data-stu-id="4cffc-139">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="4cffc-140">Cela peut être utile pour les applications où différents niveaux d’accès existent pour les différentes identités.</span><span class="sxs-lookup"><span data-stu-id="4cffc-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="4cffc-141">Par exemple, les utilisateurs peuvent être en mesure d’afficher les données de profil à l’aide d’une connexion de mot de passe, mais un administrateur doit utiliser l’authentification multifacteur pour accéder aux pages d’administration.</span><span class="sxs-lookup"><span data-stu-id="4cffc-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="4cffc-142">Étendre la connexion avec une revendication MFA</span><span class="sxs-lookup"><span data-stu-id="4cffc-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="4cffc-143">Le code de démonstration est configuré à l’aide de ASP.NET Core avec les Identity Razor pages et.</span><span class="sxs-lookup"><span data-stu-id="4cffc-143">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="4cffc-144">La `AddIdentity` méthode est utilisée au lieu d' `AddDefaultIdentity` une, donc une `IUserClaimsPrincipalFactory` implémentation peut être utilisée pour ajouter des revendications à l’identité après une connexion réussie.</span><span class="sxs-lookup"><span data-stu-id="4cffc-144">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

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

<span data-ttu-id="4cffc-145">La `AdditionalUserClaimsPrincipalFactory` classe ajoute la `amr` revendication aux revendications de l’utilisateur uniquement après une connexion réussie.</span><span class="sxs-lookup"><span data-stu-id="4cffc-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="4cffc-146">La valeur de la revendication est lue à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="4cffc-146">The claim's value is read from the database.</span></span> <span data-ttu-id="4cffc-147">La revendication est ajoutée ici, car l’utilisateur ne doit accéder qu’à l’affichage protégé le plus élevé si l’identité s’est connectée avec MFA.</span><span class="sxs-lookup"><span data-stu-id="4cffc-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="4cffc-148">Si la vue de base de données est lue directement à partir de la base de données au lieu d’utiliser la revendication, il est possible d’accéder à la vue sans MFA directement après l’activation de l’authentification multifacteur.</span><span class="sxs-lookup"><span data-stu-id="4cffc-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

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

<span data-ttu-id="4cffc-149">Étant donné que l' Identity installation du service a changé dans la `Startup` classe, les dispositions du Identity doivent être mises à jour.</span><span class="sxs-lookup"><span data-stu-id="4cffc-149">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="4cffc-150">Générez l’échafaudage des Identity pages dans l’application.</span><span class="sxs-lookup"><span data-stu-id="4cffc-150">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="4cffc-151">Définissez la disposition dans le fichier *Identity /Account/Manage/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4cffc-151">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="4cffc-152">Affectez également la disposition de toutes les pages de gestion à partir des Identity pages :</span><span class="sxs-lookup"><span data-stu-id="4cffc-152">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="4cffc-153">Valider les conditions d’authentification multifacteur dans la page d’administration</span><span class="sxs-lookup"><span data-stu-id="4cffc-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="4cffc-154">La Razor page d’administration vérifie que l’utilisateur s’est connecté à l’aide de l’authentification multifacteur.</span><span class="sxs-lookup"><span data-stu-id="4cffc-154">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="4cffc-155">Dans la `OnGet` méthode, l’identité est utilisée pour accéder aux revendications de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4cffc-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="4cffc-156">La `amr` valeur est vérifiée pour la revendication `mfa` .</span><span class="sxs-lookup"><span data-stu-id="4cffc-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="4cffc-157">Si cette revendication ne figure pas dans l’identité ou si a la valeur `false` , la page est redirigée vers la page activer mfa.</span><span class="sxs-lookup"><span data-stu-id="4cffc-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="4cffc-158">Cela est possible parce que l’utilisateur s’est déjà connecté, mais sans MFA.</span><span class="sxs-lookup"><span data-stu-id="4cffc-158">This is possible because the user has logged in already, but without MFA.</span></span>

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

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="4cffc-159">Logique d’interface utilisateur pour activer/désactiver les informations de connexion de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="4cffc-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="4cffc-160">Une stratégie d’autorisation a été ajoutée au démarrage.</span><span class="sxs-lookup"><span data-stu-id="4cffc-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="4cffc-161">La stratégie requiert la `amr` revendication avec la valeur `mfa` .</span><span class="sxs-lookup"><span data-stu-id="4cffc-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="4cffc-162">Cette stratégie peut ensuite être utilisée dans la `_Layout` vue pour afficher ou masquer le menu d' **administration** avec l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="4cffc-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="4cffc-163">Si l’identité s’est connectée à l’aide de l’authentification multifacteur, le menu **admin** s’affiche sans l’avertissement d’info-bulle.</span><span class="sxs-lookup"><span data-stu-id="4cffc-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="4cffc-164">Lorsque l’utilisateur s’est connecté sans MFA, le menu **admin (non activé)** s’affiche avec l’info-bulle qui informe l’utilisateur (ce qui explique l’avertissement).</span><span class="sxs-lookup"><span data-stu-id="4cffc-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

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

<span data-ttu-id="4cffc-165">Si l’utilisateur se connecte sans MFA, l’avertissement s’affiche :</span><span class="sxs-lookup"><span data-stu-id="4cffc-165">If the user logs in without MFA, the warning is displayed:</span></span>

![Authentification MFA administrateur](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="4cffc-167">L’utilisateur est redirigé vers la vue d’activation de l’authentification MFA quand vous cliquez sur le lien d' **administration** :</span><span class="sxs-lookup"><span data-stu-id="4cffc-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![L’administrateur Active l’authentification MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="4cffc-169">Envoyer une exigence de connexion MFA au serveur OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="4cffc-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="4cffc-170">Le `acr_values` paramètre peut être utilisé pour transmettre la `mfa` valeur requise du client au serveur dans une demande d’authentification.</span><span class="sxs-lookup"><span data-stu-id="4cffc-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="4cffc-171">Le `acr_values` paramètre doit être géré sur le serveur OpenID Connect pour que cela fonctionne.</span><span class="sxs-lookup"><span data-stu-id="4cffc-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="4cffc-172">OpenID Connect ASP.NET Core client</span><span class="sxs-lookup"><span data-stu-id="4cffc-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="4cffc-173">ASP.NET Core l' Razor application cliente OpenID pages Connect utilise la `AddOpenIdConnect` méthode pour se connecter au serveur OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="4cffc-173">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="4cffc-174">Le `acr_values` paramètre est défini avec la `mfa` valeur et est envoyé avec la demande d’authentification.</span><span class="sxs-lookup"><span data-stu-id="4cffc-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="4cffc-175">`OpenIdConnectEvents`Est utilisé pour ajouter ce.</span><span class="sxs-lookup"><span data-stu-id="4cffc-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="4cffc-176">Pour connaître les valeurs de paramètre recommandées `acr_values` , consultez valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="4cffc-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

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

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="4cffc-177">Exemple OpenID Connect Identity Server 4 Server avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="4cffc-177">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="4cffc-178">Sur le serveur OpenID Connect, qui est implémenté à l’aide ASP.NET Core Identity de avec les vues MVC, une nouvelle vue nommée *ErrorEnable2FA. cshtml* est créée.</span><span class="sxs-lookup"><span data-stu-id="4cffc-178">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="4cffc-179">La vue :</span><span class="sxs-lookup"><span data-stu-id="4cffc-179">The view:</span></span>

* <span data-ttu-id="4cffc-180">Indique si le Identity provient d’une application qui requiert l’authentification MFA, mais que l’utilisateur n’a pas activé cette fonctionnalité dans Identity .</span><span class="sxs-lookup"><span data-stu-id="4cffc-180">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="4cffc-181">Informe l’utilisateur et ajoute un lien pour activer cette.</span><span class="sxs-lookup"><span data-stu-id="4cffc-181">Informs the user and adds a link to activate this.</span></span>

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

<span data-ttu-id="4cffc-182">Dans la `Login` méthode, l' `IIdentityServerInteractionService` implémentation de l’interface `_interaction` est utilisée pour accéder aux paramètres de la demande de connexion OpenID.</span><span class="sxs-lookup"><span data-stu-id="4cffc-182">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="4cffc-183">Le `acr_values` paramètre est accessible à l’aide de la `AcrValues` propriété.</span><span class="sxs-lookup"><span data-stu-id="4cffc-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="4cffc-184">À mesure que le client l' `mfa` a envoyé avec set, cette valeur peut ensuite être vérifiée.</span><span class="sxs-lookup"><span data-stu-id="4cffc-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="4cffc-185">Si l’authentification multifacteur est requise et que l’authentification MFA est activée pour l’utilisateur dans ASP.NET Core Identity , la connexion se poursuit.</span><span class="sxs-lookup"><span data-stu-id="4cffc-185">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="4cffc-186">Lorsque l’option MFA n’est pas activée pour l’utilisateur, l’utilisateur est redirigé vers la vue personnalisée *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4cffc-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml* .</span></span> <span data-ttu-id="4cffc-187">ASP.NET Core IdentityConnecte ensuite l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4cffc-187">Then ASP.NET Core Identity signs the user in.</span></span>

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

<span data-ttu-id="4cffc-188">La `ExternalLoginCallback` méthode fonctionne comme la Identity connexion locale.</span><span class="sxs-lookup"><span data-stu-id="4cffc-188">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="4cffc-189">La `AcrValues` valeur est recherchée dans la propriété `mfa` .</span><span class="sxs-lookup"><span data-stu-id="4cffc-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="4cffc-190">Si la `mfa` valeur est présente, l’authentification MFA est forcée avant la fin de la connexion (par exemple, redirigé vers la `ErrorEnable2FA` vue).</span><span class="sxs-lookup"><span data-stu-id="4cffc-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

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

<span data-ttu-id="4cffc-191">Si l’utilisateur est déjà connecté, l’application cliente :</span><span class="sxs-lookup"><span data-stu-id="4cffc-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="4cffc-192">Valide toujours la `amr` revendication.</span><span class="sxs-lookup"><span data-stu-id="4cffc-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="4cffc-193">Peut configurer l’authentification multifacteur avec un lien vers la ASP.NET Core Identity vue.</span><span class="sxs-lookup"><span data-stu-id="4cffc-193">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="4cffc-195">Forcer ASP.NET Core client OpenID Connect à exiger l’authentification MFA</span><span class="sxs-lookup"><span data-stu-id="4cffc-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="4cffc-196">Cet exemple montre comment une Razor application de Page ASP.net Core, qui utilise OpenID Connect pour se connecter, peut exiger que les utilisateurs soient authentifiés à l’aide de l’authentification multifacteur.</span><span class="sxs-lookup"><span data-stu-id="4cffc-196">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="4cffc-197">Pour valider l’exigence d’authentification multifacteur, une `IAuthorizationRequirement` spécification est créée.</span><span class="sxs-lookup"><span data-stu-id="4cffc-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="4cffc-198">Il sera ajouté aux pages à l’aide d’une stratégie qui requiert l’authentification MFA.</span><span class="sxs-lookup"><span data-stu-id="4cffc-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="4cffc-199">Un `AuthorizationHandler` est implémenté qui utilisera la `amr` revendication et vérifiera la valeur `mfa` .</span><span class="sxs-lookup"><span data-stu-id="4cffc-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="4cffc-200">`amr`Est retourné dans le `id_token` d’une authentification réussie et peut avoir de nombreuses valeurs différentes comme défini dans la spécification des valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="4cffc-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="4cffc-201">La valeur retournée dépend de la manière dont l’identité a été authentifiée et de l’implémentation du serveur OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="4cffc-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="4cffc-202">Le `AuthorizationHandler` utilise la `RequireMfa` spécification et valide la `amr` revendication.</span><span class="sxs-lookup"><span data-stu-id="4cffc-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="4cffc-203">Le serveur OpenID Connect peut être implémenté à l’aide Identity de 4 avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="4cffc-203">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="4cffc-204">Lorsqu’un utilisateur se connecte à l’aide de TOTP, la `amr` revendication est retournée avec une valeur mfa.</span><span class="sxs-lookup"><span data-stu-id="4cffc-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="4cffc-205">Si vous utilisez une autre implémentation de serveur OpenID Connect ou un autre type d’authentification multifacteur, la `amr` revendication peut avoir une valeur différente.</span><span class="sxs-lookup"><span data-stu-id="4cffc-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="4cffc-206">Le code doit être étendu pour accepter cela également.</span><span class="sxs-lookup"><span data-stu-id="4cffc-206">The code must be extended to accept this as well.</span></span>

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

<span data-ttu-id="4cffc-207">Dans la `Startup.ConfigureServices` méthode, la `AddOpenIdConnect` méthode est utilisée comme schéma de stimulation par défaut.</span><span class="sxs-lookup"><span data-stu-id="4cffc-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="4cffc-208">Le gestionnaire d’autorisations, qui est utilisé pour vérifier la `amr` revendication, est ajouté à l’inversion du conteneur de contrôle.</span><span class="sxs-lookup"><span data-stu-id="4cffc-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="4cffc-209">Une stratégie est ensuite créée, ce qui ajoute la `RequireMfa` spécification.</span><span class="sxs-lookup"><span data-stu-id="4cffc-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

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

<span data-ttu-id="4cffc-210">Cette stratégie est ensuite utilisée dans la Razor page, selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="4cffc-210">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="4cffc-211">La stratégie peut également être ajoutée globalement pour l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="4cffc-211">The policy could be added globally for the entire app as well.</span></span>

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

<span data-ttu-id="4cffc-212">Si l’utilisateur s’authentifie sans MFA, la `amr` revendication aura probablement une `pwd` valeur.</span><span class="sxs-lookup"><span data-stu-id="4cffc-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="4cffc-213">La demande n’est pas autorisée à accéder à la page.</span><span class="sxs-lookup"><span data-stu-id="4cffc-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="4cffc-214">À l’aide des valeurs par défaut, l’utilisateur est redirigé vers la page *compte/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="4cffc-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="4cffc-215">Ce comportement peut être modifié ou vous pouvez implémenter votre propre logique personnalisée ici.</span><span class="sxs-lookup"><span data-stu-id="4cffc-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="4cffc-216">Dans cet exemple, un lien est ajouté afin que l’utilisateur valide puisse configurer MFA pour son compte.</span><span class="sxs-lookup"><span data-stu-id="4cffc-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

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

<span data-ttu-id="4cffc-217">Désormais, seuls les utilisateurs qui s’authentifient avec MFA peuvent accéder à la page ou au site Web.</span><span class="sxs-lookup"><span data-stu-id="4cffc-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="4cffc-218">Si différents types d’authentification multifacteur sont utilisés ou si 2FA est OK, la `amr` revendication aura des valeurs différentes et doit être traitée correctement.</span><span class="sxs-lookup"><span data-stu-id="4cffc-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="4cffc-219">Différents serveurs OpenID Connect retournent également des valeurs différentes pour cette revendication et peuvent ne pas suivre la spécification des valeurs de référence de la [méthode d’authentification](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="4cffc-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="4cffc-220">Lors de la connexion sans authentification multifacteur (par exemple, à l’aide d’un mot de passe uniquement) :</span><span class="sxs-lookup"><span data-stu-id="4cffc-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="4cffc-221">`amr`A la `pwd` valeur :</span><span class="sxs-lookup"><span data-stu-id="4cffc-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="4cffc-223">Accès refusé :</span><span class="sxs-lookup"><span data-stu-id="4cffc-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="4cffc-225">Vous pouvez également ouvrir une session avec un mot de passe à usage unique avec Identity :</span><span class="sxs-lookup"><span data-stu-id="4cffc-225">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="4cffc-227">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4cffc-227">Additional resources</span></span>

* [<span data-ttu-id="4cffc-228">Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4cffc-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="4cffc-229">Options d’authentification sans mot de passe pour Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="4cffc-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="4cffc-230">FIDO2 bibliothèque .NET pour l’attestation et l’assertion FIDO2/webauthn à l’aide de .NET</span><span class="sxs-lookup"><span data-stu-id="4cffc-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="4cffc-231">Authentification Isard</span><span class="sxs-lookup"><span data-stu-id="4cffc-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
