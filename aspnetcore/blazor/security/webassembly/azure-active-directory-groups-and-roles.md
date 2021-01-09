---
title: ASP.NET Core Blazor WebAssembly avec des groupes et des rôles Azure Active Directory
author: guardrex
description: Découvrez comment configurer Blazor WebAssembly pour utiliser des groupes et des rôles Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 96a7dde9a5a756e40125ffda4c54fbf24fdc616a
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058257"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a>Groupes Azure Active Directory (AAD), rôles d’administrateur et rôles définis par l’utilisateur

Par [Luke Latham](https://github.com/guardrex) et [Javier Calvarro Nelson](https://github.com/javiercn)

> [!NOTE]
> Cet article s’applique à Blazor ASP.net Core Apps version 3,1 avec Microsoft Identity 1,0 et sera mis à jour à 5,0 avec Identity 2,0. Pour plus d’informations, consultez le problème GitHub suivant et la demande de tirage (pull request). L’onglet **fichiers modifiés** de la requête de tirage contient le texte brouillon et des exemples pour les mises à jour de l’article. Après la révision et les mises à jour finales, la requête de tirage sera fusionnée dans le jeu de documentation en direct.
>
> * Problème : [ Blazor WASM avec les groupes et rôles AAD (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)
> * Requête de tirage : [ Blazor groupes et rôles AAD, rubrique 5,0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)

Azure Active Directory (AAD) fournit plusieurs approches d’autorisation qui peuvent être combinées avec ASP.NET Core Identity :

* Groupes définis par l’utilisateur
  * Sécurité
  * Microsoft 365
  * Distribution
* Rôles
  * Rôles d’administrateur AAD
  * Rôles définis par l’utilisateur

Les instructions de cet article s’appliquent aux Blazor WebAssembly scénarios de déploiement AAD décrits dans les rubriques suivantes :

* [Autonome avec des comptes Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Autonome avec AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hébergé avec AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a>Étendues

Un appel d' [API Microsoft Graph](/graph/use-the-api) est requis pour tout utilisateur d’application disposant de plus de cinq appartenances de groupe de sécurité et de rôle d’administrateur AAD.

Pour autoriser les appels API Graph, attribuez à l’application autonome ou à *`Client`* une solution hébergée l' Blazor une des [autorisations API Graph suivantes (étendues)](/graph/permissions-reference) dans le portail Azure :

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` est l’étendue la moins privilégiée et est l’étendue utilisée pour l’exemple décrit dans cet article.

## <a name="user-defined-groups-and-administrator-roles"></a>Groupes définis par l’utilisateur et rôles d’administrateur

Pour configurer l’application dans le Portail Azure pour fournir une `groups` revendication d’appartenance, consultez les articles Azure suivants. Affecter des utilisateurs à des groupes AAD définis par l’utilisateur et des rôles d’administrateur AAD.

* [Rôles utilisant les groupes de sécurité Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Les exemples suivants partent du principe qu’un utilisateur est affecté au rôle d' *administrateur de facturation* AAD.

La `groups` revendication unique envoyée par AAD présente les groupes et les rôles de l’utilisateur en tant qu’ID d’objet (Guid) dans un tableau JSON. L’application doit convertir le tableau JSON de groupes et de rôles en `group` revendications individuelles pour lesquelles l’application peut créer des [stratégies](xref:security/authorization/policies) .

Lorsque le nombre de rôles d’administrateur AAD attribués et de groupes définis par l’utilisateur est supérieur à cinq, AAD envoie une `hasgroups` revendication avec une `true` valeur au lieu d’envoyer une `groups` revendication. Toute application pouvant comporter plus de cinq rôles et groupes affectés à ses utilisateurs doit effectuer un appel de API Graph séparé pour obtenir les rôles et les groupes d’un utilisateur. L’exemple d’implémentation fourni dans cet article s’adresse à ce scénario. Pour plus d’informations, consultez `groups` l' `hasgroups` article et les informations sur les revendications dans les jetons d’accès de la [plateforme Microsoft Identity : revendications de charge utile](/azure/active-directory/develop/access-tokens#payload-claims) .

Étendez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> pour inclure les propriétés de tableau pour les groupes et les rôles. Assignez un tableau vide à chaque propriété afin qu' `null` il ne soit pas nécessaire de vérifier si ces propriétés sont utilisées ultérieurement dans les `foreach` boucles.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

Utilisez l' **une** des approches suivantes pour créer des revendications pour les groupes et rôles AAD :

* [Utiliser le kit de développement logiciel (SDK) Graph](#use-the-graph-sdk)
* [Utiliser un nommé `HttpClient`](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a>Utiliser le kit de développement logiciel (SDK) Graph

Ajoutez une référence de package à l’application autonome ou à l' *`Client`* application d’une solution hébergée Blazor pour [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .

Ajoutez les classes et la configuration de l’utilitaire du SDK Graph dans la section *Kit de développement logiciel (SDK) Graph* de l' <xref:blazor/security/webassembly/graph-api#graph-sdk> article.

Ajoutez la fabrique de comptes d’utilisateur personnalisée suivante à l’APPO autonome ou à l' *`Client`* application d’une solution hébergée Blazor ( `CustomAccountFactory.cs` ). La fabrique d’utilisateurs personnalisée est utilisée pour traiter les revendications de rôles et de groupes. Le `roles` tableau de revendications est abordé dans la section [rôles définis par l’utilisateur](#user-defined-roles) . Si la `hasgroups` revendication est présente, le kit de développement logiciel (SDK) Graph est utilisé pour effectuer une demande autorisée à API Graph pour obtenir les rôles et les groupes de l’utilisateur :

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Le code précédent n’inclut pas les appartenances transitives. Si l’application requiert des revendications d’appartenance de groupe directe et transitive :

* Remplacez le `IUserMemberOfCollectionWithReferencesPage` type par `groupsAndAzureRoles` `IUserTransitiveMemberOfCollectionWithReferencesPage` .
* Lorsque vous demandez les groupes et les rôles de l’utilisateur, remplacez la `MemberOf` propriété par `TransitiveMemberOf` .

Dans `Program.Main` ( `Program.cs` ), configurez l’authentification MSAL pour utiliser la fabrique de comptes d’utilisateur personnalisée : si l’application utilise une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a>Utiliser un nommé `HttpClient`

::: moniker-end

Dans l’application autonome ou l' *`Client`* application d’une solution hébergée Blazor , créez une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personnalisée. Utilisez l’étendue appropriée pour les appels de API Graph qui obtiennent des informations sur les rôles et les groupes.

`GraphAPIAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

Dans `Program.Main` ( `Program.cs` ), ajoutez le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service d’implémentation et ajoutez un nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes de API Graph. L’exemple suivant nomme le client `GraphAPI` :

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Créez des classes d’objets d’annuaire AAD pour recevoir les groupes et rôles Open Data Protocol (OData) d’un appel de API Graph. OData arrive au format JSON, et un appel à <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> remplit une instance de la `DirectoryObjects` classe.

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

Créez une fabrique d’utilisateurs personnalisée pour traiter les revendications de rôles et de groupes. L’exemple d’implémentation suivant gère également le `roles` tableau de revendications, qui est abordé dans la section [rôles définis par l’utilisateur](#user-defined-roles) . Si la `hasgroups` revendication est présente, le nommé <xref:System.Net.Http.HttpClient> est utilisé pour effectuer une demande autorisée à API Graph pour obtenir les rôles et les groupes de l’utilisateur. Cette implémentation utilise le Identity point de terminaison Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` (documentation de l'[API](/graph/api/user-list-memberof)).

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `groups` , le cas échéant, car elle est automatiquement supprimée par l’infrastructure.

> [!NOTE]
> L’approche dans cet exemple :
>
> * Ajoute une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personnalisée pour joindre des jetons d’accès aux demandes sortantes.
> * Ajoute un nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes d’API Web à un point de terminaison d’API Web sécurisé et externe.
> * Utilise le nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes autorisées.
>
> La couverture générale de cette approche est disponible dans l' <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.

Inscrire la fabrique dans `Program.Main` ( `Program.cs` ) de l’application autonome ou *`Client`* de l’application d’une solution hébergée Blazor . Consentement à l' `Directory.Read.All` étendue comme étendue supplémentaire pour l’application :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a>Configuration de l’autorisation

Créez une [stratégie](xref:security/authorization/policies) pour chaque groupe ou rôle dans `Program.Main` . L’exemple suivant crée une stratégie pour le rôle d' *administrateur de facturation* AAD :

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Pour obtenir la liste complète des ID d’objet de rôle AAD, consultez la section [ID d’objet de rôle d’administrateur AAD](#aad-administrator-role-object-ids) .

Dans les exemples suivants, l’application utilise la stratégie précédente pour autoriser l’utilisateur.

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

L’accès à un composant entier peut être basé sur la stratégie à l’aide de la [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) :

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Si l’utilisateur n’est pas connecté, il est redirigé vers la page de connexion AAD, puis renvoyé au composant une fois qu’il se connecte.

Une vérification de stratégie peut également être [effectuée dans le code avec la logique procédurale](xref:blazor/security/index#procedural-logic):

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a>Autoriser l’accès à l’API serveur pour les groupes définis par l’utilisateur et les rôles d’administrateur

Outre l’autorisation des utilisateurs dans l’application webassembly côté client pour accéder aux pages et aux ressources, l’API serveur peut autoriser les utilisateurs à accéder aux points de terminaison d’API sécurisés. Une fois que l’application *serveur* a validé le jeton d’accès de l’utilisateur :

* L’application API serveur utilise la revendication d’identificateur d’objet immuable de l’utilisateur [( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) à partir de son jeton d’accès pour obtenir un jeton d’accès pour API Graph.
* Un appel de API Graph obtient le groupe de sécurité défini par l’utilisateur Azure et les appartenances de rôle d’administrateur en appelant [`memberOf`](/graph/api/user-list-memberof) l’utilisateur.
* Les appartenances sont utilisées pour établir des `group` revendications.
* Les [stratégies d’autorisation](xref:security/authorization/policies) peuvent être utilisées pour limiter l’accès utilisateur aux points de terminaison de l’API serveur au sein de l’application.

> [!NOTE]
> Ce guide n’inclut pas actuellement l’autorisation des utilisateurs sur la base de leurs [rôles AAD définis par l’utilisateur](#user-defined-roles).

Les instructions de cette section configurent l’application API serveur en tant qu' [*application démon*](/azure/active-directory/develop/scenario-daemon-overview) pour l’appel d’API Microsoft Graph. Cette approche n’effectue **pas** les opérations suivantes :

* Exiger la `access_as_user` portée.
* Accédez API Graph pour le compte de l’utilisateur/client qui effectue la demande d’API.

L’appel à API Graph par l’application API serveur nécessite uniquement une **application** API de serveur API Graph étendue pour `Directory.Read.All` dans le portail Azure. Cette approche empêche absolument l’application cliente d’accéder aux données d’annuaire que l’API serveur n’autorise pas explicitement. L’application cliente peut uniquement accéder aux points de terminaison de contrôleur de l’application API serveur.

### <a name="azure-configuration"></a>Configuration Azure

* Vérifiez que l’inscription de l’application *serveur* est donnée à l' **application** (non **déléguée**) API Graph pour `Directory.Read.All` , qui est le niveau d’accès le moins privilégié pour les groupes de sécurité. Confirmez que le consentement de l’administrateur est appliqué à l’étendue après avoir effectué l’attribution de l’étendue.
* Attribuez une nouvelle clé secrète client à l’application *serveur* . Notez le secret de la configuration de l’application dans la section paramètres de l' [application](#app-settings) .

### <a name="app-settings"></a>Paramètres de l’application

Dans le fichier de paramètres d’application ( `appsettings.json` ou `appsettings.Production.json` ), créez une `ClientSecret` entrée avec la clé secrète client de l’application *serveur* à partir de la portail Azure :

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

Exemple :

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Si le domaine de l’éditeur du locataire n’est pas vérifié, l’étendue de l’API serveur pour l’accès utilisateur/client utilise un `https://` URI basé sur. Dans ce scénario, l’application API serveur requiert une `Audience` configuration dans le `appsettings.json` fichier. Dans la configuration suivante, la fin de la `Audience` valeur n’inclut **pas** l’étendue par défaut `/{DEFAULT SCOPE}` , où l’espace réservé `{DEFAULT SCOPE}` est l’étendue par défaut :
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> Dans l’exemple de configuration précédent :
>
> * Le domaine du locataire est `contoso.onmicrosoft.com` .
> * L’application API serveur `ClientId` est `41451fa7-82d9-4673-8fa5-69eff5a761fd` .
>
> > [!NOTE]
> > La configuration d’une en `Audience` général n’est **pas** obligatoire pour les applications avec un domaine d’éditeur vérifié qui a une `api://` étendue d’API basée sur.
>
> Pour plus d'informations, consultez <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.

::: moniker-end

### <a name="authorization-policies"></a>Vous pouvez également utiliser les stratégies d'autorisation

Créer des [stratégies d’autorisation](xref:security/authorization/policies) pour les groupes de sécurité AAD et les rôles d’administrateur AAD dans les applications *serveur* `Startup.ConfigureServices` ( `Startup.cs` ) en fonction des ID d’objet de groupe et des [ID d’objet de rôle d’administrateur AAD](#aad-administrator-role-object-ids).

Par exemple, une stratégie de rôle d’administrateur de facturation Azure a la configuration suivante :

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Pour plus d'informations, consultez <xref:security/authorization/policies>.

### <a name="controller-access"></a>Accès du contrôleur

Exiger des stratégies sur les contrôleurs de l’application *serveur* .

L’exemple suivant limite l’accès aux données de facturation du `BillingDataController` à des administrateurs de facturation Azure avec le nom de stratégie `BillingAdmin` , tel que configuré dans la section [stratégies d’autorisation](#authorization-policies) :

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a>Packages

Ajoutez des références de package à l’application *serveur* pour les packages suivants :

* [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft. Identity . Client](https://www.nuget.org/packages/Microsoft.Identity.Client)

### <a name="services"></a>Services

Dans la méthode de l’application *serveur* `Startup.ConfigureServices` , des espaces de noms supplémentaires sont requis pour le code de la `Startup` classe de l’application *serveur* . Ajoutez les espaces de noms suivants à `Startup.cs` :

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

Lors de la configuration <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* Incluez éventuellement le traitement de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Par exemple, l’application peut consigner l’échec de l’authentification.
* Dans <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effectuez un appel API Graph pour obtenir les groupes et les rôles de l’utilisateur.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fournit des informations d’identification personnelle (PII) dans la journalisation des messages. Activez uniquement les PII pour le débogage avec les comptes d’utilisateur de test.

Dans `Startup.ConfigureServices` :

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

Dans le code précédent, la gestion des erreurs de jeton suivantes est facultative :

* `MsalUiRequiredException`: L’application n’a pas les autorisations suffisantes (étendues).
  * Déterminez si les étendues de l’application API serveur dans le Portail Azure incluent une autorisation d' **application** pour `Directory.Read.All` .
  * Confirmez que l’administrateur client a accordé des autorisations à l’application.
* `MsalServiceException` ( `AADSTS70011` ) : Confirmez que l’étendue est `https://graph.microsoft.com/.default` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a>Packages

Ajoutez des références de package à l’application *serveur* pour les packages suivants :

* [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)

### <a name="service-configuration"></a>Configuration de service

Dans la méthode de l’application *serveur* , `Startup.ConfigureServices` Ajoutez une logique pour que les API Graph appellent et établissent `group` des revendications d’utilisateur pour les groupes de sécurité et les rôles de l’utilisateur.

> [!NOTE]
> L’exemple de code dans cette section utilise le Bibliothèque d’authentification Active Directory (ADAL), qui est basé sur Microsoft Identity Platform v 1.0.

Des espaces de noms supplémentaires sont requis pour le code de la `Startup` classe de l’application *serveur* . L’ensemble d' `using` instructions suivant contient les espaces de noms requis pour le code qui suit dans cette section :

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

Lors de la configuration <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* Incluez éventuellement le traitement de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Par exemple, l’application peut consigner l’échec de l’authentification.
* Dans <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effectuez un appel API Graph pour obtenir les groupes et les rôles de l’utilisateur.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fournit des informations d’identification personnelle (PII) dans la journalisation des messages. Activez uniquement les PII pour le débogage avec les comptes d’utilisateur de test.

Dans `Startup.ConfigureServices` :

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

Dans l’exemple précédent :

* L’acquisition du jeton silencieux ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ) est tentée en premier, car le jeton d’accès a peut-être déjà été stocké dans le cache de jetons Adal. Il est plus rapide d’obtenir le jeton à partir du cache que de demander un nouveau jeton.
* Si le jeton d’accès n’est pas acquis à partir du cache ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> ou <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> est levé), une assertion utilisateur ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) est effectuée avec les informations d’identification du client ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) pour obtenir le jeton pour le compte de l’utilisateur ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ). Ensuite, le `Microsoft.Graph.GraphServiceClient` peut continuer à utiliser le jeton pour effectuer l’appel de API Graph. Le jeton est placé dans le cache de jetons ADAL. Pour les appels de API Graph ultérieurs pour le même utilisateur, le jeton est acquis à partir du cache en mode silencieux avec <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .

::: moniker-end

Le code de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> n’obtient pas les appartenances transitives. Pour modifier le code afin d’obtenir les appartenances directes et transitives :

* Pour la ligne de code :

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  Remplacez la ligne précédente par :

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* Pour la ligne de code :

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  Remplacez la ligne précédente par :

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

Le code dans <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> ne fait pas la distinction entre les groupes de sécurité AAD et les rôles d’administrateur AAD lors de la création de revendications. Pour que l’application fasse la distinction entre les groupes et les rôles, activez la case à cocher lors de l' `entry.ODataType` itération des groupes et des rôles. Pour créer des revendications de rôle et de groupe de sécurité distinctes, utilisez un code similaire à ce qui suit :

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a>Rôles définis par l’utilisateur

Une application inscrite à AAD peut également être configurée pour utiliser des rôles définis par l’utilisateur.

Pour configurer l’application dans le Portail Azure pour fournir une `roles` revendication d’appartenance, consultez [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) de la documentation Azure.

L’exemple suivant suppose qu’une application est configurée avec deux rôles :

* `admin`
* `developer`

> [!NOTE]
> Bien que vous ne puissiez pas attribuer des rôles à des groupes de sécurité sans compte Azure AD Premium, vous pouvez affecter des utilisateurs à des rôles et recevoir une `roles` revendication pour les utilisateurs disposant d’un compte Azure standard. Les instructions de cette section ne nécessitent pas de compte Azure AD Premium.
>
> Plusieurs rôles sont affectés dans le Portail Azure en **_rajoutant un utilisateur_** pour chaque attribution de rôle supplémentaire.

La `roles` revendication unique envoyée par AAD présente les rôles définis par l’utilisateur en tant que `appRoles` `value` s dans un tableau JSON. L’application doit convertir le tableau JSON de rôles en `role` revendications individuelles.

La `CustomUserFactory` section des [groupes définis par l’utilisateur et des rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) est configurée pour agir sur une `roles` revendication avec une valeur de tableau JSON. Ajoutez et inscrivez `CustomUserFactory` dans l’application autonome ou dans l' *`Client`* application d’une Blazor solution hébergée, comme indiqué dans la section [groupes définis par l’utilisateur et rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) . Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `roles` , car elle est automatiquement supprimée par l’infrastructure.

Dans `Program.Main` l’application autonome ou l' *`Client`* application d’une solution hébergée Blazor , spécifiez la revendication nommée « `role` » comme revendication de rôle :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Les approches d’autorisation des composants sont fonctionnelles à ce stade. L’un des mécanismes d’autorisation des composants peut utiliser le `admin` rôle pour autoriser l’utilisateur :

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

## <a name="aad-administrator-role-object-ids"></a>ID d’objet du rôle administrateur AAD

Les ID d’objet présentés dans le tableau suivant sont utilisés pour créer des [stratégies](xref:security/authorization/policies) pour les `group` revendications. Les stratégies permettent à une application d’autoriser les utilisateurs à effectuer différentes activités dans une application. Pour plus d’informations, consultez la section [groupes définis par l’utilisateur et rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) .

Rôle administrateur AAD | ID de l'objet
--- | ---
Administrateur d’application | fa11557b-4f15-4ddd-85d5-313c7cd74047
Développeur d’applications | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Administrateur d’authentification | 02d110a1-96b1-419e-af87-746461b60ed7
Administrateur Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Administrateur Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
Administrateur de jeux de clés IEF B2C | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Administrateur de la stratégie B2C IEF | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Administrateur de workflow utilisateur B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Administrateur de l’attribut de workflow de l’utilisateur B2C | dd0baca0-a535-48c1-b871-8431abe16452
Administrateur de facturation | 69ff516a-B57D-4697-A429-9de4af7b5609
Administrateur d’application cloud | 250b5fe3-b553-458d-9a53-b782c13c34bf
Administrateur d’appareil cloud | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Administrateur de conformité | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Administrateur des données de conformité | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrateur de l’accès conditionnel | 8f71a611-137d-49af-87ad-e97f1fd5da76
Approbateur d’accès au référentiel sécurisé client | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Administrateur Desktop Analytics | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Lecteurs d’annuaires | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Administrateur Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Administrateur Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityAdministrateur du fournisseur externe | febfaeb4-e478-407a-b4b3-f4d9716618a2
Administrateur général | a45ba61b-44db-462c-924b-3b2719152588
Lecteur général | f6903b21-6aba-4124-B44C-76671796b9d5
Administrateur de groupes | 158b3e5a-d89d-460b-92b5-3b34985f0197
Inviteur d’invités | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Administrateur du support technique | 108678c8-6628-44e1-8d01-caf598a6a5f5
Administrateur Intune | 79950741-23fa-4189-b2cb-46640601c497
Administrateur Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Administrateur de licence | 3355458a-e423-44bf-8b98-4ac5e572cea5
Lecteur de confidentialité du Centre de messages | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Lecteur du Centre de messages | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Administrateur d’applications Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Administrateur de mots de passe | 466e48b7-5d66-4ae5-8911-1a118de74941
Administrateur Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Administrateur de plateforme Power | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Administrateur d’authentification privilégié | 0829f731-b46d-419F-9742-aeb122367d11
Administrateur de rôle privilégié | f20a725a-d1c8-4107-83ea-1171c97d00c7
Lecteur de rapports | 54635450-e8ed-4f2d-9632-07db2517b4de
Administrateur de recherche | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Éditeur de recherche | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Administrateur de sécurité | 20fa50e3-6531-44d8-bd39-b251420568ad
Opérateur de sécurité | 43aae017-8e51-4188-91ab-e6debd572800
Lecteur de sécurité | 45035cd3-fd97-4250-8197-3a53d3562d9b
Administrateur de support de service | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Administrateur SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Administrateur Skype Entreprise | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Administrateur des communications Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Ingénieur de support des communications Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Spécialiste des communications des équipes | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Administrateur du service Teams | 8846a0be-197b-443a-b13c-11192691fa24
Administrateur d’utilisateurs | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
