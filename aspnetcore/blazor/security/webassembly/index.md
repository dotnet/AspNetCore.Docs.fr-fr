---
title: ASP.NET Core sécurisé Blazor WebAssembly
author: guardrex
description: Découvrez comment sécuriser des applications Blazor WebAssemlby en tant qu’applications à page unique (spas).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: 48a6227c30f025e118782b13e46fe0c7d6505851
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280834"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>ASP.NET Core sécurisé Blazor WebAssembly

Blazor WebAssembly les applications sont sécurisées de la même façon que les applications à page unique (SPAs). Il existe plusieurs approches pour l’authentification des utilisateurs sur la base de cas, mais l’approche la plus courante et la plus complète consiste à utiliser une implémentation basée sur le [protocole OAuth 2,0](https://oauth.net/), tel que [OpenID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Bibliothèque d’authentification

Blazor WebAssembly prend en charge l’authentification et l’autorisation d’applications à l’aide de OIDC via la [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) bibliothèque. La bibliothèque fournit un ensemble de primitives pour l’authentification en toute transparence par rapport aux principaux de ASP.NET Core. La bibliothèque s’intègre ASP.NET Core Identity à la prise en charge des autorisations d’API basée sur le [ Identity serveur](https://identityserver.io/). La bibliothèque peut s’authentifier auprès d’un Identity fournisseur tiers (IP) qui prend en charge OIDC, qui sont appelés fournisseurs OpenID (op).

La prise en charge de l’authentification dans Blazor WebAssembly est basée sur la `oidc-client.js` bibliothèque, qui est utilisée pour gérer les détails du protocole d’authentification sous-jacent.

D’autres options d’authentification de la fonction de déSameSitement sont disponibles, telles que l’utilisation de cookie . Toutefois, la conception d’ingénierie de Blazor WebAssembly est réglée sur OAuth et OIDC comme meilleure option pour l’authentification dans les Blazor WebAssembly applications. [L’authentification basée sur les jetons](xref:security/anti-request-forgery#token-based-authentication) basée sur des [jetons Web JSON (jetons JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a été choisie pour des raisons fonctionnelles et de sécurité : [ cookie ](xref:security/anti-request-forgery#cookie-based-authentication)

* L’utilisation d’un protocole basé sur les jetons offre une surface d’attaque plus réduite, car les jetons ne sont pas envoyés dans toutes les demandes.
* Les points de terminaison de serveur ne nécessitent pas de protection contre la [falsification de requête intersites (CSRF)](xref:security/anti-request-forgery) , car les jetons sont envoyés explicitement. Cela vous permet d’héberger des applications en même temps que des Blazor WebAssembly applications MVC ou Razor pages.
* Les jetons ont des autorisations plus étroites que cookie s. Par exemple, les jetons ne peuvent pas être utilisés pour gérer le compte d’utilisateur ou modifier le mot de passe d’un utilisateur, sauf si une telle fonctionnalité est implémentée de manière explicite.
* Les jetons ont une durée de vie brève, par défaut d’une heure, qui limite la fenêtre d’attaque. Les jetons peuvent également être révoqués à tout moment.
* Les jetons JWT autonomes offrent des garanties au client et au serveur sur le processus d’authentification. Par exemple, un client a la possibilité de détecter et de valider que les jetons qu’il reçoit sont légitimes et ont été émis dans le cadre d’un processus d’authentification donné. Si un tiers tente de basculer un jeton au milieu du processus d’authentification, le client peut détecter le jeton commuté et éviter de l’utiliser.
* Les jetons avec OAuth et OIDC ne reposent pas sur l’agent utilisateur qui se comporte correctement pour garantir la sécurité de l’application.
* Les protocoles basés sur les jetons, tels que OAuth et OIDC, permettent d’authentifier et d’autoriser des applications hébergées et autonomes avec le même ensemble de caractéristiques de sécurité.

## <a name="authentication-process-with-oidc"></a>Processus d’authentification avec OIDC

La [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) bibliothèque propose plusieurs primitives pour implémenter l’authentification et l’autorisation à l’aide de OIDC. En général, l’authentification fonctionne comme suit :

* Lorsqu’un utilisateur anonyme sélectionne le bouton de connexion ou demande une page pour laquelle l' [ `[Authorize]` attribut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) est appliqué, l’utilisateur est redirigé vers la page de connexion de l’application ( `/authentication/login` ).
* Dans la page de connexion, la bibliothèque d’authentification prépare une redirection vers le point de terminaison d’autorisation. Le point de terminaison d’autorisation est en dehors de l' Blazor WebAssembly application et peut être hébergé à une origine distincte. Le point de terminaison est chargé de déterminer si l’utilisateur est authentifié et d’émettre un ou plusieurs jetons en réponse. La bibliothèque d’authentification fournit un rappel de connexion pour recevoir la réponse d’authentification.
  * Si l’utilisateur n’est pas authentifié, l’utilisateur est redirigé vers le système d’authentification sous-jacent, ce qui est généralement le cas ASP.NET Core Identity .
  * Si l’utilisateur a déjà été authentifié, le point de terminaison d’autorisation génère les jetons appropriés et redirige le navigateur vers le point de terminaison de rappel de connexion ( `/authentication/login-callback` ).
* Lorsque l' Blazor WebAssembly application charge le point de terminaison de rappel de connexion ( `/authentication/login-callback` ), la réponse d’authentification est traitée.
  * Si le processus d’authentification se termine correctement, l’utilisateur est authentifié et éventuellement renvoyé à l’URL protégée d’origine que l’utilisateur a demandée.
  * Si le processus d’authentification échoue pour une raison quelconque, l’utilisateur est envoyé à la page Échec de la connexion ( `/authentication/login-failed` ) et une erreur s’affiche.

## <a name="authentication-component"></a>Composant `Authentication`

Le `Authentication` composant ( `Pages/Authentication.razor` ) gère les opérations d’authentification à distance et permet à l’application d’effectuer les opérations suivantes :

* Configurez les itinéraires d’application pour les États d’authentification.
* Définir le contenu de l’interface utilisateur pour les États d’authentification.
* Gérez l’état d’authentification.

Les actions d’authentification, telles que l’inscription ou la connexion d’un utilisateur, sont passées au Blazor composant de l’infrastructure <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> , qui rend persistant et contrôle l’état entre les opérations d’authentification.

Pour plus d’informations et d’exemples, consultez <xref:blazor/security/webassembly/additional-scenarios>.

## <a name="authorization"></a>Autorisation

Dans Blazor WebAssembly les applications, les vérifications d’autorisation peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs. Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.

**Effectuez toujours les vérifications d’autorisation sur le serveur au sein des points de terminaison de l’API auxquels votre application côté client accède.**

## <a name="require-authorization-for-the-entire-app"></a>Exiger une autorisation pour l’ensemble de l’application

Appliquez l' [ `[Authorize]` attribut](xref:blazor/security/index#authorize-attribute) ([documentation](xref:System.Web.Mvc.AuthorizeAttribute)de l’API) à chaque Razor composant de l’application à l’aide de l’une des approches suivantes :

* Utilisez la [`@attribute`](xref:mvc/views/razor#attribute) directive dans le `_Imports.razor` fichier :

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* Ajoutez l’attribut à chaque Razor composant dans le `Pages` dossier.

> [!NOTE]
> La définition d’une <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> sur une stratégie avec <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> n’est **pas** prise en charge.

## <a name="refresh-tokens"></a>Jetons d’actualisation

Les jetons d’actualisation ne peuvent pas être sécurisés côté client dans les Blazor WebAssembly applications. Par conséquent, les jetons d’actualisation ne doivent pas être envoyés à l’application pour une utilisation directe.

Les jetons d’actualisation peuvent être conservés et utilisés par l’application côté serveur dans une solution hébergée Blazor WebAssembly pour accéder aux API tierces. Pour plus d’informations, consultez <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="establish-claims-for-users"></a>Établir des revendications pour les utilisateurs

Les applications requièrent souvent des revendications pour les utilisateurs en fonction d’un appel d’API Web à un serveur. Par exemple, les revendications sont fréquemment utilisées pour [établir une autorisation](xref:blazor/security/index#authorization) dans une application. Dans ces scénarios, l’application demande un jeton d’accès pour accéder au service et utilise le jeton pour obtenir les données utilisateur pour les revendications. Pour obtenir des exemples, consultez les ressources suivantes :

* [Scénarios supplémentaires : personnaliser l’utilisateur](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-identity-server"></a>Azure App Service sur Linux avec Identity serveur

Spécifiez l’émetteur de manière explicite lors du déploiement sur Azure App Service sur Linux avec le Identity serveur. Pour plus d’informations, consultez <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="windows-authentication"></a>Authentification Windows

Nous vous déconseillons d’utiliser l’authentification Windows avec Blazor webassembly ou avec n’importe quel autre Framework Spa. Nous vous recommandons d’utiliser des protocoles basés sur des jetons au lieu de l’authentification Windows, tels que OIDC avec services de fédération Active Directory (AD FS) (ADFS).

Si l’authentification Windows est utilisée avec Blazor webassembly ou avec tout autre Framework Spa, des mesures supplémentaires sont requises pour protéger l’application des jetons de falsification de requête intersites (CSRF). Les mêmes préoccupations que celles qui s’appliquent à s' cookie appliquent à l’authentification Windows avec l’ajout que l’authentification Windows n’offre aucun mécanisme pour empêcher le partage du contexte d’authentification entre les origines. Les applications qui utilisent l’authentification Windows sans protection supplémentaire à partir de CSRF doivent être au moins limitées à l’intranet d’une organisation et ne pas être utilisées sur Internet.

Pour plus d’informations, consultez <xref:security/anti-request-forgery> .

## <a name="implementation-guidance"></a>Conseils d’implémentation

Les Articles de cette *vue d’ensemble* fournissent des informations sur l’authentification des utilisateurs dans les Blazor WebAssembly applications contre des fournisseurs spécifiques.

Applications autonomes Blazor WebAssembly :

* [Aide générale pour les fournisseurs OIDC et la bibliothèque d’authentification webassembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Comptes Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

Applications hébergées Blazor WebAssembly :

* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [Identity Serveurs](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

D’autres conseils de configuration figurent dans les articles suivants :

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Pour plus d’informations sur la configuration, consultez <xref:blazor/security/webassembly/additional-scenarios> .

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:host-and-deploy/proxy-load-balancer>: Fournit des conseils sur :
  * Utilisation de l’intergiciel d’en-têtes transférés pour conserver les informations de schéma HTTPs entre les serveurs proxy et les réseaux internes.
  * Scénarios et cas d’utilisation supplémentaires, y compris la configuration manuelle du schéma, les modifications du chemin des demandes pour le routage des demandes correct et le transfert du modèle de demande pour les proxys inversés Linux et non-IIS.
