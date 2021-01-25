---
title: ASP.NET Core Blazor WebAssembly avec des groupes et des rôles Azure Active Directory
author: guardrex
description: Découvrez comment configurer Blazor WebAssembly pour utiliser des groupes et des rôles Azure Active Directory.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 8d18a8f9282e83c3b3ef5b9c7c6651c9b525a21f
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751582"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Groupes Azure Active Directory (AAD), rôles d’administrateur et rôles d’application

Par [Luke Latham](https://github.com/guardrex) et [Javier Calvarro Nelson](https://github.com/javiercn)

> [!NOTE]
> Cet article s’applique à Blazor ASP.net Core Apps version 3,1 avec Microsoft Identity 1,0 et sera mis à jour à 5,0 avec Identity 2,0. Pour plus d’informations, consultez le problème GitHub suivant et la demande de tirage (pull request). L’onglet **fichiers modifiés** de la requête de tirage contient le texte brouillon et des exemples pour les mises à jour de l’article. Après la révision et les mises à jour finales, la requête de tirage sera fusionnée dans le jeu de documentation en direct.
>
> * Problème : [ Blazor WASM avec les groupes et rôles AAD (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)
> * Requête de tirage : [ Blazor groupes et rôles AAD, rubrique 5,0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)

Azure Active Directory (AAD) fournit plusieurs approches d’autorisation qui peuvent être combinées avec ASP.NET Core Identity :

* Groupes
  * Sécurité
  * Microsoft 365
  * Distribution
* Rôles
  * Rôles d’administrateur AAD
  * Rôles d’application

Les instructions de cet article s’appliquent aux Blazor WebAssembly scénarios de déploiement AAD décrits dans les rubriques suivantes :

* [Autonome avec des comptes Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Autonome avec AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hébergé avec AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

L’aide de l’article fournit des instructions pour les applications client et serveur :

* **Client**: applications autonomes Blazor WebAssembly ou *`Client`* application d’une solution hébergée Blazor .
* **Serveur**: autonome ASP.net Core les applications API serveur/API Web ou l' *`Server`* application d’une Blazor solution hébergée.

## <a name="scopes"></a>Étendues

Pour autoriser [Microsoft Graph](/graph/use-the-api) appels d’API pour les données de profil utilisateur, d’attribution de rôle et d’appartenance au groupe, le **client** est configuré avec ( `https://graph.microsoft.com/User.Read` ) [API Graph autorisation (étendue)](/graph/permissions-reference) dans le portail Azure.

Une application **serveur** qui appelle API Graph pour les données d’appartenance aux rôles et aux groupes est fournie `GroupMember.Read.All` ( `https://graph.microsoft.com/GroupMember.Read.All` ) [API Graph autorisation (étendue)](/graph/permissions-reference) dans le portail Azure.

Ces étendues sont requises en plus des étendues requises dans les scénarios de déploiement AAD décrits dans les rubriques répertoriées dans la première section de cet article.

> [!NOTE]
> Les mots « autorisation » et « étendue » sont utilisés de manière interchangeable dans le Portail Azure et dans divers ensembles de documentation Microsoft et externes. Cet article utilise le mot « Scope » dans les limites des autorisations affectées à une application dans le Portail Azure.

## <a name="group-membership-claims-attribute"></a>Attribut revendications d’appartenance au groupe

Dans le manifeste de l’application dans le Portail Azure pour les applications **clientes** et **serveur** , affectez à l' [ `groupMembershipClaims` attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) la valeur `All` . La valeur `All` permet d’obtenir tous les groupes de sécurité, groupes de distribution et rôles dont l’utilisateur connecté est membre.

1. Ouvrez l’inscription de l’application Portail Azure.
1. Sélectionnez **gérer**  >  le **manifeste** dans la barre latérale.
1. Recherchez l' `groupMembershipClaims` attribut.
1. Définissez la valeur sur `All`.
1. Sélectionnez le bouton **Enregistrer**.

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>Compte d’utilisateur personnalisé

Affectez des utilisateurs aux groupes de sécurité AAD et aux rôles d’administrateur AAD dans le Portail Azure.

Les exemples de cet article sont les suivants :

* Supposons qu’un utilisateur est affecté au rôle d' *administrateur de facturation* AAD dans le locataire portail Azure AAD pour l’autorisation d’accéder aux données de l’API du serveur.
* Utilisez des [stratégies d’autorisation](xref:security/authorization/policies) pour contrôler l’accès au sein des applications **clientes** et **serveur** .

Dans l’application **cliente** , étendez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> pour inclure les propriétés de :

* `Roles`: Tableau des rôles d’application AAD (abordé dans la section [rôles d’application](#app-roles) )
* `Wids`: Rôles d’administrateur AAD dans des [revendications d’ID bien connues ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims)
* `Oid`: Revendication d' [identificateur d’objet immuable ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (identifie de façon unique un utilisateur dans et entre les locataires)

Assignez un tableau vide à chaque propriété de tableau afin que la vérification de `null` ne soit pas requise lorsque ces propriétés sont utilisées dans les `foreach` boucles.

`CustomUserAccount.cs`:

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

Ajoutez une référence de package au fichier projet de l’application **cliente** pour [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .

Ajoutez les classes et la configuration de l’utilitaire du SDK Graph dans la section *Kit de développement logiciel (SDK) Graph* de l' <xref:blazor/security/webassembly/graph-api#graph-sdk> article. Dans la `GraphClientExtensions` classe, spécifiez l' `User.Read` étendue du jeton d’accès dans la `AuthenticateRequestAsync` méthode :

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

Ajoutez la fabrique de comptes d’utilisateur personnalisée suivante à l’application **cliente** . La fabrique d’utilisateurs personnalisée est utilisée pour établir les éléments suivants :

* Revendications de rôle d’application ( `appRole` ) (traitées dans la section [rôles d’application](#app-roles) )
* Revendications de rôle d’administrateur AAD ( `directoryRole` )
* Exemple de revendication de données de profil utilisateur pour le numéro de téléphone mobile de l’utilisateur ( `mobilePhone` )
* Revendications de groupe AAD ( `directoryGroup` )

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Le code précédent n’inclut pas les appartenances transitives. Si l’application requiert des revendications d’appartenance de groupe directe et transitive, remplacez la `MemberOf` propriété ( `IUserMemberOfCollectionWithReferencesRequestBuilder` ) par `TransitiveMemberOf` ( `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` ).

Le code précédent ignore les revendications d’appartenance à un groupe ( `groups` ) qui sont des rôles d’administrateur AAD ( `#microsoft.graph.directoryRole` type), car les valeurs GUID retournées par la Identity plateforme Microsoft 2,0 sont des **ID d’entité** de rôle d’administrateur AAD et non des ID de modèle de [**rôle**](/azure/active-directory/roles/permissions-reference#role-template-ids). Les ID d’entité ne sont pas stables entre les locataires dans la Identity plate-forme Microsoft 2,0 et ne doivent pas être utilisés pour créer des stratégies d’autorisation pour les utilisateurs dans les applications. Utilisez toujours les **ID de modèle de rôle** pour les rôles d’administrateur AAD **fournis par les `wids` revendications**.

Dans `Program.Main` de l’application **cliente** , configurez l’authentification MSAL pour utiliser la fabrique de comptes d’utilisateurs personnalisée.

`Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a>Configuration de l’autorisation

Dans l’application **cliente** , créez une [stratégie](xref:security/authorization/policies) pour chaque [rôle d’application](#app-roles), rôle d’administrateur AAD ou groupe de sécurité dans `Program.Main` . L’exemple suivant crée une stratégie pour le rôle d' *administrateur de facturation* AAD :

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Pour obtenir la liste complète des ID des rôles d’administrateur AAD, consultez [ID de modèle de rôle](/azure/active-directory/roles/permissions-reference#role-template-ids) dans la documentation Azure. Pour plus d’informations sur les stratégies d’autorisation, consultez <xref:security/authorization/policies> .

Dans les exemples suivants, l’application **cliente** utilise la stratégie précédente pour autoriser l’utilisateur.

Le [ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) fonctionne avec la stratégie :

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

L’accès à un composant entier peut être basé sur la stratégie à l’aide d’une [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) :

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Si l’utilisateur n’est pas connecté, il est redirigé vers la page de connexion AAD, puis renvoyé au composant une fois qu’il se connecte.

Une vérification de stratégie peut également être [effectuée dans le code avec la logique procédurale](xref:blazor/security/index#procedural-logic).

`Pages/CheckPolicy.razor`:

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a>Autoriser l’accès à l’API serveur/API Web

Une application API **serveur** peut autoriser les utilisateurs à accéder à des points de terminaison d’API sécurisés avec des [stratégies d’autorisation](xref:security/authorization/policies) pour les groupes de sécurité, les rôles d’administrateur AAD et les rôles d’application lorsqu’un jeton d’accès contient des `groups` `wids` revendications, et `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` . L’exemple suivant crée une stratégie pour le rôle d' *administrateur de facturation* AAD dans `Startup.ConfigureServices` à l’aide des `wids` revendications (ID de modèle de rôle/ID de rôle) :

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Pour obtenir la liste complète des ID des rôles d’administrateur AAD, consultez [ID de modèle de rôle](/azure/active-directory/roles/permissions-reference#role-template-ids) dans la documentation Azure. Pour plus d’informations sur les stratégies d’autorisation, consultez <xref:security/authorization/policies> .

L’accès à un contrôleur dans l’application **serveur** peut être basé sur l’utilisation d’un [ `[Authorize]` attribut](xref:security/authorization/simple) avec le nom de la stratégie (documentation de l’API : <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).

L’exemple suivant limite l’accès aux données de facturation des `BillingDataController` administrateurs de facturation Azure avec le nom de stratégie `BillingAdministrator` :

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

Pour plus d’informations, consultez <xref:security/authorization/policies>.

## <a name="app-roles"></a>Rôles d’application

Pour configurer l’application dans le Portail Azure pour fournir des revendications d’appartenance à des rôles d’application, consultez [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) dans la documentation Azure.

L’exemple suivant suppose que les applications **client** et **serveur** sont configurées avec deux rôles, et que les rôles sont attribués à un utilisateur de test :

* `admin`
* `developer`

> [!NOTE]
> Lors du développement d’une application hébergée Blazor WebAssembly ou d’une paire client-serveur d’applications autonomes (une Blazor WebAssembly application autonome et une application API Web/API ASP.net Core Server autonome), la `appRoles` propriété de manifeste du client et du serveur portail Azure inscriptions d’applications doit inclure les mêmes rôles configurés. Après avoir établi les rôles dans le manifeste de l’application cliente, copiez-les dans leur intégralité dans le manifeste de l’application serveur. Si vous ne mettez pas en miroir le manifeste `appRoles` entre les inscriptions d’applications clientes et serveur, les revendications de rôle ne sont pas établies pour les utilisateurs authentifiés de l’API serveur/API Web, même si leur jeton d’accès a les revendications de rôles appropriées.

> [!NOTE]
> Bien que vous ne puissiez pas attribuer des rôles à des groupes sans compte Azure AD Premium, vous pouvez attribuer des rôles aux utilisateurs et recevoir une revendication de rôle pour les utilisateurs disposant d’un compte Azure standard. Les instructions de cette section ne nécessitent pas de compte AAD Premium.
>
> Plusieurs rôles sont affectés dans le Portail Azure en **_rajoutant un utilisateur_** pour chaque attribution de rôle supplémentaire.

Le `CustomAccountFactory` affiché dans la section [compte d’utilisateur personnalisé](#custom-user-account) est configuré pour agir sur une `roles` revendication avec une valeur de tableau JSON. Ajoutez et inscrivez `CustomAccountFactory` dans l’application **cliente** , comme indiqué dans la section [compte d’utilisateur personnalisé](#custom-user-account) . Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `roles` , car elle est automatiquement supprimée par l’infrastructure.

Dans `Program.Main` une application **cliente** , spécifiez la revendication nommée « `appRole` » comme revendication de rôle pour les <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> Vérifications :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> Si vous préférez utiliser la `directoryRoles` revendication (ajouter des rôles d’administrateur), assignez « `directoryRoles` » à <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .

Dans `Startup.ConfigureServices` une application **serveur** , spécifiez la revendication nommée « `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` » comme revendication de rôle pour les <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> Vérifications :

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> Si vous préférez utiliser la `wids` revendication (ajouter des rôles d’administrateur), assignez « `wids` » à <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .

Les approches d’autorisation des composants sont fonctionnelles à ce stade. L’un des mécanismes d’autorisation des composants de l’application **cliente** peut utiliser le `admin` rôle pour autoriser l’utilisateur :

* [`AuthorizeView` -](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [Logique procédurale](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

Plusieurs tests de rôle sont pris en charge :

* Exigez **que l’utilisateur soit dans** le `admin` **rôle ou** `developer` avec le `AuthorizeView` composant :

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* Exiger que l’utilisateur se trouve à la **fois** dans les `admin`  `developer` rôles et avec le `AuthorizeView` composant :

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* Exigez **que l’utilisateur soit dans** le `admin` **rôle ou** `developer` avec l' `[Authorize]` attribut :

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* Exiger que l’utilisateur soit à la **fois** dans les `admin`  `developer` rôles et avec l' `[Authorize]` attribut :

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* Exiger **que l’utilisateur soit dans le** rôle `admin` **ou** `developer` avec le code procédural :

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* Exigez que l’utilisateur se trouve à la **fois** dans les `admin` rôles **et** `developer` avec le code procédural en remplaçant le [ou conditionnel ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) par un [conditionnel and ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) dans l’exemple précédent :

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

L’un des mécanismes d’autorisation des contrôleurs de l’application **serveur** peut utiliser le `admin` rôle pour autoriser l’utilisateur :

* [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [Logique procédurale](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

Plusieurs tests de rôle sont pris en charge :

* Exigez **que l’utilisateur soit dans** le `admin` **rôle ou** `developer` avec l' `[Authorize]` attribut :

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* Exiger que l’utilisateur soit à la **fois** dans les `admin`  `developer` rôles et avec l' `[Authorize]` attribut :

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* Exiger **que l’utilisateur soit dans le** rôle `admin` **ou** `developer` avec le code procédural :

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* Exigez que l’utilisateur se trouve à la **fois** dans les `admin` rôles **et** `developer` avec le code procédural en remplaçant le [ou conditionnel ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) par un [conditionnel and ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) dans l’exemple précédent :

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>Ressources supplémentaires

* [ID de modèle de rôle (documentation Azure)](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [`groupMembershipClaims` attribute (documentation Azure)](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton (documentation Azure)](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [Rôles d’application (documentation Azure)](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
