---
title: Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory B2C
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory B2C.
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 14eda03419e22538e17b7b4d6fa697d61cb384c8
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343703"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory B2C

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

Cet article explique comment créer une [ Blazor WebAssembly application autonome](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.

Créez un locataire ou identifiez un locataire B2C existant que l’application doit utiliser dans la Portail Azure en suivant les instructions de l’article [créer un locataire AAD B2C (documentation Azure)](/azure/active-directory-b2c/tutorial-create-tenant) .

Notez les informations suivantes :

* AAD B2C instance (par exemple, `https://contoso.b2clogin.com/` , qui comprend la barre oblique finale) : l’instance est le schéma et l’hôte d’une inscription d’application Azure B2C, que vous pouvez trouver en ouvrant la fenêtre **points de terminaison** à partir de la page **inscriptions d’applications** de la portail Azure.
* AAD B2C domaine principal/éditeur/locataire (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.

Inscrire une application AAD B2C (aide connexe dans la documentation Azure : [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)) :

::: moniker range=">= aspnetcore-5.0"

1. Dans **Azure Active Directory** > **inscriptions d’applications** , sélectionnez **nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, **Blazor AAD B2C autonome** ).
1. Pour les **types de comptes pris en charge** , sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**
1. Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` . Le port par défaut pour une application s’exécutant sur Kestrel est 5001. Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application. Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** . Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection. Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.
1. Vérifiez que **Permissions** > **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.
1. Sélectionnez **Inscription**.

Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).

Dans configurations de plateforme **d’authentification** , > **Platform configurations** > **application à page unique (Spa)** :

1. Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.
1. Pour **octroi implicite** , assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Dans **Azure Active Directory** > **inscriptions d’applications** , sélectionnez **nouvelle inscription**.
1. Fournissez un **nom** pour l’application (par exemple, **Blazor AAD B2C autonome** ).
1. Pour les **types de comptes pris en charge** , sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**
1. Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` . Le port par défaut pour une application s’exécutant sur Kestrel est 5001. Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application. Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** . Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection. Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.
1. Vérifiez que **Permissions** > **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.
1. Sélectionnez **Inscription**.

Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).

Dans **Authentication** le > **Platform configurations** > **site Web** configurations de la plateforme d’authentification :

1. Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.
1. Pour **octroi implicite** , activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

::: moniker-end

Dans la **page** d'  >  **Azure ad B2C** des  >  **flux utilisateur** :

[Créer un flux d’utilisateur d’inscription et de connexion](/azure/active-directory-b2c/tutorial-create-user-flows)

Au minimum, sélectionnez l' **Application claims**  >  attribut utilisateur **nom complet** des revendications d’application pour remplir le `context.User.Identity.Name` dans le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ).

Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple, `B2C_1_signupsignin` ).

Dans un dossier vide, remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Espace réservé                   | Nom du portail Azure               | Exemple                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | Instance                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | ID d’application (client)         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | Workflow d’inscription/de connexion       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | Domaine principal/serveur de publication/locataire | `contoso.onmicrosoft.com`              |

L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.

> [!NOTE]
> Dans le Portail Azure, l' **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.
>
> Si l’application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .
>
> Si le port n’a pas été configuré précédemment avec le port connu de l’application, revenez à l’inscription de l’application dans la Portail Azure et mettez à jour l’URI de redirection avec le port approprié.

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :

* Connectez-vous à l’application à l’aide d’un compte d’utilisateur AAD.
* Demander des jetons d’accès pour les API Microsoft. Pour plus d'informations, consultez les pages suivantes :
  * [Étendues de jeton d’accès](#access-token-scopes)
  * [Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser un compte B2C individuel ( `IndividualB2C` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.

## <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package. Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD lorsque vous inscrivez l’application.

La configuration est fournie par le `wwwroot/appsettings.json` fichier :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Exemple :

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a>Étendues de jeton d’accès

Le Blazor WebAssembly modèle ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée. Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par défaut du <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :

* [Demander des jetons d’accès supplémentaires](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Attacher des jetons aux demandes sortantes](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a>Mode de connexion

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>Fichier d’importation

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Composant d’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Composant LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/security/webassembly/additional-scenarios>
* [Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Tutoriel : Créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
