---
title: 'Utiliser :::no-loc(cookie)::: l’authentification sans :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: 'Découvrez comment utiliser :::no-loc(cookie)::: l’authentification sans :::no-loc(ASP.NET Core Identity)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/:::no-loc(cookie):::'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061351"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="926ed-103">Utiliser :::no-loc(cookie)::: l’authentification sans :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="926ed-103">Use :::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="926ed-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="926ed-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="926ed-105">:::no-loc(ASP.NET Core Identity)::: est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="926ed-105">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="926ed-106">Toutefois, :::no-loc(cookie)::: il n’est pas possible d’utiliser un fournisseur d’authentification basé sur :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="926ed-106">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="926ed-107">Pour plus d'informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="926ed-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="926ed-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="926ed-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="926ed-109">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="926ed-110">Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="926ed-111">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="926ed-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="926ed-112">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="926ed-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="926ed-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="926ed-113">Configuration</span></span>

<span data-ttu-id="926ed-114">Dans la `Startup.ConfigureServices` méthode, créez les services d’intergiciel (middleware) d’authentification avec les <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="926ed-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="926ed-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-115"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="926ed-116">`AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d' :::no-loc(cookie)::: authentification et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="926ed-116">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="926ed-117">L’affectation de la `AuthenticationScheme` valeur à [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) fournit la valeur « :::no-loc(Cookie)::: s » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="926ed-117">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="926ed-118">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="926ed-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="926ed-119">Le schéma d’authentification de l’application est différent du schéma d’authentification de l’application :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="926ed-119">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="926ed-120">Lorsqu’un :::no-loc(cookie)::: schéma d’authentification n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , il utilise `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (« :::no-loc(Cookie)::: s »).</span><span class="sxs-lookup"><span data-stu-id="926ed-120">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="926ed-121">La :::no-loc(cookie)::: propriété de l’authentification <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> a la valeur `true` par défaut.</span><span class="sxs-lookup"><span data-stu-id="926ed-121">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="926ed-122">:::no-loc(cookie):::Les s d’authentification sont autorisées lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="926ed-122">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="926ed-123">Pour plus d'informations, consultez <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="926ed-123">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="926ed-124">Dans `Startup.Configure` , appelez `UseAuthentication` et `UseAuthorization` pour définir la `HttpContext.User` propriété et exécuter l’intergiciel (middleware) des autorisations pour les demandes.</span><span class="sxs-lookup"><span data-stu-id="926ed-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="926ed-125">Appelez les `UseAuthentication` `UseAuthorization` méthodes et avant d’appeler `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="926ed-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="926ed-126">La <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="926ed-126">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="926ed-127">Définissez `:::no-loc(Cookie):::AuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="926ed-127">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="926ed-128">:::no-loc(Cookie)::: Intergiciel de stratégie</span><span class="sxs-lookup"><span data-stu-id="926ed-128">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="926ed-129">L' [ :::no-loc(Cookie)::: intergiciel (middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) ) de stratégie active les :::no-loc(cookie)::: fonctionnalités de stratégie.</span><span class="sxs-lookup"><span data-stu-id="926ed-129">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="926ed-130">L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="926ed-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="926ed-131">À utiliser <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> fourni à l’intergiciel (middleware) de :::no-loc(Cookie)::: stratégie pour contrôler les caractéristiques globales du :::no-loc(cookie)::: traitement et raccorder des :::no-loc(cookie)::: gestionnaires de traitement lorsque :::no-loc(cookie)::: des s sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="926ed-131">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="926ed-132">La valeur par défaut <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="926ed-132">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="926ed-133">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="926ed-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="926ed-134">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de :::no-loc(cookie)::: sécurité pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="926ed-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="926ed-135">Le :::no-loc(Cookie)::: paramètre d’intergiciel de stratégie pour `MinimumSameSitePolicy` peut affecter la valeur de `:::no-loc(Cookie):::.SameSite` dans `:::no-loc(Cookie):::AuthenticationOptions` les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="926ed-135">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="926ed-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="926ed-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="926ed-137">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="926ed-137">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="926ed-138">Résultante :::no-loc(Cookie)::: . Paramètre SameSite</span><span class="sxs-lookup"><span data-stu-id="926ed-138">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="926ed-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-139">SameSiteMode.None</span></span>     | <span data-ttu-id="926ed-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-140">SameSiteMode.None</span></span><br><span data-ttu-id="926ed-141">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-142">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="926ed-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-143">SameSiteMode.None</span></span><br><span data-ttu-id="926ed-144">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-145">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="926ed-146">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="926ed-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-147">SameSiteMode.None</span></span><br><span data-ttu-id="926ed-148">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-149">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="926ed-150">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-151">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-152">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="926ed-153">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="926ed-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-154">SameSiteMode.None</span></span><br><span data-ttu-id="926ed-155">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-156">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="926ed-157">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="926ed-158">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="926ed-159">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="926ed-160">Créer une authentification :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="926ed-160">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="926ed-161">Pour créer un :::no-loc(cookie)::: contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="926ed-161">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="926ed-162">Les informations utilisateur sont sérialisées et stockées dans le :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="926ed-162">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="926ed-163">Créez un <xref:System.Security.Claims.Claims:::no-loc(Identity):::> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="926ed-163">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="926ed-164">`SignInAsync` crée un chiffré :::no-loc(cookie)::: et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="926ed-164">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="926ed-165">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="926ed-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="926ed-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> est utilisé uniquement sur quelques chemins spécifiques par défaut, par exemple, le chemin d’accès de connexion et les chemins d’accès de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="926ed-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="926ed-167">Pour plus d’informations, consultez la [ :::no-loc(Cookie)::: source AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="926ed-167">For more information see the [:::no-loc(Cookie):::AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/:::no-loc(Cookie):::s/src/:::no-loc(Cookie):::AuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="926ed-168">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="926ed-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="926ed-169">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="926ed-170">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="926ed-170">Sign out</span></span>

