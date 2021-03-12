---
title: Authentification et autorisation dans gRPC pour ASP.NET Core
author: jamesnk
description: Découvrez comment utiliser l’authentification et l’autorisation dans gRPC pour ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 5120964459a81ef2d668877bb08ecde512c2389d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587994"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Authentification et autorisation dans gRPC pour ASP.NET Core

Par [James Newton-King](https://twitter.com/jamesnk)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Authentifier les utilisateurs appelant un service gRPC

gRPC peut être utilisé avec [l’authentification ASP.net Core](xref:security/authentication/identity) pour associer un utilisateur à chaque appel.

Voici un exemple `Startup.Configure` qui utilise l’authentification gRPC et ASP.net Core :

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> L’ordre dans lequel vous inscrivez l’intergiciel (middleware) d’authentification ASP.NET Core est important. Appelez toujours `UseAuthentication` et `UseAuthorization` après `UseRouting` et avant `UseEndpoints` .

Le mécanisme d’authentification utilisé par votre application pendant un appel doit être configuré. La configuration de l’authentification est ajoutée dans `Startup.ConfigureServices` et sera différente selon le mécanisme d’authentification utilisé par votre application. Pour obtenir des exemples d’utilisation de la sécurisation des applications ASP.NET Core, consultez [exemples d’authentification](xref:security/authentication/samples).

Une fois l’authentification configurée, l’utilisateur est accessible dans une méthode de service gRPC via le `ServerCallContext` .

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Authentification du jeton du porteur

Le client peut fournir un jeton d’accès pour l’authentification. Le serveur valide le jeton et l’utilise pour identifier l’utilisateur.

Sur le serveur, l’authentification du jeton du porteur est configurée à l’aide de l’intergiciel (middleware) du [porteur JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

Dans le client .NET gRPC, le jeton peut être envoyé avec des appels à l’aide de la `Metadata` collection. Les entrées de la `Metadata` collection sont envoyées avec un appel gRPC en tant qu’en-têtes http :

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

La configuration `ChannelCredentials` sur un canal est une autre façon d’envoyer le jeton au service avec des appels gRPC. Un `ChannelCredentials` peut inclure `CallCredentials` , qui fournit un moyen de définir automatiquement `Metadata` .

`CallCredentials` est exécuté à chaque fois qu’un appel gRPC est effectué, ce qui évite d’avoir à écrire du code à plusieurs endroits pour passer le jeton vous-même. Notez que ne s' `CallCredentials` appliquent que si le canal est sécurisé avec TLS. `CallCredentials` ne sont pas appliquées sur les canaux non TLS non sécurisés.

Les informations d’identification dans l’exemple suivant configure le canal pour envoyer le jeton avec chaque appel gRPC :

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Authentification du certificat du client

Un client peut également fournir un certificat client pour l’authentification. L' [authentification par certificat](https://tools.ietf.org/html/rfc5246#section-7.4.4) se produit au niveau du TLS, à long terme avant qu’il ne soit ASP.net core. Lorsque la demande entre ASP.NET Core, le [package d’authentification du certificat client](xref:security/authentication/certauth) vous permet de résoudre le certificat en `ClaimsPrincipal` .

> [!NOTE]
> Configurez le serveur pour qu’il accepte les certificats clients. Pour plus d’informations sur l’acceptation des certificats clients dans Kestrel, IIS et Azure, consultez <xref:security/authentication/certauth#configure-your-server-to-require-certificates> .

Dans le client .NET gRPC, le certificat client est ajouté à `HttpClientHandler` qui est ensuite utilisé pour créer le client gRPC :

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Autres mécanismes d’authentification

Un grand nombre ASP.NET Core mécanismes d’authentification pris en charge fonctionnent avec gRPC :

* Azure Active Directory
* Certificat client
* IdentityServeur
* Jeton JWT
* OAuth 2.0
* OpenID Connect
* Un certificat de fournisseur d'identité WS-Federation

Pour plus d’informations sur la configuration de l’authentification sur le serveur, consultez [ASP.net Core l’authentification](xref:security/authentication/identity).

La configuration du client gRPC pour utiliser l’authentification dépend du mécanisme d’authentification que vous utilisez. Les exemples précédents du jeton de porteur et du certificat client illustrent deux façons de configurer le client gRPC pour envoyer des métadonnées d’authentification avec des appels gRPC :

* Les clients gRPC fortement typés utilisent en `HttpClient` interne. L’authentification peut être configurée sur [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)ou en ajoutant des instances [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personnalisées à l' `HttpClient` .
* Chaque appel gRPC a un `CallOptions` argument facultatif. Les en-têtes personnalisés peuvent être envoyés à l’aide de la collection d’en-têtes de l’option.

> [!NOTE]
> L’authentification Windows (NTLM/Kerberos/Negotiate) ne peut pas être utilisée avec gRPC. gRPC requiert HTTP/2 et HTTP/2 ne prend pas en charge l’authentification Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autoriser les utilisateurs à accéder aux services et aux méthodes de service

Par défaut, toutes les méthodes d’un service peuvent être appelées par des utilisateurs non authentifiés. Pour exiger une authentification, appliquez l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut au service :

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Vous pouvez utiliser les arguments de constructeur et les propriétés de l' `[Authorize]` attribut pour limiter l’accès uniquement aux utilisateurs correspondant à des [stratégies d’autorisation](xref:security/authorization/policies)spécifiques. Par exemple, si vous avez une stratégie d’autorisation personnalisée nommée `MyAuthorizationPolicy` , assurez-vous que seuls les utilisateurs qui correspondent à cette stratégie peuvent accéder au service à l’aide du code suivant :

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

L’attribut peut également être appliqué à chaque méthode de service `[Authorize]` . Si l’utilisateur actuel ne correspond pas aux stratégies appliquées à la **fois** à la méthode et à la classe, une erreur est retournée à l’appelant :

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Authentification du jeton du porteur dans ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Configurer l’authentification par certificat client dans ASP.NET Core](xref:security/authentication/certauth)
