---
title: 'ASP.NET Core Blazor WebAssembly des scénarios de sécurité supplémentaires'
author: guardrex
description: 'Découvrez comment configurer Blazor WebAssembly pour d’autres scénarios de sécurité.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: baed18df2d127b592f420aac0432e0b28f076d46
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508043"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="74b8d-103">ASP.NET Core Blazor WebAssembly des scénarios de sécurité supplémentaires</span><span class="sxs-lookup"><span data-stu-id="74b8d-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="74b8d-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="74b8d-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="74b8d-105">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="74b8d-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="74b8d-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> est <xref:System.Net.Http.DelegatingHandler> utilisé pour attacher des jetons d’accès aux instances sortantes <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="74b8d-107">Les jetons sont acquis à l’aide du <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, qui est enregistré par le Framework.</span><span class="sxs-lookup"><span data-stu-id="74b8d-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="74b8d-108">Si un jeton ne peut pas être acquis, une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> exception est levée.</span><span class="sxs-lookup"><span data-stu-id="74b8d-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="74b8d-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> dispose d’une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> méthode qui peut être utilisée pour accéder au fournisseur d’identité de l’utilisateur afin d’acquérir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="74b8d-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="74b8d-110">Pour plus de commodité, le Framework fournit l' <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> adresse préconfigurée avec l’adresse de base de l’application en tant qu’URL autorisée.</span><span class="sxs-lookup"><span data-stu-id="74b8d-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="74b8d-111">**Les jetons d’accès sont ajoutés uniquement lorsque l’URI de la demande se trouve dans l’URI de base de l’application.**</span><span class="sxs-lookup"><span data-stu-id="74b8d-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="74b8d-112">Lorsque les URI de demande sortants ne se trouvent pas dans l’URI de base de l’application, utilisez une [ `AuthorizationMessageHandler` classe personnalisée ( *recommandé* )](#custom-authorizationmessagehandler-class) ou [configurez le `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="74b8d-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class ( *recommended* )](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="74b8d-113">Outre la configuration de l’application cliente pour l’accès à l’API serveur, l’API serveur doit également autoriser les requêtes Cross-Origin (CORS) lorsque le client et le serveur ne se trouvent pas à la même adresse de base.</span><span class="sxs-lookup"><span data-stu-id="74b8d-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="74b8d-114">Pour plus d’informations sur la configuration CORS côté serveur, consultez la section relative au [partage des ressources Cross-Origin (cors)](#cross-origin-resource-sharing-cors) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="74b8d-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="74b8d-115">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="74b8d-115">In the following example:</span></span>

