---
title: ASP.NET Core Blazor WebAssembly des scénarios de sécurité supplémentaires
author: guardrex
description: Découvrez comment configurer Blazor WebAssembly pour d’autres scénarios de sécurité.
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 88970b0e53b456467bdc2218a3a6b943bbbf0df5
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234385"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly des scénarios de sécurité supplémentaires

Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>Attacher des jetons aux demandes sortantes

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> est <xref:System.Net.Http.DelegatingHandler> utilisé pour attacher des jetons d’accès aux instances sortantes <xref:System.Net.Http.HttpResponseMessage> . Les jetons sont acquis à l’aide du <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, qui est enregistré par le Framework. Si un jeton ne peut pas être acquis, une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> exception est levée. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> dispose d’une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> méthode qui peut être utilisée pour accéder au fournisseur d’identité de l’utilisateur afin d’acquérir un nouveau jeton.

Pour plus de commodité, le Framework fournit l' <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> adresse préconfigurée avec l’adresse de base de l’application en tant qu’URL autorisée. **Les jetons d’accès sont ajoutés uniquement lorsque l’URI de la demande se trouve dans l’URI de base de l’application.** Lorsque les URI de demande sortants ne se trouvent pas dans l’URI de base de l’application, utilisez une [ `AuthorizationMessageHandler` classe personnalisée ( *recommandé* )](#custom-authorizationmessagehandler-class) ou [configurez le `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).

> [!NOTE]
> Outre la configuration de l’application cliente pour l’accès à l’API serveur, l’API serveur doit également autoriser les requêtes Cross-Origin (CORS) lorsque le client et le serveur ne se trouvent pas à la même adresse de base. Pour plus d’informations sur la configuration CORS côté serveur, consultez la section relative au [partage des ressources Cross-Origin (cors)](#cross-origin-resource-sharing-cors) plus loin dans cet article.

Dans l’exemple suivant :

* <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> Ajoute <xref:System.Net.Http.IHttpClientFactory> des services connexes à la collection de services et configure un nommé <xref:System.Net.Http.HttpClient> ( `ServerAPI` ). <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> adresse de base de l’URI de la ressource lors de l’envoi des demandes. <xref:System.Net.Http.IHttpClientFactory> est fourni par le [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package NuGet.
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> est <xref:System.Net.Http.DelegatingHandler> utilisé pour attacher des jetons d’accès aux instances sortantes <xref:System.Net.Http.HttpResponseMessage> . Les jetons d’accès sont ajoutés uniquement lorsque l’URI de la demande se trouve dans l’URI de base de l’application.
* <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> crée et configure une <xref:System.Net.Http.HttpClient> instance pour les demandes sortantes à l’aide de la configuration qui correspond au nommé <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).

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

Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, les URI de requête se trouvent dans l’URI de base de l’application par défaut. Par conséquent, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) est assigné au <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> dans une application générée à partir du modèle de projet.

Le configuré <xref:System.Net.Http.HttpClient> est utilisé pour effectuer des demandes autorisées à l’aide du [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) modèle :

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

### <a name="custom-authorizationmessagehandler-class"></a>`AuthorizationMessageHandler`Classe personnalisée

*Ce guide de cette section est recommandé pour les applications clientes qui effectuent des requêtes sortantes vers des URI qui ne se trouvent pas dans l’URI de base de l’application.*

Dans l’exemple suivant, une classe personnalisée étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> pour une utilisation en tant que <xref:System.Net.Http.DelegatingHandler> pour un <xref:System.Net.Http.HttpClient> . <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Configure ce gestionnaire pour autoriser les requêtes HTTP sortantes à l’aide d’un jeton d’accès. Le jeton d’accès est attaché uniquement si au moins l’une des URL autorisées est une base de l’URI de la demande ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).

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

Dans `Program.Main` ( `Program.cs` ), `CustomAuthorizationMessageHandler` est inscrit en tant que service délimitéd et est configuré en tant que <xref:System.Net.Http.DelegatingHandler> pour les instances sortantes <xref:System.Net.Http.HttpResponseMessage> effectuées par un nommé <xref:System.Net.Http.HttpClient> :

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) est assigné à <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> par défaut.

