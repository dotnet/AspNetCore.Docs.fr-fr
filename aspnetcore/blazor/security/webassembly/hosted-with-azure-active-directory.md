---
title: Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core hébergée avec Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 17f96be762ece8c59577445eb2ae630a8ee3b3dd
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234476"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Pour les Blazor WebAssembly applications créées dans Visual Studio qui sont configurées pour prendre en charge les comptes dans un annuaire d’organisation AAD, Visual Studio ne configure pas correctement l’application lors de la génération du projet. Cela sera résolu dans une prochaine version de Visual Studio. Cet article explique comment créer l’application avec la commande de l’CLI .NET Core `dotnet new` . Si vous préférez créer l’application avec Visual Studio avant la mise à jour de l’IDE pour les modèles les plus récents Blazor dans ASP.NET Core 5,0, reportez-vous à chaque section de cet article et confirmez ou mettez à jour la configuration de l’application une fois que Visual Studio a créé l’application.

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a>Inscrire des applications dans AAD et créer une solution

### <a name="create-a-tenant"></a>Créer un locataire

Suivez les instructions de [démarrage rapide : configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.

### <a name="register-a-server-api-app"></a>Inscrire une application API serveur

Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application API serveur* , puis procédez comme suit :

1. Dans **Azure Active Directory**  >  **inscriptions d’applications** , sélectionnez **nouvelle inscription** .
1. Fournissez un **nom** pour l’application (par exemple, **Blazor Server AAD** ).
1. Choisissez un **type de compte pris en charge** . Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).
1. L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.
1. Désactivez **la case**  >  à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .
1. Sélectionnez **Inscription** .

Notez les informations suivantes :

* *Application API serveur* ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* ID de répertoire (locataire) (par exemple, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )
* Le domaine principal/serveur de publication/client AAD (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.

Dans **autorisations d’API** , supprimez l’autorisation **Microsoft Graph**  >  **User. Read** , car l’application ne nécessite pas d’accès de connexion ou de profil utilisateur.

Dans **exposer une API** :

1. sélectionner **Ajouter une étendue** .
1. Sélectionnez **Enregistrer et continuer** .
1. Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).
1. Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).
1. Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).
1. Confirmez que l' **État** est défini sur **activé** .
1. Sélectionnez **Ajouter une étendue** .

Notez les informations suivantes :

* URI ID d’application (par exemple,, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ou la valeur personnalisée que vous fournissez)
* Nom de l’étendue (par exemple, `API.Access` )

### <a name="register-a-client-app"></a>Inscrire une application cliente

Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *`Client`* application, puis procédez comme suit :

::: moniker range=">= aspnetcore-5.0"

1. Dans **Azure Active Directory** > **inscriptions d’applications** , sélectionnez **nouvelle inscription** .
1. Fournissez un **nom** pour l’application (par exemple, **Blazor client AAD** ).
1. Choisissez un **type de compte pris en charge** . Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).
1. Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` . Le port par défaut pour une application s’exécutant sur Kestrel est 5001. Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application. Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les *`Server`* Propriétés de l’application dans le panneau **débogage** . Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection. Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.
1. Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .
1. Sélectionnez **Inscription** .

Enregistrez l' *`Client`* ID de l’application (client) de l’application (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Dans configurations de plateforme **d’authentification** , > **Platform configurations** > **application à page unique (Spa)** :

1. Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.
1. Pour **octroi implicite** , assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer** .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Dans **Azure Active Directory** > **inscriptions d’applications** , sélectionnez **nouvelle inscription** .
1. Fournissez un **nom** pour l’application (par exemple, **Blazor client AAD** ).
1. Choisissez un **type de compte pris en charge** . Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).
1. Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` . Le port par défaut pour une application s’exécutant sur Kestrel est 5001. Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application. Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les *`Server`* Propriétés de l’application dans le panneau **débogage** . Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection. Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.
1. Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .
1. Sélectionnez **Inscription** .

Enregistrez l' *`Client`* ID de l’application (client) de l’application (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Dans **Authentication** le > **Platform configurations** > **site Web** configurations de la plateforme d’authentification :

1. Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.
1. Pour **octroi implicite** , activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** .
1. Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.
1. Sélectionnez le bouton **Enregistrer** .

::: moniker-end

Dans **autorisations d’API** :

1. Vérifiez que l’application a **Microsoft Graph**  >  autorisation **User. Read** .
1. Sélectionnez **Ajouter une autorisation** suivi de **mes API** .
1. Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, **Blazor Server AAD** ).
1. Ouvrez la liste des **API** .
1. Activez l’accès à l’API (par exemple, `API.Access` ).
1. Sélectionnez **Ajouter des autorisations** .
1. Sélectionnez le bouton **accorder le consentement de l’administrateur pour {nom du locataire}** . Sélectionnez **Oui** pour confirmer l’opération.

### <a name="create-the-app"></a>Créer l’application

Dans un dossier vide, remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Espace réservé                  | Nom du portail Azure                                     | Exemple                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | ID de l’application (client) pour l' *`Client`* application        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | Nom de l’étendue                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | ID de l’application (client) pour l’application *API serveur*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | URI d’ID d’application&dagger;                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | Domaine principal/serveur de publication/locataire                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | ID de l’annuaire (locataire)                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