<span data-ttu-id="926ed-171">Pour déconnecter l’utilisateur actuel et supprimer son :::no-loc(cookie)::: , appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="926ed-171">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/3.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="926ed-172">Si `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (ou « :::no-loc(Cookie)::: s ») n’est pas utilisé comme modèle (par exemple, « Contoso :::no-loc(Cookie)::: »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="926ed-172">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="926ed-173">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="926ed-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="926ed-174">Lorsque le navigateur se ferme, il supprime automatiquement les :::no-loc(cookie)::: s basés sur une session (s non persistantes :::no-loc(cookie)::: ), mais aucun :::no-loc(cookie)::: s n’est effacé lorsqu’un onglet individuel est fermé.</span><span class="sxs-lookup"><span data-stu-id="926ed-174">When the browser closes it automatically deletes session based :::no-loc(cookie):::s (non-persistent :::no-loc(cookie):::s), but no :::no-loc(cookie):::s are cleared when an individual tab is closed.</span></span> <span data-ttu-id="926ed-175">Le serveur n’est pas averti des événements de fermeture de tabulation ou de navigateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="926ed-176">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="926ed-176">React to back-end changes</span></span>

<span data-ttu-id="926ed-177">Une fois :::no-loc(cookie)::: créé, :::no-loc(cookie)::: est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="926ed-177">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="926ed-178">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="926ed-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="926ed-179">Le :::no-loc(cookie)::: système d’authentification de l’application continue à traiter les demandes en fonction de l’authentification :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="926ed-179">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="926ed-180">L’utilisateur reste connecté à l’application tant que l’authentification :::no-loc(cookie)::: est valide.</span><span class="sxs-lookup"><span data-stu-id="926ed-180">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="926ed-181">L' <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l' :::no-loc(cookie)::: identité.</span><span class="sxs-lookup"><span data-stu-id="926ed-181">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="926ed-182">La validation de :::no-loc(cookie)::: à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-182">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="926ed-183">Une approche de :::no-loc(cookie)::: la validation est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-183">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="926ed-184">Si la base de données n’a pas été modifiée depuis la publication de l’utilisateur :::no-loc(cookie)::: , il n’est pas nécessaire de réauthentifier l’utilisateur si son :::no-loc(cookie)::: est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="926ed-184">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="926ed-185">Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur.</span><span class="sxs-lookup"><span data-stu-id="926ed-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="926ed-186">Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="926ed-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="926ed-187">Afin d’invalider un :::no-loc(cookie)::: lorsque la base de données change en fonction de la `LastChanged` valeur, créez le :::no-loc(cookie)::: avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="926ed-187">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="926ed-188">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="926ed-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="926ed-189">Voici un exemple d’implémentation de `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="926ed-189">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="926ed-190">Inscrivez l’instance événements lors :::no-loc(cookie)::: de l’inscription du service dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="926ed-190">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="926ed-191">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `Custom:::no-loc(Cookie):::AuthenticationEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="926ed-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="926ed-192">Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="926ed-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="926ed-193">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .</span><span class="sxs-lookup"><span data-stu-id="926ed-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="926ed-194">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="926ed-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="926ed-195">La validation des :::no-loc(cookie)::: s d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-195">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="926ed-196">Persistant :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="926ed-196">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="926ed-197">Vous souhaiterez peut-être :::no-loc(cookie)::: conserver le à travers les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-197">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="926ed-198">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="926ed-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="926ed-199">L’extrait de code suivant crée une identité et correspondant :::no-loc(cookie)::: qui subsiste dans les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-199">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="926ed-200">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="926ed-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="926ed-201">Si le :::no-loc(cookie)::: expire pendant que le navigateur est fermé, le navigateur efface le :::no-loc(cookie)::: lorsqu’il est redémarré.</span><span class="sxs-lookup"><span data-stu-id="926ed-201">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="926ed-202">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="926ed-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="926ed-203">:::no-loc(cookie):::Expiration absolue</span><span class="sxs-lookup"><span data-stu-id="926ed-203">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="926ed-204">Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="926ed-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="926ed-205">Pour créer un persistant :::no-loc(cookie)::: , `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="926ed-205">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="926ed-206">Dans le cas contraire, le :::no-loc(cookie)::: est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="926ed-206">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="926ed-207">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> , si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="926ed-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="926ed-208">L’extrait de code suivant crée une identité :::no-loc(cookie)::: qui correspond à 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="926ed-208">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="926ed-209">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="926ed-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="926ed-210">:::no-loc(ASP.NET Core Identity)::: est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="926ed-210">:::no-loc(ASP.NET Core Identity)::: is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="926ed-211">Toutefois, :::no-loc(cookie)::: il n’est pas possible d’utiliser un fournisseur d’authentification basé sur :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="926ed-211">However, a :::no-loc(cookie):::-based authentication provider without :::no-loc(ASP.NET Core Identity)::: can be used.</span></span> <span data-ttu-id="926ed-212">Pour plus d'informations, consultez <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="926ed-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="926ed-213">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="926ed-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/:::no-loc(cookie):::/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="926ed-214">À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="926ed-215">Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="926ed-216">L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="926ed-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="926ed-217">Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.</span><span class="sxs-lookup"><span data-stu-id="926ed-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="926ed-218">Configuration</span><span class="sxs-lookup"><span data-stu-id="926ed-218">Configuration</span></span>

