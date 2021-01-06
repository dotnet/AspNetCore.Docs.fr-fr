---
title: Sécuriser une application ASP.NET Core hébergée Blazor WebAssembly avec le Identity serveur
author: guardrex
description: Découvrez comment sécuriser une application ASP.NET Core hébergée Blazor WebAssembly avec le Identity serveur.
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 80196945bc6891d5517d7da0e07ca1b0debddd28
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854681"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec le Identity serveur

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise le [ Identity serveur](https://identityserver.io/) pour authentifier les utilisateurs et les appels d’API.

> [!NOTE]
> Pour configurer une application autonome ou hébergée Blazor WebAssembly afin d’utiliser une instance de serveur externe existante Identity , suivez les instructions dans <xref:blazor/security/webassembly/standalone-with-authentication-library> .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour créer un nouveau Blazor WebAssembly projet avec un mécanisme d’authentification :

1. Après avoir choisi le modèle d' **Blazor WebAssembly application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.

1. Sélectionnez **des comptes d’utilisateur individuels** avec l’option **stocker les comptes d’utilisateur dans l’application** pour stocker les utilisateurs au sein de l’application à l’aide du système de ASP.net Core [Identity](xref:security/authentication/identity) .

1. Activez la case à cocher **ASP.net Core hébergée** dans la section **avancé** .

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

Pour créer un Blazor WebAssembly projet avec un mécanisme d’authentification dans un dossier vide, spécifiez le `Individual` mécanisme d’authentification avec l' `-au|--auth` option permettant de stocker les utilisateurs au sein de l’application à l’aide du système de ASP.net Core [Identity](xref:security/authentication/identity) :

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| Espace réservé  | Exemple        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.

Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Pour créer un nouveau Blazor WebAssembly projet avec un mécanisme d’authentification :

1. Dans l’étape **configurer votre nouvelle Blazor WebAssembly application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .

1. L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core [Identity](xref:security/authentication/identity) .

1. Activez la case à cocher **ASP.net Core hébergé** .

---

## <a name="server-app-configuration"></a>*`Server`* configuration de l’application

Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse.

### <a name="startup-class"></a>Classe de démarrage

La `Startup` classe a les ajouts suivants.

* Dans `Startup.ConfigureServices` :

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServeur avec une <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance supplémentaire qui définit les conventions de ASP.net Core par défaut sur le Identity serveur :

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentification avec une <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance supplémentaire qui configure l’application pour valider les jetons JWT générés par le Identity serveur :

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Dans `Startup.Configure` :

  * L' Identity intergiciel (middleware) de serveur expose les points de terminaison OpenID Connect (OIDC) :

    ```csharp
    app.UseIdentityServer();
    ```

  * L’intergiciel d’authentification est responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :

    ```csharp
    app.UseAuthentication();
    ```

  * L’intergiciel d’autorisation active les fonctionnalités d’autorisation :

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

La <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance configure [ Identity Server](https://identityserver.io/) pour les scénarios de ASP.net core. IdentityLe serveur est un Framework puissant et extensible pour gérer les problèmes de sécurité des applications. IdentityLe serveur expose une complexité inutile pour les scénarios les plus courants. Par conséquent, un ensemble de conventions et d’options de configuration est fourni, que nous considérons comme un bon point de départ. Une fois vos besoins d’authentification modifiés, toute la puissance du Identity serveur est disponible pour personnaliser l’authentification en fonction des exigences d’une application.

### <a name="addno-locidentityserverjwt"></a>Ajouter Identity ServerJwt

La <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut. La stratégie est configurée pour autoriser Identity à gérer toutes les demandes routées vers n’importe quel sous-chemin dans l' Identity espace d’URL `/Identity` . Le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gère toutes les autres requêtes. En outre, cette méthode :

* Inscrit une `{APPLICATION NAME}API` ressource API avec Identity le serveur avec une étendue par défaut de `{APPLICATION NAME}API` .
* Configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par Identity le serveur pour l’application.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

Dans `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est appliqué à la classe. L’attribut indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource. La stratégie d’autorisation par défaut est configurée pour utiliser le schéma d’authentification par défaut, qui est configuré par <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . La méthode d’assistance configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> en tant que gestionnaire par défaut pour les demandes à l’application.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Dans le `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> s’étend pour inclure le schéma pour le Identity serveur. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> est dérivé de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Pour obtenir le contrôle total du schéma de base de données, héritez de l’une des Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponibles et configurez le contexte pour inclure le Identity schéma en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dans la <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> méthode.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Dans le `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), le point de terminaison client est approvisionné pour servir les paramètres OIDC.

### <a name="app-settings"></a>Paramètres de l’application

Dans le fichier de paramètres d’application ( `appsettings.json` ) à la racine du projet, la `IdentityServer` section décrit la liste des clients configurés. Dans l’exemple suivant, il existe un seul client. Le nom du client correspond au nom de l’application et est mappé par Convention au `ClientId` paramètre OAuth. Le profil indique le type d’application en cours de configuration. Le profil est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.Client` ).

## <a name="client-app-configuration"></a>*`Client`* configuration de l’application

### <a name="authentication-package"></a>Package d’authentification

Quand une application est créée pour utiliser des comptes d’utilisateur individuels ( `Individual` ), l’application reçoit automatiquement une référence de package pour le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package dans le fichier projet de l’application. Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.

Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

### <a name="httpclient-configuration"></a>`HttpClient` configuré

Dans `Program.Main` ( `Program.cs` ), un nommé <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) est configuré pour fournir des <xref:System.Net.Http.HttpClient> instances qui incluent des jetons d’accès lors de l’exécution de requêtes à l’API serveur :

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Si vous configurez une Blazor WebAssembly application pour qu’elle utilise une Identity instance de serveur existante qui ne fait pas partie d’une solution hébergée Blazor , modifiez l' <xref:System.Net.Http.HttpClient> inscription de l’adresse de base <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) en spécifiant l’URL du point de terminaison d’autorisation d’API de l’application serveur.

