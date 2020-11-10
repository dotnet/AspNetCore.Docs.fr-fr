---
title: BlazorAuthentification et autorisation ASP.net Core
author: guardrex
description: En savoir plus sur Blazor les scénarios d’authentification et d’autorisation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: a333c189e81a9f44e94deb6b37097f1a8b19a0f9
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430924"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a>BlazorAuthentification et autorisation ASP.net Core

Par [Steve Sanderson](https://github.com/SteveSandersonMS) et [Luke Latham](https://github.com/guardrex)

ASP.NET Core prend en charge la configuration et la gestion de la sécurité dans les Blazor applications.

Les scénarios de sécurité diffèrent entre Blazor Server les Blazor WebAssembly applications et. Étant donné que Blazor Server les applications s’exécutent sur le serveur, les contrôles d’autorisation peuvent déterminer les éléments suivants :

* Les options de l’interface utilisateur présentées à un utilisateur (par exemple, les entrées de menu disponibles pour un utilisateur).
* Les règles d’accès pour les zones de l’application et les composants.

Blazor WebAssembly les applications s’exécutent sur le client. L’autorisation est *uniquement* utilisée pour déterminer les options de l’interface utilisateur à afficher. Étant donné que les contrôles côté client peuvent être modifiés ou ignorés par un utilisateur, une Blazor WebAssembly application ne peut pas appliquer les règles d’accès d’autorisation.

Les [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization) ne s’appliquent pas aux composants routables Razor . Si un composant non routable Razor est [incorporé dans une page](xref:blazor/components/prerendering-and-integration), les conventions d’autorisation de la page affectent indirectement le Razor composant ainsi que le reste du contenu de la page.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601> et <xref:Microsoft.AspNetCore.Identity.UserManager%601> ne sont pas pris en charge dans les Razor composants.

## <a name="authentication"></a>Authentification

Blazor utilise les mécanismes d’authentification ASP.NET Core existants pour établir l’identité de l’utilisateur. Le mécanisme exact dépend de la façon dont l' Blazor application est hébergée, Blazor WebAssembly ou Blazor Server .

### <a name="no-locblazor-webassembly-authentication"></a>l’authentification Blazor WebAssembly

Dans Blazor WebAssembly les applications, les vérifications d’authentification peuvent être ignorées, car tout le code côté client peut être modifié par les utilisateurs. Cela vaut également pour toutes les technologies d’application côté client, y compris les infrastructures d’application JavaScript SPA ou les applications natives pour n’importe quel système d’exploitation.

Ajoutez ce qui suit :

* Référence de package pour [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) le fichier projet de l’application.
* `Microsoft.AspNetCore.Components.Authorization`Espace de noms du fichier de l’application `_Imports.razor` .

Pour gérer l’authentification, l’utilisation d’un service intégré ou personnalisé <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> est traitée dans les sections suivantes.

Pour plus d’informations sur la création d’applications et la configuration, consultez <xref:blazor/security/webassembly/index> .

### <a name="no-locblazor-server-authentication"></a>l’authentification Blazor Server

Blazor Server les applications fonctionnent sur une connexion en temps réel créée à l’aide de SignalR . L' [authentification dans les SignalR applications basées](xref:signalr/authn-and-authz) sur est gérée lorsque la connexion est établie. L’authentification peut être basée sur un cookie ou un autre jeton du porteur.

Le <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service intégré pour les Blazor Server applications obtient les données d’état d’authentification du ASP.net Core `HttpContext.User` . C’est ainsi que l’état d’authentification s’intègre aux mécanismes d’authentification ASP.NET Core existants.

Pour plus d’informations sur la création d’applications et la configuration, consultez <xref:blazor/security/server/index> .

## <a name="authenticationstateprovider-service"></a>Service AuthenticationStateProvider

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> est le service sous-jacent utilisé par le composant <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> et le composant <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> pour obtenir l’état d’authentification.

Vous n’utilisez généralement pas <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directement. Utilisez le ou les [ `AuthorizeView` composants](#authorizeview-component) [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) décrits plus loin dans cet article. Le principal inconvénient de l’utilisation directe de <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> est que le composant n’est pas automatiquement averti en cas de modifications de données d’état de l’authentification sous-jacente.

Le service <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> peut fournir les données <xref:System.Security.Claims.ClaimsPrincipal> de l’utilisateur actuel, comme indiqué dans l’exemple suivant :

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Si `user.Identity.IsAuthenticated` est `true` et parce que l’utilisateur est <xref:System.Security.Claims.ClaimsPrincipal>, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.

Pour plus d’informations sur l’injection de dépendances et les services, consultez <xref:blazor/fundamentals/dependency-injection> et <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implémenter un AuthenticationStateProvider personnalisé

Si l’application requiert un fournisseur personnalisé, implémentez <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> et substituez `GetAuthenticationStateAsync` :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

Dans une Blazor WebAssembly application, le `CustomAuthStateProvider` service est inscrit dans `Main` `Program.cs` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Dans une Blazor Server application, le `CustomAuthStateProvider` service est inscrit dans `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

À l’aide `CustomAuthStateProvider` de dans l’exemple précédent, tous les utilisateurs sont authentifiés avec le nom d’utilisateur `mrfibuli` .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Exposer l’état d’authentification comme un paramètre en cascade

Si les données d’état d’authentification sont requises pour la logique procédurale, par exemple lors de l’exécution d’une action déclenchée par l’utilisateur, obtenez les données d’état d’authentification en définissant un paramètre en cascade de type `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Si `user.Identity.IsAuthenticated` est `true`, les revendications peuvent être énumérées et l’appartenance aux rôles évaluée.

Configurez le `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` paramètre en cascade à l’aide des <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> composants et dans le `App` composant ( `App.razor` ) :

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

Dans une Blazor WebAssembly application, ajoutez des services pour les options et l’autorisation pour `Program.Main` :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Dans une Blazor Server application, les services pour les options et l’autorisation sont déjà présents, donc aucune action supplémentaire n’est requise.

## <a name="authorization"></a>Autorisation

Une fois qu’un utilisateur est authentifié, les règles *d’autorisation* sont appliquées pour contrôler ce que l’utilisateur peut faire.

L’accès est généralement accordé ou refusé selon les conditions suivantes :

* Un utilisateur est authentifié (connecté).
* Un utilisateur appartient à un *rôle*.
* Un utilisateur a une *revendication*.
* Une *stratégie* est satisfaite.

Chacun de ces concepts est identique à celui d’une application ASP.NET Core MVC ou Razor pages. Pour plus d’informations sur la sécurité de ASP.NET Core, consultez les articles sous [ASP.net Core sécurité et Identity ](xref:security/index).

## <a name="authorizeview-component"></a>Composant AuthorizeView

Le composant <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> affiche sélectivement l’interface utilisateur en fonction de l’autorisation que l’utilisateur a pour l’afficher. Cette approche est utile lorsque vous devez uniquement *afficher* les données de l’utilisateur et que vous n’avez pas besoin d’utiliser l’identité de l’utilisateur dans la logique procédurale.

Le composant expose une variable `context` de type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, que vous pouvez utiliser pour accéder aux informations relatives à l’utilisateur connecté :

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Vous pouvez également fournir un contenu différent à afficher si l’utilisateur n’est pas authentifié :

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

Le <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> composant peut être utilisé dans le `NavMenu` composant ( `Shared/NavMenu.razor` ) pour afficher un élément de liste ( `<li>...</li>` ) pour un [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), mais notez que cette approche supprime uniquement l’élément de liste de la sortie rendue. Elle n’empêche pas l’utilisateur de naviguer jusqu’au composant.

Le contenu des `<Authorized>` `<NotAuthorized>` balises et peut inclure des éléments arbitraires, tels que d’autres composants interactifs.

Les conditions d’autorisation, comme les rôles ou les stratégies qui contrôlent les options d’interface utilisateur ou d’accès, sont traitées dans la section [Autorisation](#authorization).

Si les conditions d’autorisation ne sont pas spécifiées, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> utilise une stratégie par défaut et traite :

* Les utilisateurs authentifiés (connectés) comme étant autorisés.
* Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.

### <a name="role-based-and-policy-based-authorization"></a>Autorisation en fonction du rôle et de la stratégie

Le composant <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> prend en charge l’autorisation *basée sur le rôle* et *basée sur les stratégies*.

Pour l’autorisation en fonction du rôle, utilisez le paramètre <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> :

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Pour plus d'informations, consultez <xref:security/authorization/roles>.

Pour l’autorisation en fonction des stratégies, utilisez le paramètre <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> :

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

L’autorisation basée sur les revendications est un cas spécial d’autorisation basée sur les stratégies. Par exemple, vous pouvez définir une stratégie qui impose aux utilisateurs d’avoir une certaine revendication. Pour plus d'informations, consultez <xref:security/authorization/policies>.

Ces API peuvent être utilisées dans les Blazor Server Blazor WebAssembly applications ou.

Si ni <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> ni <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> n’est spécifié, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> utilise la stratégie par défaut.

### <a name="content-displayed-during-asynchronous-authentication"></a>Contenu affiché lors de l’authentification asynchrone

Blazor permet de déterminer l’état d’authentification de *manière asynchrone*. Le scénario principal de cette approche se trouve dans les Blazor WebAssembly applications qui effectuent une demande à un point de terminaison externe pour l’authentification.

Lorsque l’authentification est en cours, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> n’affiche aucun contenu par défaut. Pour afficher le contenu pendant que l’authentification se produit, utilisez la `<Authorizing>` balise :

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Cette approche n’est normalement pas applicable aux Blazor Server applications. Blazor Server les applications connaissent l’état d’authentification dès que l’État est établi. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> le contenu peut être fourni dans Blazor Server le composant d’une application <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , mais le contenu n’est jamais affiché.

## <a name="authorize-attribute"></a>Attribut [Authorize]

L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut peut être utilisé dans les Razor composants :

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Utilisez uniquement [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) sur `@page` les composants atteints via le Blazor routeur. L’autorisation est effectuée uniquement en tant qu’aspect du routage et *pas* pour les composants enfants rendus dans une page. Pour autoriser l’affichage d’éléments spécifiques dans une page, utilisez <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> à la place.

L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut prend également en charge l’autorisation basée sur les rôles ou la stratégie. Pour l’autorisation en fonction du rôle, utilisez le paramètre <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> :

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Pour l’autorisation en fonction des stratégies, utilisez le paramètre <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> :

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Si ni <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> ni <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> n’est spécifié, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) utilise la stratégie par défaut, qui par défaut consiste à traiter :

* Les utilisateurs authentifiés (connectés) comme étant autorisés.
* Les utilisateurs non authentifiés (déconnectés) comme étant non autorisés.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Personnaliser le contenu non autorisé avec le composant Router

Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, conjointement avec le <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> composant, permet à l’application de spécifier du contenu personnalisé dans les cas suivants :

* Le contenu est introuvable.
* L’utilisateur n’a pas [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) de condition appliquée au composant. L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est abordé dans la section [ `[Authorize]` attribut](#authorize-attribute) .
* Une authentification asynchrone est en cours.

Dans le modèle de projet par défaut Blazor Server , le `App` composant ( `App.razor` ) montre comment définir un contenu personnalisé :

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

Le contenu des `<NotFound>` `<NotAuthorized>` `<Authorizing>` balises, et peut inclure des éléments arbitraires, tels que d’autres composants interactifs.

Si la `<NotAuthorized>` balise n’est pas spécifiée, le <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> utilise le message de secours suivant :

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notification sur les changements d’état de l’authentification

Si l’application détermine que les données d’état d’authentification sous-jacentes ont changé (par exemple, parce que l’utilisateur s’est déconnecté ou qu’un autre utilisateur a modifié ses rôles), un [ `AuthenticationStateProvider` personnalisé](#implement-a-custom-authenticationstateprovider) peut éventuellement appeler la méthode <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> sur la <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> classe de base. Cela prévient les consommateurs des données d’état d’authentification (par exemple, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) qu’elles doivent appliquer un nouveau rendu à l’aide des nouvelles données.

## <a name="procedural-logic"></a>Logique procédurale

Si l’application est requise pour vérifier les règles d’autorisation dans le cadre de la logique procédurale, utilisez un paramètre en cascade de type `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` pour obtenir le de l’utilisateur <xref:System.Security.Claims.ClaimsPrincipal> . `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` peut être combiné avec d’autres services, tels que `IAuthorizationService` , pour évaluer des stratégies.

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Dans un Blazor WebAssembly composant d’application, ajoutez <xref:Microsoft.AspNetCore.Authorization> les <xref:Microsoft.AspNetCore.Components.Authorization> espaces de noms et :
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Ces espaces de noms peuvent être fournis globalement en les ajoutant au fichier de l’application `_Imports.razor` .

## <a name="troubleshoot-errors"></a>Résoudre les erreurs

Erreurs courantes :

* **L’autorisation nécessite un paramètre en cascade de type `Task\<AuthenticationState>` . Envisagez `CascadingAuthenticationState` d’utiliser pour fournir ce.**

* **`null` la valeur est reçue pour `authenticationStateTask`**

Il est probable que le projet n’a pas été créé à l’aide d’un Blazor Server modèle pour lequel l’authentification est activée. Encapsulez un `<CascadingAuthenticationState>` autour d’une partie de l’arborescence de l’interface utilisateur, par exemple dans le `App` composant ( `App.razor` ) comme suit :

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

Le <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> fournit le `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` paramètre en cascade, qui à son tour est reçu du service di sous-jacent <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Isard Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) exemples de liens vers la communauté d’authentification
