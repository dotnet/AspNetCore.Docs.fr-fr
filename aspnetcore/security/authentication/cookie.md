---
title: Utiliser cookie l’authentification sans ASP.NET Core Identity
author: rick-anderson
description: Découvrez comment utiliser cookie l’authentification sans ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 5b1f1bb3de7126c401a81b89b99a45c7e45f8f8d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586291"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="9f161-103">Utiliser cookie l’authentification sans ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9f161-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="9f161-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9f161-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9f161-105">ASP.NET Core Identity est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="9f161-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="9f161-106">Toutefois, cookie il n’est pas possible d’utiliser un fournisseur d’authentification basé sur ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="9f161-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="9f161-107">Pour plus d’informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="9f161-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="9f161-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f161-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9f161-109">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="9f161-110">Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="9f161-111">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="9f161-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="9f161-112">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="9f161-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="9f161-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="9f161-113">Configuration</span></span>

<span data-ttu-id="9f161-114">Dans la `Startup.ConfigureServices` méthode, créez les services d’intergiciel (middleware) d’authentification avec les <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="9f161-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="9f161-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="9f161-116">`AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d' cookie authentification et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="9f161-116">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="9f161-117">L’affectation de la `AuthenticationScheme` valeur à [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « Cookie s » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="9f161-117">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="9f161-118">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="9f161-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="9f161-119">Le schéma d’authentification de l’application est différent du schéma d’authentification de l’application cookie .</span><span class="sxs-lookup"><span data-stu-id="9f161-119">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="9f161-120">Lorsqu’un cookie schéma d’authentification n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« Cookie s »).</span><span class="sxs-lookup"><span data-stu-id="9f161-120">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="9f161-121">La cookie propriété de l’authentification <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> a la valeur `true` par défaut.</span><span class="sxs-lookup"><span data-stu-id="9f161-121">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="9f161-122">cookieLes s d’authentification sont autorisées lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="9f161-122">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="9f161-123">Pour plus d’informations, consultez <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="9f161-123">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="9f161-124">Dans `Startup.Configure` , appelez `UseAuthentication` et `UseAuthorization` pour définir la `HttpContext.User` propriété et exécuter l’intergiciel (middleware) des autorisations pour les demandes.</span><span class="sxs-lookup"><span data-stu-id="9f161-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="9f161-125">Appelez les `UseAuthentication` `UseAuthorization` méthodes et avant d’appeler `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="9f161-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="9f161-126">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="9f161-126">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="9f161-127">Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="9f161-127">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="9f161-128">Cookie Intergiciel de stratégie</span><span class="sxs-lookup"><span data-stu-id="9f161-128">Cookie Policy Middleware</span></span>

<span data-ttu-id="9f161-129">L' [ Cookie intergiciel (middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ) de stratégie active les cookie fonctionnalités de stratégie.</span><span class="sxs-lookup"><span data-stu-id="9f161-129">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="9f161-130">L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="9f161-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="9f161-131">À utiliser <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de Cookie stratégie pour contrôler les caractéristiques globales du cookie traitement et raccorder des cookie gestionnaires de traitement lorsque cookie des s sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="9f161-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="9f161-132">La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="9f161-132">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="9f161-133">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="9f161-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="9f161-134">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de cookie sécurité pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="9f161-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="9f161-135">Le Cookie paramètre d’intergiciel de stratégie pour `MinimumSameSitePolicy` peut affecter la valeur de `Cookie.SameSite` dans `CookieAuthenticationOptions` les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f161-135">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="9f161-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="9f161-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="9f161-137">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="9f161-137">Cookie.SameSite</span></span> | <span data-ttu-id="9f161-138">Résultante Cookie . Paramètre SameSite</span><span class="sxs-lookup"><span data-stu-id="9f161-138">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="9f161-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-139">SameSiteMode.None</span></span>     | <span data-ttu-id="9f161-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-140">SameSiteMode.None</span></span><br><span data-ttu-id="9f161-141">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-142">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="9f161-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-143">SameSiteMode.None</span></span><br><span data-ttu-id="9f161-144">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-145">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="9f161-146">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="9f161-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-147">SameSiteMode.None</span></span><br><span data-ttu-id="9f161-148">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-149">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="9f161-150">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-151">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-152">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="9f161-153">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="9f161-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-154">SameSiteMode.None</span></span><br><span data-ttu-id="9f161-155">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-156">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="9f161-157">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="9f161-158">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="9f161-159">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="9f161-160">Créer une authentification cookie</span><span class="sxs-lookup"><span data-stu-id="9f161-160">Create an authentication cookie</span></span>

