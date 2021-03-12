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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="893c5-103">Authentification et autorisation dans gRPC pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="893c5-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="893c5-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="893c5-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="893c5-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="893c5-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="893c5-106">Authentifier les utilisateurs appelant un service gRPC</span><span class="sxs-lookup"><span data-stu-id="893c5-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="893c5-107">gRPC peut être utilisé avec [l’authentification ASP.net Core](xref:security/authentication/identity) pour associer un utilisateur à chaque appel.</span><span class="sxs-lookup"><span data-stu-id="893c5-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="893c5-108">Voici un exemple `Startup.Configure` qui utilise l’authentification gRPC et ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="893c5-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="893c5-109">L’ordre dans lequel vous inscrivez l’intergiciel (middleware) d’authentification ASP.NET Core est important.</span><span class="sxs-lookup"><span data-stu-id="893c5-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="893c5-110">Appelez toujours `UseAuthentication` et `UseAuthorization` après `UseRouting` et avant `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="893c5-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="893c5-111">Le mécanisme d’authentification utilisé par votre application pendant un appel doit être configuré.</span><span class="sxs-lookup"><span data-stu-id="893c5-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="893c5-112">La configuration de l’authentification est ajoutée dans `Startup.ConfigureServices` et sera différente selon le mécanisme d’authentification utilisé par votre application.</span><span class="sxs-lookup"><span data-stu-id="893c5-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="893c5-113">Pour obtenir des exemples d’utilisation de la sécurisation des applications ASP.NET Core, consultez [exemples d’authentification](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="893c5-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="893c5-114">Une fois l’authentification configurée, l’utilisateur est accessible dans une méthode de service gRPC via le `ServerCallContext` .</span><span class="sxs-lookup"><span data-stu-id="893c5-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="893c5-115">Authentification du jeton du porteur</span><span class="sxs-lookup"><span data-stu-id="893c5-115">Bearer token authentication</span></span>

<span data-ttu-id="893c5-116">Le client peut fournir un jeton d’accès pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="893c5-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="893c5-117">Le serveur valide le jeton et l’utilise pour identifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="893c5-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="893c5-118">Sur le serveur, l’authentification du jeton du porteur est configurée à l’aide de l’intergiciel (middleware) du [porteur JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="893c5-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="893c5-119">Dans le client .NET gRPC, le jeton peut être envoyé avec des appels à l’aide de la `Metadata` collection.</span><span class="sxs-lookup"><span data-stu-id="893c5-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="893c5-120">Les entrées de la `Metadata` collection sont envoyées avec un appel gRPC en tant qu’en-têtes http :</span><span class="sxs-lookup"><span data-stu-id="893c5-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

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

<span data-ttu-id="893c5-121">La configuration `ChannelCredentials` sur un canal est une autre façon d’envoyer le jeton au service avec des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="893c5-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="893c5-122">Un `ChannelCredentials` peut inclure `CallCredentials` , qui fournit un moyen de définir automatiquement `Metadata` .</span><span class="sxs-lookup"><span data-stu-id="893c5-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="893c5-123">`CallCredentials` est exécuté à chaque fois qu’un appel gRPC est effectué, ce qui évite d’avoir à écrire du code à plusieurs endroits pour passer le jeton vous-même.</span><span class="sxs-lookup"><span data-stu-id="893c5-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="893c5-124">Notez que ne s' `CallCredentials` appliquent que si le canal est sécurisé avec TLS.</span><span class="sxs-lookup"><span data-stu-id="893c5-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="893c5-125">`CallCredentials` ne sont pas appliquées sur les canaux non TLS non sécurisés.</span><span class="sxs-lookup"><span data-stu-id="893c5-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="893c5-126">Les informations d’identification dans l’exemple suivant configure le canal pour envoyer le jeton avec chaque appel gRPC :</span><span class="sxs-lookup"><span data-stu-id="893c5-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="893c5-127">Authentification du certificat du client</span><span class="sxs-lookup"><span data-stu-id="893c5-127">Client certificate authentication</span></span>

<span data-ttu-id="893c5-128">Un client peut également fournir un certificat client pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="893c5-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="893c5-129">L' [authentification par certificat](https://tools.ietf.org/html/rfc5246#section-7.4.4) se produit au niveau du TLS, à long terme avant qu’il ne soit ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="893c5-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="893c5-130">Lorsque la demande entre ASP.NET Core, le [package d’authentification du certificat client](xref:security/authentication/certauth) vous permet de résoudre le certificat en `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="893c5-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="893c5-131">Configurez le serveur pour qu’il accepte les certificats clients.</span><span class="sxs-lookup"><span data-stu-id="893c5-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="893c5-132">Pour plus d’informations sur l’acceptation des certificats clients dans Kestrel, IIS et Azure, consultez <xref:security/authentication/certauth#configure-your-server-to-require-certificates> .</span><span class="sxs-lookup"><span data-stu-id="893c5-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="893c5-133">Dans le client .NET gRPC, le certificat client est ajouté à `HttpClientHandler` qui est ensuite utilisé pour créer le client gRPC :</span><span class="sxs-lookup"><span data-stu-id="893c5-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="893c5-134">Autres mécanismes d’authentification</span><span class="sxs-lookup"><span data-stu-id="893c5-134">Other authentication mechanisms</span></span>

<span data-ttu-id="893c5-135">Un grand nombre ASP.NET Core mécanismes d’authentification pris en charge fonctionnent avec gRPC :</span><span class="sxs-lookup"><span data-stu-id="893c5-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="893c5-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="893c5-136">Azure Active Directory</span></span>
* <span data-ttu-id="893c5-137">Certificat client</span><span class="sxs-lookup"><span data-stu-id="893c5-137">Client Certificate</span></span>
* <span data-ttu-id="893c5-138">IdentityServeur</span><span class="sxs-lookup"><span data-stu-id="893c5-138">IdentityServer</span></span>
* <span data-ttu-id="893c5-139">Jeton JWT</span><span class="sxs-lookup"><span data-stu-id="893c5-139">JWT Token</span></span>
* <span data-ttu-id="893c5-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="893c5-140">OAuth 2.0</span></span>
* <span data-ttu-id="893c5-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="893c5-141">OpenID Connect</span></span>
* <span data-ttu-id="893c5-142">Un certificat de fournisseur d'identité WS-Federation</span><span class="sxs-lookup"><span data-stu-id="893c5-142">WS-Federation</span></span>

<span data-ttu-id="893c5-143">Pour plus d’informations sur la configuration de l’authentification sur le serveur, consultez [ASP.net Core l’authentification](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="893c5-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="893c5-144">La configuration du client gRPC pour utiliser l’authentification dépend du mécanisme d’authentification que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="893c5-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="893c5-145">Les exemples précédents du jeton de porteur et du certificat client illustrent deux façons de configurer le client gRPC pour envoyer des métadonnées d’authentification avec des appels gRPC :</span><span class="sxs-lookup"><span data-stu-id="893c5-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="893c5-146">Les clients gRPC fortement typés utilisent en `HttpClient` interne.</span><span class="sxs-lookup"><span data-stu-id="893c5-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="893c5-147">L’authentification peut être configurée sur [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)ou en ajoutant des instances [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personnalisées à l' `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="893c5-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="893c5-148">Chaque appel gRPC a un `CallOptions` argument facultatif.</span><span class="sxs-lookup"><span data-stu-id="893c5-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="893c5-149">Les en-têtes personnalisés peuvent être envoyés à l’aide de la collection d’en-têtes de l’option.</span><span class="sxs-lookup"><span data-stu-id="893c5-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="893c5-150">L’authentification Windows (NTLM/Kerberos/Negotiate) ne peut pas être utilisée avec gRPC.</span><span class="sxs-lookup"><span data-stu-id="893c5-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="893c5-151">gRPC requiert HTTP/2 et HTTP/2 ne prend pas en charge l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="893c5-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="893c5-152">Autoriser les utilisateurs à accéder aux services et aux méthodes de service</span><span class="sxs-lookup"><span data-stu-id="893c5-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="893c5-153">Par défaut, toutes les méthodes d’un service peuvent être appelées par des utilisateurs non authentifiés.</span><span class="sxs-lookup"><span data-stu-id="893c5-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="893c5-154">Pour exiger une authentification, appliquez l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut au service :</span><span class="sxs-lookup"><span data-stu-id="893c5-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="893c5-155">Vous pouvez utiliser les arguments de constructeur et les propriétés de l' `[Authorize]` attribut pour limiter l’accès uniquement aux utilisateurs correspondant à des [stratégies d’autorisation](xref:security/authorization/policies)spécifiques.</span><span class="sxs-lookup"><span data-stu-id="893c5-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="893c5-156">Par exemple, si vous avez une stratégie d’autorisation personnalisée nommée `MyAuthorizationPolicy` , assurez-vous que seuls les utilisateurs qui correspondent à cette stratégie peuvent accéder au service à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="893c5-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="893c5-157">L’attribut peut également être appliqué à chaque méthode de service `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="893c5-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="893c5-158">Si l’utilisateur actuel ne correspond pas aux stratégies appliquées à la **fois** à la méthode et à la classe, une erreur est retournée à l’appelant :</span><span class="sxs-lookup"><span data-stu-id="893c5-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="893c5-159">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="893c5-159">Additional resources</span></span>

* [<span data-ttu-id="893c5-160">Authentification du jeton du porteur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="893c5-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="893c5-161">Configurer l’authentification par certificat client dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="893c5-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