<span data-ttu-id="926ed-219">Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour [Microsoft. AspNetCore. Authentication. :::no-loc(Cookie)::: package s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) .</span><span class="sxs-lookup"><span data-stu-id="926ed-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s/) package.</span></span>

<span data-ttu-id="926ed-220">Dans la `Startup.ConfigureServices` méthode, créez le service d’intergiciel d’authentification avec <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> les <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> méthodes et :</span><span class="sxs-lookup"><span data-stu-id="926ed-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> methods:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet1)]

<span data-ttu-id="926ed-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-221"><xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="926ed-222">`AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d' :::no-loc(cookie)::: authentification et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="926ed-222">`AuthenticationScheme` is useful when there are multiple instances of :::no-loc(cookie)::: authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="926ed-223">L’affectation de la `AuthenticationScheme` valeur à [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) fournit la valeur « :::no-loc(Cookie)::: s » pour le schéma.</span><span class="sxs-lookup"><span data-stu-id="926ed-223">Setting the `AuthenticationScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) provides a value of ":::no-loc(Cookie):::s" for the scheme.</span></span> <span data-ttu-id="926ed-224">Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.</span><span class="sxs-lookup"><span data-stu-id="926ed-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="926ed-225">Le schéma d’authentification de l’application est différent du schéma d’authentification de l’application :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="926ed-225">The app's authentication scheme is different from the app's :::no-loc(cookie)::: authentication scheme.</span></span> <span data-ttu-id="926ed-226">Lorsqu’un :::no-loc(cookie)::: schéma d’authentification n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> , il utilise `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (« :::no-loc(Cookie)::: s »).</span><span class="sxs-lookup"><span data-stu-id="926ed-226">When a :::no-loc(cookie)::: authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, it uses `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="926ed-227">La :::no-loc(cookie)::: propriété de l’authentification <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> a la valeur `true` par défaut.</span><span class="sxs-lookup"><span data-stu-id="926ed-227">The authentication :::no-loc(cookie):::'s <xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="926ed-228">:::no-loc(cookie):::Les s d’authentification sont autorisées lorsqu’un visiteur du site n’a pas consenti à la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="926ed-228">Authentication :::no-loc(cookie):::s are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="926ed-229">Pour plus d'informations, consultez <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span><span class="sxs-lookup"><span data-stu-id="926ed-229">For more information, see <xref:security/gdpr#essential-:::no-loc(cookie):::s>.</span></span>

