---
title: Conserver des revendications et des jetons supplémentaires à partir de fournisseurs externes dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer des revendications et des jetons supplémentaires à partir de fournisseurs externes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2021
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 513de6133455e26552b79de0f07cf135e8b36825
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605566"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="7fe5e-103">Conserver des revendications et des jetons supplémentaires à partir de fournisseurs externes dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7fe5e-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7fe5e-104">Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="7fe5e-105">Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="7fe5e-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7fe5e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7fe5e-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="7fe5e-107">Prerequisites</span></span>

<span data-ttu-id="7fe5e-108">Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="7fe5e-109">Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="7fe5e-110">Pour plus d’informations, consultez <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="7fe5e-111">L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="7fe5e-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="7fe5e-112">Définir l’ID client et la clé secrète client</span><span class="sxs-lookup"><span data-stu-id="7fe5e-112">Set the client ID and client secret</span></span>

<span data-ttu-id="7fe5e-113">Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="7fe5e-114">Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="7fe5e-115">Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="7fe5e-116">Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="7fe5e-117">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="7fe5e-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7fe5e-118">Authentification Google</span><span class="sxs-lookup"><span data-stu-id="7fe5e-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="7fe5e-119">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="7fe5e-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7fe5e-120">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="7fe5e-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7fe5e-121">Autres fournisseurs d’authentification</span><span class="sxs-lookup"><span data-stu-id="7fe5e-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="7fe5e-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="7fe5e-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="7fe5e-123">L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="7fe5e-124">Établir l’étendue de l’authentification</span><span class="sxs-lookup"><span data-stu-id="7fe5e-124">Establish the authentication scope</span></span>

<span data-ttu-id="7fe5e-125">Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant le <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="7fe5e-126">Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="7fe5e-127">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="7fe5e-127">Provider</span></span>  | <span data-ttu-id="7fe5e-128">Étendue</span><span class="sxs-lookup"><span data-stu-id="7fe5e-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="7fe5e-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="7fe5e-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="7fe5e-130">Google</span><span class="sxs-lookup"><span data-stu-id="7fe5e-130">Google</span></span>    | <span data-ttu-id="7fe5e-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="7fe5e-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="7fe5e-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="7fe5e-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="7fe5e-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="7fe5e-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="7fe5e-134">Dans l’exemple d’application, les `profile` `email` portées Google, et `openid` sont automatiquement ajoutées par le Framework quand <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> est appelé sur le <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="7fe5e-135">Si l’application requiert des étendues supplémentaires, ajoutez-les aux options.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="7fe5e-136">Dans l’exemple suivant, l' `https://www.googleapis.com/auth/user.birthday.read` étendue Google est ajoutée pour récupérer l’anniversaire d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="7fe5e-137">Mapper des clés de données utilisateur et créer des revendications</span><span class="sxs-lookup"><span data-stu-id="7fe5e-137">Map user data keys and create claims</span></span>

<span data-ttu-id="7fe5e-138">Dans les options du fournisseur, spécifiez un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="7fe5e-139">Pour plus d’informations sur les types de revendication, consultez <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="7fe5e-140">L’exemple d’application crée `urn:google:locale` des revendications de paramètres régionaux () et d’image ( `urn:google:picture` ) à partir des `locale` `picture` clés et dans les données utilisateur de Google :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="7fe5e-141">Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="7fe5e-142">Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker `ApplicationUser` des revendications pour les données utilisateur disponibles à partir du <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="7fe5e-143">Dans l’exemple d’application, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) établit les revendications de paramètres régionaux ( `urn:google:locale` ) et d’image ( `urn:google:picture` ) pour le connecté `ApplicationUser` , y compris une revendication pour <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="7fe5e-144">Par défaut, les revendications d’un utilisateur sont stockées dans l’authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="7fe5e-145">Si l’authentification cookie est trop importante, cela peut entraîner l’échec de l’application, car :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="7fe5e-146">Le navigateur détecte que l' cookie en-tête est trop long.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="7fe5e-147">La taille globale de la demande est trop importante.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="7fe5e-148">Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="7fe5e-149">Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="7fe5e-150">Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les Cookie intergiciels (middleware) d’authentification <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> pour stocker l’identité entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="7fe5e-151">Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="7fe5e-152">Enregistrer le jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="7fe5e-152">Save the access token</span></span>

