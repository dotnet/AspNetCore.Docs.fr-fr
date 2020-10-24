---
title: Authentification et autorisation dans ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment utiliser l’authentification et l’autorisation dans ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
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
uid: signalr/authn-and-authz
ms.openlocfilehash: e16efa59a82d0f3cb1a2272ae0c07654ebec6a51
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491559"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a>Authentification et autorisation dans ASP.NET Core SignalR

Par [Andrew Stanton-infirmière](https://twitter.com/anurse)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a>Authentifier les utilisateurs se connectant à un SignalR Hub

SignalR peut être utilisé avec [l’authentification ASP.net Core](xref:security/authentication/identity) pour associer un utilisateur à chaque connexion. Dans un concentrateur, les données d’authentification sont accessibles à partir de la propriété [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . L’authentification permet au hub d’appeler des méthodes sur toutes les connexions associées à un utilisateur. Pour plus d’informations, consultez [gérer les utilisateurs et SignalR les groupes dans ](xref:signalr/groups). Plusieurs connexions peuvent être associées à un seul utilisateur.

Voici un exemple d’utilisation de `Startup.Configure` et de SignalR ASP.net Core l’authentification :

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> L’ordre dans lequel vous inscrivez le SignalR et le ASP.net Core intergiciel (middleware) d’authentification. Appelez toujours `UseAuthentication` avant `UseSignalR` pour que SignalR ait un utilisateur sur le `HttpContext` .

::: moniker-end

### <a name="no-loccookie-authentication"></a>l’authentification Cookie

Dans une application basée sur un navigateur, cookie l’authentification permet de transmettre automatiquement les informations d’identification de l’utilisateur existant aux SignalR connexions. Lorsque vous utilisez le navigateur client, aucune configuration supplémentaire n’est nécessaire. Si l’utilisateur est connecté à votre application, la SignalR connexion hérite automatiquement de cette authentification.

Cookieles s sont une méthode propre au navigateur pour envoyer des jetons d’accès, mais les clients sans navigateur peuvent les envoyer. Lorsque vous utilisez le [client .net](xref:signalr/dotnet-client), la `Cookies` propriété peut être configurée dans l' `.WithUrl` appel pour fournir un cookie . Toutefois, l’utilisation cookie de l’authentification à partir du client .net requiert que l’application fournisse une API pour échanger les données d’authentification d’un cookie .

### <a name="bearer-token-authentication"></a>Authentification du jeton du porteur

Le client peut fournir un jeton d’accès au lieu d’utiliser un cookie . Le serveur valide le jeton et l’utilise pour identifier l’utilisateur. Cette validation est effectuée uniquement lorsque la connexion est établie. Pendant la durée de vie de la connexion, le serveur n’est pas automatiquement revalidé pour vérifier la révocation des jetons.

Dans le client JavaScript, le jeton peut être fourni à l’aide de l’option [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

Dans le client .NET, il existe une propriété [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) similaire qui peut être utilisée pour configurer le jeton :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> La fonction de jeton d’accès que vous fournissez est appelée avant **chaque** requête HTTP effectuée par SignalR . Si vous devez renouveler le jeton pour maintenir la connexion active (car elle peut expirer pendant la connexion), faites-le à partir de cette fonction et retournez le jeton mis à jour.

Dans les API Web standard, les jetons du porteur sont envoyés dans un en-tête HTTP. Toutefois, SignalR ne peut pas définir ces en-têtes dans les navigateurs lors de l’utilisation de certains transports. Lorsque vous utilisez les événements WebSocket et Server-Sent, le jeton est transmis sous la forme d’un paramètre de chaîne de requête. 

#### <a name="built-in-jwt-authentication"></a>Authentification JWT intégrée

Sur le serveur, l’authentification par jeton du porteur est configurée à l’aide de l’intergiciel (middleware) du [porteur JWT](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> La chaîne de requête est utilisée sur les navigateurs lors de la connexion aux événements WebSocket et Server-Sent en raison des limitations de l’API du navigateur. Lors de l’utilisation de HTTPs, les valeurs de chaîne de requête sont sécurisées par la connexion TLS. Toutefois, de nombreux serveurs consignent des valeurs de chaîne de requête. Pour plus d’informations, consultez [Considérations sur la SignalR sécurité dans ASP.net Core ](xref:signalr/security). SignalR utilise des en-têtes pour transmettre des jetons dans des environnements qui les prennent en charge (tels que les clients .NET et Java).

#### <a name="no-locidentity-server-jwt-authentication"></a>Identity Authentification JWT du serveur

Lorsque Identity vous utilisez le serveur, ajoutez un <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> service au projet :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
public class ConfigureJwtBearerOptions : IPostConfigureOptions<JwtBearerOptions>
{
    public void PostConfigure(string name, JwtBearerOptions options)
    {
        var originalOnMessageReceived = options.Events.OnMessageReceived;
        options.Events.OnMessageReceived = async context =>
        {
            await originalOnMessageReceived(context);
                
            if (string.IsNullOrEmpty(context.Token))
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;
                
                if (!string.IsNullOrEmpty(accessToken) && 
                    path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }
            }
        };
    }
}
```

Inscrire le service dans `Startup.ConfigureServices` après l’ajout de services pour l’authentification ( <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> ) et le gestionnaire d’authentification pour le Identity serveur ( <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ) :

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="no-loccookies-vs-bearer-tokens"></a>Cookiejetons s et porteur 

Cookieles s sont spécifiques aux navigateurs. Leur envoi à partir d’autres types de clients augmente la complexité par rapport à l’envoi des jetons de porteur. Par conséquent, l' cookie authentification n’est pas recommandée, sauf si l’application doit uniquement authentifier les utilisateurs à partir du navigateur client. L’authentification par jeton du porteur est l’approche recommandée lors de l’utilisation de clients autres que le navigateur client.

### <a name="windows-authentication"></a>Authentification Windows

Si [l’authentification Windows](xref:security/authentication/windowsauth) est configurée dans votre application, SignalR peut utiliser cette identité pour sécuriser les hubs. Toutefois, pour envoyer des messages à des utilisateurs individuels, vous devez ajouter un fournisseur d’ID d’utilisateur personnalisé. Le système d’authentification Windows ne fournit pas la revendication « identificateur de nom ». SignalR utilise la revendication pour déterminer le nom d’utilisateur.

Ajoutez une nouvelle classe qui implémente `IUserIdProvider` et récupère l’une des revendications de l’utilisateur à utiliser comme identificateur. Par exemple, pour utiliser la revendication « Name » (nom d’utilisateur Windows sous la forme `[Domain]\[Username]` ), créez la classe suivante :

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Au lieu de `ClaimTypes.Name` , vous pouvez utiliser n’importe quelle valeur de `User` (comme l’identificateur SID Windows, etc.).

> [!NOTE]
> La valeur que vous choisissez doit être unique parmi tous les utilisateurs de votre système. Dans le cas contraire, un message destiné à un utilisateur peut finir par passer à un autre utilisateur.

Inscrivez ce composant dans votre `Startup.ConfigureServices` méthode.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

Dans le client .NET, l’authentification Windows doit être activée en définissant la propriété [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

L’authentification Windows est prise en charge dans Internet Explorer et Microsoft Edge, mais pas dans tous les navigateurs. Par exemple, dans chrome et Safari, toute tentative d’utilisation de l’authentification Windows et de WebSocket échoue. Lorsque l’authentification Windows échoue, le client tente de revenir à d’autres transports qui peuvent fonctionner.

### <a name="use-claims-to-customize-identity-handling"></a>Utiliser des revendications pour personnaliser la gestion des identités

Une application qui authentifie les utilisateurs peut dériver SignalR des ID d’utilisateur des revendications d’utilisateur. Pour spécifier comment SignalR crée des ID d’utilisateur, implémentez `IUserIdProvider` et inscrivez l’implémentation.

L’exemple de code montre comment utiliser les revendications pour sélectionner l’adresse e-mail de l’utilisateur comme propriété d’identification. 

> [!NOTE]
> La valeur que vous choisissez doit être unique parmi tous les utilisateurs de votre système. Dans le cas contraire, un message destiné à un utilisateur peut finir par passer à un autre utilisateur.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

L’inscription du compte ajoute une revendication de type `ClaimsTypes.Email` à la base de données d’identité ASP.net.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Inscrivez ce composant dans votre `Startup.ConfigureServices` .

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autoriser les utilisateurs à accéder aux hubs et aux méthodes de concentrateur

Par défaut, toutes les méthodes d’un concentrateur peuvent être appelées par un utilisateur non authentifié. Pour exiger une authentification, appliquez l’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) au hub :

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Vous pouvez utiliser les arguments de constructeur et les propriétés de l' `[Authorize]` attribut pour limiter l’accès uniquement aux utilisateurs correspondant à des [stratégies d’autorisation](xref:security/authorization/policies)spécifiques. Par exemple, si vous avez une stratégie d’autorisation personnalisée appelée, `MyAuthorizationPolicy` vous pouvez vous assurer que seuls les utilisateurs qui correspondent à cette stratégie peuvent accéder au hub à l’aide du code suivant :

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

L’attribut peut également être appliqué à des méthodes de concentrateur individuelles `[Authorize]` . Si l’utilisateur actuel ne correspond pas à la stratégie appliquée à la méthode, une erreur est retournée à l’appelant :

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Utiliser des gestionnaires d’autorisations pour personnaliser l’autorisation de méthode de concentrateur

SignalR fournit une ressource personnalisée aux gestionnaires d’autorisations lorsqu’une méthode de concentrateur requiert une autorisation. La ressource est une instance de `HubInvocationContext` . Le `HubInvocationContext` comprend le `HubCallerContext` , le nom de la méthode de concentrateur appelée et les arguments de la méthode de concentrateur.

Prenons l’exemple d’une salle de conversation permettant à plusieurs entreprises de se connecter via Azure Active Directory. Toute personne disposant d’un compte Microsoft peut se connecter à chat, mais seuls les membres de l’organisation propriétaire doivent être en mesure d’interdire les utilisateurs ou d’afficher les historiques de conversation des utilisateurs. En outre, nous pouvons souhaiter restreindre certaines fonctionnalités de certains utilisateurs. L’utilisation des fonctionnalités mises à jour dans ASP.NET Core 3,0 est tout à fait possible. Notez comment le `DomainRestrictedRequirement` sert de personnalisé `IAuthorizationRequirement` . Maintenant que le `HubInvocationContext` paramètre de ressource est passé, la logique interne peut inspecter le contexte dans lequel le Hub est appelé et prendre des décisions pour permettre à l’utilisateur d’exécuter des méthodes de concentrateur individuelles.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

Dans `Startup.ConfigureServices` , ajoutez la nouvelle stratégie, en fournissant la `DomainRestrictedRequirement` spécification personnalisée comme paramètre pour créer la `DomainRestricted` stratégie.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

Dans l’exemple précédent, la `DomainRestrictedRequirement` classe est à la fois un `IAuthorizationRequirement` et son propre `AuthorizationHandler` pour cette spécification. Il est acceptable de répartir ces deux composants dans des classes distinctes pour séparer les préoccupations. L’une des avantages de l’approche de l’exemple est qu’il n’est pas nécessaire d’injecter le `AuthorizationHandler` pendant le démarrage, car l’exigence et le gestionnaire sont identiques.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [Authentification du jeton du porteur dans ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Autorisation basée sur les ressources](xref:security/authorization/resourcebased)