<span data-ttu-id="926ed-230">Dans la `Startup.Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification qui définit la `HttpContext.User` propriété.</span><span class="sxs-lookup"><span data-stu-id="926ed-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="926ed-231">Appelez la `UseAuthentication` méthode avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="926ed-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Startup.cs?name=snippet2)]

<span data-ttu-id="926ed-232">La <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="926ed-232">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="926ed-233">Définissez `:::no-loc(Cookie):::AuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="926ed-233">Set `:::no-loc(Cookie):::AuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="926ed-234">:::no-loc(Cookie)::: Intergiciel de stratégie</span><span class="sxs-lookup"><span data-stu-id="926ed-234">:::no-loc(Cookie)::: Policy Middleware</span></span>

<span data-ttu-id="926ed-235">L' [ :::no-loc(Cookie)::: intergiciel (middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) ) de stratégie active les :::no-loc(cookie)::: fonctionnalités de stratégie.</span><span class="sxs-lookup"><span data-stu-id="926ed-235">[:::no-loc(Cookie)::: Policy Middleware](xref:Microsoft.AspNetCore.:::no-loc(Cookie):::Policy.:::no-loc(Cookie):::PolicyMiddleware) enables :::no-loc(cookie)::: policy capabilities.</span></span> <span data-ttu-id="926ed-236">L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="926ed-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.Use:::no-loc(Cookie):::Policy(:::no-loc(cookie):::PolicyOptions);
```

<span data-ttu-id="926ed-237">À utiliser <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> fourni à l’intergiciel (middleware) de :::no-loc(Cookie)::: stratégie pour contrôler les caractéristiques globales du :::no-loc(cookie)::: traitement et raccorder des :::no-loc(cookie)::: gestionnaires de traitement lorsque :::no-loc(cookie)::: des s sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="926ed-237">Use <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions> provided to the :::no-loc(Cookie)::: Policy Middleware to control global characteristics of :::no-loc(cookie)::: processing and hook into :::no-loc(cookie)::: processing handlers when :::no-loc(cookie):::s are appended or deleted.</span></span>