<span data-ttu-id="7fe5e-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> définit si les jetons d’accès et d’actualisation doivent être stockés dans le <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> après une autorisation réussie.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="7fe5e-154">`SaveTokens` a la valeur `false` par défaut pour réduire la taille de l’authentification finale cookie .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="7fe5e-155">L’exemple d’application définit la valeur de `SaveTokens` sur `true` dans <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="7fe5e-156">Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe dans le `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="7fe5e-157">L’exemple d’application enregistre le jeton d’accès dans `OnPostConfirmationAsync` (nouvel enregistrement utilisateur) et `OnGetCallbackAsync` (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="7fe5e-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> <span data-ttu-id="7fe5e-158">Pour plus d’informations sur le passage de jetons aux Razor composants d’une Blazor Server application, consultez <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-158">For information on passing tokens to the Razor components of a Blazor Server app, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="7fe5e-159">Comment ajouter des jetons personnalisés supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7fe5e-159">How to add additional custom tokens</span></span>

<span data-ttu-id="7fe5e-160">Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre de `SaveTokens` , l’exemple d’application ajoute un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> avec le actuel <xref:System.DateTime> pour un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-160">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="7fe5e-161">Création et ajout de revendications</span><span class="sxs-lookup"><span data-stu-id="7fe5e-161">Creating and adding claims</span></span>

<span data-ttu-id="7fe5e-162">L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-162">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="7fe5e-163">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-163">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="7fe5e-164">Les utilisateurs peuvent définir des actions personnalisées en dérivant de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> et en implémentant la méthode abstraite <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-164">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="7fe5e-165">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-165">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="add-and-update-user-claims"></a><span data-ttu-id="7fe5e-166">Ajouter et mettre à jour des revendications utilisateur</span><span class="sxs-lookup"><span data-stu-id="7fe5e-166">Add and update user claims</span></span>

