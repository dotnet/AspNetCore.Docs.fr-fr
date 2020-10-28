---
title: 'Utilisez API Graph avec ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Découvrez comment utiliser API Graph avec les :::no-loc(Blazor)::: applications WebAssemlby.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 3c77a8b39562c0145d1441cfdfe1dcf57aa3a613
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92692085"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="2b0c4-103">Utilisez API Graph avec ASP.NET Core :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="2b0c4-103">Use Graph API with ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="2b0c4-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b0c4-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2b0c4-105">[Microsoft Graph API](/graph/use-the-api) est une API Web RESTful qui permet :::no-loc(Blazor)::: à et d’autres applications .NET Framework d’accéder aux ressources de service Microsoft Cloud.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-105">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables :::no-loc(Blazor)::: and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="2b0c4-106">SDK Graph</span><span class="sxs-lookup"><span data-stu-id="2b0c4-106">Graph SDK</span></span>

<span data-ttu-id="2b0c4-107">Les kits de développement logiciel ( [SDK) Microsoft Graph](/graph/sdks/sdks-overview) sont conçus pour simplifier la création d’applications de haute qualité, efficaces et résilientes qui accèdent à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-107">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="2b0c4-108">Les exemples de cette section nécessitent des références de package pour les packages suivants dans le fichier projet du fichier projet autonome ou de l' *`Client`* application :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-108">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="2b0c4-109">Les classes et la configuration de l’utilitaire suivantes sont utilisées dans chacune des sous-sections suivantes de cet article :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-109">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="2b0c4-110">Appeler API Graph à partir d’un composant à l’aide du kit de développement logiciel (SDK) Graph</span><span class="sxs-lookup"><span data-stu-id="2b0c4-110">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="2b0c4-111">Personnaliser les revendications de l’utilisateur avec le kit de développement logiciel Graph</span><span class="sxs-lookup"><span data-stu-id="2b0c4-111">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="2b0c4-112">Après l’ajout des étendues d’API Microsoft Graph dans la zone AAD du Portail Azure :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-112">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="2b0c4-113">Ajoutez la `GraphClientExtensions.cs` classe suivante à l’application autonome ou à l' *`Client`* application d’une solution hébergée :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="2b0c4-113">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution.</span></span>
* <span data-ttu-id="2b0c4-114">Fournissez les étendues requises à la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> propriété de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> dans la `AuthenticateRequestAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-114">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="2b0c4-115">Dans l’exemple suivant, l' `User.Read` étendue est spécifiée pour correspondre aux exemples des sections ultérieures de cet article.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-115">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

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

        public IAccessTokenProvider Provider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await Provider.RequestAccessToken(
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
        private readonly HttpClient client;

        public HttpClientHttpProvider(HttpClient client)
        {
            this.client = client;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return client.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return client.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

<span data-ttu-id="2b0c4-116">L’espace réservé `{STRING ARRAY OF SCOPES}` dans le code précédent est un tableau de chaînes des étendues autorisées.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-116">The placeholder `{STRING ARRAY OF SCOPES}` in the preceding code is a string array of the permitted scopes.</span></span> <span data-ttu-id="2b0c4-117">Par exemple, définissez `Scopes` sur l' `User.Read` étendue des exemples des sections suivantes de cet article :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-117">For example, set `Scopes` to the `User.Read` scope for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="2b0c4-118">Dans `Program.Main` ( `Program.cs` ), ajoutez le graphique services du client et la configuration avec la `AddGraphClient` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-118">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

<span data-ttu-id="2b0c4-119">L’espace réservé `{STRING ARRAY OF SCOPES}` dans le code précédent est un tableau de chaînes des étendues autorisées.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-119">The placeholder `{STRING ARRAY OF SCOPES}` in the preceding code is a string array of the permitted scopes.</span></span> <span data-ttu-id="2b0c4-120">Par exemple, transmettez l' `User.Read` étendue à `AddGraphClient` pour les exemples dans les sections suivantes de cet article :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-120">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="2b0c4-121">Appeler API Graph à partir d’un composant à l’aide du kit de développement logiciel (SDK) Graph</span><span class="sxs-lookup"><span data-stu-id="2b0c4-121">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="2b0c4-122">Cette section utilise les [classes utilitaires ( `GraphClientExtensions.cs` )](#graph-sdk) décrites précédemment dans cet article.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-122">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="2b0c4-123">Le `GraphExample` composant suivant utilise un injecté `GraphServiceClient` pour obtenir les données de profil AAD de l’utilisateur et afficher son numéro de téléphone mobile :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-123">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

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

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="2b0c4-124">Personnaliser les revendications de l’utilisateur avec le kit de développement logiciel Graph</span><span class="sxs-lookup"><span data-stu-id="2b0c4-124">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="2b0c4-125">Cette section utilise les [classes utilitaires ( `GraphClientExtensions.cs` )](#graph-sdk) décrites précédemment dans cet article.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-125">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="2b0c4-126">Dans l’exemple suivant, l’application crée une revendication de numéro de téléphone mobile pour un utilisateur à partir du numéro de téléphone mobile de son profil utilisateur AAD.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-126">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="2b0c4-127">L' `User.Read` étendue de API Graph doit être configurée dans AAD pour l’application.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-127">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="2b0c4-128">Dans la fabrique de comptes d’utilisateurs personnalisée suivante, le Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> représente le compte de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-128">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="2b0c4-129">Si l’application nécessite une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-129">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="2b0c4-130">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b0c4-130">`CustomAccountFactory.cs`:</span></span>

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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("mobilephone", 
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

<span data-ttu-id="2b0c4-131">Dans `Program.Main` ( `Program.cs` ), configurez l’authentification MSAL pour utiliser la fabrique de comptes d’utilisateur personnalisée : si l’application utilise une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-131">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

## <a name="named-client-with-graph-api"></a><span data-ttu-id="2b0c4-132">Client nommé avec API Graph</span><span class="sxs-lookup"><span data-stu-id="2b0c4-132">Named client with Graph API</span></span>

<span data-ttu-id="2b0c4-133">Les exemples de cette section utilisent un nommé <xref:System.Net.Http.HttpClient> pour API Graph pour obtenir le numéro de téléphone mobile d’un utilisateur pour traiter un appel.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-133">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="2b0c4-134">Les exemples de cette section nécessitent une référence de package pour [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) dans le fichier projet du fichier projet autonome ou de l' *`Client`* application.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-134">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="2b0c4-135">Créez la classe et la configuration de projet suivantes pour l’utilisation de API Graph.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-135">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="2b0c4-136">La classe et la configuration suivantes sont utilisées dans chacune des sous-sections suivantes de cet article :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-136">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="2b0c4-137">Appeler API Graph à partir d’un composant</span><span class="sxs-lookup"><span data-stu-id="2b0c4-137">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="2b0c4-138">Personnaliser les revendications de l’utilisateur avec API Graph et un client nommé</span><span class="sxs-lookup"><span data-stu-id="2b0c4-138">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="2b0c4-139">Après avoir ajouté les étendues d’API Microsoft Graph dans la zone AAD du Portail Azure, fournissez les étendues requises au gestionnaire configuré de l’application pour API Graph.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-139">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="2b0c4-140">L’exemple suivant configure le gestionnaire de l' `User.Read` étendue.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-140">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="2b0c4-141">Des étendues supplémentaires peuvent être ajoutées.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-141">Additional scopes can be added.</span></span>

<span data-ttu-id="2b0c4-142">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b0c4-142">`GraphAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="2b0c4-143">Dans `Program.Main` ( `Program.cs` ), configurez le nommé <xref:System.Net.Http.HttpClient> pour API Graph :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-143">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="2b0c4-144">Appeler API Graph à partir d’un composant</span><span class="sxs-lookup"><span data-stu-id="2b0c4-144">Call Graph API from a component</span></span>

