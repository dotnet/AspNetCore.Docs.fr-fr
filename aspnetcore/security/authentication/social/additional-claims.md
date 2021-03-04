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
ms.openlocfilehash: 9c04ca466566e956b5e6dfec8131096c3995bc14
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110142"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Conserver des revendications et des jetons supplémentaires à partir de fournisseurs externes dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter. Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application. Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client. Pour plus d’informations, consultez <xref:security/authentication/social/index>. L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Définir l’ID client et la clé secrète client

Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client. Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur. Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur. Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :

* [Authentification Facebook](xref:security/authentication/facebook-logins)
* [Authentification Google](xref:security/authentication/google-logins)
* [Authentification Microsoft](xref:security/authentication/microsoft-logins)
* [Authentification Twitter](xref:security/authentication/twitter-logins)
* [Autres fournisseurs d’authentification](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Établir l’étendue de l’authentification

Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant le <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.

| Fournisseur  | Étendue                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `profile`, `email`, `openid`                                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Dans l’exemple d’application, les `profile` `email` portées Google, et `openid` sont automatiquement ajoutées par le Framework quand <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> est appelé sur le <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Si l’application requiert des étendues supplémentaires, ajoutez-les aux options. Dans l’exemple suivant, l' `https://www.googleapis.com/auth/user.birthday.read` étendue Google est ajoutée pour récupérer l’anniversaire d’un utilisateur :

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapper des clés de données utilisateur et créer des revendications

Dans les options du fournisseur, spécifiez un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion. Pour plus d’informations sur les types de revendication, consultez <xref:System.Security.Claims.ClaimTypes> .

L’exemple d’application crée `urn:google:locale` des revendications de paramètres régionaux () et d’image ( `urn:google:picture` ) à partir des `locale` `picture` clés et dans les données utilisateur de Google :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker `ApplicationUser` des revendications pour les données utilisateur disponibles à partir du <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

Dans l’exemple d’application, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) établit les revendications de paramètres régionaux ( `urn:google:locale` ) et d’image ( `urn:google:picture` ) pour le connecté `ApplicationUser` , y compris une revendication pour <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Par défaut, les revendications d’un utilisateur sont stockées dans l’authentification cookie . Si l’authentification cookie est trop importante, cela peut entraîner l’échec de l’application, car :

* Le navigateur détecte que l' cookie en-tête est trop long.
* La taille globale de la demande est trop importante.

Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :

* Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.
* Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les Cookie intergiciels (middleware) d’authentification <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> pour stocker l’identité entre les demandes. Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.

## <a name="save-the-access-token"></a>Enregistrer le jeton d’accès

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> définit si les jetons d’accès et d’actualisation doivent être stockés dans le <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> après une autorisation réussie. `SaveTokens` a la valeur `false` par défaut pour réduire la taille de l’authentification finale cookie .

L’exemple d’application définit la valeur de `SaveTokens` sur `true` dans <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe dans le `ApplicationUser` `AuthenticationProperties` .

