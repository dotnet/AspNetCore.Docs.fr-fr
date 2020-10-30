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
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061351"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a>Utiliser cookie l’authentification sans ASP.NET Core Identity

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Identity est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions. Toutefois, cookie il n’est pas possible d’utiliser un fournisseur d’authentification basé sur ASP.NET Core Identity . Pour plus d'informations, consultez <xref:security/authentication/identity>.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application. Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur. L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* . Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.

## <a name="configuration"></a>Configuration

Dans la `Startup.ConfigureServices` méthode, créez les services d’intergiciel (middleware) d’authentification avec les <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application. `AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d' cookie authentification et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme). L’affectation de la `AuthenticationScheme` valeur à [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « Cookie s » pour le schéma. Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.

Le schéma d’authentification de l’application est différent du schéma d’authentification de l’application cookie . Lorsqu’un cookie schéma d’authentification n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« Cookie s »).

La cookie propriété de l’authentification <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> a la valeur `true` par défaut. cookieLes s d’authentification sont autorisées lorsqu’un visiteur du site n’a pas consenti à la collecte de données. Pour plus d'informations, consultez <xref:security/gdpr#essential-cookies>.

Dans `Startup.Configure` , appelez `UseAuthentication` et `UseAuthorization` pour définir la `HttpContext.User` propriété et exécuter l’intergiciel (middleware) des autorisations pour les demandes. Appelez les `UseAuthentication` `UseAuthorization` méthodes et avant d’appeler `UseEndpoints` :

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.

Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie Intergiciel de stratégie

L' [ Cookie intergiciel (middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ) de stratégie active les cookie fonctionnalités de stratégie. L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

À utiliser <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de Cookie stratégie pour contrôler les caractéristiques globales du cookie traitement et raccorder des cookie gestionnaires de traitement lorsque cookie des s sont ajoutés ou supprimés.

La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2. Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` . Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de cookie sécurité pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Le Cookie paramètre d’intergiciel de stratégie pour `MinimumSameSitePolicy` peut affecter la valeur de `Cookie.SameSite` dans `CookieAuthenticationOptions` les paramètres en fonction du tableau ci-dessous.

| MinimumSameSitePolicy | Cookie. SameSite | Résultante Cookie . Paramètre SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode. None     | SameSiteMode. None<br>SameSiteMode. Lax<br>SameSiteMode. strict | SameSiteMode. None<br>SameSiteMode. Lax<br>SameSiteMode. strict |
| SameSiteMode. Lax      | SameSiteMode. None<br>SameSiteMode. Lax<br>SameSiteMode. strict | SameSiteMode. Lax<br>SameSiteMode. Lax<br>SameSiteMode. strict |
| SameSiteMode. strict   | SameSiteMode. None<br>SameSiteMode. Lax<br>SameSiteMode. strict | SameSiteMode. strict<br>SameSiteMode. strict<br>SameSiteMode. strict |