### <a name="api-authorization-support"></a>Prise en charge des autorisations d’API

La prise en charge de l’authentification des utilisateurs est insérée dans le conteneur de service par la méthode d’extension fournie dans le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) Package. Cette méthode Configure les services requis par l’application pour interagir avec le système d’autorisation existant.

```csharp
builder.Services.AddApiAuthorization();
```

Par défaut, la configuration de l’application est chargée par Convention à partir de `_configuration/{client-id}` . Par Convention, l’ID client est défini sur le nom de l’assembly de l’application. Cette URL peut être modifiée pour pointer vers un point de terminaison distinct en appelant la surcharge avec des options.

### <a name="imports-file"></a>Fichier d’importation

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a>Page d'index

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a>Composant d’application

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a>Composant RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Composant LoginDisplay

Le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ) est rendu dans le `MainLayout` composant ( `Shared/MainLayout.razor` ) et gère les comportements suivants :

* Pour les utilisateurs authentifiés :
  * Affiche le nom de l’utilisateur actuel.
  * Propose un lien vers la page de profil utilisateur dans ASP.NET Core Identity .
  * Offre un bouton permettant de se déconnecter de l’application.
* Pour les utilisateurs anonymes :
  * Offre la possibilité de s’inscrire.
  * Offre la possibilité de se connecter.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Composant d’authentification

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a>Composant FetchData

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a>Exécuter l’application

Exécutez l’application à partir du projet serveur. Lorsque vous utilisez Visual Studio, vous pouvez :

* Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .
* Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .

## <a name="name-and-role-claim-with-api-authorization"></a>Nom et revendication de rôle avec autorisation d’API

### <a name="custom-user-factory"></a>Fabrique d’utilisateur personnalisée

Dans l' *`Client`* application, créez une fabrique d’utilisateurs personnalisée. Identity Le serveur envoie plusieurs rôles sous forme de tableau JSON dans une seule `role` revendication. Un rôle unique est envoyé sous la forme d’une valeur de chaîne dans la revendication. La fabrique crée une `role` revendication individuelle pour chacun des rôles de l’utilisateur.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

Dans l' *`Client`* application, inscrivez la fabrique dans `Program.Main` ( `Program.cs` ) :

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

Dans l' *`Server`* application, appelez <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> sur le Identity générateur, qui ajoute des services liés aux rôles :

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a>Configurer le Identity serveur

Utilisez l' **une** des approches suivantes :