L’exemple d’application enregistre le jeton d’accès dans `OnPostConfirmationAsync` (nouvel enregistrement utilisateur) et `OnGetCallbackAsync` (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> Pour plus d’informations sur le passage de jetons aux Razor composants d’une Blazor Server application, consultez <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .

## <a name="how-to-add-additional-custom-tokens"></a>Comment ajouter des jetons personnalisés supplémentaires

Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre de `SaveTokens` , l’exemple d’application ajoute un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> avec le actuel <xref:System.DateTime> pour un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Création et ajout de revendications

L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection. Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Les utilisateurs peuvent définir des actions personnalisées en dérivant de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> et en implémentant la méthode abstraite <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .

Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="add-and-update-user-claims"></a>Ajouter et mettre à jour des revendications utilisateur

Les revendications sont copiées à partir de fournisseurs externes dans la base de données utilisateur lors de la première inscription, et non lors de la connexion. Si des revendications supplémentaires sont activées dans une application après qu’un utilisateur s’est inscrit pour utiliser l’application, appelez [SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) sur un utilisateur pour forcer la génération d’une nouvelle authentification cookie .

Dans l’environnement de développement qui utilise des comptes d’utilisateur de test, vous pouvez simplement supprimer et recréer le compte d’utilisateur. Pour les systèmes de production, les nouvelles revendications ajoutées à l’application peuvent être remplies dans les comptes d’utilisateur. Après la génération de la [structure de la `ExternalLogin` page](xref:security/authentication/scaffold-identity) dans l’application à l’adresse `Areas/Pages/Identity/Account/Manage` , ajoutez le code suivant à `ExternalLoginModel` dans le `ExternalLogin.cshtml.cs` fichier.

Ajoutez un dictionnaire de revendications ajoutées. Utilisez les clés de dictionnaire pour stocker les types de revendication et utilisez les valeurs pour contenir une valeur par défaut. Ajoutez la ligne suivante au début de la classe. L’exemple suivant suppose qu’une revendication est ajoutée pour l’image Google de l’utilisateur avec une image headshot générique comme valeur par défaut :

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

Remplacez le code par défaut de la `OnGetCallbackAsync` méthode par le code suivant. Le code parcourt le dictionnaire de revendications. Les revendications sont ajoutées (remplies) ou mises à jour pour chaque utilisateur. Lorsque des revendications sont ajoutées ou mises à jour, la connexion de l’utilisateur est actualisée à l’aide de <xref:Microsoft.AspNetCore.Identity.SignInManager%601> , en préservant les propriétés d’authentification existantes ( `AuthenticationProperties` ).

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

Une approche similaire est prise lorsque les revendications sont modifiées pendant qu’un utilisateur est connecté, mais qu’une étape de renvoi n’est pas nécessaire. Pour mettre à jour les revendications d’un utilisateur, appelez la commande suivante sur l’utilisateur :

* [Usermanager. ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) sur l’utilisateur pour les revendications stockées dans la base de données d’identité.
* [SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) sur l’utilisateur pour forcer la génération d’une nouvelle authentification cookie .

## <a name="removal-of-claim-actions-and-claims"></a>Suppression des revendications et des actions de revendication

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour le donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la collection. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.

## <a name="sample-app-output"></a>Exemple de sortie d’application

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

Une application ASP.NET Core peut établir des revendications et des jetons supplémentaires à partir de fournisseurs d’authentification externes, tels que Facebook, Google, Microsoft et Twitter. Chaque fournisseur révèle des informations différentes sur les utilisateurs sur sa plateforme, mais le modèle de réception et de transformation des données utilisateur en revendications supplémentaires est le même.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Choisissez les fournisseurs d’authentification externes à prendre en charge dans l’application. Pour chaque fournisseur, inscrivez l’application et obtenez un ID client et une clé secrète client. Pour plus d’informations, consultez <xref:security/authentication/social/index>. L’exemple d’application utilise le [fournisseur d’authentification Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Définir l’ID client et la clé secrète client

Le fournisseur d’authentification OAuth établit une relation d’approbation avec une application à l’aide d’un ID client et d’une clé secrète client. Les valeurs ID client et clé secrète client sont créées pour l’application par le fournisseur d’authentification externe lorsque l’application est inscrite auprès du fournisseur. Chaque fournisseur externe utilisé par l’application doit être configuré indépendamment avec l’ID client et la clé secrète client du fournisseur. Pour plus d’informations, consultez les rubriques fournisseur d’authentification externe qui s’appliquent à votre scénario :

* [Authentification Facebook](xref:security/authentication/facebook-logins)
* [Authentification Google](xref:security/authentication/google-logins)
* [Authentification Microsoft](xref:security/authentication/microsoft-logins)
* [Authentification Twitter](xref:security/authentication/twitter-logins)
* [Autres fournisseurs d’authentification](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

L’exemple d’application configure le fournisseur d’authentification Google avec un ID client et une clé secrète client fournis par Google :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Établir l’étendue de l’authentification

Spécifiez la liste des autorisations à récupérer du fournisseur en spécifiant le <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . Les étendues d’authentification pour les fournisseurs externes communs apparaissent dans le tableau suivant.

| Fournisseur  | Étendue                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Dans l’exemple d’application, `userinfo.profile` la portée de Google est automatiquement ajoutée par le Framework quand <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> est appelé sur le <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Si l’application requiert des étendues supplémentaires, ajoutez-les aux options. Dans l’exemple suivant, l' `https://www.googleapis.com/auth/user.birthday.read` étendue Google est ajoutée afin de récupérer l’anniversaire d’un utilisateur :

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapper des clés de données utilisateur et créer des revendications

Dans les options du fournisseur, spécifiez un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> ou <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pour chaque clé/sous-clé dans les données utilisateur JSON du fournisseur externe pour que l’identité de l’application soit lue lors de la connexion. Pour plus d’informations sur les types de revendication, consultez <xref:System.Security.Claims.ClaimTypes> .

L’exemple d’application crée `urn:google:locale` des revendications de paramètres régionaux () et d’image ( `urn:google:picture` ) à partir des `locale` `picture` clés et dans les données utilisateur de Google :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

Dans `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` , un <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) est connecté à l’application avec <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . Pendant le processus de connexion, le <xref:Microsoft.AspNetCore.Identity.UserManager%601> peut stocker `ApplicationUser` des revendications pour les données utilisateur disponibles à partir du <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

Dans l’exemple d’application, `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) établit les revendications de paramètres régionaux ( `urn:google:locale` ) et d’image ( `urn:google:picture` ) pour le connecté `ApplicationUser` , y compris une revendication pour <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Par défaut, les revendications d’un utilisateur sont stockées dans l’authentification cookie . Si l’authentification cookie est trop importante, cela peut entraîner l’échec de l’application, car :

* Le navigateur détecte que l' cookie en-tête est trop long.
* La taille globale de la demande est trop importante.

Si une grande quantité de données utilisateur est requise pour le traitement des demandes de l’utilisateur :

* Limitez le nombre et la taille des revendications utilisateur pour le traitement des demandes uniquement pour ce que l’application requiert.
* Utilisez un personnalisé <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pour les Cookie intergiciels (middleware) d’authentification <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> pour stocker l’identité entre les demandes. Conservez de grandes quantités d’informations d’identité sur le serveur tout en envoyant une petite clé d’identificateur de session au client uniquement.

## <a name="save-the-access-token"></a>Enregistrer le jeton d’accès

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> définit si les jetons d’accès et d’actualisation doivent être stockés dans le <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> après une autorisation réussie. `SaveTokens` a la valeur `false` par défaut pour réduire la taille de l’authentification finale cookie .

L’exemple d’application définit la valeur de `SaveTokens` sur `true` dans <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Lorsque `OnPostConfirmationAsync` exécute, stockez le jeton d’accès ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) à partir du fournisseur externe dans le `ApplicationUser` `AuthenticationProperties` .

L’exemple d’application enregistre le jeton d’accès dans `OnPostConfirmationAsync` (nouvel enregistrement utilisateur) et `OnGetCallbackAsync` (utilisateur précédemment inscrit) dans *Account/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Comment ajouter des jetons personnalisés supplémentaires

Pour montrer comment ajouter un jeton personnalisé, qui est stocké dans le cadre de `SaveTokens` , l’exemple d’application ajoute un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> avec le actuel <xref:System.DateTime> pour un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated` :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Création et ajout de revendications

L’infrastructure fournit des actions courantes et des méthodes d’extension pour créer et ajouter des revendications à la collection. Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> et <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Les utilisateurs peuvent définir des actions personnalisées en dérivant de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> et en implémentant la méthode abstraite <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .

Pour plus d’informations, consultez <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Suppression des revendications et des actions de revendication

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) supprime toutes les actions de revendication pour le donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la collection. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) supprime une revendication du donné <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de l’identité. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> est principalement utilisé avec [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) pour supprimer les revendications générées par le protocole.

## <a name="sample-app-output"></a>Exemple de sortie d’application

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

## <a name="additional-resources"></a>Ressources supplémentaires

* [application SocialSample d’ingénierie dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): l’exemple d’application lié se trouve sur la branche [d’ingénierie dotnet/AspNetCore GitHub référentiel](https://github.com/dotnet/AspNetCore) `master` . La `master` branche contient du code sous développement actif pour la prochaine version de ASP.net core. Pour afficher une version de l’exemple d’application pour une version finale de ASP.NET Core, utilisez la liste déroulante **branche** pour sélectionner une branche de version (par exemple `release/{X.Y}` ).