* <span data-ttu-id="74b8d-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> Ajoute <xref:System.Net.Http.IHttpClientFactory> des services connexes à la collection de services et configure un nommé <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="74b8d-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> adresse de base de l’URI de la ressource lors de l’envoi des demandes.</span><span class="sxs-lookup"><span data-stu-id="74b8d-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="74b8d-118"><xref:System.Net.Http.IHttpClientFactory> est fourni par le [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package NuGet.</span><span class="sxs-lookup"><span data-stu-id="74b8d-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="74b8d-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> est <xref:System.Net.Http.DelegatingHandler> utilisé pour attacher des jetons d’accès aux instances sortantes <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="74b8d-120">Les jetons d’accès sont ajoutés uniquement lorsque l’URI de la demande se trouve dans l’URI de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="74b8d-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="74b8d-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> crée et configure une <xref:System.Net.Http.HttpClient> instance pour les demandes sortantes à l’aide de la configuration qui correspond au nommé <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="74b8d-122">Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, les URI de requête se trouvent dans l’URI de base de l’application par défaut.</span><span class="sxs-lookup"><span data-stu-id="74b8d-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="74b8d-123">Par conséquent, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) est assigné au <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> dans une application générée à partir du modèle de projet.</span><span class="sxs-lookup"><span data-stu-id="74b8d-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="74b8d-124">Le configuré <xref:System.Net.Http.HttpClient> est utilisé pour effectuer des demandes autorisées à l’aide du [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) modèle :</span><span class="sxs-lookup"><span data-stu-id="74b8d-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Http.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="74b8d-125">`AuthorizationMessageHandler`Classe personnalisée</span><span class="sxs-lookup"><span data-stu-id="74b8d-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="74b8d-126">*Ce guide de cette section est recommandé pour les applications clientes qui effectuent des requêtes sortantes vers des URI qui ne se trouvent pas dans l’URI de base de l’application.*</span><span class="sxs-lookup"><span data-stu-id="74b8d-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="74b8d-127">Dans l’exemple suivant, une classe personnalisée étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> pour une utilisation en tant que <xref:System.Net.Http.DelegatingHandler> pour un <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="74b8d-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Configure ce gestionnaire pour autoriser les requêtes HTTP sortantes à l’aide d’un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="74b8d-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="74b8d-129">Le jeton d’accès est attaché uniquement si au moins l’une des URL autorisées est une base de l’URI de la demande ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="74b8d-130">Dans `Program.Main` ( `Program.cs` ), `CustomAuthorizationMessageHandler` est inscrit en tant que service délimitéd et est configuré en tant que <xref:System.Net.Http.DelegatingHandler> pour les instances sortantes <xref:System.Net.Http.HttpResponseMessage> effectuées par un nommé <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="74b8d-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="74b8d-131">Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) est assigné à <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> par défaut.</span><span class="sxs-lookup"><span data-stu-id="74b8d-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="74b8d-132">Le configuré <xref:System.Net.Http.HttpClient> est utilisé pour effectuer des demandes autorisées à l’aide du [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) modèle.</span><span class="sxs-lookup"><span data-stu-id="74b8d-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="74b8d-133">Lorsque le client est créé avec <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) Package), <xref:System.Net.Http.HttpClient> est fourni les instances qui incluent des jetons d’accès lors de l’exécution de requêtes à l’API serveur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="74b8d-134">Si l’URI de la demande est un URI relatif, comme c’est le cas dans l’exemple suivant ( `ExampleAPIMethod` ), il est combiné avec le <xref:System.Net.Http.HttpClient.BaseAddress> lorsque l’application cliente effectue la requête :</span><span class="sxs-lookup"><span data-stu-id="74b8d-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="74b8d-135">Configurer `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="74b8d-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="74b8d-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> peut être configuré avec des URL, des étendues et une URL de retour autorisées à l’aide de la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="74b8d-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="74b8d-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configure le gestionnaire pour autoriser les requêtes HTTP sortantes à l’aide d’un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="74b8d-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="74b8d-138">Le jeton d’accès est attaché uniquement si au moins l’une des URL autorisées est une base de l’URI de la demande ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="74b8d-139">Si l’URI de la demande est un URI relatif, il est associé au <xref:System.Net.Http.HttpClient.BaseAddress> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="74b8d-140">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configure un <xref:System.Net.Http.HttpClient> dans `Program.Main` ( `Program.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="74b8d-141">Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> est assigné aux éléments suivants par défaut :</span><span class="sxs-lookup"><span data-stu-id="74b8d-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="74b8d-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="74b8d-143">URL du `authorizedUrls` tableau.</span><span class="sxs-lookup"><span data-stu-id="74b8d-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="74b8d-144">Tapé `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="74b8d-144">Typed `HttpClient`</span></span>

<span data-ttu-id="74b8d-145">Vous pouvez définir un client typé qui gère tous les problèmes d’acquisition de jetons et HTTP dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="74b8d-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="74b8d-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="74b8d-146">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient http;
 
    public WeatherForecastClient(HttpClient http)
    {
        this.http = http;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="74b8d-147">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `using static BlazorSample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="74b8d-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="74b8d-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="74b8d-149">Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) est assigné à <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> par défaut.</span><span class="sxs-lookup"><span data-stu-id="74b8d-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="74b8d-150">`FetchData` composant ( `Pages/FetchData.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="74b8d-151">Configurer le `HttpClient` Gestionnaire</span><span class="sxs-lookup"><span data-stu-id="74b8d-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="74b8d-152">Le gestionnaire peut être configuré avec <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pour les requêtes http sortantes.</span><span class="sxs-lookup"><span data-stu-id="74b8d-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="74b8d-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="74b8d-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="74b8d-154">Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> est assigné aux éléments suivants par défaut :</span><span class="sxs-lookup"><span data-stu-id="74b8d-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="74b8d-155"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="74b8d-156">URL du `authorizedUrls` tableau.</span><span class="sxs-lookup"><span data-stu-id="74b8d-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="74b8d-157">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="74b8d-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="74b8d-158">Si l' Blazor WebAssembly application utilise généralement une valeur par défaut sécurisée <xref:System.Net.Http.HttpClient> , l’application peut également effectuer des demandes d’API Web non authentifiées ou non autorisées en configurant un nom <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="74b8d-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="74b8d-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="74b8d-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="74b8d-160">Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) est assigné à <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> par défaut.</span><span class="sxs-lookup"><span data-stu-id="74b8d-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="74b8d-161">L’inscription précédente s’ajoute à l’inscription par défaut sécurisée existante <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="74b8d-162">Un composant crée le <xref:System.Net.Http.HttpClient> à partir du <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) Package) pour effectuer des demandes non authentifiées ou non autorisées :</span><span class="sxs-lookup"><span data-stu-id="74b8d-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="74b8d-163">Dans l’exemple précédent, le contrôleur de l’API serveur `WeatherForecastNoAuthenticationController` n’est pas marqué avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="74b8d-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="74b8d-164">Le développeur doit décider s’il faut utiliser un client sécurisé ou un client non sécurisé comme instance par défaut <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="74b8d-165">L’une des façons de prendre cette décision est de prendre en compte le nombre de points de terminaison authentifiés et non authentifiés que l’application contacte.</span><span class="sxs-lookup"><span data-stu-id="74b8d-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="74b8d-166">Si la majorité des demandes de l’application sont de sécuriser les points de terminaison d’API, utilisez l’instance authentifiée <xref:System.Net.Http.HttpClient> comme valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="74b8d-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="74b8d-167">Sinon, inscrivez l’instance non authentifiée <xref:System.Net.Http.HttpClient> comme valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="74b8d-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="74b8d-168">Une autre approche de l’utilisation de l' <xref:System.Net.Http.IHttpClientFactory> consiste à créer un [client typé](#typed-httpclient) pour l’accès non authentifié aux points de terminaison anonymes.</span><span class="sxs-lookup"><span data-stu-id="74b8d-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="74b8d-169">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="74b8d-169">Request additional access tokens</span></span>