Le configuré <xref:System.Net.Http.HttpClient> est utilisé pour effectuer des demandes autorisées à l’aide du [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) modèle. Lorsque le client est créé avec <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) Package), <xref:System.Net.Http.HttpClient> est fourni les instances qui incluent des jetons d’accès lors de l’exécution de requêtes à l’API serveur. Si l’URI de la demande est un URI relatif, comme c’est le cas dans l’exemple suivant ( `ExampleAPIMethod` ), il est combiné avec le <xref:System.Net.Http.HttpClient.BaseAddress> lorsque l’application cliente effectue la requête :

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

### <a name="configure-authorizationmessagehandler"></a>Configurer `AuthorizationMessageHandler`

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> peut être configuré avec des URL, des étendues et une URL de retour autorisées à l’aide de la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> méthode. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configure le gestionnaire pour autoriser les requêtes HTTP sortantes à l’aide d’un jeton d’accès. Le jeton d’accès est attaché uniquement si au moins l’une des URL autorisées est une base de l’URI de la demande ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ). Si l’URI de la demande est un URI relatif, il est associé au <xref:System.Net.Http.HttpClient.BaseAddress> .

Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configure un <xref:System.Net.Http.HttpClient> dans `Program.Main` ( `Program.cs` ) :

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

Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> est assigné aux éléments suivants par défaut :

* <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* URL du `authorizedUrls` tableau.

## <a name="typed-httpclient"></a>Tapé `HttpClient`

Vous pouvez définir un client typé qui gère tous les problèmes d’acquisition de jetons et HTTP dans une même classe.

`WeatherForecastClient.cs`:

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

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `using static BlazorSample.Data;` ).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) est assigné à <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> par défaut.

`FetchData` composant ( `Pages/FetchData.razor` ) :

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Configurer le `HttpClient` Gestionnaire

Le gestionnaire peut être configuré avec <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pour les requêtes http sortantes.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> est assigné aux éléments suivants par défaut :

* <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* URL du `authorizedUrls` tableau.

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé

Si l' Blazor WebAssembly application utilise généralement une valeur par défaut sécurisée <xref:System.Net.Http.HttpClient> , l’application peut également effectuer des demandes d’API Web non authentifiées ou non autorisées en configurant un nom <xref:System.Net.Http.HttpClient> :

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

Pour une Blazor application basée sur le Blazor WebAssembly modèle de projet hébergé, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) est assigné à <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> par défaut.

L’inscription précédente s’ajoute à l’inscription par défaut sécurisée existante <xref:System.Net.Http.HttpClient> .

Un composant crée le <xref:System.Net.Http.HttpClient> à partir du <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) Package) pour effectuer des demandes non authentifiées ou non autorisées :

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
> Dans l’exemple précédent, le contrôleur de l’API serveur `WeatherForecastNoAuthenticationController` n’est pas marqué avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut.

Le développeur doit décider s’il faut utiliser un client sécurisé ou un client non sécurisé comme instance par défaut <xref:System.Net.Http.HttpClient> . L’une des façons de prendre cette décision est de prendre en compte le nombre de points de terminaison authentifiés et non authentifiés que l’application contacte. Si la majorité des demandes de l’application sont de sécuriser les points de terminaison d’API, utilisez l’instance authentifiée <xref:System.Net.Http.HttpClient> comme valeur par défaut. Sinon, inscrivez l’instance non authentifiée <xref:System.Net.Http.HttpClient> comme valeur par défaut.

Une autre approche de l’utilisation de l' <xref:System.Net.Http.IHttpClientFactory> consiste à créer un [client typé](#typed-httpclient) pour l’accès non authentifié aux points de terminaison anonymes.

## <a name="request-additional-access-tokens"></a>Demander des jetons d’accès supplémentaires

Les jetons d’accès peuvent être obtenus manuellement en appelant `IAccessTokenProvider.RequestAccessToken` . Dans l’exemple suivant, une étendue supplémentaire est requise par une application pour la valeur par défaut <xref:System.Net.Http.HttpClient> . L’exemple de la bibliothèque d’authentification Microsoft (MSAL) configure l’étendue avec `MsalProviderOptions` :

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

Les `{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` espaces réservés et dans l’exemple précédent sont des étendues personnalisées.

La `IAccessTokenProvider.RequestToken` méthode fournit une surcharge qui permet à une application de configurer un jeton d’accès avec un ensemble donné d’étendues.

Dans un Razor composant :

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

Les `{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` espaces réservés et dans l’exemple précédent sont des étendues personnalisées.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> Cette