## <a name="create-an-authentication-no-loccookie"></a>Créer une authentification cookie

Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> . Les informations utilisateur sont sérialisées et stockées dans le cookie . 

Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync` crée un chiffré cookie et l’ajoute à la réponse actuelle. Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> est utilisé uniquement sur quelques chemins spécifiques par défaut, par exemple, le chemin d’accès de connexion et les chemins d’accès de déconnexion. Pour plus d’informations, consultez la [ Cookie source AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).

Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement. Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.

## <a name="sign-out"></a>Se déconnecter

Pour déconnecter l’utilisateur actuel et supprimer son cookie , appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « Cookie s ») n’est pas utilisé comme modèle (par exemple, « Contoso Cookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification. Dans le cas contraire, le schéma par défaut est utilisé.

Lorsque le navigateur se ferme, il supprime automatiquement les cookie s basés sur une session (s non persistantes cookie ), mais aucun cookie s n’est effacé lorsqu’un onglet individuel est fermé. Le serveur n’est pas averti des événements de fermeture de tabulation ou de navigateur.

## <a name="react-to-back-end-changes"></a>Réagir aux modifications principales

Une fois cookie créé, cookie est la seule source d’identité. Si un compte d’utilisateur est désactivé dans les systèmes principaux :

* Le cookie système d’authentification de l’application continue à traiter les demandes en fonction de l’authentification cookie .
* L’utilisateur reste connecté à l’application tant que l’authentification cookie est valide.

L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l' cookie identité. La validation de cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.

Une approche de cookie la validation est basée sur le suivi de la modification de la base de données utilisateur. Si la base de données n’a pas été modifiée depuis la publication de l’utilisateur cookie , il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide. Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur. Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.

Afin d’invalider un cookie lorsque la base de données change en fonction de la `LastChanged` valeur, créez le cookie avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :

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

Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Voici un exemple d’implémentation de `CookieAuthenticationEvents` :

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

Inscrivez l’instance événements lors cookie de l’inscription du service dans la `Startup.ConfigureServices` méthode. Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `CustomCookieAuthenticationEvents` classe :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité. Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .

> [!WARNING]
> L’approche décrite ici est déclenchée à chaque demande. La validation des cookie s d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.

## <a name="persistent-no-loccookies"></a>Persistant cookie s

Vous souhaiterez peut-être cookie conserver le à travers les sessions de navigateur. Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire. 

L’extrait de code suivant crée une identité et correspondant cookie qui subsiste dans les fermetures du navigateur. Les paramètres d’expiration décalés précédemment configurés sont honorés. Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie lorsqu’il est redémarré.

Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :

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

## <a name="absolute-no-loccookie-expiration"></a>cookieExpiration absolue

Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . Pour créer un persistant cookie , `IsPersistent` doit également être défini. Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient. Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , si elle est définie.

L’extrait de code suivant crée une identité cookie qui correspond à 20 minutes. Cela ignore tous les paramètres d’expiration décalés précédemment configurés.

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

ASP.NET Core Identity est un fournisseur d’authentification complet et complet pour la création et la gestion des connexions. Toutefois, cookie il n’est pas possible d’utiliser un fournisseur d’authentification basé sur ASP.NET Core Identity . Pour plus d'informations, consultez <xref:security/authentication/identity>.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

À des fins de démonstration dans l’exemple d’application, le compte d’utilisateur de l’utilisateur hypothétique, Maria Rodriguez, est codé en dur dans l’application. Utilisez l’adresse de **messagerie** `maria.rodriguez@contoso.com` et un mot de passe pour vous connecter à l’utilisateur. L’utilisateur est authentifié dans la `AuthenticateUser` méthode dans le fichier *pages/Account/login. cshtml. cs* . Dans un exemple réel, l’utilisateur est authentifié par rapport à une base de données.

## <a name="configuration"></a>Configuration

Si l’application n’utilise pas le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), créez une référence de package dans le fichier projet pour [Microsoft. AspNetCore. Authentication. Cookie package s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

Dans la `Startup.ConfigureServices` méthode, créez le service d’intergiciel d’authentification avec <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> les <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes et :

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passé à `AddAuthentication` définit le schéma d’authentification par défaut pour l’application. `AuthenticationScheme` est utile lorsqu’il existe plusieurs instances d' cookie authentification et que vous souhaitez [autoriser avec un schéma spécifique](xref:security/authorization/limitingidentitybyscheme). L’affectation de la `AuthenticationScheme` valeur à [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fournit la valeur « Cookie s » pour le schéma. Vous pouvez fournir n’importe quelle valeur de chaîne qui distingue le schéma.

Le schéma d’authentification de l’application est différent du schéma d’authentification de l’application cookie . Lorsqu’un cookie schéma d’authentification n’est pas fourni à <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , il utilise `CookieAuthenticationDefaults.AuthenticationScheme` (« Cookie s »).

La cookie propriété de l’authentification <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> a la valeur `true` par défaut. cookieLes s d’authentification sont autorisées lorsqu’un visiteur du site n’a pas consenti à la collecte de données. Pour plus d'informations, consultez <xref:security/gdpr#essential-cookies>.

Dans la `Startup.Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification qui définit la `HttpContext.User` propriété. Appelez la `UseAuthentication` méthode avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc` :

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe est utilisée pour configurer les options du fournisseur d’authentification.

Définissez `CookieAuthenticationOptions` dans la configuration de service pour l’authentification dans la `Startup.ConfigureServices` méthode :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie Intergiciel de stratégie

L' [ Cookie intergiciel (middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ) de stratégie active les cookie fonctionnalités de stratégie. L’ajout de l’intergiciel au pipeline de traitement de l’application est sensible à l’ordre &mdash; . il affecte uniquement les composants en aval inscrits dans le pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

À utiliser <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fourni à l’intergiciel (middleware) de Cookie stratégie pour contrôler les caractéristiques globales du cookie traitement et raccorder des cookie gestionnaires de traitement lorsque cookie des s sont ajoutés ou supprimés.

La valeur par défaut <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> est `SameSiteMode.Lax` d’autoriser l’authentification OAuth2. Pour appliquer strictement la même stratégie de site de `SameSiteMode.Strict` , définissez le `MinimumSameSitePolicy` . Bien que ce paramètre interrompe OAuth2 et d’autres schémas d’authentification Cross-Origin, il élève le niveau de cookie sécurité pour les autres types d’applications qui ne reposent pas sur le traitement des demandes Cross-Origin.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Le Cookie paramètre d’intergiciel de stratégie pour `MinimumSameSitePolicy` peut affecter la valeur de `Cookie.SameSite` dans `CookieAuthenticationOptions` les paramètres en fonction du tableau ci-dessous.

| MinimumSameSitePolicy | Cookie. SameSite | Résultante Cookie . Paramètre SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode. None     | SameSiteMode. None<br>SameSiteMode. Lax<br>SameSiteMode. strict | SameSiteMode. None<br>SameSiteMode. Lax<br>SameSiteMode. strict |
| SameSiteMode. Lax      | SameSiteMode. None<br>SameSiteMode. Lax<br>SameSiteMode. strict | SameSiteMode. Lax<br>SameSiteMode. Lax<br>SameSiteMode. strict |
| SameSiteMode. strict   | SameSiteMode. None<br>SameSiteMode. Lax<br>SameSiteMode. strict | SameSiteMode. strict<br>SameSiteMode. strict<br>SameSiteMode. strict |

## <a name="create-an-authentication-no-loccookie"></a>Créer une authentification cookie

Pour créer un cookie contenant des informations sur l’utilisateur, construisez un <xref:System.Security.Claims.ClaimsPrincipal> . Les informations utilisateur sont sérialisées et stockées dans le cookie . 

Créez un <xref:System.Security.Claims.ClaimsIdentity> avec les <xref:System.Security.Claims.Claim> s requis et appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pour vous connecter à l’utilisateur :

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` crée un chiffré cookie et l’ajoute à la réponse actuelle. Si `AuthenticationScheme` n’est pas spécifié, le schéma par défaut est utilisé.