<span data-ttu-id="74b8d-170">Les jetons d’accès peuvent être obtenus manuellement en appelant `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="74b8d-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="74b8d-171">Dans l’exemple suivant, une étendue supplémentaire est requise par une application pour la valeur par défaut <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-171">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="74b8d-172">L’exemple de la bibliothèque d’authentification Microsoft (MSAL) configure l’étendue avec `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="74b8d-172">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="74b8d-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="74b8d-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="74b8d-174">Les `{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` espaces réservés et dans l’exemple précédent sont des étendues personnalisées.</span><span class="sxs-lookup"><span data-stu-id="74b8d-174">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="74b8d-175">La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de configurer un jeton d’accès avec un ensemble donné d’étendues.</span><span class="sxs-lookup"><span data-stu-id="74b8d-175">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="74b8d-176">Dans un Razor composant :</span><span class="sxs-lookup"><span data-stu-id="74b8d-176">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="74b8d-177">Les `{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` espaces réservés et dans l’exemple précédent sont des étendues personnalisées.</span><span class="sxs-lookup"><span data-stu-id="74b8d-177">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="74b8d-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> Cette</span><span class="sxs-lookup"><span data-stu-id="74b8d-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="74b8d-179">`true` avec le `token` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="74b8d-179">`true` with the `token` for use.</span></span>
* <span data-ttu-id="74b8d-180">`false` Si le jeton n’est pas récupéré.</span><span class="sxs-lookup"><span data-stu-id="74b8d-180">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="74b8d-181">Partage des ressources cross-origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="74b8d-181">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="74b8d-182">Lors de l’envoi d’informations d’identification ( cookie s/en-têtes d’autorisation) sur les demandes cors, l' `Authorization` en-tête doit être autorisé par la stratégie cors.</span><span class="sxs-lookup"><span data-stu-id="74b8d-182">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="74b8d-183">La stratégie suivante comprend la configuration pour :</span><span class="sxs-lookup"><span data-stu-id="74b8d-183">The following policy includes configuration for:</span></span>

* <span data-ttu-id="74b8d-184">Origines des demandes ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-184">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="74b8d-185">Toute méthode (verbe).</span><span class="sxs-lookup"><span data-stu-id="74b8d-185">Any method (verb).</span></span>
* <span data-ttu-id="74b8d-186">`Content-Type` et `Authorization` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="74b8d-186">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="74b8d-187">Pour autoriser un en-tête personnalisé (par exemple, `x-custom-header` ), répertoriez l’en-tête lors de l’appel de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-187">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="74b8d-188">Informations d’identification définies par le code JavaScript côté client (la `credentials` propriété a la valeur `include` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-188">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="74b8d-189">Une solution hébergée Blazor basée sur le Blazor modèle de projet hébergé utilise la même adresse de base pour les applications clientes et serveur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-189">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="74b8d-190">La valeur de l’URI de l’application cliente <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> est `builder.HostEnvironment.BaseAddress` par défaut.</span><span class="sxs-lookup"><span data-stu-id="74b8d-190">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="74b8d-191">La configuration CORS n’est **pas** requise dans la configuration par défaut d’une application hébergée créée à partir du Blazor modèle de projet hébergé.</span><span class="sxs-lookup"><span data-stu-id="74b8d-191">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="74b8d-192">Les applications clientes supplémentaires qui ne sont pas hébergées par le projet serveur et ne partagent pas **l’adresse de** base de l’application serveur nécessitent une configuration cors dans le projet serveur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-192">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="74b8d-193">Pour plus d’informations, consultez <xref:security/cors> et le composant testeur de requêtes http de l’exemple d’application ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-193">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="74b8d-194">Gérer les erreurs de demande de jeton</span><span class="sxs-lookup"><span data-stu-id="74b8d-194">Handle token request errors</span></span>