&dagger;Le Blazor WebAssembly modèle ajoute automatiquement un schéma de `api://` à l’argument d’URI ID d’application passé dans la `dotnet new` commande. Lorsque vous fournissez l’URI ID d’application pour l' `{SERVER API APP ID URI}` espace réservé et si le schéma est `api://` , supprimez le schéma ( `api://` ) de l’argument, comme le montre l’exemple de valeur dans le tableau précédent. Si l’URI ID d’application est une valeur personnalisée ou s’il en existe un autre (par exemple, `https://` pour un domaine du serveur de publication non approuvé similaire à `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), vous devez mettre à jour manuellement l’URI de l’étendue par défaut et supprimer le `api://` schéma une fois que l' *`Client`* application a été créée par le modèle. Pour plus d’informations, consultez la remarque dans la section [étendues de jetons d’accès](#access-token-scopes) . Le Blazor WebAssembly modèle peut être modifié dans une version ultérieure de ASP.net Core pour résoudre ces scénarios. Pour plus d’informations, consultez [schéma double pour l’URI ID d’application avec Blazor modèle WASM (hébergé, unique org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Une modification de configuration peut être nécessaire lors de l’utilisation d’un client Azure avec un domaine d’éditeur non vérifié, qui est décrit dans la section paramètres de l' [application](#app-settings) .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Une modification de configuration peut être nécessaire lors de l’utilisation d’un client Azure avec un domaine d’éditeur non vérifié, qui est décrit dans la section [étendues de jeton d’accès](#access-token-scopes) .

::: moniker-end

> [!NOTE]
> Dans le Portail Azure, l' *`Client`* **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.
>
> Si l' *`Client`* application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l' *application API serveur* dans le panneau **débogage** .
>
> Si le port n’a pas été configuré précédemment avec le *`Client`* port connu de l’application, revenez à l’inscription de l' *`Client`* application dans la portail Azure et mettez à jour l’URI de redirection avec le port approprié.

## <a name="server-app-configuration"></a>*`Server`* configuration de l’application

*Cette section se rapporte à l’application de la solution **`Server`** .*

### <a name="authentication-package"></a>Package d’authentification

::: moniker range=">= aspnetcore-5.0"

La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core les API Web avec la Identity plate-forme Microsoft est assurée par les packages suivants :

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur NuGet.org.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) Package :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

::: moniker-end

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

::: moniker range=">= aspnetcore-5.0"

La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut. La <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> méthode Configure les services pour protéger l’API Web avec Microsoft Identity Platform v 2.0. Cette méthode attend une `AzureAd` section dans la configuration de l’application avec les paramètres nécessaires pour initialiser les options d’authentification.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut. La <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory :

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Assurez-vous que :

* L’application tente d’analyser et de valider les jetons sur les demandes entrantes.
* Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>Utilisateur. Identity . Nomme

Par défaut, l' *`Server`* API d’application remplit `User.Identity.Name` la valeur du type de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` revendication (par exemple, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).

Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication, configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Paramètres de l’application

::: moniker range=">= aspnetcore-5.0"

Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

Exemple :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Exemple :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a>Contrôleur WeatherForecast

Le contrôleur WeatherForecast ( *Controllers/WeatherForecastController. cs* ) expose une API protégée avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut appliqué au contrôleur. Il est **important** de comprendre que :

* L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.
* L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut utilisé dans l' Blazor WebAssembly application sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>*`Client`* configuration de l’application

*Cette section se rapporte à l’application de la solution **`Client`** .*

### <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.

### <a name="authentication-service-support"></a>Prise en charge du service d’authentification

La prise en charge des <xref:System.Net.Http.HttpClient> instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.ServerAPI` ).

La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package. Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application. Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.

La configuration est fournie par le `wwwroot/appsettings.json` fichier :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Exemple :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Étendues de jeton d’accès

Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :

* Inclus par défaut dans la demande de connexion.
* Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.

Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles. Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Le Blazor WebAssembly modèle ajoute automatiquement un schéma de `api://` à l’argument d’URI ID d’application passé dans la `dotnet new` commande. Lors de la génération d’une application à partir du Blazor modèle de projet, vérifiez que la valeur de l’étendue du jeton d’accès par défaut utilise la valeur d’URI ID d’application personnalisée correcte que vous avez fournie dans le portail Azure ou une valeur avec l' **un** des formats suivants :
>
> * Lorsque le domaine de l’éditeur de l’annuaire est **approuvé** , l’étendue du jeton d’accès par défaut est généralement une valeur similaire à celle de l’exemple suivant, où `API.Access` est le nom de l’étendue par défaut :
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Inspectez la valeur d’un schéma double ( `api://api://...` ). Si un double schéma est présent, supprimez le premier `api://` schéma de la valeur.
>
> * Lorsque le domaine du serveur de publication de l’annuaire n’est pas **approuvé** , l’étendue du jeton d’accès par défaut est généralement une valeur similaire à celle de l’exemple suivant, où `API.Access` est le nom de l’étendue par défaut :
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Inspectez la valeur d’un `api://` schéma supplémentaire ( `api://https://contoso.onmicrosoft.com/...` ). Si un `api://` schéma supplémentaire est présent, supprimez le `api://` schéma de la valeur.
>
> Le Blazor WebAssembly modèle peut être modifié dans une version ultérieure de ASP.net Core pour résoudre ces scénarios. Pour plus d’informations, consultez [schéma double pour l’URI ID d’application avec Blazor modèle WASM (hébergé, unique org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

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

### <a name="login-mode"></a>Mode de connexion

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Fichier d’importation

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Page d'index

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Composant d’application

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Composant LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Composant FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Exécuter l’application

Exécutez l’application à partir du projet serveur. Lorsque vous utilisez Visual Studio, vous pouvez :

* Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .
* Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/security/webassembly/additional-scenarios>
* [Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentation sur la plateforme d’identités Microsoft](/azure/active-directory/develop/)