<span data-ttu-id="926ed-238">La valeur par défaut <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2.</span><span class="sxs-lookup"><span data-stu-id="926ed-238">The default <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="926ed-239">Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="926ed-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="926ed-240">Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de :::no-loc(cookie)::: sécurité pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="926ed-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of :::no-loc(cookie)::: security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var :::no-loc(cookie):::PolicyOptions = new :::no-loc(Cookie):::PolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="926ed-241">Le :::no-loc(Cookie)::: paramètre d’intergiciel de stratégie pour `MinimumSameSitePolicy` peut affecter la valeur de `:::no-loc(Cookie):::.SameSite` dans `:::no-loc(Cookie):::AuthenticationOptions` les paramètres en fonction du tableau ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="926ed-241">The :::no-loc(Cookie)::: Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `:::no-loc(Cookie):::.SameSite` in `:::no-loc(Cookie):::AuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="926ed-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="926ed-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="926ed-243">:::no-loc(Cookie):::. SameSite</span><span class="sxs-lookup"><span data-stu-id="926ed-243">:::no-loc(Cookie):::.SameSite</span></span> | <span data-ttu-id="926ed-244">Résultante :::no-loc(Cookie)::: . Paramètre SameSite</span><span class="sxs-lookup"><span data-stu-id="926ed-244">Resultant :::no-loc(Cookie):::.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="926ed-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-245">SameSiteMode.None</span></span>     | <span data-ttu-id="926ed-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-246">SameSiteMode.None</span></span><br><span data-ttu-id="926ed-247">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-248">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="926ed-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-249">SameSiteMode.None</span></span><br><span data-ttu-id="926ed-250">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-251">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="926ed-252">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="926ed-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-253">SameSiteMode.None</span></span><br><span data-ttu-id="926ed-254">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-255">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="926ed-256">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-257">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-258">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="926ed-259">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="926ed-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="926ed-260">SameSiteMode.None</span></span><br><span data-ttu-id="926ed-261">SameSiteMode. Lax</span><span class="sxs-lookup"><span data-stu-id="926ed-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="926ed-262">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="926ed-263">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="926ed-264">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="926ed-265">SameSiteMode. strict</span><span class="sxs-lookup"><span data-stu-id="926ed-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="926ed-266">Créer une authentification :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="926ed-266">Create an authentication :::no-loc(cookie):::</span></span>

<span data-ttu-id="926ed-267">Pour créer un :::no-loc(cookie)::: contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="926ed-267">To create a :::no-loc(cookie)::: holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="926ed-268">Les informations utilisateur sont sérialisées et stockées dans le :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="926ed-268">The user information is serialized and stored in the :::no-loc(cookie):::.</span></span> 

<span data-ttu-id="926ed-269">Créez un <xref:System.Security.Claims.Claims:::no-loc(Identity):::> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="926ed-269">Create a <xref:System.Security.Claims.Claims:::no-loc(Identity):::> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="926ed-270">`SignInAsync` crée un chiffré :::no-loc(cookie)::: et l’ajoute à la réponse actuelle.</span><span class="sxs-lookup"><span data-stu-id="926ed-270">`SignInAsync` creates an encrypted :::no-loc(cookie)::: and adds it to the current response.</span></span> <span data-ttu-id="926ed-271">Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="926ed-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="926ed-272">Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="926ed-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="926ed-273">Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="926ed-274">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="926ed-274">Sign out</span></span>

<span data-ttu-id="926ed-275">Pour déconnecter l’utilisateur actuel et supprimer son :::no-loc(cookie)::: , appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="926ed-275">To sign out the current user and delete their :::no-loc(cookie):::, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](:::no-loc(cookie):::/samples/2.x/:::no-loc(Cookie):::Sample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="926ed-276">Si `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (ou « :::no-loc(Cookie)::: s ») n’est pas utilisé comme modèle (par exemple, « Contoso :::no-loc(Cookie)::: »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification.</span><span class="sxs-lookup"><span data-stu-id="926ed-276">If `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (or ":::no-loc(Cookie):::s") isn't used as the scheme (for example, "Contoso:::no-loc(Cookie):::"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="926ed-277">Dans le cas contraire, le schéma par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="926ed-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="926ed-278">Réagir aux modifications principales</span><span class="sxs-lookup"><span data-stu-id="926ed-278">React to back-end changes</span></span>

<span data-ttu-id="926ed-279">Une fois :::no-loc(cookie)::: créé, :::no-loc(cookie)::: est la seule source d’identité.</span><span class="sxs-lookup"><span data-stu-id="926ed-279">Once a :::no-loc(cookie)::: is created, the :::no-loc(cookie)::: is the single source of identity.</span></span> <span data-ttu-id="926ed-280">Si un compte d’utilisateur est désactivé dans les systèmes principaux :</span><span class="sxs-lookup"><span data-stu-id="926ed-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="926ed-281">Le :::no-loc(cookie)::: système d’authentification de l’application continue à traiter les demandes en fonction de l’authentification :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="926ed-281">The app's :::no-loc(cookie)::: authentication system continues to process requests based on the authentication :::no-loc(cookie):::.</span></span>
* <span data-ttu-id="926ed-282">L’utilisateur reste connecté à l’application tant que l’authentification :::no-loc(cookie)::: est valide.</span><span class="sxs-lookup"><span data-stu-id="926ed-282">The user remains signed into the app as long as the authentication :::no-loc(cookie)::: is valid.</span></span>

<span data-ttu-id="926ed-283">L' <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l' :::no-loc(cookie)::: identité.</span><span class="sxs-lookup"><span data-stu-id="926ed-283">The <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the :::no-loc(cookie)::: identity.</span></span> <span data-ttu-id="926ed-284">La validation de :::no-loc(cookie)::: à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-284">Validating the :::no-loc(cookie)::: on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="926ed-285">Une approche de :::no-loc(cookie)::: la validation est basée sur le suivi de la modification de la base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-285">One approach to :::no-loc(cookie)::: validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="926ed-286">Si la base de données n’a pas été modifiée depuis la publication de l’utilisateur :::no-loc(cookie)::: , il n’est pas nécessaire de réauthentifier l’utilisateur si son :::no-loc(cookie)::: est toujours valide.</span><span class="sxs-lookup"><span data-stu-id="926ed-286">If the database hasn't been changed since the user's :::no-loc(cookie)::: was issued, there's no need to re-authenticate the user if their :::no-loc(cookie)::: is still valid.</span></span> <span data-ttu-id="926ed-287">Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur.</span><span class="sxs-lookup"><span data-stu-id="926ed-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="926ed-288">Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.</span><span class="sxs-lookup"><span data-stu-id="926ed-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="926ed-289">Afin d’invalider un :::no-loc(cookie)::: lorsque la base de données change en fonction de la `LastChanged` valeur, créez le :::no-loc(cookie)::: avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :</span><span class="sxs-lookup"><span data-stu-id="926ed-289">In order to invalidate a :::no-loc(cookie)::: when the database changes based on the `LastChanged` value, create the :::no-loc(cookie)::: with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claims:::no-loc(Identity)::: = new Claims:::no-loc(Identity):::(
    claims, 
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claims:::no-loc(Identity):::));
```

<span data-ttu-id="926ed-290">Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="926ed-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext)
```

<span data-ttu-id="926ed-291">Voici un exemple d’implémentation de `:::no-loc(Cookie):::AuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="926ed-291">The following is an example implementation of `:::no-loc(Cookie):::AuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;

public class Custom:::no-loc(Cookie):::AuthenticationEvents : :::no-loc(Cookie):::AuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public Custom:::no-loc(Cookie):::AuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(:::no-loc(Cookie):::ValidatePrincipalContext context)
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
                :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="926ed-292">Inscrivez l’instance événements lors :::no-loc(cookie)::: de l’inscription du service dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="926ed-292">Register the events instance during :::no-loc(cookie)::: service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="926ed-293">Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `Custom:::no-loc(Cookie):::AuthenticationEvents` classe :</span><span class="sxs-lookup"><span data-stu-id="926ed-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `Custom:::no-loc(Cookie):::AuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.EventsType = typeof(Custom:::no-loc(Cookie):::AuthenticationEvents);
    });