<span data-ttu-id="9f161-161">Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="9f161-161">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="9f161-162">Les informations utilisateur sont sérialisées et stockées dans le cookie .</span><span class="sxs-lookup"><span data-stu-id="9f161-162">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="9f161-163">Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="9f161-163">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="9f161-164">`SignInAsync` crée un chiffré cookie et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="9f161-164">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="9f161-165">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="9f161-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="9f161-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> est utilisé uniquement sur quelques chemins spécifiques par défaut, par exemple, le chemin d’accès de connexion et les chemins d’accès de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="9f161-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="9f161-167">Pour plus d’informations, consultez la [ Cookie source AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="9f161-167">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="9f161-168">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="9f161-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="9f161-169">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="9f161-170">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="9f161-170">Sign out</span></span>

<span data-ttu-id="9f161-171">Pour déconnecter l’utilisateur actuel et supprimer son cookie , appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="9f161-171">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="9f161-172">Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « Cookie s ») n’est pas utilisé comme modèle (par exemple, « Contoso Cookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="9f161-172">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="9f161-173">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="9f161-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="9f161-174">Lorsque le navigateur se ferme, il supprime automatiquement les cookie s basés sur une session (s non persistantes cookie ), mais aucun cookie s n’est effacé lorsqu’un onglet individuel est fermé.</span><span class="sxs-lookup"><span data-stu-id="9f161-174">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="9f161-175">Le serveur n’est pas averti des événements de fermeture de tabulation ou de navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="9f161-176">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="9f161-176">React to back-end changes</span></span>

<span data-ttu-id="9f161-177">Une fois cookie créé, cookie est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="9f161-177">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="9f161-178">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="9f161-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="9f161-179">Le cookie système d’authentification de l’application continue à traiter les demandes en fonction de l’authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="9f161-179">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="9f161-180">L’utilisateur reste connecté à l’application tant que l’authentification cookie est valide.</span><span class="sxs-lookup"><span data-stu-id="9f161-180">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="9f161-181">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l' cookie identité.</span><span class="sxs-lookup"><span data-stu-id="9f161-181">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="9f161-182">La validation de cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-182">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="9f161-183">Une approche de cookie la validation est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-183">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="9f161-184">Si la base de données n’a pas été modifiée depuis la publication de l’utilisateur cookie , il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="9f161-184">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="9f161-185">Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur.</span><span class="sxs-lookup"><span data-stu-id="9f161-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="9f161-186">Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="9f161-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="9f161-187">Afin d’invalider un cookie lorsque la base de données change en fonction de la `LastChanged` valeur, créez le cookie avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="9f161-187">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="9f161-188">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="9f161-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="9f161-189">Voici un exemple d’implémentation de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="9f161-189">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="9f161-190">Inscrivez l’instance événements lors cookie de l’inscription du service dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="9f161-190">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9f161-191">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `CustomCookieAuthenticationEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="9f161-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="9f161-192">Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="9f161-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="9f161-193">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .</span><span class="sxs-lookup"><span data-stu-id="9f161-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="9f161-194">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="9f161-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="9f161-195">La validation des cookie s d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-195">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="9f161-196">Persistant cookie s</span><span class="sxs-lookup"><span data-stu-id="9f161-196">Persistent cookies</span></span>

<span data-ttu-id="9f161-197">Vous souhaiterez peut-être cookie conserver le à travers les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-197">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="9f161-198">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="9f161-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="9f161-199">L’extrait de code suivant crée une identité et correspondant cookie qui subsiste dans les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-199">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="9f161-200">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="9f161-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="9f161-201">Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie lorsqu’il est redémarré.</span><span class="sxs-lookup"><span data-stu-id="9f161-201">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="9f161-202">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="9f161-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="9f161-203">cookieExpiration absolue</span><span class="sxs-lookup"><span data-stu-id="9f161-203">Absolute cookie expiration</span></span>

<span data-ttu-id="9f161-204">Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="9f161-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="9f161-205">Pour créer un persistant cookie , `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="9f161-205">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="9f161-206">Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="9f161-206">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="9f161-207">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="9f161-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="9f161-208">L’extrait de code suivant crée une identité cookie qui correspond à 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="9f161-208">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="9f161-209">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="9f161-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f161-210">ASP.NET Core Identity est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="9f161-210">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="9f161-211">Toutefois, cookie il n’est pas possible d’utiliser un fournisseur d’authentification basé sur ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="9f161-211">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="9f161-212">Pour plus d’informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="9f161-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="9f161-213">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f161-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9f161-214">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="9f161-215">Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="9f161-216">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="9f161-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="9f161-217">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="9f161-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="9f161-218">Configuration</span><span class="sxs-lookup"><span data-stu-id="9f161-218">Configuration</span></span>

<span data-ttu-id="9f161-219">Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour [Microsoft. AspNetCore. Authentication. Cookie package s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="9f161-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="9f161-220">Dans la `Startup.ConfigureServices` méthode, créez le service d’intergiciel d’authentification avec <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> les <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="9f161-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="9f161-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="9f161-222">`AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d' cookie authentification et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="9f161-222">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="9f161-223">L’affectation de la `AuthenticationScheme` valeur à [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « Cookie s » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="9f161-223">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="9f161-224">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="9f161-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="9f161-225">Le schéma d’authentification de l’application est différent du schéma d’authentification de l’application cookie .</span><span class="sxs-lookup"><span data-stu-id="9f161-225">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="9f161-226">Lorsqu’un cookie schéma d’authentification n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« Cookie s »).</span><span class="sxs-lookup"><span data-stu-id="9f161-226">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="9f161-227">La cookie propriété de l’authentification <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> a la valeur `true` par défaut.</span><span class="sxs-lookup"><span data-stu-id="9f161-227">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="9f161-228">cookieLes s d’authentification sont autorisées lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="9f161-228">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="9f161-229">Pour plus d’informations, consultez <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="9f161-229">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="9f161-230">Dans la `Startup.Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification qui définit la `HttpContext.User` propriété.</span><span class="sxs-lookup"><span data-stu-id="9f161-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="9f161-231">Appelez la `UseAuthentication` méthode avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="9f161-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="9f161-232">La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="9f161-232">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="9f161-233">Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="9f161-233">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="9f161-234">Cookie Intergiciel de stratégie</span><span class="sxs-lookup"><span data-stu-id="9f161-234">Cookie Policy Middleware</span></span>

<span data-ttu-id="9f161-235">L' [ Cookie intergiciel (middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ) de stratégie active les cookie fonctionnalités de stratégie.</span><span class="sxs-lookup"><span data-stu-id="9f161-235">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="9f161-236">L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="9f161-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="9f161-237">À utiliser <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de Cookie stratégie pour contrôler les caractéristiques globales du cookie traitement et raccorder des cookie gestionnaires de traitement lorsque cookie des s sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="9f161-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="9f161-238">La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="9f161-238">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="9f161-239">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="9f161-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="9f161-240">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de cookie sécurité pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="9f161-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="9f161-241">Le Cookie paramètre d’intergiciel de stratégie pour `MinimumSameSitePolicy` peut affecter la valeur de `Cookie.SameSite` dans `CookieAuthenticationOptions` les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f161-241">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="9f161-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="9f161-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="9f161-243">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="9f161-243">Cookie.SameSite</span></span> | <span data-ttu-id="9f161-244">Résultante Cookie . Paramètre SameSite</span><span class="sxs-lookup"><span data-stu-id="9f161-244">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="9f161-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-245">SameSiteMode.None</span></span>     | <span data-ttu-id="9f161-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-246">SameSiteMode.None</span></span><br><span data-ttu-id="9f161-247">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-248">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="9f161-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-249">SameSiteMode.None</span></span><br><span data-ttu-id="9f161-250">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-251">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="9f161-252">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="9f161-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-253">SameSiteMode.None</span></span><br><span data-ttu-id="9f161-254">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-255">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="9f161-256">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-257">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-258">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="9f161-259">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="9f161-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="9f161-260">SameSiteMode.None</span></span><br><span data-ttu-id="9f161-261">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="9f161-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="9f161-262">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="9f161-263">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="9f161-264">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="9f161-265">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="9f161-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="9f161-266">Créer une authentification cookie</span><span class="sxs-lookup"><span data-stu-id="9f161-266">Create an authentication cookie</span></span>

<span data-ttu-id="9f161-267">Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="9f161-267">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="9f161-268">Les informations utilisateur sont sérialisées et stockées dans le cookie .</span><span class="sxs-lookup"><span data-stu-id="9f161-268">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="9f161-269">Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="9f161-269">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="9f161-270">`SignInAsync` crée un chiffré cookie et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="9f161-270">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="9f161-271">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="9f161-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="9f161-272">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="9f161-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="9f161-273">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="9f161-274">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="9f161-274">Sign out</span></span>

<span data-ttu-id="9f161-275">Pour déconnecter l’utilisateur actuel et supprimer son cookie , appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="9f161-275">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="9f161-276">Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « Cookie s ») n’est pas utilisé comme modèle (par exemple, « Contoso Cookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="9f161-276">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="9f161-277">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="9f161-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="9f161-278">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="9f161-278">React to back-end changes</span></span>

<span data-ttu-id="9f161-279">Une fois cookie créé, cookie est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="9f161-279">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="9f161-280">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="9f161-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="9f161-281">Le cookie système d’authentification de l’application continue à traiter les demandes en fonction de l’authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="9f161-281">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="9f161-282">L’utilisateur reste connecté à l’application tant que l’authentification cookie est valide.</span><span class="sxs-lookup"><span data-stu-id="9f161-282">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="9f161-283">L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l' cookie identité.</span><span class="sxs-lookup"><span data-stu-id="9f161-283">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="9f161-284">La validation de cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-284">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="9f161-285">Une approche de cookie la validation est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-285">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="9f161-286">Si la base de données n’a pas été modifiée depuis la publication de l’utilisateur cookie , il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="9f161-286">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="9f161-287">Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur.</span><span class="sxs-lookup"><span data-stu-id="9f161-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="9f161-288">Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="9f161-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="9f161-289">Afin d’invalider un cookie lorsque la base de données change en fonction de la `LastChanged` valeur, créez le cookie avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="9f161-289">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="9f161-290">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="9f161-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="9f161-291">Voici un exemple d’implémentation de `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="9f161-291">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="9f161-292">Inscrivez l’instance événements lors cookie de l’inscription du service dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="9f161-292">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9f161-293">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `CustomCookieAuthenticationEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="9f161-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="9f161-294">Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="9f161-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="9f161-295">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .</span><span class="sxs-lookup"><span data-stu-id="9f161-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="9f161-296">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="9f161-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="9f161-297">La validation des cookie s d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="9f161-297">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="9f161-298">Persistant cookie s</span><span class="sxs-lookup"><span data-stu-id="9f161-298">Persistent cookies</span></span>

<span data-ttu-id="9f161-299">Vous souhaiterez peut-être cookie conserver le à travers les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-299">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="9f161-300">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="9f161-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="9f161-301">L’extrait de code suivant crée une identité et correspondant cookie qui subsiste dans les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f161-301">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="9f161-302">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="9f161-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="9f161-303">Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie lorsqu’il est redémarré.</span><span class="sxs-lookup"><span data-stu-id="9f161-303">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="9f161-304">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="9f161-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="9f161-305">cookieExpiration absolue</span><span class="sxs-lookup"><span data-stu-id="9f161-305">Absolute cookie expiration</span></span>

<span data-ttu-id="9f161-306">Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="9f161-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="9f161-307">Pour créer un persistant cookie , `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="9f161-307">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="9f161-308">Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="9f161-308">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="9f161-309">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="9f161-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="9f161-310">L’extrait de code suivant crée une identité cookie qui correspond à 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="9f161-310">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="9f161-311">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="9f161-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9f161-312">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f161-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="9f161-313">Vérifications des rôles basés sur des stratégies</span><span class="sxs-lookup"><span data-stu-id="9f161-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