Le système de [protection des données](xref:security/data-protection/using-data-protection) de ASP.net Core est utilisé pour le chiffrement. Pour une application hébergée sur plusieurs ordinateurs, l’équilibrage de charge entre les applications ou l’utilisation d’une batterie de serveurs Web, [configurez la protection des données](xref:security/data-protection/configuration/overview) pour utiliser le même anneau de clé et l’identificateur d’application.

## <a name="sign-out"></a>Se déconnecter

Pour déconnecter l’utilisateur actuel et supprimer son cookie , appelez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Si `CookieAuthenticationDefaults.AuthenticationScheme` (ou « Cookie s ») n’est pas utilisé comme modèle (par exemple, « Contoso Cookie »), fournissez le schéma utilisé lors de la configuration du fournisseur d’authentification. Dans le cas contraire, le schéma par défaut est utilisé.

## <a name="react-to-back-end-changes"></a>Réagir aux modifications principales

Une fois cookie créé, cookie est la seule source d’identité. Si un compte d’utilisateur est désactivé dans les systèmes principaux :

* Le cookie système d’authentification de l’application continue à traiter les demandes en fonction de l’authentification cookie .
* L’utilisateur reste connecté à l’application tant que l’authentification cookie est valide.

L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> événement peut être utilisé pour intercepter et remplacer la validation de l' cookie identité. La validation de cookie à chaque demande atténue le risque d’accès des utilisateurs révoqués à l’application.