<span data-ttu-id="2b0c4-145">Cette section utilise le [Gestionnaire de messages d’autorisation Graph ( `GraphAuthorizationMessageHandler.cs` ) et `Program.Main` les ajouts à l’application](#named-client-with-graph-api) décrite précédemment dans cet article, qui fournit un nommé <xref:System.Net.Http.HttpClient> pour API Graph.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-145">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="2b0c4-146">Dans un :::no-loc(Razor)::: composant :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-146">In a :::no-loc(Razor)::: component:</span></span>

* <span data-ttu-id="2b0c4-147">Créez un <xref:System.Net.Http.HttpClient> pour API Graph et émettez une demande pour les données de profil de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-147">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="2b0c4-148">La `UserInfo.cs` classe désigne les propriétés de profil utilisateur requises avec l' <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribut et le nom JSON utilisé par AAD pour ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-148">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="2b0c4-149">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b0c4-149">`Pages/CallUser.razor`:</span></span>

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
> <span data-ttu-id="2b0c4-150">Dans l’exemple précédent, le développeur implémente le personnalisé `ICallProcessor` ( `CallProcessor` ) dans la file d’attente, puis place les appels automatisés.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-150">In the preceding example, the developer implements the custom `ICallProcessor` (`CallProcessor`) to queue and then place automated calls.</span></span>

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="2b0c4-151">Personnaliser les revendications de l’utilisateur avec API Graph et un client nommé</span><span class="sxs-lookup"><span data-stu-id="2b0c4-151">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="2b0c4-152">Cette section utilise le [Gestionnaire de messages d’autorisation Graph ( `GraphAuthorizationMessageHandler.cs` ) et `Program.Main` les ajouts à l’application](#named-client-with-graph-api) décrite précédemment dans cet article, qui fournit un nommé <xref:System.Net.Http.HttpClient> pour API Graph.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-152">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="2b0c4-153">Dans l’exemple suivant, l’application crée une revendication de numéro de téléphone mobile pour l’utilisateur à partir du numéro de téléphone mobile de son profil utilisateur AAD.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-153">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="2b0c4-154">L' `User.Read` étendue de API Graph doit être configurée dans AAD pour l’application.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-154">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="2b0c4-155">Ajoutez une `UserInfo.cs` classe à l’application et désignez les propriétés de profil utilisateur requises avec l' <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribut et le nom JSON utilisé par AAD pour ces propriétés :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-155">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="2b0c4-156">Dans la fabrique de comptes d’utilisateurs personnalisée suivante, le Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> représente le compte de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-156">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="2b0c4-157">Si l’application nécessite une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-157">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="2b0c4-158">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b0c4-158">`CustomAccountFactory.cs`:</span></span>

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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("mobilephone", 
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

<span data-ttu-id="2b0c4-159">Dans `Program.Main` ( `Program.cs` ), configurez l’authentification MSAL pour utiliser la fabrique de comptes d’utilisateur personnalisée : si l’application utilise une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2b0c4-159">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

<span data-ttu-id="2b0c4-160">L’exemple précédent concerne une application qui utilise l’authentification AAD avec MSAL.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-160">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="2b0c4-161">Des modèles similaires existent pour l’authentification OIDC et l’API.</span><span class="sxs-lookup"><span data-stu-id="2b0c4-161">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="2b0c4-162">Pour plus d’informations, consultez les exemples de la section [personnaliser l’utilisateur avec une revendication de charge utile](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) .</span><span class="sxs-lookup"><span data-stu-id="2b0c4-162">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
