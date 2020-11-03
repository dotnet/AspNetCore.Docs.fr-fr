---
title: Utilisez API Graph avec ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment utiliser API Graph avec les Blazor applications WebAssemlby.
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 569a88630f7b75e866d8ecda99605ebe3bc58db8
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234425"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>Utilisez API Graph avec ASP.NET Core Blazor WebAssembly

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

[Microsoft Graph API](/graph/use-the-api) est une API Web RESTful qui permet Blazor à et d’autres applications .NET Framework d’accéder aux ressources de service Microsoft Cloud.

## <a name="graph-sdk"></a>SDK Graph

Les kits de développement logiciel ( [SDK) Microsoft Graph](/graph/sdks/sdks-overview) sont conçus pour simplifier la création d’applications de haute qualité, efficaces et résilientes qui accèdent à Microsoft Graph.

Les exemples de cette section nécessitent des références de package pour les packages suivants dans le fichier projet du fichier projet autonome ou de l' *`Client`* application :

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

Les classes et la configuration de l’utilitaire suivantes sont utilisées dans chacune des sous-sections suivantes de cet article :

* [Appeler API Graph à partir d’un composant à l’aide du kit de développement logiciel (SDK) Graph](#call-graph-api-from-a-component-using-the-graph-sdk)
* [Personnaliser les revendications de l’utilisateur avec le kit de développement logiciel Graph](#customize-user-claims-with-the-graph-sdk)

Après l’ajout des étendues d’API Microsoft Graph dans la zone AAD du Portail Azure :

* Ajoutez la `GraphClientExtensions.cs` classe suivante à l’application autonome ou à l' *`Client`* application d’une solution hébergée Blazor .
* Fournissez les étendues requises à la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> propriété de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> dans la `AuthenticateRequestAsync` méthode. Dans l’exemple suivant, l' `User.Read` étendue est spécifiée pour correspondre aux exemples des sections ultérieures de cet article.

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider provider)
        {
            Provider = provider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = {STRING ARRAY OF SCOPES}
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

L’espace réservé `{STRING ARRAY OF SCOPES}` dans le code précédent est un tableau de chaînes des étendues autorisées. Par exemple, définissez `Scopes` sur l' `User.Read` étendue des exemples des sections suivantes de cet article :

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

Dans `Program.Main` ( `Program.cs` ), ajoutez le graphique services du client et la configuration avec la `AddGraphClient` méthode d’extension :

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

L’espace réservé `{STRING ARRAY OF SCOPES}` dans le code précédent est un tableau de chaînes des étendues autorisées. Par exemple, transmettez l' `User.Read` étendue à `AddGraphClient` pour les exemples dans les sections suivantes de cet article :

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>Appeler API Graph à partir d’un composant à l’aide du kit de développement logiciel (SDK) Graph

Cette section utilise les [classes utilitaires ( `GraphClientExtensions.cs` )](#graph-sdk) décrites précédemment dans cet article. Le `GraphExample` composant suivant utilise un injecté `GraphServiceClient` pour obtenir les données de profil AAD de l’utilisateur et afficher son numéro de téléphone mobile :

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a>Personnaliser les revendications de l’utilisateur avec le kit de développement logiciel Graph

Cette section utilise les [classes utilitaires ( `GraphClientExtensions.cs` )](#graph-sdk) décrites précédemment dans cet article.

Dans l’exemple suivant, l’application crée une revendication de numéro de téléphone mobile pour un utilisateur à partir du numéro de téléphone mobile de son profil utilisateur AAD. L' `User.Read` étendue de API Graph doit être configurée dans AAD pour l’application.

Dans la fabrique de comptes d’utilisateurs personnalisée suivante, le Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> représente le compte de l’utilisateur. Si l’application nécessite une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant.

`CustomAccountFactory.cs`:

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
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
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
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
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

Dans `Program.Main` ( `Program.cs` ), configurez l’authentification MSAL pour utiliser la fabrique de comptes d’utilisateur personnalisée : si l’application utilise une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a>Client nommé avec API Graph

Les exemples de cette section utilisent un nommé <xref:System.Net.Http.HttpClient> pour API Graph pour obtenir le numéro de téléphone mobile d’un utilisateur pour traiter un appel.

Les exemples de cette section nécessitent une référence de package pour [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) dans le fichier projet du fichier projet autonome ou de l' *`Client`* application.

Créez la classe et la configuration de projet suivantes pour l’utilisation de API Graph. La classe et la configuration suivantes sont utilisées dans chacune des sous-sections suivantes de cet article :

* [Appeler API Graph à partir d’un composant](#call-graph-api-from-a-component)
* [Personnaliser les revendications de l’utilisateur avec API Graph et un client nommé](#customize-user-claims-with-graph-api-and-a-named-client)

Après avoir ajouté les étendues d’API Microsoft Graph dans la zone AAD du Portail Azure, fournissez les étendues requises au gestionnaire configuré de l’application pour API Graph. L’exemple suivant configure le gestionnaire de l' `User.Read` étendue. Des étendues supplémentaires peuvent être ajoutées.

`GraphAuthorizationMessageHandler.cs`:

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
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

Dans `Program.Main` ( `Program.cs` ), configurez le nommé <xref:System.Net.Http.HttpClient> pour API Graph :

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>Appeler API Graph à partir d’un composant

Cette section utilise le [Gestionnaire de messages d’autorisation Graph ( `GraphAuthorizationMessageHandler.cs` ) et `Program.Main` les ajouts à l’application](#named-client-with-graph-api) décrite précédemment dans cet article, qui fournit un nommé <xref:System.Net.Http.HttpClient> pour API Graph.

Dans un Razor composant :

* Créez un <xref:System.Net.Http.HttpClient> pour API Graph et émettez une demande pour les données de profil de l’utilisateur.
* La `UserInfo.cs` classe désigne les propriétés de profil utilisateur requises avec l' <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribut et le nom JSON utilisé par AAD pour ces propriétés.

`Pages/CallUser.razor`:

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

> [!NOTE]
> Dans l’exemple précédent, le développeur implémente le personnalisé `ICallProcessor` ( `CallProcessor` ) dans la file d’attente, puis place les appels automatisés.

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>Personnaliser les revendications de l’utilisateur avec API Graph et un client nommé

Cette section utilise le [Gestionnaire de messages d’autorisation Graph ( `GraphAuthorizationMessageHandler.cs` ) et `Program.Main` les ajouts à l’application](#named-client-with-graph-api) décrite précédemment dans cet article, qui fournit un nommé <xref:System.Net.Http.HttpClient> pour API Graph.

Dans l’exemple suivant, l’application crée une revendication de numéro de téléphone mobile pour l’utilisateur à partir du numéro de téléphone mobile de son profil utilisateur AAD. L' `User.Read` étendue de API Graph doit être configurée dans AAD pour l’application.

Ajoutez une `UserInfo.cs` classe à l’application et désignez les propriétés de profil utilisateur requises avec l' <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribut et le nom JSON utilisé par AAD pour ces propriétés :

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

Dans la fabrique de comptes d’utilisateurs personnalisée suivante, le Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> représente le compte de l’utilisateur. Si l’application nécessite une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant.

`CustomAccountFactory.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Dans `Program.Main` ( `Program.cs` ), configurez l’authentification MSAL pour utiliser la fabrique de comptes d’utilisateur personnalisée : si l’application utilise une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

L’exemple précédent concerne une application qui utilise l’authentification AAD avec MSAL. Des modèles similaires existent pour l’authentification OIDC et l’API. Pour plus d’informations, consultez les exemples de la section [personnaliser l’utilisateur avec une revendication de charge utile](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) .