<span data-ttu-id="7fe5e-167">Les revendications sont copiées à partir de fournisseurs externes dans la base de données utilisateur lors de la première inscription, et non lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-167">Claims are copied from external providers to the user database on first registration, not on sign in.</span></span> <span data-ttu-id="7fe5e-168">Si des revendications supplémentaires sont activées dans une application après qu’un utilisateur s’est inscrit pour utiliser l’application, appelez [SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) sur un utilisateur pour forcer la génération d’une nouvelle authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-168">If additional claims are enabled in an app after a user registers to use the app, call [SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on a user to force the generation of a new authentication cookie.</span></span>

<span data-ttu-id="7fe5e-169">Dans l’environnement de développement qui utilise des comptes d’utilisateur de test, vous pouvez simplement supprimer et recréer le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-169">In the Development environment working with test user accounts, you can simply delete and recreate the user account.</span></span> <span data-ttu-id="7fe5e-170">Pour les systèmes de production, les nouvelles revendications ajoutées à l’application peuvent être remplies dans les comptes d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-170">For production systems, new claims added to the app can be backfilled into user accounts.</span></span> <span data-ttu-id="7fe5e-171">Après la génération de la [structure de la `ExternalLogin` page](xref:security/authentication/scaffold-identity) dans l’application à l’adresse `Areas/Pages/Identity/Account/Manage` , ajoutez le code suivant à `ExternalLoginModel` dans le `ExternalLogin.cshtml.cs` fichier.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-171">After [scaffolding the `ExternalLogin` page](xref:security/authentication/scaffold-identity) into the app at `Areas/Pages/Identity/Account/Manage`, add the following code to the `ExternalLoginModel` in the `ExternalLogin.cshtml.cs` file.</span></span>

<span data-ttu-id="7fe5e-172">Ajoutez un dictionnaire de revendications ajoutées.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-172">Add a dictionary of added claims.</span></span> <span data-ttu-id="7fe5e-173">Utilisez les clés de dictionnaire pour stocker les types de revendication et utilisez les valeurs pour contenir une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-173">Use the dictionary keys to hold the claim types, and use the values to hold a default value.</span></span> <span data-ttu-id="7fe5e-174">Ajoutez la ligne suivante au début de la classe.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-174">Add the following line to the top of the class.</span></span> <span data-ttu-id="7fe5e-175">L’exemple suivant suppose qu’une revendication est ajoutée pour l’image Google de l’utilisateur avec une image headshot générique comme valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-175">The following example assumes that one claim is added for the user's Google picture with a generic headshot image as the default value:</span></span>

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

<span data-ttu-id="7fe5e-176">Remplacez le code par défaut de la `OnGetCallbackAsync` méthode par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-176">Replace the default code of the `OnGetCallbackAsync` method with the following code.</span></span> <span data-ttu-id="7fe5e-177">Le code parcourt le dictionnaire de revendications.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-177">The code loops through the claims dictionary.</span></span> <span data-ttu-id="7fe5e-178">Les revendications sont ajoutées (remplies) ou mises à jour pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-178">Claims are added (backfilled) or updated for each user.</span></span> <span data-ttu-id="7fe5e-179">Lorsque des revendications sont ajoutées ou mises à jour, la connexion de l’utilisateur est actualisée à l’aide de <xref:Microsoft.AspNetCore.Identity.SignInManager%601> , en préservant les propriétés d’authentification existantes ( `AuthenticationProperties` ).</span><span class="sxs-lookup"><span data-stu-id="7fe5e-179">When claims are added or updated, the user sign-in is refreshed using the <xref:Microsoft.AspNetCore.Identity.SignInManager%601>, preserving the existing authentication properties (`AuthenticationProperties`).</span></span>

```csharp
public async Task<IActionResult> OnGetCallbackAsync(
    string returnUrl = null, string remoteError = null)
{
    returnUrl = returnUrl ?? Url.Content("~/");

    if (remoteError != null)
    {
        ErrorMessage = $"Error from external provider: {remoteError}";

        return RedirectToPage("./Login", new {ReturnUrl = returnUrl });
    }

    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        ErrorMessage = "Error loading external login information.";
        return RedirectToPage("./Login", new { ReturnUrl = returnUrl });
    }

    // Sign in the user with this external login provider if the user already has a 
    // login.
    var result = await _signInManager.ExternalLoginSignInAsync(info.LoginProvider, 
        info.ProviderKey, isPersistent: false, bypassTwoFactor : true);

    if (result.Succeeded)
    {
        _logger.LogInformation("{Name} logged in with {LoginProvider} provider.", 
            info.Principal.Identity.Name, info.LoginProvider);

        if (_claimsToSync.Count > 0)
        {
            var user = await _userManager.FindByLoginAsync(info.LoginProvider, 
                info.ProviderKey);
            var userClaims = await _userManager.GetClaimsAsync(user);
            bool refreshSignIn = false;

            foreach (var addedClaim in _claimsToSync)
            {
                var userClaim = userClaims
                    .FirstOrDefault(c => c.Type == addedClaim.Key);

                if (info.Principal.HasClaim(c => c.Type == addedClaim.Key))
                {
                    var externalClaim = info.Principal.FindFirst(addedClaim.Key);

                    if (userClaim == null)
                    {
                        await _userManager.AddClaimAsync(user, 
                            new Claim(addedClaim.Key, externalClaim.Value));
                        refreshSignIn = true;
                    }
                    else if (userClaim.Value != externalClaim.Value)
                    {
                        await _userManager
                            .ReplaceClaimAsync(user, userClaim, externalClaim);
                        refreshSignIn = true;
                    }
                }
                else if (userClaim == null)
                {
                    // Fill with a default value
                    await _userManager.AddClaimAsync(user, new Claim(addedClaim.Key, 
                        addedClaim.Value));
                    refreshSignIn = true;
                }
            }

            if (refreshSignIn)
            {
                await _signInManager.RefreshSignInAsync(user);
            }
        }

        return LocalRedirect(returnUrl);
    }

    if (result.IsLockedOut)
    {
        return RedirectToPage("./Lockout");
    }
    else
    {
        // If the user does not have an account, then ask the user to create an 
        // account.
        ReturnUrl = returnUrl;
        ProviderDisplayName = info.ProviderDisplayName;

        if (info.Principal.HasClaim(c => c.Type == ClaimTypes.Email))
        {
            Input = new InputModel
            {
                Email = info.Principal.FindFirstValue(ClaimTypes.Email)
            };
        }

        return Page();
    }
}
```

<span data-ttu-id="7fe5e-180">Une approche similaire est prise lorsque les revendications sont modifiées pendant qu’un utilisateur est connecté, mais qu’une étape de renvoi n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-180">A similar approach is taken when claims change while a user is signed in but a backfill step isn't required.</span></span> <span data-ttu-id="7fe5e-181">Pour mettre à jour les revendications d’un utilisateur, appelez la commande suivante sur l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-181">To update a user's claims, call the following on the user:</span></span>

* <span data-ttu-id="7fe5e-182">[Usermanager. ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) sur l’utilisateur pour les revendications stockées dans la base de données d’identité.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-182">[UserManager.ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) on the user for claims stored in the identity database.</span></span>
* <span data-ttu-id="7fe5e-183">[SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) sur l’utilisateur pour forcer la génération d’une nouvelle authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-183">[SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on the user to force the generation of a new authentication cookie.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="7fe5e-184">Suppression des revendications et des actions de revendication</span><span class="sxs-lookup"><span data-stu-id="7fe5e-184">Removal of claim actions and claims</span></span>

<span data-ttu-id="7fe5e-185">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour le donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la collection.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-185">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="7fe5e-186">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-186">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="7fe5e-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="7fe5e-188">Exemple de sortie d’application</span><span class="sxs-lookup"><span data-stu-id="7fe5e-188">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7fe5e-189">Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-189">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="7fe5e-190">Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-190">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="7fe5e-191">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7fe5e-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7fe5e-192">Prérequis</span><span class="sxs-lookup"><span data-stu-id="7fe5e-192">Prerequisites</span></span>

<span data-ttu-id="7fe5e-193">Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-193">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="7fe5e-194">Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-194">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="7fe5e-195">Pour plus d’informations, consultez <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-195">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="7fe5e-196">L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="7fe5e-196">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="7fe5e-197">Définir l’ID client et la clé secrète client</span><span class="sxs-lookup"><span data-stu-id="7fe5e-197">Set the client ID and client secret</span></span>

<span data-ttu-id="7fe5e-198">Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-198">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="7fe5e-199">Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-199">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="7fe5e-200">Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-200">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="7fe5e-201">Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-201">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="7fe5e-202">Authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="7fe5e-202">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7fe5e-203">Authentification Google</span><span class="sxs-lookup"><span data-stu-id="7fe5e-203">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="7fe5e-204">Authentification Microsoft</span><span class="sxs-lookup"><span data-stu-id="7fe5e-204">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7fe5e-205">Authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="7fe5e-205">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7fe5e-206">Autres fournisseurs d’authentification</span><span class="sxs-lookup"><span data-stu-id="7fe5e-206">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="7fe5e-207">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="7fe5e-207">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="7fe5e-208">L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-208">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="7fe5e-209">Établir l’étendue de l’authentification</span><span class="sxs-lookup"><span data-stu-id="7fe5e-209">Establish the authentication scope</span></span>

<span data-ttu-id="7fe5e-210">Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant le <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-210">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="7fe5e-211">Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-211">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="7fe5e-212">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="7fe5e-212">Provider</span></span>  | <span data-ttu-id="7fe5e-213">Étendue</span><span class="sxs-lookup"><span data-stu-id="7fe5e-213">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="7fe5e-214">Facebook</span><span class="sxs-lookup"><span data-stu-id="7fe5e-214">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="7fe5e-215">Google</span><span class="sxs-lookup"><span data-stu-id="7fe5e-215">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="7fe5e-216">Microsoft</span><span class="sxs-lookup"><span data-stu-id="7fe5e-216">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="7fe5e-217">Twitter</span><span class="sxs-lookup"><span data-stu-id="7fe5e-217">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="7fe5e-218">Dans l’exemple d’application, `userinfo.profile` la portée de Google est automatiquement ajoutée par le Framework quand <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> est appelé sur le <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-218">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="7fe5e-219">Si l’application requiert des étendues supplémentaires, ajoutez-les aux options.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-219">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="7fe5e-220">Dans l’exemple suivant, l' `https://www.googleapis.com/auth/user.birthday.read` étendue Google est ajoutée afin de récupérer l’anniversaire d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-220">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="7fe5e-221">Mapper des clés de données utilisateur et créer des revendications</span><span class="sxs-lookup"><span data-stu-id="7fe5e-221">Map user data keys and create claims</span></span>

<span data-ttu-id="7fe5e-222">Dans les options du fournisseur, spécifiez un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-222">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="7fe5e-223">Pour plus d’informations sur les types de revendication, consultez <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-223">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="7fe5e-224">L’exemple d’application crée `urn:google:locale` des revendications de paramètres régionaux () et d’image ( `urn:google:picture` ) à partir des `locale` `picture` clés et dans les données utilisateur de Google :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-224">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="7fe5e-225">Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-225">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="7fe5e-226">Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker `ApplicationUser` des revendications pour les données utilisateur disponibles à partir du <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-226">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="7fe5e-227">Dans l’exemple d’application, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) établit les revendications de paramètres régionaux ( `urn:google:locale` ) et d’image ( `urn:google:picture` ) pour le connecté `ApplicationUser` , y compris une revendication pour <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-227">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="7fe5e-228">Par défaut, les revendications d’un utilisateur sont stockées dans l’authentification cookie .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-228">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="7fe5e-229">Si l’authentification cookie est trop importante, cela peut entraîner l’échec de l’application, car :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-229">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="7fe5e-230">Le navigateur détecte que l' cookie en-tête est trop long.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-230">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="7fe5e-231">La taille globale de la demande est trop importante.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-231">The overall size of the request is too large.</span></span>

<span data-ttu-id="7fe5e-232">Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-232">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="7fe5e-233">Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-233">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="7fe5e-234">Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les Cookie intergiciels (middleware) d’authentification <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> pour stocker l’identité entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-234">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="7fe5e-235">Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-235">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="7fe5e-236">Enregistrer le jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="7fe5e-236">Save the access token</span></span>

<span data-ttu-id="7fe5e-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> définit si les jetons d’accès et d’actualisation doivent être stockés dans le <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> après une autorisation réussie.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="7fe5e-238">`SaveTokens` a la valeur `false` par défaut pour réduire la taille de l’authentification finale cookie .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-238">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="7fe5e-239">L’exemple d’application définit la valeur de `SaveTokens` sur `true` dans <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-239">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="7fe5e-240">Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe dans le `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-240">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="7fe5e-241">L’exemple d’application enregistre le jeton d’accès dans `OnPostConfirmationAsync` (nouvel enregistrement utilisateur) et `OnGetCallbackAsync` (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="7fe5e-241">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="7fe5e-242">Comment ajouter des jetons personnalisés supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7fe5e-242">How to add additional custom tokens</span></span>

<span data-ttu-id="7fe5e-243">Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre de `SaveTokens` , l’exemple d’application ajoute un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> avec le actuel <xref:System.DateTime> pour un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="7fe5e-243">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="7fe5e-244">Création et ajout de revendications</span><span class="sxs-lookup"><span data-stu-id="7fe5e-244">Creating and adding claims</span></span>

<span data-ttu-id="7fe5e-245">L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-245">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="7fe5e-246">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-246">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="7fe5e-247">Les utilisateurs peuvent définir des actions personnalisées en dérivant de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> et en implémentant la méthode abstraite <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-247">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="7fe5e-248">Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-248">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="7fe5e-249">Suppression des revendications et des actions de revendication</span><span class="sxs-lookup"><span data-stu-id="7fe5e-249">Removal of claim actions and claims</span></span>

<span data-ttu-id="7fe5e-250">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour le donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la collection.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-250">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="7fe5e-251">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-251">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="7fe5e-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="7fe5e-253">Exemple de sortie d’application</span><span class="sxs-lookup"><span data-stu-id="7fe5e-253">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7fe5e-254">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7fe5e-254">Additional resources</span></span>

* <span data-ttu-id="7fe5e-255">[application SocialSample d’ingénierie dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample): l’exemple d’application lié se trouve sur la branche [d’ingénierie dotnet/AspNetCore GitHub référentiel](https://github.com/dotnet/AspNetCore) `main` .</span><span class="sxs-lookup"><span data-stu-id="7fe5e-255">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `main` engineering branch.</span></span> <span data-ttu-id="7fe5e-256">La `main` branche contient du code sous développement actif pour la prochaine version de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="7fe5e-256">The `main` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="7fe5e-257">Pour afficher une version de l’exemple d’application pour une version finale de ASP.NET Core, utilisez la liste déroulante **branche** pour sélectionner une branche de version (par exemple `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="7fe5e-257">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