<span data-ttu-id="74b8d-195">Lorsqu’une application à page unique (SPA) authentifie un utilisateur à l’aide de OpenID Connect (OIDC), l’état d’authentification est conservé localement au sein du SPA et dans le Identity fournisseur (IP) sous la forme d’une session cookie définie à la suite de l’utilisateur qui fournit ses informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="74b8d-195">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="74b8d-196">Les jetons que l’adresse IP émet pour l’utilisateur sont généralement valides pendant de courtes périodes, environ une heure normalement, de sorte que l’application cliente doit régulièrement extraire les nouveaux jetons.</span><span class="sxs-lookup"><span data-stu-id="74b8d-196">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="74b8d-197">Dans le cas contraire, l’utilisateur est déconnecté après l’expiration des jetons accordés.</span><span class="sxs-lookup"><span data-stu-id="74b8d-197">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="74b8d-198">Dans la plupart des cas, les clients OIDC sont en mesure de configurer de nouveaux jetons sans que l’utilisateur soit obligé de s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservée au sein de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="74b8d-198">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="74b8d-199">Dans certains cas, le client ne peut pas obtenir un jeton sans intervention de l’utilisateur, par exemple, lorsque, pour une raison quelconque, l’utilisateur se déconnecte explicitement de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="74b8d-199">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="74b8d-200">Ce scénario se produit si un utilisateur visite `https://login.microsoftonline.com` et se déconnecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout jeton que le client contient peut ne plus être valide.</span><span class="sxs-lookup"><span data-stu-id="74b8d-200">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="74b8d-201">En outre, le client n’est pas en mesure d’approvisionner un nouveau jeton sans interaction de l’utilisateur après l’expiration du jeton actuel.</span><span class="sxs-lookup"><span data-stu-id="74b8d-201">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="74b8d-202">Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons.</span><span class="sxs-lookup"><span data-stu-id="74b8d-202">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="74b8d-203">Elles font partie de la nature des SPAs.</span><span class="sxs-lookup"><span data-stu-id="74b8d-203">They are part of the nature of SPAs.</span></span> <span data-ttu-id="74b8d-204">Un SPA utilisant cookie s ne parvient pas non plus à appeler une API serveur si l’authentification cookie est supprimée.</span><span class="sxs-lookup"><span data-stu-id="74b8d-204">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="74b8d-205">Quand une application effectue des appels d’API vers des ressources protégées, vous devez tenir compte des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="74b8d-205">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="74b8d-206">Pour approvisionner un nouveau jeton d’accès pour appeler l’API, l’utilisateur peut être amené à s’authentifier à nouveau.</span><span class="sxs-lookup"><span data-stu-id="74b8d-206">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="74b8d-207">Même si le client possède un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-207">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="74b8d-208">Lorsque l’application demande un jeton, deux résultats sont possibles :</span><span class="sxs-lookup"><span data-stu-id="74b8d-208">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="74b8d-209">La demande a échoué et l’application a un jeton valide.</span><span class="sxs-lookup"><span data-stu-id="74b8d-209">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="74b8d-210">La demande échoue et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="74b8d-210">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="74b8d-211">En cas d’échec d’une demande de jeton, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection.</span><span class="sxs-lookup"><span data-stu-id="74b8d-211">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="74b8d-212">Il existe plusieurs approches avec des niveaux de complexité de plus en plus complexes :</span><span class="sxs-lookup"><span data-stu-id="74b8d-212">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="74b8d-213">Stocke l’état actuel de la page dans le stockage de session.</span><span class="sxs-lookup"><span data-stu-id="74b8d-213">Store the current page state in session storage.</span></span> <span data-ttu-id="74b8d-214">Pendant l' [ `OnInitializedAsync` événement de cycle de vie](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), vérifiez si l’État peut être restauré avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="74b8d-214">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="74b8d-215">Ajoutez un paramètre de chaîne de requête et utilisez-le comme méthode pour signaler à l’application qu’elle doit réalimenter l’état enregistré précédemment.</span><span class="sxs-lookup"><span data-stu-id="74b8d-215">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="74b8d-216">Ajoutez un paramètre de chaîne de requête avec un identificateur unique pour stocker les données dans le stockage de session sans risquer des collisions avec d’autres éléments.</span><span class="sxs-lookup"><span data-stu-id="74b8d-216">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="74b8d-217">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="74b8d-217">The following example shows how to:</span></span>

* <span data-ttu-id="74b8d-218">Conserver l’état avant la redirection vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="74b8d-218">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="74b8d-219">Récupérez l’état précédent après l’authentification à l’aide du paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="74b8d-219">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider
...

<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var http = new HttpClient();
        http.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            http.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await http.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="74b8d-220">Enregistrer l’état de l’application avant une opération d’authentification</span><span class="sxs-lookup"><span data-stu-id="74b8d-220">Save app state before an authentication operation</span></span>

