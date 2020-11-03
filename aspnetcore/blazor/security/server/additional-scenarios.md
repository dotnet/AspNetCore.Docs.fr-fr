---
title: ASP.NET Core Blazor Server des scénarios de sécurité supplémentaires
author: guardrex
description: Découvrez comment configurer Blazor Server pour d’autres scénarios de sécurité.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 56b226f8e4a10aa996b0344f10c76dad2ae32b51
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234432"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server des scénarios de sécurité supplémentaires

Par [Javier Calvarro Nelson](https://github.com/javiercn)

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Passer des jetons à une Blazor Server application</h2>

Les jetons disponibles en dehors des Razor composants d’une Blazor Server application peuvent être passés aux composants avec l’approche décrite dans cette section.

Authentifiez l' Blazor Server application comme vous le feriez avec une Razor application de pages ou MVC standard. Approvisionnez et enregistrez les jetons dans l’authentification cookie . Par exemple :

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

Si vous le souhaitez, des étendues supplémentaires sont ajoutées avec `options.Scope.Add("{SCOPE}");` , où l’espace réservé `{SCOPE}` est l’étendue supplémentaire à ajouter.

Définissez un service de fournisseur de jetons **délimités** qui peut être utilisé dans l' Blazor application pour résoudre les jetons à partir de l' [injection de dépendances (di)](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Dans `Startup.ConfigureServices` , ajoutez des services pour :

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Définissez une classe à passer à l’état initial de l’application avec les jetons d’accès et d’actualisation :

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Dans le `_Host.cshtml` fichier, créez l’instance et, `InitialApplicationState` puis transmettez-la en tant que paramètre à l’application :

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

Dans le `App` composant ( `App.razor` ), résolvez le service et initialisez-le avec les données du paramètre :

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Ajoutez une référence de package à l’application pour le [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) package NuGet.

Dans le service qui effectue une demande d’API sécurisée, injectez le fournisseur de jetons et récupérez le jeton pour la demande d’API :

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Définir le schéma d’authentification</h2>

Pour une application qui utilise plusieurs intergiciels (middleware) d’authentification et qui possède donc plusieurs schémas d’authentification, le schéma qui Blazor utilise peut être défini explicitement dans la configuration de point de terminaison de `Startup.Configure` . L’exemple suivant définit le schéma de Azure Active Directory :

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Passer des jetons à une Blazor Server application</h2>

Les jetons disponibles en dehors des Razor composants d’une Blazor Server application peuvent être passés aux composants avec l’approche décrite dans cette section.

Authentifiez l' Blazor Server application comme vous le feriez avec une Razor application de pages ou MVC standard. Approvisionnez et enregistrez les jetons dans l’authentification cookie . Par exemple :

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

Si vous le souhaitez, des étendues supplémentaires sont ajoutées avec `options.Scope.Add("{SCOPE}");` , où l’espace réservé `{SCOPE}` est l’étendue supplémentaire à ajouter.

Si vous le souhaitez, la ressource est spécifiée avec `options.Resource = "{RESOURCE}";` , où l’espace réservé `{RESOURCE}` est la ressource. Par exemple :

```csharp
options.Resource = "https://graph.microsoft.com";
```

Définissez une classe à passer à l’état initial de l’application avec les jetons d’accès et d’actualisation :

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Définissez un service de fournisseur de jetons **délimités** qui peut être utilisé dans l' Blazor application pour résoudre les jetons à partir de l' [injection de dépendances (di)](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Dans `Startup.ConfigureServices` , ajoutez des services pour :

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Dans le `_Host.cshtml` fichier, créez l’instance et, `InitialApplicationState` puis transmettez-la en tant que paramètre à l’application :

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

Dans le `App` composant ( `App.razor` ), résolvez le service et initialisez-le avec les données du paramètre :

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Ajoutez une référence de package à l’application pour le [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) package NuGet.

Dans le service qui effectue une demande d’API sécurisée, injectez le fournisseur de jetons et récupérez le jeton pour la demande d’API :

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Définir le schéma d’authentification</h2>

Pour une application qui utilise plusieurs intergiciels (middleware) d’authentification et qui possède donc plusieurs schémas d’authentification, le schéma qui Blazor utilise peut être défini explicitement dans la configuration de point de terminaison de `Startup.Configure` . L’exemple suivant définit le schéma de Azure Active Directory :

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Utiliser des points de terminaison OpenID Connect (OIDC) v 2.0

Dans les versions de ASP.NET Core antérieures à 5,0, la bibliothèque d’authentification et les Blazor modèles utilisent des points de terminaison OpenID Connect (OIDC) v 1.0. Pour utiliser un point de terminaison v 2.0 avec des versions de ASP.NET Core antérieures à 5,0, configurez l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option dans le <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Le paramètre peut également être défini dans le fichier de paramètres de l’application ( `appsettings.json` ) :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Si l’ajout d’un segment à l’autorité n’est pas approprié pour le fournisseur OIDC de l’application, par exemple avec les fournisseurs non AAD, définissez la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriété directement. Définissez la propriété dans <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ou dans le fichier de paramètres de l’application avec la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> clé.

### <a name="code-changes"></a>Modifications du code

* La liste des revendications dans le jeton d’ID change pour les points de terminaison v 2.0. Pour plus d’informations, consultez [pourquoi mettre à jour vers Microsoft Identity Platform (v 2.0) ?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) dans la documentation Azure.
* Étant donné que les ressources sont spécifiées dans les URI de portée pour les points de terminaison v 2.0, supprimez le <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> paramètre de propriété dans <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  Pour plus d’informations, consultez [étendues, et non pas les ressources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) dans la documentation Azure.

### <a name="app-id-uri"></a>URI ID de l’application

* Lors de l’utilisation de points de terminaison v 2.0, les API définissent un *`App ID URI`* , qui est destiné à représenter un identificateur unique pour l’API.
* Toutes les étendues incluent l’URI ID d’application en tant que préfixe, et les points de terminaison v 2.0 émettent des jetons d’accès avec l’URI ID d’application en tant que public.
* Lors de l’utilisation de points de terminaison V 2.0, l’ID client configuré dans l’API du serveur passe de l’ID d’application API (ID client) à l’URI ID d’application.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

Vous pouvez trouver l’URI ID d’application à utiliser dans la description de l’inscription de l’application du fournisseur OIDC.

::: moniker-end