Une approche de cookie la validation est basée sur le suivi de la modification de la base de données utilisateur. Si la base de données n’a pas été modifiée depuis la publication de l’utilisateur cookie , il n’est pas nécessaire de réauthentifier l’utilisateur si son cookie est toujours valide. Dans l’exemple d’application, la base de données est implémentée dans `IUserRepository` et stocke une `LastChanged` valeur. Lorsqu’un utilisateur est mis à jour dans la base de données, la `LastChanged` valeur est définie sur l’heure actuelle.

Afin d’invalider un cookie lorsque la base de données change en fonction de la `LastChanged` valeur, créez le cookie avec une `LastChanged` revendication contenant la `LastChanged` valeur actuelle de la base de données :

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

Pour implémenter une substitution pour l' `ValidatePrincipal` événement, écrivez une méthode avec la signature suivante dans une classe qui dérive de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Voici un exemple d’implémentation de `CookieAuthenticationEvents` :

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

Inscrivez l’instance événements lors cookie de l’inscription du service dans la `Startup.ConfigureServices` méthode. Fournissez une [inscription de service étendue](xref:fundamentals/dependency-injection#service-lifetimes) pour votre `CustomCookieAuthenticationEvents` classe :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Imaginez une situation dans laquelle le nom de l’utilisateur est mis à jour &mdash; une décision qui n’affecte en rien la sécurité. Si vous souhaitez mettre à jour le principal de l’utilisateur de manière non destructrice, appelez `context.ReplacePrincipal` et affectez à la propriété la valeur `context.ShouldRenew` `true` .

> [!WARNING]
> L’approche décrite ici est déclenchée à chaque demande. La validation des cookie s d’authentification pour tous les utilisateurs à chaque demande peut entraîner une baisse importante des performances de l’application.

## <a name="persistent-no-loccookies"></a>Persistant cookie s

Vous souhaiterez peut-être cookie conserver le à travers les sessions de navigateur. Cette persistance doit être activée uniquement avec le consentement explicite de l’utilisateur avec une case à cocher « Mémoriser mes propres » lors de la connexion ou d’un mécanisme similaire. 

L’extrait de code suivant crée une identité et correspondant cookie qui subsiste dans les fermetures du navigateur. Les paramètres d’expiration décalés précédemment configurés sont honorés. Si le cookie expire pendant que le navigateur est fermé, le navigateur efface le cookie lorsqu’il est redémarré.

Définir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> sur `true` dans <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :

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

## <a name="absolute-no-loccookie-expiration"></a>cookieExpiration absolue

Une heure d’expiration absolue peut être définie avec <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . Pour créer un persistant cookie , `IsPersistent` doit également être défini. Dans le cas contraire, le cookie est créé avec une durée de vie basée sur la session et peut expirer avant ou après le ticket d’authentification qu’il contient. Lorsque `ExpiresUtc` est défini, il remplace la valeur de l' <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , si elle est définie.

L’extrait de code suivant crée une identité cookie qui correspond à 20 minutes. Cela ignore tous les paramètres d’expiration décalés précédemment configurés.

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

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Vérifications des rôles basés sur des stratégies](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
