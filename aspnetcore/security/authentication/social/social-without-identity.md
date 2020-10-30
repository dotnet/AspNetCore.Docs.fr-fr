---
title: L’authentification Facebook, Google et fournisseur externe sans ASP.NET Core Identity
author: rick-anderson
description: Explication de l’utilisation de l’authentification utilisateur Facebook, Google, Twitter, etc. sans ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060285"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a>Utiliser l’authentification du fournisseur de connexion sociale sans ASP.NET Core Identity

Par [Kirk Larkin](https://twitter.com/serpent5) et [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> décrit comment permettre aux utilisateurs de se connecter à l’aide d’OAuth 2,0 avec les informations d’identification des fournisseurs d’authentification externes. L’approche décrite dans cette rubrique comprend ASP.NET Core Identity un fournisseur d’authentification.

Cet exemple montre comment utiliser un fournisseur d’authentification externe **sans** ASP.NET Core Identity . Cela est utile pour les applications qui n’ont pas besoin de toutes les fonctionnalités de ASP.NET Core Identity , mais qui nécessitent néanmoins une intégration avec un fournisseur d’authentification externe approuvé.

Cet exemple utilise [l’authentification Google](xref:security/authentication/google-logins) pour authentifier les utilisateurs. L’utilisation de l’authentification Google décale un grand nombre des complexités liées à la gestion du processus de connexion à Google. Pour une intégration avec un autre fournisseur d’authentification externe, consultez les rubriques suivantes :

* [Authentification Facebook](xref:security/authentication/facebook-logins)
* [Authentification Microsoft](xref:security/authentication/microsoft-logins)
* [Authentification Twitter](xref:security/authentication/twitter-logins)
* [Autres fournisseurs](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuration

Dans la `ConfigureServices` méthode, configurez les schémas d’authentification de l’application avec les <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> méthodes, et <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

L’appel à <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> définit le de l’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . `DefaultScheme`Est le schéma par défaut utilisé par les `HttpContext` méthodes d’extension d’authentification suivantes :

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

La définition de l’application `DefaultScheme` sur [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (« Cookie s ») configure l’application pour qu’elle utilise les Cookie s comme schéma par défaut pour ces méthodes d’extension. La définition de l’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> sur [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) (« Google ») configure l’application pour utiliser Google comme modèle par défaut pour les appels à `ChallengeAsync` . `DefaultChallengeScheme` remplace `DefaultScheme` . Pour obtenir des <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> propriétés supplémentaires qui remplacent lorsqu’elles sont `DefaultScheme` définies, consultez.

Dans `Startup.Configure` , appelez `UseAuthentication` et `UseAuthorization` entre appelant `UseRouting` et `UseEndpoints` . Cela définit la `HttpContext.User` propriété et exécute l’intergiciel (middleware) des autorisations pour les demandes :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Pour en savoir plus sur les schémas d’authentification, consultez [concepts d’authentification](xref:security/authentication/index#authentication-concepts). Pour en savoir plus sur cookie l’authentification, consultez <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Appliquer l’autorisation

Testez la configuration d’authentification de l’application en appliquant l' `AuthorizeAttribute` attribut à un contrôleur, une action ou une page. Le code suivant limite l’accès à la page de *confidentialité* aux utilisateurs qui ont été authentifiés :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Se déconnecter

Pour déconnecter l’utilisateur actuel et supprimer son cookie , appelez [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Le code suivant ajoute un `Logout` Gestionnaire de page à la page d' *index* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Notez que l’appel à `SignOutAsync` ne spécifie pas de schéma d’authentification. Le de l' `DefaultScheme` application `CookieAuthenticationDefaults.AuthenticationScheme` est utilisé comme un recul.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> décrit comment permettre aux utilisateurs de se connecter à l’aide d’OAuth 2,0 avec les informations d’identification des fournisseurs d’authentification externes. L’approche décrite dans cette rubrique comprend ASP.NET Core Identity un fournisseur d’authentification.

Cet exemple montre comment utiliser un fournisseur d’authentification externe **sans** ASP.NET Core Identity . Cela est utile pour les applications qui n’ont pas besoin de toutes les fonctionnalités de ASP.NET Core Identity , mais qui nécessitent néanmoins une intégration avec un fournisseur d’authentification externe approuvé.

Cet exemple utilise [l’authentification Google](xref:security/authentication/google-logins) pour authentifier les utilisateurs. L’utilisation de l’authentification Google décale un grand nombre des complexités liées à la gestion du processus de connexion à Google. Pour une intégration avec un autre fournisseur d’authentification externe, consultez les rubriques suivantes :

* [Authentification Facebook](xref:security/authentication/facebook-logins)
* [Authentification Microsoft](xref:security/authentication/microsoft-logins)
* [Authentification Twitter](xref:security/authentication/twitter-logins)
* [Autres fournisseurs](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuration

Dans la `ConfigureServices` méthode, configurez les schémas d’authentification de l’application avec les `AddAuthentication` `AddCookie` méthodes, et `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

L’appel à [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) définit le [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)de l’application. `DefaultScheme`Est le schéma par défaut utilisé par les `HttpContext` méthodes d’extension d’authentification suivantes :

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

La définition de l’application `DefaultScheme` sur [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (« Cookie s ») configure l’application pour qu’elle utilise les Cookie s comme schéma par défaut pour ces méthodes d’extension. La définition de l’application <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> sur [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) (« Google ») configure l’application pour utiliser Google comme modèle par défaut pour les appels à `ChallengeAsync` . `DefaultChallengeScheme` remplace `DefaultScheme` . Pour obtenir des <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> propriétés supplémentaires qui remplacent lorsqu’elles sont `DefaultScheme` définies, consultez.

Dans la `Configure` méthode, appelez la `UseAuthentication` méthode pour appeler l’intergiciel (middleware) d’authentification qui définit la `HttpContext.User` propriété. Appelez la `UseAuthentication` méthode avant d’appeler `UseMvcWithDefaultRoute` ou `UseMvc` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Pour en savoir plus sur les schémas d’authentification, consultez [concepts d’authentification](xref:security/authentication/index#authentication-concepts). Pour en savoir plus sur cookie l’authentification, consultez <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Appliquer l’autorisation

Testez la configuration d’authentification de l’application en appliquant l' `AuthorizeAttribute` attribut à un contrôleur, une action ou une page. Le code suivant limite l’accès à la page de *confidentialité* aux utilisateurs qui ont été authentifiés :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Se déconnecter

Pour déconnecter l’utilisateur actuel et supprimer son cookie , appelez [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Le code suivant ajoute un `Logout` Gestionnaire de page à la page d' *index* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Notez que l’appel à `SignOutAsync` ne spécifie pas de schéma d’authentification. Le de l' `DefaultScheme` application `CookieAuthenticationDefaults.AuthenticationScheme` est utilisé comme un recul.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