* [Options d’autorisation d’API](#api-authorization-options)
* [Service de profil](#profile-service)

#### <a name="api-authorization-options"></a>Options d’autorisation d’API

Dans l' *`Server`* application :

* Configurez Identity le serveur pour placer les `name` `role` revendications et dans le jeton d’ID et le jeton d’accès.
* Empêche le mappage par défaut des rôles dans le gestionnaire de jetons JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Service de profil

Dans l' *`Server`* application, créez une `ProfileService` implémentation.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

Dans l' *`Server`* application, inscrivez le service de profil dans `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Utiliser des mécanismes d’autorisation

Dans l' *`Client`* application, les approches d’autorisation des composants sont fonctionnelles à ce stade. L’un des mécanismes d’autorisation des composants peut utiliser un rôle pour autoriser l’utilisateur :

* [ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )
* [Logique procédurale](xref:blazor/security/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )

  Plusieurs tests de rôle sont pris en charge :

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name` est renseigné dans l' *`Client`* application avec le nom d’utilisateur de l’utilisateur, qui est généralement son adresse e-mail de connexion.

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a>Héberger dans Azure App Service avec un domaine personnalisé

Les instructions suivantes expliquent comment déployer une application hébergée Blazor WebAssembly avec le Identity serveur pour [Azure App service](https://azure.microsoft.com/services/app-service/) avec un domaine personnalisé.

Pour ce scénario d’hébergement, n’utilisez **pas** le même certificat pour la [ Identity clé de signature de jeton du serveur](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) et la communication sécurisée HTTPS du site avec les navigateurs :

* L’utilisation de différents certificats pour ces deux exigences est une bonne pratique de sécurité, car elle isole les clés privées pour chaque objectif.
* Les certificats TLS pour la communication avec les navigateurs sont gérés indépendamment sans affecter Identity la signature des jetons du serveur.
* Lorsque [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fournit un certificat à une application App service pour une liaison de domaine personnalisée, Identity le serveur ne peut pas obtenir le même certificat auprès d’Azure Key Vault pour la signature de jetons. Bien qu’il Identity soit possible de configurer le serveur pour qu’il utilise le même certificat TLS à partir d’un chemin d’accès physique, il est déconseillé de placer des certificats de sécurité dans le contrôle de code source **et de les éviter dans la plupart des scénarios**.

Dans les instructions suivantes, un certificat auto-signé est créé dans Azure Key Vault uniquement pour la Identity signature de jeton de serveur. La Identity configuration du serveur utilise le certificat de coffre de clés via le magasin de certificats de l’application `My`  >  `CurrentUser` . D’autres certificats utilisés pour le trafic HTTPs avec des domaines personnalisés sont créés et configurés séparément du Identity certificat de signature du serveur.

Pour configurer une application, Azure App Service et Azure Key Vault pour héberger avec un domaine personnalisé et HTTPs :

1. Créez un [plan de App service](/azure/app-service/overview-hosting-plans) avec un niveau de plan `Basic B1` supérieur ou égal à. App Service nécessite un `Basic B1` niveau de service ou supérieur pour utiliser des domaines personnalisés.
1. Créez un certificat PFX pour la communication sécurisée du navigateur du site (protocole HTTPs) avec un nom commun du nom de domaine complet (FQDN) du site que votre organisation contrôle (par exemple, `www.contoso.com` ). Créez le certificat avec :
   * Utilisations de la clé
     * Validation de signature numérique ( `digitalSignature` )
     * Chiffrement de la clé ( `keyEncipherment` )
   * Utilisations de la clé améliorée/étendue
     * Authentification du client (1.3.6.1.5.5.7.3.2)
     * Authentification du serveur (1.3.6.1.5.5.7.3.1)

   Pour créer le certificat, utilisez l’une des approches suivantes ou tout autre outil approprié ou service en ligne :

   * [Azure Key Vault](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [MakeCert sur Windows](/windows/desktop/seccrypto/makecert)
   * [OpenSSL](https://www.openssl.org)

   Notez le mot de passe, qui est utilisé ultérieurement pour importer le certificat dans Azure Key Vault.

   Pour plus d’informations sur les certificats Azure Key Vault, consultez [Azure Key Vault : certificats](/azure/key-vault/certificates/).
1. Créez un Azure Key Vault ou utilisez un coffre de clés existant dans votre abonnement Azure.
1. Dans la zone **certificats** du coffre de clés, importez le certificat de site pfx. Notez l’empreinte numérique du certificat, qui est utilisée ultérieurement dans la configuration de l’application.
1. Dans Azure Key Vault, générez un nouveau certificat auto-signé pour la Identity signature de jetons du serveur. Attribuez un **nom** et un **objet** au certificat. Le **sujet** est spécifié en tant que `CN={COMMON NAME}` , où l' `{COMMON NAME}` espace réservé est le nom commun du certificat. Le nom commun peut être n’importe quelle chaîne alphanumérique. Par exemple, `CN=IdentityServerSigning` est un **objet** de certificat valide. Utilisez les paramètres de **configuration de stratégie avancée** par défaut. Notez l’empreinte numérique du certificat, qui est utilisée ultérieurement dans la configuration de l’application.
1. Accédez à Azure App Service dans le Portail Azure et créez une nouvelle App Service avec la configuration suivante :
   * **Publication** définie sur `Code` .
   * **Pile d’exécution** définie sur le runtime de l’application.
   * Pour la **référence SKU et la taille**, vérifiez que le niveau de App service est `Basic B1` ou supérieur.  App Service nécessite un `Basic B1` niveau de service ou supérieur pour utiliser des domaines personnalisés.
1. Une fois que Azure a créé le App Service, ouvrez la **configuration** de l’application et ajoutez un nouveau paramètre d’application en spécifiant les empreintes numériques de certificat enregistrées précédemment. La clé du paramètre d’application est `WEBSITE_LOAD_CERTIFICATES` . Séparez les empreintes numériques du certificat dans la valeur du paramètre de l’application par une virgule, comme le montre l’exemple suivant :
   * Clé :`WEBSITE_LOAD_CERTIFICATES`
   * Valeur: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`

   Dans la Portail Azure, l’enregistrement des paramètres de l’application est un processus en deux étapes : enregistrer le `WEBSITE_LOAD_CERTIFICATES` paramètre clé-valeur, puis sélectionner le bouton **Enregistrer** en haut du panneau.
1. Sélectionnez les **paramètres TLS/SSL** de l’application. Sélectionnez **certificats de clé privée (. pfx)**. Utilisez le processus d' **importation Key Vault certificat** à deux reprises pour importer le certificat du site pour la communication HTTPS et le certificat de signature de jetons de serveur auto-signé du site Identity .
1. Accédez au panneau **domaines personnalisés** . Sur le site Web de votre bureau d’enregistrement de domaines, utilisez l' **adresse IP** et l' **ID de vérification du domaine personnalisé** pour configurer le domaine. Une configuration de domaine classique comprend les éléments suivants :
   * Un **enregistrement a** avec un **hôte** `@` et une valeur d’adresse IP de la portail Azure.
   * Un **enregistrement txt** avec un **hôte** de `asuid` et la valeur de l’ID de vérification généré par Azure et fourni par le portail Azure.

   Veillez à enregistrer correctement les modifications sur le site Web de votre bureau d’enregistrement de domaines. Certains sites Web du Bureau d’enregistrement nécessitent un processus en deux étapes pour enregistrer les enregistrements de domaine : un ou plusieurs enregistrements sont enregistrés individuellement, puis mis à jour l’inscription du domaine avec un bouton distinct.
1. Revenez au panneau **domaines personnalisés** dans le portail Azure. Sélectionnez **Ajouter un domaine personnalisé**. Sélectionnez l’option **A record** . Fournissez le domaine et sélectionnez **valider**. Si les enregistrements de domaine sont corrects et propagés sur Internet, le portail vous permet de sélectionner le bouton **Ajouter un domaine personnalisé** .

   Plusieurs jours peuvent être nécessaires pour que les modifications apportées à l’inscription du domaine se propagent sur les serveurs DNS (Internet Domain Name Server) une fois qu’ils sont traités par votre bureau d’enregistrement de domaines. Si les enregistrements de domaine ne sont pas mis à jour dans les trois jours ouvrables, vérifiez que les enregistrements sont correctement définis avec le Bureau d’enregistrement de domaines et contactez le support technique.
1. Dans le panneau **domaines personnalisés** , l' **État SSL** pour le domaine est marqué `Not Secure` . Sélectionnez le lien **Ajouter une liaison** . Sélectionnez le certificat HTTPs du site dans le coffre de clés pour la liaison de domaine personnalisée.
1. Dans Visual Studio, ouvrez le fichier de paramètres d’application du projet *serveur* ( `appsettings.json` ou `appsettings.Production.json` ). Dans la Identity configuration du serveur, ajoutez la `Key` section suivante. Spécifiez le **sujet** du certificat auto-signé pour la `Name` clé. Dans l’exemple suivant, le nom commun du certificat affecté dans le coffre de clés est `IdentityServerSigning` , qui donne l' **objet** `CN=IdentityServerSigning` :

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. Dans Visual Studio, créez un Azure App Service [profil de publication](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) pour le projet *serveur* . Dans la barre de menus, sélectionnez : **générer**  >  **publier**  >  **de nouveaux**  >    >  **Azure App service** Azure (Windows ou Linux). Lorsque Visual Studio est connecté à un abonnement Azure, vous pouvez définir l' **affichage** des ressources Azure par **type de ressource**. Naviguez dans la liste des **applications Web** pour rechercher les app service de l’application et sélectionnez-la. Sélectionnez **Terminer**.
1. Lorsque Visual Studio revient à la fenêtre de **publication** , les dépendances du coffre de clés et du service de base de données SQL Server sont détectées automatiquement.

   Aucune modification de la configuration des paramètres par défaut n’est requise pour le service de coffre de clés.

   À des fins de test, la base de données [SQLite](https://www.sqlite.org/index.html) locale d’une application, qui est configurée par défaut par le Blazor modèle, peut être déployée avec l’application sans configuration supplémentaire. La configuration d’une base de données différente pour Identity le serveur en production dépasse le cadre de cet article. Pour plus d’informations, consultez les ressources de base de données dans les ensembles de documentation suivants :
   * [App Service](/azure/app-service/)
   * [Identity Serveurs](https://identityserver4.readthedocs.io/en/latest/)

1. Sélectionnez le lien **modifier** sous le nom du profil de déploiement en haut de la fenêtre. Remplacez l’URL de destination par l’URL de domaine personnalisée du site (par exemple, `https://www.contoso.com` ). Enregistrez les paramètres.
1. Publiez l’application. Visual Studio ouvre une fenêtre de navigateur et demande le site dans son domaine personnalisé.

La documentation Azure contient des informations supplémentaires sur l’utilisation des services Azure et des domaines personnalisés avec une liaison TLS dans App Service, y compris des informations sur l’utilisation d’enregistrements CNAMe à la place d’enregistrements A. Pour plus d’informations, consultez les ressources suivantes :

* [Documentation App Service](/azure/app-service/)
* [Tutoriel : Mapper un nom DNS personnalisé existant à Azure App Service](/azure/app-service/app-service-web-tutorial-custom-domain)
* [Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service](/azure/app-service/configure-ssl-bindings)
* [Azure Key Vault](/azure/key-vault/)

Nous vous recommandons d’utiliser une nouvelle fenêtre de navigateur incognito ou privée pour chaque exécution de test d’application après une modification de l’application, la configuration de l’application ou les services Azure dans la Portail Azure. cookieLes s en attente d’une série de tests précédente peuvent entraîner l’échec de l’authentification ou de l’autorisation lors du test du site, même lorsque la configuration du site est correcte. Pour plus d’informations sur la configuration de Visual Studio pour ouvrir une nouvelle fenêtre de navigateur dans incognito ou privée pour chaque série de tests, consultez la section [ Cookie s et données de site](#cookies-and-site-data) .

Lorsque App Service configuration est modifiée dans le Portail Azure, les mises à jour prennent généralement effet rapidement mais ne sont pas instantanées. Parfois, vous devez attendre un peu de temps pour qu’un App Service redémarre afin que la modification de la configuration prenne effet.

Si vous résolvez un problème de chargement de certificat, exécutez la commande suivante dans une interface de commande PowerShell Portail Azure [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) . La commande fournit une liste de certificats auxquels l’application peut accéder à partir du `My`  >  `CurrentUser` magasin de certificats. La sortie comprend les objets de certificat et les empreintes numériques utiles lors du débogage d’une application :

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Déploiement sur Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Importer un certificat à partir d’Key Vault (documentation Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
