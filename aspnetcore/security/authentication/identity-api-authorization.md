---
title: Présentation de l’authentification pour les applications à page unique sur ASP.NET Core
author: javiercn
description: Utilisez Identity avec une application à page unique hébergée dans une application ASP.net core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 8acc34c88bf62b3da1b920acc7318c94435c100e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051978"
---
# <a name="authentication-and-authorization-for-spas"></a>Authentification et autorisation pour SPAs

Les modèles ASP.NET Core 3,1 et versions ultérieures offrent une authentification dans les applications à page unique (SPAs) à l’aide de la prise en charge de l’autorisation de l’API. ASP.NET Core Identitypour l’authentification et le stockage des utilisateurs est associé au [ Identity serveur](https://identityserver.io/) pour l’implémentation de OpenID Connect.

Un paramètre d’authentification a été ajouté aux modèles de projet **angulaire** et **REACT** qui est similaire au paramètre d’authentification dans les modèles de projet **application Web (Model-View-Controller)** et **application Web** ( Razor pages). Les valeurs de paramètre autorisées sont **None** et **Individual** . Le modèle de projet **React.js et Redux** ne prend pas en charge le paramètre d’authentification pour l’instant.

## <a name="create-an-app-with-api-authorization-support"></a>Créer une application avec prise en charge des autorisations d’API

L’authentification et l’autorisation de l’utilisateur peuvent être utilisées avec les deux types d’angle et de réaction. Ouvrez une interface de commande, puis exécutez la commande suivante :

**Angulaire** :

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Réaction** :

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

La commande précédente crée une application ASP.NET Core avec un répertoire *ClientApp* contenant le Spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Description générale des composants ASP.NET Core de l’application

Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse :

### <a name="startup-class"></a>Classe de démarrage

Les exemples de code suivants reposent sur [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Package NuGet du serveur. Les exemples configurent l’authentification et l’autorisation des API à l’aide des <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> méthodes d’extension et. Les projets qui utilisent les modèles de projet REACT ou SPA angulaire avec l’authentification incluent une référence à ce package.

La `Startup` classe comporte les ajouts suivants :

* À l’intérieur de la `Startup.ConfigureServices` méthode :
  * Identity avec l’interface utilisateur par défaut :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServeur avec une `AddApiAuthorization` méthode d’assistance supplémentaire qui définit des conventions de ASP.net Core par défaut sur le Identity serveur :

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentification avec une `AddIdentityServerJwt` méthode d’assistance supplémentaire qui configure l’application pour valider les jetons JWT générés par le Identity serveur :

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* À l’intérieur de la `Startup.Configure` méthode :
  * Intergiciel d’authentification responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :

    ```csharp
    app.UseAuthentication();
    ```

  * L' Identity intergiciel de serveur qui expose les points de terminaison OpenID Connect :

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Cette méthode d’assistance configure le Identity serveur pour qu’il utilise notre configuration prise en charge. IdentityLe serveur est un Framework puissant et extensible pour gérer les problèmes de sécurité des applications. En même temps, cela expose une complexité inutile pour les scénarios les plus courants. Par conséquent, un ensemble de conventions et d’options de configuration qui vous sont fournies est considéré comme un bon point de départ. Une fois vos besoins d’authentification modifiés, toute la puissance du Identity serveur est toujours disponible pour personnaliser l’authentification en fonction de vos besoins.

### <a name="addno-locidentityserverjwt"></a>Ajouter Identity ServerJwt

Cette méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut. La stratégie est configurée pour permettre à de Identity gérer toutes les demandes routées vers n’importe quel sous-chemin dans l' Identity espace d’URL « / Identity ». Le `JwtBearerHandler` gère toutes les autres requêtes. En outre, cette méthode enregistre une `<<ApplicationName>>API` ressource API avec Identity le serveur avec une étendue par défaut `<<ApplicationName>>API` et configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par Identity le serveur pour l’application.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

Dans le fichier *Controllers\WeatherForecastController.cs* , notez l' `[Authorize]` attribut appliqué à la classe qui indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource. La stratégie d’autorisation par défaut doit être configurée pour utiliser le schéma d’authentification par défaut, qui est configuré par `AddIdentityServerJwt` le modèle de stratégie mentionné plus haut, ce qui fait du `JwtBearerHandler` configuré par une telle méthode d’assistance le gestionnaire par défaut pour les demandes à l’application.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Dans le fichier *Data\ApplicationDbContext.cs* , Notez que le même `DbContext` est utilisé dans, à Identity l’exception qu’il étend `ApiAuthorizationDbContext` (une classe plus dérivée de `IdentityDbContext` ) pour inclure le schéma pour le Identity serveur.

Pour obtenir le contrôle total du schéma de base de données, héritez de l’une des Identity `DbContext` classes disponibles et configurez le contexte pour inclure le Identity schéma en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` sur la `OnModelCreating` méthode.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Dans le fichier *Controllers\OidcConfigurationController.cs* , notez le point de terminaison configuré pour servir les paramètres OIDC que le client doit utiliser.

### appsettings.json

Dans le *appsettings.json* fichier de la racine du projet, une nouvelle `IdentityServer` section décrit la liste des clients configurés. Dans l’exemple suivant, il existe un seul client. Le nom du client correspond au nom de l’application et est mappé par Convention au `ClientId` paramètre OAuth. Le profil indique le type d’application en cours de configuration. Il est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur. Plusieurs profils sont disponibles, comme expliqué dans la section [profils d’application](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.js

Dans le *appsettings.Development.js* fichier de la racine du projet, il existe une `IdentityServer` section qui décrit la clé utilisée pour signer les jetons. Lors du déploiement en production, une clé doit être approvisionnée et déployée parallèlement à l’application, comme expliqué dans la section [déploiement en production](#deploy-to-production) .

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Description générale de l’application angulaire

L’authentification et la prise en charge de l’autorisation d’API dans le modèle angulaire résident dans son propre module angulaire dans le répertoire *ClientApp\src\api-Authorization* . Le module est composé des éléments suivants :

* 3 composants :
  * *login. Component. TS* : gère le déroulement de la connexion de l’application.
  * *logout. Component. TS* : gère le déroulement de la déconnexion de l’application.
  * *login-menu. Component. TS* : un widget qui affiche l’un des ensembles de liens suivants :
    * Gestion des profils utilisateur et déconnexion des liens lorsque l’utilisateur est authentifié.
    * Enregistrement et connexion des liens lorsque l’utilisateur n’est pas authentifié.
* Une protection de routage `AuthorizeGuard` qui peut être ajoutée aux itinéraires et requiert qu’un utilisateur soit authentifié avant de visiter l’itinéraire.
* Intercepteur HTTP `AuthorizeInterceptor` qui associe le jeton d’accès aux requêtes http sortantes ciblant l’API lorsque l’utilisateur est authentifié.
* Service `AuthorizeService` qui gère les détails de niveau inférieur du processus d’authentification et expose des informations sur l’utilisateur authentifié au reste de l’application à des fins de consommation.
* Module angulaire qui définit les itinéraires associés aux parties d’authentification de l’application. Il expose le composant de menu de connexion, l’intercepteur, la protection et le service à consommer à partir du reste de l’application.

## <a name="general-description-of-the-react-app"></a>Description générale de l’application REACT

La prise en charge de l’authentification et de l’autorisation d’API dans le modèle REACT réside dans le répertoire *ClientApp\src\components\api-Authorization* . Il se compose des éléments suivants :

* 4 composants :
  * *Login.js* : gère le déroulement de la connexion de l’application.
  * *Logout.js* : gère le déroulement de la déconnexion de l’application.
  * *LoginMenu.js* : un widget qui affiche l’un des ensembles de liens suivants :
    * Gestion des profils utilisateur et déconnexion des liens lorsque l’utilisateur est authentifié.
    * Enregistrement et connexion des liens lorsque l’utilisateur n’est pas authentifié.
  * *AuthorizeRoute.js* : composant de routage qui requiert l’authentification d’un utilisateur avant le rendu du composant indiqué dans le `Component` paramètre.
* Instance exportée `authService` de la classe `AuthorizeService` qui gère les détails de niveau inférieur du processus d’authentification et expose des informations sur l’utilisateur authentifié au reste de l’application à des fins de consommation.

Maintenant que vous avez vu les principaux composants de la solution, vous pouvez examiner de manière plus détaillée les scénarios individuels de l’application.

## <a name="require-authorization-on-a-new-api"></a>Exiger une autorisation sur une nouvelle API

Par défaut, le système est configuré pour demander facilement une autorisation pour les nouvelles API. Pour ce faire, créez un nouveau contrôleur et ajoutez l' `[Authorize]` attribut à la classe de contrôleur ou à n’importe quelle action dans le contrôleur.

## <a name="customize-the-api-authentication-handler"></a>Personnaliser le gestionnaire d’authentification d’API

Pour personnaliser la configuration du gestionnaire JWT de l’API, configurez son <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance :

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

Le gestionnaire JWT de l’API déclenche des événements qui permettent de contrôler le processus d’authentification à l’aide de `JwtBearerEvents` . Pour assurer la prise en charge de l’autorisation d’API, `AddIdentityServerJwt` inscrit ses propres gestionnaires d’événements.

Pour personnaliser la gestion d’un événement, encapsulez le gestionnaire d’événements existant avec une logique supplémentaire, le cas échéant. Exemple :

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

Dans le code précédent, le `OnTokenValidated` Gestionnaire d’événements est remplacé par une implémentation personnalisée. Cette implémentation :

1. Appelle l’implémentation d’origine fournie par la prise en charge de l’autorisation d’API.
1. Exécutez sa propre logique personnalisée.

## <a name="protect-a-client-side-route-angular"></a>Protéger un itinéraire côté client (angulaire)

La protection d’un itinéraire côté client s’effectue en ajoutant la protection d’autorisation à la liste des gardes à exécuter lors de la configuration d’un itinéraire. Par exemple, vous pouvez voir comment l' `fetch-data` itinéraire est configuré dans le module angulaire principal de l’application :

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Il est important de mentionner que la protection d’un itinéraire ne protège pas le point de terminaison réel (qui requiert toujours un `[Authorize]` attribut qui lui est appliqué), mais qu’il empêche uniquement l’utilisateur de naviguer vers l’itinéraire donné côté client lorsqu’il n’est pas authentifié.

## <a name="authenticate-api-requests-angular"></a>Authentifier les demandes d’API (angulaires)

L’authentification des demandes auprès des API hébergées parallèlement à l’application s’effectue automatiquement par le biais de l’intercepteur client HTTP défini par l’application.

## <a name="protect-a-client-side-route-react"></a>Protéger un itinéraire côté client (REACT)

Protégez un itinéraire côté client à l’aide du `AuthorizeRoute` composant au lieu du `Route` composant simple. Par exemple, notez la configuration de l' `fetch-data` itinéraire dans le `App` composant :

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Protection d’un itinéraire :

* Ne protège pas le point de terminaison réel (qui requiert toujours un `[Authorize]` attribut qui lui est appliqué).
* Empêche uniquement l’utilisateur de naviguer vers l’itinéraire donné côté client lorsqu’il n’est pas authentifié.

## <a name="authenticate-api-requests-react"></a>Authentifier les demandes d’API (REACT)

L’authentification des demandes avec REACT est effectuée en important `authService` d’abord l’instance à partir du `AuthorizeService` . Le jeton d’accès est récupéré à partir de `authService` et est attaché à la demande, comme indiqué ci-dessous. Dans les composants REACT, ce travail est généralement effectué dans la `componentDidMount` méthode Lifecycle ou en tant que résultat de certaines interactions de l’utilisateur.

### <a name="import-the-authservice-into-your-component"></a>Importer le authService dans votre composant

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Récupérer et attacher le jeton d’accès à la réponse

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Déployer en production

Pour déployer l’application en production, vous devez configurer les ressources suivantes :

* Une base de données pour stocker les Identity comptes d’utilisateur et le Identity serveur.
* Certificat de production à utiliser pour la signature des jetons.
  * Il n’existe aucune exigence spécifique pour ce certificat. Il peut s’agir d’un certificat auto-signé ou d’un certificat approvisionné par le biais d’une autorité de certification.
  * Il peut être généré à l’aide d’outils standard tels que PowerShell ou OpenSSL.
  * Il peut être installé dans le magasin de certificats sur les ordinateurs cibles ou déployé en tant que fichier *. pfx* avec un mot de passe fort.

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a>Exemple : déployer sur un fournisseur d’hébergement Web non Azure

Dans le volet d’hébergement Web, créez ou chargez votre certificat. Ensuite, dans le fichier de l’application *appsettings.json* , modifiez la `IdentityServer` section pour inclure les détails de la clé. Exemple :

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

Dans l’exemple précédent :

* `StoreName` représente le nom du magasin de certificats dans lequel le certificat est stocké. Dans ce cas, il pointe vers le magasin d’hébergement Web.
* `StoreLocation` représente l’emplacement à partir duquel le certificat doit être chargé ( `CurrentUser` dans le cas présent).
* `Name` correspond au sujet distinctif du certificat.

### <a name="example-deploy-to-azure-app-service"></a>Exemple : déployer sur Azure App Service

Cette section décrit le déploiement de l’application sur Azure App Service à l’aide d’un certificat stocké dans le magasin de certificats. Pour modifier l’application afin de charger un certificat à partir du magasin de certificats, un plan de service de niveau standard ou mieux est requis lorsque vous configurez l’application dans le Portail Azure à une étape ultérieure.

Dans le fichier de l’application *appsettings.json* , modifiez la `IdentityServer` section pour inclure les détails de la clé :

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* Le nom du magasin représente le nom du magasin de certificats dans lequel le certificat est stocké. Dans ce cas, il pointe vers le magasin de l’utilisateur personnel.
* L’emplacement du magasin représente l’emplacement de chargement du certificat à partir de ( `CurrentUser` ou `LocalMachine` ).
* La propriété Name du certificat correspond au sujet distinctif du certificat.

Pour effectuer un déploiement sur Azure App Service, suivez les étapes de la section [déployer l’application sur Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), qui explique comment créer les ressources Azure nécessaires et comment déployer l’application en production.

Après avoir effectué les instructions précédentes, l’application est déployée dans Azure, mais n’est pas encore fonctionnelle. Le certificat utilisé par l’application doit être configuré dans la Portail Azure. Localisez l’empreinte numérique du certificat et suivez les étapes décrites dans [charger vos certificats](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Bien que ces étapes mentionnent SSL, il existe une section **Private Certificates** dans le portail Azure où vous pouvez télécharger le certificat approvisionné à utiliser avec l’application.

Après avoir configuré l’application et les paramètres de l’application dans la Portail Azure, redémarrez l’application dans le portail.

## <a name="other-configuration-options"></a>Autres options de configuration

La prise en charge de l’autorisation d’API s’appuie sur le Identity serveur avec un ensemble de conventions, de valeurs par défaut et d’améliorations pour simplifier l’expérience de la création de la base de code. Inutile de préciser que la pleine puissance du Identity serveur est disponible en arrière-plan si les intégrations de ASP.net Core ne couvrent pas votre scénario. La prise en charge ASP.NET Core est axée sur les applications « internes », où toutes les applications sont créées et déployées par notre organisation. Par conséquent, le support n’est pas proposé pour les éléments tels que le consentement ou la Fédération. Pour ces scénarios, utilisez Identity le serveur et suivez leur documentation.

### <a name="application-profiles"></a>Profils d’application

Les profils d’application sont des configurations prédéfinies pour les applications qui définissent davantage leurs paramètres. À ce stade, les profils suivants sont pris en charge :

* `IdentityServerSPA`: Représente un SPA hébergé Identity en même temps que le serveur en tant qu’unité unique.
  * La `redirect_uri` valeur par défaut est `/authentication/login-callback` .
  * La `post_logout_redirect_uri` valeur par défaut est `/authentication/logout-callback` .
  * L’ensemble d’étendues comprend `openid` , `profile` et toutes les étendues définies pour les API dans l’application.
  * L’ensemble des types de réponses OIDC autorisés est `id_token token` ou chacun d’eux individuellement ( `id_token` , `token` ).
  * Le mode de réponse autorisé est `fragment` .
* `SPA`: Représente un SPA qui n’est pas hébergé avec le Identity serveur.
  * L’ensemble d’étendues comprend `openid` , `profile` et toutes les étendues définies pour les API dans l’application.
  * L’ensemble des types de réponses OIDC autorisés est `id_token token` ou chacun d’eux individuellement ( `id_token` , `token` ).
  * Le mode de réponse autorisé est `fragment` .
* `IdentityServerJwt`: Représente une API hébergée en même temps que le Identity serveur.
  * L’application est configurée pour avoir une seule étendue qui correspond par défaut au nom de l’application.
* `API`: Représente une API qui n’est pas hébergée sur le Identity serveur.
  * L’application est configurée pour avoir une seule étendue qui correspond par défaut au nom de l’application.

### <a name="configuration-through-appsettings"></a>Configuration via AppSettings

Configurez les applications par le biais du système de configuration en les ajoutant à la liste de `Clients` ou `Resources` .

Configurez les `redirect_uri` Propriétés et de chaque client `post_logout_redirect_uri` , comme indiqué dans l’exemple suivant :

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Lors de la configuration des ressources, vous pouvez configurer les étendues de la ressource comme indiqué ci-dessous :

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Configuration par le biais du code

Vous pouvez également configurer les clients et les ressources par le biais du code à l’aide d’une surcharge de `AddApiAuthorization` qui prend une mesure pour configurer les options.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