* `true` avec le `token` à utiliser.
* `false` Si le jeton n’est pas récupéré.

## <a name="cross-origin-resource-sharing-cors"></a>Partage des ressources cross-origin (CORS)

Lors de l’envoi d’informations d’identification ( cookie s/en-têtes d’autorisation) sur les demandes cors, l' `Authorization` en-tête doit être autorisé par la stratégie cors.

La stratégie suivante comprend la configuration pour :

* Origines des demandes ( `http://localhost:5000` , `https://localhost:5001` ).
* Toute méthode (verbe).
* `Content-Type` et `Authorization` en-têtes. Pour autoriser un en-tête personnalisé (par exemple, `x-custom-header` ), répertoriez l’en-tête lors de l’appel de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .
* Informations d’identification définies par le code JavaScript côté client (la `credentials` propriété a la valeur `include` ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Une solution hébergée Blazor basée sur le Blazor modèle de projet hébergé utilise la même adresse de base pour les applications clientes et serveur. La valeur de l’URI de l’application cliente <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> est `builder.HostEnvironment.BaseAddress` par défaut. La configuration CORS n’est **pas** requise dans la configuration par défaut d’une application hébergée créée à partir du Blazor modèle de projet hébergé. Les applications clientes supplémentaires qui ne sont pas hébergées par le projet serveur et ne partagent pas **l’adresse de** base de l’application serveur nécessitent une configuration cors dans le projet serveur.

Pour plus d’informations, consultez <xref:security/cors> et le composant testeur de requêtes http de l’exemple d’application ( `Components/HTTPRequestTester.razor` ).

## <a name="handle-token-request-errors"></a>Gérer les erreurs de demande de jeton

Lorsqu’une application à page unique (SPA) authentifie un utilisateur à l’aide de OpenID Connect (OIDC), l’état d’authentification est conservé localement au sein du SPA et dans le Identity fournisseur (IP) sous la forme d’une session cookie définie à la suite de l’utilisateur qui fournit ses informations d’identification.

Les jetons que l’adresse IP émet pour l’utilisateur sont généralement valides pendant de courtes périodes, environ une heure normalement, de sorte que l’application cliente doit régulièrement extraire les nouveaux jetons. Dans le cas contraire, l’utilisateur est déconnecté après l’expiration des jetons accordés. Dans la plupart des cas, les clients OIDC sont en mesure de configurer de nouveaux jetons sans que l’utilisateur soit obligé de s’authentifier à nouveau grâce à l’état d’authentification ou à la « session » qui est conservée au sein de l’adresse IP.

Dans certains cas, le client ne peut pas obtenir un jeton sans intervention de l’utilisateur, par exemple, lorsque, pour une raison quelconque, l’utilisateur se déconnecte explicitement de l’adresse IP. Ce scénario se produit si un utilisateur visite `https://login.microsoftonline.com` et se déconnecte. Dans ces scénarios, l’application ne sait pas immédiatement que l’utilisateur s’est déconnecté. Tout jeton que le client contient peut ne plus être valide. En outre, le client n’est pas en mesure d’approvisionner un nouveau jeton sans interaction de l’utilisateur après l’expiration du jeton actuel.

Ces scénarios ne sont pas spécifiques à l’authentification basée sur les jetons. Elles font partie de la nature des SPAs. Un SPA utilisant cookie s ne parvient pas non plus à appeler une API serveur si l’authentification cookie est supprimée.

Quand une application effectue des appels d’API vers des ressources protégées, vous devez tenir compte des éléments suivants :

* Pour approvisionner un nouveau jeton d’accès pour appeler l’API, l’utilisateur peut être amené à s’authentifier à nouveau.
* Même si le client possède un jeton qui semble être valide, l’appel au serveur peut échouer parce que le jeton a été révoqué par l’utilisateur.

Lorsque l’application demande un jeton, deux résultats sont possibles :

* La demande a échoué et l’application a un jeton valide.
* La demande échoue et l’application doit authentifier à nouveau l’utilisateur pour obtenir un nouveau jeton.

En cas d’échec d’une demande de jeton, vous devez décider si vous souhaitez enregistrer un état actuel avant d’effectuer une redirection. Il existe plusieurs approches avec des niveaux de complexité de plus en plus complexes :

* Stocke l’état actuel de la page dans le stockage de session. Pendant l' [ `OnInitializedAsync` événement de cycle de vie](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), vérifiez si l’État peut être restauré avant de continuer.
* Ajoutez un paramètre de chaîne de requête et utilisez-le comme méthode pour signaler à l’application qu’elle doit réalimenter l’état enregistré précédemment.
* Ajoutez un paramètre de chaîne de requête avec un identificateur unique pour stocker les données dans le stockage de session sans risquer des collisions avec d’autres éléments.

L’exemple suivant montre comment :

* Conserver l’état avant la redirection vers la page de connexion.
* Récupérez l’état précédent après l’authentification à l’aide du paramètre de chaîne de requête.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Enregistrer l’état de l’application avant une opération d’authentification

Au cours d’une opération d’authentification, il existe des cas où vous souhaitez enregistrer l’état de l’application avant que le navigateur soit redirigé vers l’adresse IP. Cela peut être le cas lorsque vous utilisez un conteneur d’État et que vous souhaitez restaurer l’État une fois l’authentification réussie. Vous pouvez utiliser un objet d’état d’authentification personnalisé pour conserver l’état spécifique à l’application ou une référence à celui-ci, et restaurer cet État une fois l’opération d’authentification terminée. L’exemple suivant illustre l’approche.

Une classe de conteneur d’État est créée dans l’application avec des propriétés pour contenir les valeurs d’état de l’application. Dans l’exemple suivant, le conteneur est utilisé pour conserver la valeur de compteur du composant du modèle de projet par défaut `Counter` ( `Pages/Counter.razor` ). Les méthodes de sérialisation et de désérialisation du conteneur sont basées sur <xref:System.Text.Json> .

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

Le `Counter` composant utilise le conteneur d’État pour conserver la `currentCount` valeur en dehors du composant :

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

Créez un `ApplicationAuthenticationState` à partir de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> . Fournissez une `Id` propriété, qui sert d’identificateur pour l’État stocké localement.

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

Le `Authentication` composant ( `Pages/Authentication.razor` ) enregistre et restaure l’état de l’application à l’aide du stockage de session locale avec les `StateContainer` méthodes de sérialisation et de désérialisation, `GetStateForLocalStorage` et `SetStateFromLocalStorage` :

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

Cet exemple utilise Azure Active Directory (AAD) pour l’authentification. Dans `Program.Main` ( `Program.cs` ) :

* Le `ApplicationAuthenticationState` est configuré en tant que type Microsoft authentification Library (MSAL) `RemoteAuthenticationState` .
* Le conteneur d’État est inscrit dans le conteneur de service.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Personnaliser les itinéraires de l’application

Par défaut, la [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) bibliothèque utilise les itinéraires indiqués dans le tableau suivant pour représenter des États d’authentification différents.

| Route                            | Objectif |
| -------------------------------- | ------- |
| `authentication/login`           | Déclenche une opération de connexion. |
| `authentication/login-callback`  | Gère le résultat de toute opération de connexion. |
| `authentication/login-failed`    | Affiche des messages d’erreur lorsque l’opération de connexion échoue pour une raison quelconque. |
| `authentication/logout`          | Déclenche une opération de déconnexion. |
| `authentication/logout-callback` | Gère le résultat d’une opération de déconnexion. |
| `authentication/logout-failed`   | Affiche des messages d’erreur lorsque l’opération de déconnexion échoue pour une raison quelconque. |
| `authentication/logged-out`      | Indique que l’utilisateur a réussi à se déconnecter. |
| `authentication/profile`         | Déclenche une opération de modification du profil utilisateur. |
| `authentication/register`        | Déclenche une opération pour inscrire un nouvel utilisateur. |

Les itinéraires indiqués dans le tableau précédent sont configurables via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> . Quand vous définissez des options pour fournir des itinéraires personnalisés, vérifiez que l’application a un itinéraire qui gère chaque chemin d’accès.

Dans l’exemple suivant, tous les chemins d’accès ont pour préfixe `/security` .

`Authentication` composant ( `Pages/Authentication.razor` ) :

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

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

Si la spécification exige des chemins d’accès complètement différents, définissez les itinéraires comme décrit précédemment et affichez le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> avec un paramètre d’action explicite :

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Si vous le souhaitez, vous avez la possibilité de scinder l’interface utilisateur en différentes pages.

## <a name="customize-the-authentication-user-interface"></a>Personnaliser l’interface utilisateur de l’authentification

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> comprend un ensemble par défaut de parties de l’interface utilisateur pour chaque État d’authentification. Chaque État peut être personnalisé en passant un personnalisé <xref:Microsoft.AspNetCore.Components.RenderFragment> . Pour personnaliser le texte affiché pendant le processus de connexion initial, peut modifier le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> comme suit.

`Authentication` composant ( `Pages/Authentication.razor` ) :

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

Le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> a un fragment qui peut être utilisé par itinéraire d’authentification, comme indiqué dans le tableau suivant.

| Route                            | Fragment                |
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

## <a name="customize-the-user"></a>Personnaliser l’utilisateur

Les utilisateurs liés à l’application peuvent être personnalisés.

### <a name="customize-the-user-with-a-payload-claim"></a>Personnaliser l’utilisateur avec une revendication de charge utile

Dans l’exemple suivant, les utilisateurs authentifiés de l’application reçoivent une `amr` revendication pour chacune des méthodes d’authentification de l’utilisateur. La `amr` revendication identifie la manière dont le sujet du jeton a été authentifié dans les Identity revendications de [charge utile](/azure/active-directory/develop/access-tokens#the-amr-claim)Microsoft Platform v 1.0. L’exemple utilise une classe de compte d’utilisateur personnalisée basée sur <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .

Créez une classe qui étend la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe. L’exemple suivant affecte `AuthenticationMethod` à la propriété le tableau de valeurs de `amr` propriété JSON de l’utilisateur. `AuthenticationMethod` est rempli automatiquement par le Framework lorsque l’utilisateur est authentifié.

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Créez une fabrique qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> pour créer des revendications à partir des méthodes d’authentification de l’utilisateur stockées dans `CustomUserAccount.AuthenticationMethod` :

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

Inscrivez le `CustomAccountFactory` pour le fournisseur d’authentification en cours d’utilisation. Les inscriptions suivantes sont valides :

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a>Groupes de sécurité et rôles AAD avec une classe de compte d’utilisateur personnalisée

Pour obtenir un exemple supplémentaire qui fonctionne avec les groupes de sécurité AAD et les rôles d’administrateur AAD et une classe de compte d’utilisateur personnalisée, consultez <xref:blazor/security/webassembly/aad-groups-roles> .

## <a name="support-prerendering-with-authentication"></a>Prendre en charge le prérendu avec l’authentification

Après avoir utilisé les instructions de l’une des rubriques de l’application hébergée Blazor WebAssembly , suivez les instructions ci-dessous pour créer une application qui :

* Prérend les chemins d’accès pour lesquels l’autorisation n’est pas requise.
* N’effectue pas de prérendu des chemins pour lesquels une autorisation est requise.

Dans la *`Client`* classe de l’application `Program` ( `Program.cs` ), factorisez les inscriptions de service courantes dans une méthode distincte (par exemple, `ConfigureCommonServices` ) :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

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

Dans l’application serveur `Startup.ConfigureServices` , inscrivez les services supplémentaires suivants :

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

Dans la méthode de l’application serveur `Startup.Configure` , remplacez [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) par [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Dans l’application serveur, créez un `Pages` dossier s’il n’existe pas. Créez une `_Host.cshtml` page dans le dossier de l’application serveur `Pages` . Collez le contenu du *`Client`* fichier de l’application `wwwroot/index.html` dans le `Pages/_Host.cshtml` fichier. Mettez à jour le contenu du fichier :

* Ajoutez `@page "_Host"` en haut du fichier.
* Remplacez la `<app>Loading...</app>` balise par le suivant :

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Options pour les applications hébergées et les fournisseurs de connexion tiers

Lors de l’authentification et de l’autorisation d’une application hébergée Blazor WebAssembly auprès d’un fournisseur tiers, plusieurs options sont disponibles pour l’authentification de l’utilisateur. Celui que vous choisissez dépend de votre scénario.

Pour plus d'informations, consultez <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Authentifier les utilisateurs pour appeler uniquement des API tierces protégées

Authentifiez l’utilisateur avec un fluide OAuth côté client par rapport au fournisseur d’API tiers :

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Dans ce scénario :

* Le serveur hébergeant l’application ne joue aucun rôle.
* Les API sur le serveur ne peuvent pas être protégées.
* L’application ne peut appeler que des API tierces protégées.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Authentifier les utilisateurs auprès d’un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers

Configurez Identity avec un fournisseur de connexion tiers. Obtenez les jetons requis pour l’accès de l’API tierce et stockez-les.

Lorsqu’un utilisateur se connecte, Identity collecte les jetons d’accès et d’actualisation dans le cadre du processus d’authentification. À ce stade, il existe deux approches disponibles pour effectuer des appels d’API à des API tierces.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Utiliser un jeton d’accès au serveur pour récupérer le jeton d’accès tiers

Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison d’API serveur. À partir de là, utilisez le jeton d’accès tiers pour appeler des ressources d’API tierces directement à partir de Identity sur le client.

Nous ne recommandons pas cette approche. Cette approche nécessite le traitement du jeton d’accès tiers comme s’il avait été généré pour un client public. Dans les termes OAuth, l’application publique n’a pas de clé secrète client, car elle ne peut pas être approuvée pour stocker des secrets en toute sécurité, et le jeton d’accès est généré pour un client confidentiel. Un client confidentiel est un client qui a une clé secrète client et est supposé être en mesure de stocker des secrets en toute sécurité.

* Le jeton d’accès tiers peut recevoir des étendues supplémentaires pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.
* De même, les jetons d’actualisation ne doivent pas être émis pour un client qui n’est pas approuvé, car cela donne au client un accès illimité, sauf si d’autres restrictions sont mises en place.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Effectuer des appels d’API à partir du client vers l’API du serveur afin d’appeler des API tierces

Effectuez un appel d’API à partir du client vers l’API serveur. À partir du serveur, récupérez le jeton d’accès pour la ressource d’API tierce et émettez l’appel nécessaire.

Bien que cette approche nécessite un saut de réseau supplémentaire par le biais du serveur pour appeler une API tierce, elle a finalement pour résultat une expérience plus sûre :

* Le serveur peut stocker des jetons d’actualisation et s’assurer que l’application ne perd pas l’accès aux ressources tierces.
* L’application ne peut pas perdre les jetons d’accès du serveur qui peuvent contenir des autorisations plus sensibles.

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Utiliser des points de terminaison OpenID Connect (OIDC) v 2.0

La bibliothèque d’authentification et les Blazor modèles de projet utilisent des points de terminaison OpenID Connect (OIDC) v 1.0. Pour utiliser un point de terminaison v 2.0, configurez l’option de support JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> . Dans l’exemple suivant, AAD est configuré pour v 2.0 en ajoutant un `v2.0` segment à la <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> propriété :

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Le paramètre peut également être défini dans le fichier de paramètres de l’application ( `appsettings.json` ) :

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Si l’ajout d’un segment à l’autorité n’est pas approprié pour le fournisseur OIDC de l’application, par exemple avec les fournisseurs non AAD, définissez la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriété directement. Définissez la propriété dans <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou dans le fichier de paramètres de l’application ( `appsettings.json` ) avec la `Authority` clé.

La liste des revendications dans le jeton d’ID change pour les points de terminaison v 2.0. Pour plus d’informations, consultez [pourquoi mettre à jour vers Microsoft Identity Platform (v 2.0) ?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).

## <a name="configure-and-use-grpc-in-components"></a>Configurer et utiliser gRPC dans les composants

Pour configurer une Blazor WebAssembly application afin d’utiliser l' [infrastructure ASP.net Core gRPC](xref:grpc/index):

* Activez gRPC-Web sur le serveur. Pour plus d'informations, consultez <xref:grpc/browser>.
* Inscrivez les services gRPC pour le gestionnaire de messages de l’application. L’exemple suivant configure le gestionnaire de messages d’autorisation de l’application pour qu’il utilise le [ `GreeterClient` service à partir du didacticiel gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ) :

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

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample` ). Placez le `.proto` fichier dans le `Shared` projet de la solution hébergée Blazor .

Un composant de l’application cliente peut effectuer des appels gRPC à l’aide du client gRPC ( `Pages/Grpc.razor` ) :

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

L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample` ). Pour utiliser la `Status.DebugException` propriété, utilisez [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 ou ultérieure.

Pour plus d'informations, consultez <xref:grpc/browser>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/security/webassembly/graph-api>
* [`HttpClient` et `HttpRequestMessage` avec les options de requête de l’API Fetch](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