services.AddScoped<Custom:::no-loc(Cookie):::AuthenticationEvents>();
```

<span data-ttu-id="926ed-294">Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité.</span><span class="sxs-lookup"><span data-stu-id="926ed-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="926ed-295">Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .</span><span class="sxs-lookup"><span data-stu-id="926ed-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="926ed-296">L’approche décrite ici est déclenchée à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="926ed-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="926ed-297">La validation des :::no-loc(cookie)::: s d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="926ed-297">Validating authentication :::no-loc(cookie):::s for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="926ed-298">Persistant :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="926ed-298">Persistent :::no-loc(cookie):::s</span></span>

<span data-ttu-id="926ed-299">Vous souhaiterez peut-être :::no-loc(cookie)::: conserver le à travers les sessions de navigateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-299">You may want the :::no-loc(cookie)::: to persist across browser sessions.</span></span> <span data-ttu-id="926ed-300">Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire.</span><span class="sxs-lookup"><span data-stu-id="926ed-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="926ed-301">L’extrait de code suivant crée une identité et correspondant :::no-loc(cookie)::: qui subsiste dans les fermetures du navigateur.</span><span class="sxs-lookup"><span data-stu-id="926ed-301">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that survives through browser closures.</span></span> <span data-ttu-id="926ed-302">Les paramètres d’expiration décalés précédemment configurés sont honorés.</span><span class="sxs-lookup"><span data-stu-id="926ed-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="926ed-303">Si le :::no-loc(cookie)::: expire pendant que le navigateur est fermé, le navigateur efface le :::no-loc(cookie)::: lorsqu’il est redémarré.</span><span class="sxs-lookup"><span data-stu-id="926ed-303">If the :::no-loc(cookie)::: expires while the browser is closed, the browser clears the :::no-loc(cookie)::: once it's restarted.</span></span>

<span data-ttu-id="926ed-304">Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="926ed-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="926ed-305">:::no-loc(cookie):::Expiration absolue</span><span class="sxs-lookup"><span data-stu-id="926ed-305">Absolute :::no-loc(cookie)::: expiration</span></span>

<span data-ttu-id="926ed-306">Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="926ed-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="926ed-307">Pour créer un persistant :::no-loc(cookie)::: , `IsPersistent` doit également être défini.</span><span class="sxs-lookup"><span data-stu-id="926ed-307">To create a persistent :::no-loc(cookie):::, `IsPersistent` must also be set.</span></span> <span data-ttu-id="926ed-308">Dans le cas contraire, le :::no-loc(cookie)::: est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="926ed-308">Otherwise, the :::no-loc(cookie)::: is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="926ed-309">Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions> , si elle est définie.</span><span class="sxs-lookup"><span data-stu-id="926ed-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions>, if set.</span></span>

<span data-ttu-id="926ed-310">L’extrait de code suivant crée une identité :::no-loc(cookie)::: qui correspond à 20 minutes.</span><span class="sxs-lookup"><span data-stu-id="926ed-310">The following code snippet creates an identity and corresponding :::no-loc(cookie)::: that lasts for 20 minutes.</span></span> <span data-ttu-id="926ed-311">Cela ignore tous les paramètres d’expiration décalés précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="926ed-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claims:::no-loc(Identity):::),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="926ed-312">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="926ed-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="926ed-313">Vérifications des rôles basés sur des stratégies</span><span class="sxs-lookup"><span data-stu-id="926ed-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
