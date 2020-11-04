---
title: Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec des comptes Microsoft
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core autonome avec des comptes Microsoft.
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 4ad4a70c92ce8dd61b676dd7d35ecb4f3b4fa99f
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343687"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec des comptes Microsoft

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

Cet article explique comment créer une [ Blazor WebAssembly application autonome](xref:blazor/hosting-models#blazor-webassembly) qui utilise [des comptes Microsoft avec Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pour l’authentification.

Inscrire une application AAD dans la **Azure Active Directory**  >  zone de **inscriptions d’applications** Azure Active Directory de l’portail Azure :

::: moniker range=">= aspnetcore-5.0"

1. Fournissez un **nom** pour l’application (par exemple, **Blazor comptes Microsoft AAD autonomes** ).
1. Dans **types de comptes pris en charge** , sélectionnez **comptes dans n’importe quel annuaire d’organisation**.
1. Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` . Le port par défaut pour une application s’exécutant sur Kestrel est 5001. Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application. Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** . Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection. Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.
1. Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .
1. Sélectionnez **Inscription**.

Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).

Dans configurations de plateforme **d’authentification** , > **Platform configurations** > **application à page unique (Spa)** :

1. Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.
1. Pour **octroi implicite** , assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Fournissez un **nom** pour l’application (par exemple, **Blazor comptes Microsoft AAD autonomes** ).
1. Dans **types de comptes pris en charge** , sélectionnez **comptes dans n’importe quel annuaire d’organisation**.
1. Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` . Le port par défaut pour une application s’exécutant sur Kestrel est 5001. Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application. Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** . Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection. Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.
1. Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .
1. Sélectionnez **Inscription**.

Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).

Dans **Authentication** le > **Platform configurations** > **site Web** configurations de la plateforme d’authentification :

1. Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.
1. Pour **octroi implicite** , activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer**.

::: moniker-end

Créez l'application. Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande suivante dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| Espace réservé   | Nom du portail Azure       | Exemple                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | ID d’application (client) | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

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

* Connectez-vous à l’application à l’aide d’un compte Microsoft.
* Demander des jetons d’accès pour les API Microsoft. Pour plus d'informations, consultez les pages suivantes :
  * [Étendues de jeton d’accès](#access-token-scopes)
  * [Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

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
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD lorsque vous inscrivez l’application.

La configuration est fournie par le `wwwroot/appsettings.json` fichier :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Exemple :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
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

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/security/webassembly/additional-scenarios>
* [Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [Démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Démarrage rapide : Configurer une application pour exposer des API web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