<span data-ttu-id="74b8d-221">Au cours d’une opération d’authentification, il existe des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur soit redirigé vers l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="74b8d-221">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="74b8d-222">Cela peut être le cas lorsque vous utilisez un conteneur d’État et que vous souhaitez restaurer l’État une fois l’authentification réussie.</span><span class="sxs-lookup"><span data-stu-id="74b8d-222">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="74b8d-223">Vous pouvez utiliser un objet d’état d’authentification personnalisé pour conserver l’état spécifique à l’application ou une référence à celui-ci, et restaurer cet État une fois l’opération d’authentification terminée.</span><span class="sxs-lookup"><span data-stu-id="74b8d-223">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="74b8d-224">L’exemple suivant illustre l’approche.</span><span class="sxs-lookup"><span data-stu-id="74b8d-224">The following example demonstrates the approach.</span></span>

<span data-ttu-id="74b8d-225">Une classe de conteneur d’État est créée dans l’application avec des propriétés pour contenir les valeurs d’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="74b8d-225">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="74b8d-226">Dans l’exemple suivant, le conteneur est utilisé pour conserver la valeur de compteur du composant du modèle de projet par défaut `Counter` ( `Pages/Counter.razor` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-226">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="74b8d-227">Les méthodes de sérialisation et de désérialisation du conteneur sont basées sur <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-227">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="74b8d-228">Le `Counter` composant utilise le conteneur d’État pour conserver la `currentCount` valeur en dehors du composant :</span><span class="sxs-lookup"><span data-stu-id="74b8d-228">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="74b8d-229">Créez un `ApplicationAuthenticationState` à partir de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-229">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="74b8d-230">Fournissez une `Id` propriété, qui sert d’identificateur pour l’État stocké localement.</span><span class="sxs-lookup"><span data-stu-id="74b8d-230">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="74b8d-231">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="74b8d-231">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="74b8d-232">Le `Authentication` composant ( `Pages/Authentication.razor` ) enregistre et restaure l’état de l’application à l’aide du stockage de session locale avec les `StateContainer` méthodes de sérialisation et de désérialisation, `GetStateForLocalStorage` et `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="74b8d-232">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="74b8d-233">Cet exemple utilise Azure Active Directory (AAD) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="74b8d-233">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="74b8d-234">Dans `Program.Main` ( `Program.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-234">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="74b8d-235">Le `ApplicationAuthenticationState` est configuré en tant que type Microsoft authentification Library (MSAL) `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="74b8d-235">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="74b8d-236">Le conteneur d’État est inscrit dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="74b8d-236">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="74b8d-237">Personnaliser les itinéraires de l’application</span><span class="sxs-lookup"><span data-stu-id="74b8d-237">Customize app routes</span></span>

<span data-ttu-id="74b8d-238">Par défaut, la [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) bibliothèque utilise les itinéraires indiqués dans le tableau suivant pour représenter des États d’authentification différents.</span><span class="sxs-lookup"><span data-stu-id="74b8d-238">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="74b8d-239">Route</span><span class="sxs-lookup"><span data-stu-id="74b8d-239">Route</span></span>                            | <span data-ttu-id="74b8d-240">Objectif</span><span class="sxs-lookup"><span data-stu-id="74b8d-240">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="74b8d-241">Déclenche une opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="74b8d-241">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="74b8d-242">Gère le résultat de toute opération de connexion.</span><span class="sxs-lookup"><span data-stu-id="74b8d-242">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="74b8d-243">Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="74b8d-243">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="74b8d-244">Déclenche une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="74b8d-244">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="74b8d-245">Gère le résultat d’une opération de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="74b8d-245">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="74b8d-246">Affiche des messages d’erreur lorsque l’opération de déconnexion échoue pour une raison quelconque.</span><span class="sxs-lookup"><span data-stu-id="74b8d-246">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="74b8d-247">Indique que l’utilisateur a réussi à se déconnecter.</span><span class="sxs-lookup"><span data-stu-id="74b8d-247">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="74b8d-248">Déclenche une opération de modification du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-248">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="74b8d-249">Déclenche une opération pour inscrire un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-249">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="74b8d-250">Les itinéraires indiqués dans le tableau précédent sont configurables via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-250">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="74b8d-251">Quand vous définissez des options pour fournir des itinéraires personnalisés, vérifiez que l’application a un itinéraire qui gère chaque chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="74b8d-251">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="74b8d-252">Dans l’exemple suivant, tous les chemins d’accès ont pour préfixe `/security` .</span><span class="sxs-lookup"><span data-stu-id="74b8d-252">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="74b8d-253">`Authentication` composant ( `Pages/Authentication.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-253">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="74b8d-254">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="74b8d-254">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="74b8d-255">Si la spécification exige des chemins d’accès complètement différents, définissez les itinéraires comme décrit précédemment et affichez le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> avec un paramètre d’action explicite :</span><span class="sxs-lookup"><span data-stu-id="74b8d-255">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="74b8d-256">Si vous le souhaitez, vous avez la possibilité de scinder l’interface utilisateur en différentes pages.</span><span class="sxs-lookup"><span data-stu-id="74b8d-256">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="74b8d-257">Personnaliser l’interface utilisateur de l’authentification</span><span class="sxs-lookup"><span data-stu-id="74b8d-257">Customize the authentication user interface</span></span>

<span data-ttu-id="74b8d-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> comprend un ensemble par défaut de parties de l’interface utilisateur pour chaque État d’authentification.</span><span class="sxs-lookup"><span data-stu-id="74b8d-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="74b8d-259">Chaque État peut être personnalisé en passant un personnalisé <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-259">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="74b8d-260">Pour personnaliser le texte affiché pendant le processus de connexion initial, peut modifier le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> comme suit.</span><span class="sxs-lookup"><span data-stu-id="74b8d-260">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="74b8d-261">`Authentication` composant ( `Pages/Authentication.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-261">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="74b8d-262">Le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> a un fragment qui peut être utilisé par itinéraire d’authentification, comme indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="74b8d-262">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="74b8d-263">Route</span><span class="sxs-lookup"><span data-stu-id="74b8d-263">Route</span></span>                            | <span data-ttu-id="74b8d-264">Fragment</span><span class="sxs-lookup"><span data-stu-id="74b8d-264">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="74b8d-265">Personnaliser l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="74b8d-265">Customize the user</span></span>

<span data-ttu-id="74b8d-266">Les utilisateurs liés à l’application peuvent être personnalisés.</span><span class="sxs-lookup"><span data-stu-id="74b8d-266">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="74b8d-267">Personnaliser l’utilisateur avec une revendication de charge utile</span><span class="sxs-lookup"><span data-stu-id="74b8d-267">Customize the user with a payload claim</span></span>

<span data-ttu-id="74b8d-268">Dans l’exemple suivant, les utilisateurs authentifiés de l’application reçoivent une `amr` revendication pour chacune des méthodes d’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-268">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="74b8d-269">La `amr` revendication identifie la manière dont le sujet du jeton a été authentifié dans les Identity revendications de [charge utile](/azure/active-directory/develop/access-tokens#the-amr-claim)Microsoft Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="74b8d-269">The `amr` claim identifies how the subject of the token was authenticated in Microsoft Identity Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="74b8d-270">L’exemple utilise une classe de compte d’utilisateur personnalisée basée sur <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-270">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="74b8d-271">Créez une classe qui étend la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe.</span><span class="sxs-lookup"><span data-stu-id="74b8d-271">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="74b8d-272">L’exemple suivant affecte `AuthenticationMethod` à la propriété le tableau de valeurs de `amr` propriété JSON de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-272">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="74b8d-273">`AuthenticationMethod` est rempli automatiquement par le Framework lorsque l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="74b8d-273">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="74b8d-274">Créez une fabrique qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> pour créer des revendications à partir des méthodes d’authentification de l’utilisateur stockées dans `CustomUserAccount.AuthenticationMethod` :</span><span class="sxs-lookup"><span data-stu-id="74b8d-274">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="74b8d-275">Inscrivez le `CustomAccountFactory` pour le fournisseur d’authentification en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="74b8d-275">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="74b8d-276">Les inscriptions suivantes sont valides :</span><span class="sxs-lookup"><span data-stu-id="74b8d-276">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="74b8d-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="74b8d-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="74b8d-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="74b8d-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="74b8d-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="74b8d-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="74b8d-280">Groupes de sécurité et rôles AAD avec une classe de compte d’utilisateur personnalisée</span><span class="sxs-lookup"><span data-stu-id="74b8d-280">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="74b8d-281">Pour obtenir un exemple supplémentaire qui fonctionne avec les groupes de sécurité AAD et les rôles d’administrateur AAD et une classe de compte d’utilisateur personnalisée, consultez <xref:blazor/security/webassembly/aad-groups-roles> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-281">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="74b8d-282">Prendre en charge le prérendu avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="74b8d-282">Support prerendering with authentication</span></span>

<span data-ttu-id="74b8d-283">Après avoir utilisé les instructions de l’une des rubriques de l’application hébergée Blazor WebAssembly , suivez les instructions ci-dessous pour créer une application qui :</span><span class="sxs-lookup"><span data-stu-id="74b8d-283">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="74b8d-284">Prérend les chemins d’accès pour lesquels l’autorisation n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="74b8d-284">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="74b8d-285">N’effectue pas de prérendu des chemins pour lesquels une autorisation est requise.</span><span class="sxs-lookup"><span data-stu-id="74b8d-285">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="74b8d-286">Dans la *`Client`* classe de l’application `Program` ( `Program.cs` ), factorisez les inscriptions de service courantes dans une méthode distincte (par exemple, `ConfigureCommonServices` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-286">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add...;

        builder.Services.AddScoped( ... );

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="74b8d-287">Dans l’application serveur `Startup.ConfigureServices` , inscrivez les services supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="74b8d-287">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="74b8d-288">Dans la méthode de l’application serveur `Startup.Configure` , remplacez [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) par [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-288">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="74b8d-289">Dans l’application serveur, créez un `Pages` dossier s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="74b8d-289">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="74b8d-290">Créez une `_Host.cshtml` page dans le dossier de l’application serveur `Pages` .</span><span class="sxs-lookup"><span data-stu-id="74b8d-290">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="74b8d-291">Collez le contenu du *`Client`* fichier de l’application `wwwroot/index.html` dans le `Pages/_Host.cshtml` fichier.</span><span class="sxs-lookup"><span data-stu-id="74b8d-291">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="74b8d-292">Mettez à jour le contenu du fichier :</span><span class="sxs-lookup"><span data-stu-id="74b8d-292">Update the file's contents:</span></span>

* <span data-ttu-id="74b8d-293">Ajoutez `@page "_Host"` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="74b8d-293">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="74b8d-294">Remplacez la `<app>Loading...</app>` balise par le suivant :</span><span class="sxs-lookup"><span data-stu-id="74b8d-294">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="74b8d-295">Options pour les applications hébergées et les fournisseurs de connexion tiers</span><span class="sxs-lookup"><span data-stu-id="74b8d-295">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="74b8d-296">Lors de l’authentification et de l’autorisation d’une application hébergée Blazor WebAssembly auprès d’un fournisseur tiers, plusieurs options sont disponibles pour l’authentification de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-296">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="74b8d-297">Celui que vous choisissez dépend de votre scénario.</span><span class="sxs-lookup"><span data-stu-id="74b8d-297">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="74b8d-298">Pour plus d'informations, consultez <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="74b8d-298">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="74b8d-299">Authentifier les utilisateurs pour appeler uniquement des API tierces protégées</span><span class="sxs-lookup"><span data-stu-id="74b8d-299">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="74b8d-300">Authentifiez l’utilisateur avec un fluide OAuth côté client par rapport au fournisseur d’API tiers :</span><span class="sxs-lookup"><span data-stu-id="74b8d-300">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="74b8d-301">Dans ce scénario :</span><span class="sxs-lookup"><span data-stu-id="74b8d-301">In this scenario:</span></span>

* <span data-ttu-id="74b8d-302">Le serveur hébergeant l’application ne joue aucun rôle.</span><span class="sxs-lookup"><span data-stu-id="74b8d-302">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="74b8d-303">Les API sur le serveur ne peuvent pas être protégées.</span><span class="sxs-lookup"><span data-stu-id="74b8d-303">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="74b8d-304">L’application ne peut appeler que des API tierces protégées.</span><span class="sxs-lookup"><span data-stu-id="74b8d-304">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="74b8d-305">Authentifier les utilisateurs auprès d’un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers</span><span class="sxs-lookup"><span data-stu-id="74b8d-305">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="74b8d-306">Configurez Identity avec un fournisseur de connexion tiers.</span><span class="sxs-lookup"><span data-stu-id="74b8d-306">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="74b8d-307">Obtenez les jetons requis pour l’accès de l’API tierce et stockez-les.</span><span class="sxs-lookup"><span data-stu-id="74b8d-307">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="74b8d-308">Lorsqu’un utilisateur se connecte, Identity collecte les jetons d’accès et d’actualisation dans le cadre du processus d’authentification.</span><span class="sxs-lookup"><span data-stu-id="74b8d-308">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="74b8d-309">À ce stade, il existe deux approches disponibles pour effectuer des appels d’API à des API tierces.</span><span class="sxs-lookup"><span data-stu-id="74b8d-309">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="74b8d-310">Utiliser un jeton d’accès au serveur pour récupérer le jeton d’accès tiers</span><span class="sxs-lookup"><span data-stu-id="74b8d-310">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="74b8d-311">Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison d’API serveur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-311">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="74b8d-312">À partir de là, utilisez le jeton d’accès tiers pour appeler des ressources d’API tierces directement à partir de Identity sur le client.</span><span class="sxs-lookup"><span data-stu-id="74b8d-312">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="74b8d-313">Nous ne recommandons pas cette approche.</span><span class="sxs-lookup"><span data-stu-id="74b8d-313">We don't recommend this approach.</span></span> <span data-ttu-id="74b8d-314">Cette approche nécessite le traitement du jeton d’accès tiers comme s’il avait été généré pour un client public.</span><span class="sxs-lookup"><span data-stu-id="74b8d-314">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="74b8d-315">Dans les termes OAuth, l’application publique n’a pas de clé secrète client, car elle ne peut pas être approuvée pour stocker des secrets en toute sécurité, et le jeton d’accès est généré pour un client confidentiel.</span><span class="sxs-lookup"><span data-stu-id="74b8d-315">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="74b8d-316">Un client confidentiel est un client qui a une clé secrète client et est supposé être en mesure de stocker des secrets en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="74b8d-316">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="74b8d-317">Le jeton d’accès tiers peut recevoir des étendues supplémentaires pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.</span><span class="sxs-lookup"><span data-stu-id="74b8d-317">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="74b8d-318">De même, les jetons d’actualisation ne doivent pas être émis pour un client qui n’est pas approuvé, car cela donne au client un accès illimité, sauf si d’autres restrictions sont mises en place.</span><span class="sxs-lookup"><span data-stu-id="74b8d-318">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="74b8d-319">Effectuer des appels d’API à partir du client vers l’API du serveur afin d’appeler des API tierces</span><span class="sxs-lookup"><span data-stu-id="74b8d-319">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="74b8d-320">Effectuez un appel d’API à partir du client vers l’API serveur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-320">Make an API call from the client to the server API.</span></span> <span data-ttu-id="74b8d-321">À partir du serveur, récupérez le jeton d’accès pour la ressource d’API tierce et émettez l’appel nécessaire.</span><span class="sxs-lookup"><span data-stu-id="74b8d-321">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="74b8d-322">Bien que cette approche nécessite un saut de réseau supplémentaire par le biais du serveur pour appeler une API tierce, elle a finalement pour résultat une expérience plus sûre :</span><span class="sxs-lookup"><span data-stu-id="74b8d-322">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="74b8d-323">Le serveur peut stocker des jetons d’actualisation et s’assurer que l’application ne perd pas l’accès aux ressources tierces.</span><span class="sxs-lookup"><span data-stu-id="74b8d-323">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="74b8d-324">L’application ne peut pas perdre les jetons d’accès du serveur qui peuvent contenir des autorisations plus sensibles.</span><span class="sxs-lookup"><span data-stu-id="74b8d-324">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="74b8d-325">Utiliser des points de terminaison OpenID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="74b8d-325">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="74b8d-326">La bibliothèque d’authentification et les Blazor modèles de projet utilisent des points de terminaison OpenID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="74b8d-326">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="74b8d-327">Pour utiliser un point de terminaison v 2.0, configurez l’option de support JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="74b8d-327">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="74b8d-328">Dans l’exemple suivant, AAD est configuré pour v 2.0 en ajoutant un `v2.0` segment à la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> propriété :</span><span class="sxs-lookup"><span data-stu-id="74b8d-328">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="74b8d-329">Le paramètre peut également être défini dans le fichier de paramètres de l’application ( `appsettings.json` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-329">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="74b8d-330">Si l’ajout d’un segment à l’autorité n’est pas approprié pour le fournisseur OIDC de l’application, par exemple avec les fournisseurs non AAD, définissez la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriété directement.</span><span class="sxs-lookup"><span data-stu-id="74b8d-330">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="74b8d-331">Définissez la propriété dans <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou dans le fichier de paramètres de l’application ( `appsettings.json` ) avec la `Authority` clé.</span><span class="sxs-lookup"><span data-stu-id="74b8d-331">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="74b8d-332">La liste des revendications dans le jeton d’ID change pour les points de terminaison v 2.0.</span><span class="sxs-lookup"><span data-stu-id="74b8d-332">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="74b8d-333">Pour plus d’informations, consultez [pourquoi mettre à jour vers Microsoft Identity Platform (v 2.0) ?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="74b8d-333">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="74b8d-334">Configurer et utiliser gRPC dans les composants</span><span class="sxs-lookup"><span data-stu-id="74b8d-334">Configure and use gRPC in components</span></span>

<span data-ttu-id="74b8d-335">Pour configurer une Blazor WebAssembly application afin d’utiliser l' [infrastructure ASP.net Core gRPC](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="74b8d-335">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="74b8d-336">Activez gRPC-Web sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="74b8d-336">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="74b8d-337">Pour plus d'informations, consultez <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="74b8d-337">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="74b8d-338">Inscrivez les services gRPC pour le gestionnaire de messages de l’application.</span><span class="sxs-lookup"><span data-stu-id="74b8d-338">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="74b8d-339">L’exemple suivant configure le gestionnaire de messages d’autorisation de l’application pour qu’il utilise le [ `GreeterClient` service à partir du didacticiel gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-339">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="74b8d-340">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-340">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="74b8d-341">Placez le `.proto` fichier dans le `Shared` projet de la solution hébergée Blazor .</span><span class="sxs-lookup"><span data-stu-id="74b8d-341">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="74b8d-342">Un composant de l’application cliente peut effectuer des appels gRPC à l’aide du client gRPC ( `Pages/Grpc.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="74b8d-342">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="74b8d-343">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="74b8d-343">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="74b8d-344">Pour utiliser la `Status.DebugException` propriété, utilisez [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 ou ultérieure.</span><span class="sxs-lookup"><span data-stu-id="74b8d-344">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="74b8d-345">Pour plus d'informations, consultez <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="74b8d-345">For more information, see <xref:grpc/browser>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="74b8d-346">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="74b8d-346">Additional resources</span></span>

* <xref:blazor/security/webassembly/graph-api>
* [<span data-ttu-id="74b8d-347">`HttpClient` et `HttpRequestMessage` avec les options de requête de l’API Fetch</span><span class="sxs-lookup"><span data-stu-id="74b8d-347">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
