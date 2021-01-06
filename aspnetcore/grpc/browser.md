---
title: Utiliser gRPC dans les applications de navigateur
author: jamesnk
description: Découvrez comment configurer les services gRPC sur ASP.NET Core à appeler à partir d’applications de navigateur à l’aide de gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 6456707620ae1c1f4d23f3562c78d1bf05d4844f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058907"
---
# <a name="use-grpc-in-browser-apps"></a>Utiliser gRPC dans les applications de navigateur

Par [James Newton-King](https://twitter.com/jamesnk)

 Découvrez comment configurer un service ASP.NET Core gRPC existant à appeler à partir d’applications de navigateur, à l’aide du protocole [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) . gRPC-Web permet au navigateur et Blazor aux applications JavaScript d’appeler des services gRPC. Il n’est pas possible d’appeler un service gRPC HTTP/2 à partir d’une application basée sur un navigateur. les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web parallèlement à HTTP/2 gRPC.


Pour obtenir des instructions sur l’ajout d’un service gRPC à une application ASP.NET Core existante, consultez [Ajouter des services gRPC à une application ASP.net Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).

Pour obtenir des instructions sur la création d’un projet gRPC, consultez <xref:tutorials/grpc/grpc-start> .

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web dans ASP.NET Core vs. Envoy

Il existe deux façons d’ajouter gRPC-Web à une application ASP.NET Core :

* Prenez en charge gRPC-Web en parallèle de gRPC HTTP/2 dans ASP.NET Core. Cette option utilise l’intergiciel (middleware) fourni par le `Grpc.AspNetCore.Web` Package.
* Utilisez la prise en charge gRPC-Web [du proxy Envoy](https://www.envoyproxy.io/) pour traduire GRPC-Web en gRPC http/2. L’appel traduit est ensuite transféré vers l’application ASP.NET Core.

Chaque approche présente des avantages et des inconvénients. Si l’environnement d’une application utilise déjà Envoy en tant que proxy, il peut être utile d’utiliser Envoy pour fournir la prise en charge de gRPC-Web. Pour une solution de base pour gRPC-Web qui nécessite uniquement ASP.NET Core, `Grpc.AspNetCore.Web` est un bon choix.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configurer gRPC-Web dans ASP.NET Core

les services gRPC hébergés dans ASP.NET Core peuvent être configurés pour prendre en charge gRPC-Web parallèlement à HTTP/2 gRPC. gRPC-Web ne nécessite aucune modification des services. La seule modification concerne la configuration du démarrage.

Pour activer gRPC-Web avec un service ASP.NET Core gRPC :

* Ajoutez une référence au package [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Configurez l’application pour utiliser gRPC-Web en ajoutant `UseGrpcWeb` et `EnableGrpcWeb` à *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Le code précédent :

* Ajoute l’intergiciel (middleware) Web gRPC, `UseGrpcWeb` , après le routage et avant les points de terminaison.
* Spécifie que la `endpoints.MapGrpcService<GreeterService>()` méthode prend en charge gRPC-Web avec `EnableGrpcWeb` . 

L’intergiciel gRPC-Web peut également être configuré de sorte que tous les services prennent en charge gRPC-Web par défaut et non `EnableGrpcWeb` requis. Spécifiez à `new GrpcWebOptions { DefaultEnabled = true }` quel moment l’intergiciel est ajouté.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> Il existe un problème connu qui provoque l’échec de gRPC-Web lorsqu’il est [hébergé par Http.sys](xref:fundamentals/servers/httpsys) dans .net Core 3. x.
>
> Une solution de contournement pour obtenir gRPC-Web Working sur Http.sys est disponible [ici](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web et CORS

La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web. Cette restriction s’applique à la création d’appels gRPC-Web avec des applications de navigateur. Par exemple, une application de navigateur servie par `https://www.contoso.com` est bloquée de l’appel de gRPC-services Web hébergés sur `https://services.contoso.com` . Le partage des ressources Cross-Origin (CORS) peut être utilisé pour assouplir cette restriction.

Pour permettre à une application de navigateur d’effectuer des appels gRPC-Web Cross-Origin, configurez [cors dans ASP.net Core](xref:security/cors). Utilisez la prise en charge de CORS intégrée et exposez des en-têtes spécifiques à gRPC avec <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Le code précédent :

* Appelle `AddCors` pour ajouter des services cors et configure une stratégie cors qui expose des en-têtes spécifiques à gRPC.
* Appelle `UseCors` pour ajouter l’intergiciel (middleware) cors après le routage et avant les points de terminaison.
* Spécifie que la `endpoints.MapGrpcService<GreeterService>()` méthode prend en charge cors avec `RequiresCors` .

### <a name="grpc-web-and-streaming"></a>gRPC-Web et streaming

Le gRPC traditionnel sur HTTP/2 prend en charge la diffusion en continu dans toutes les directions. gRPC-Web offre une prise en charge limitée de la diffusion en continu :

* gRPC-les clients de navigateur Web ne prennent pas en charge l’appel des méthodes de diffusion en continu bidirectionnelle et de streaming client.
* ASP.NET Core Services gRPC hébergés sur Azure App Service et IIS ne prennent pas en charge la diffusion bidirectionnelle.

Lors de l’utilisation de gRPC-Web, nous vous recommandons uniquement d’utiliser des méthodes unaires et des méthodes de diffusion de serveur.

## <a name="call-grpc-web-from-the-browser"></a>Appeler gRPC-Web à partir du navigateur

Les applications de navigateur peuvent utiliser gRPC-Web pour appeler des services gRPC. Il existe certaines exigences et limitations lors de l’appel de services gRPC avec gRPC-Web à partir du navigateur :

* Le serveur doit avoir été configuré pour prendre en charge gRPC-Web.
* Les appels de diffusion en continu et bidirectionnelle du client ne sont pas pris en charge. La diffusion en continu du serveur est prise en charge.
* L’appel de services gRPC sur un autre domaine requiert la configuration de [cors](xref:security/cors) sur le serveur.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-client Web

Il existe un client JavaScript gRPC-Web. Pour obtenir des instructions sur l’utilisation de gRPC-Web à partir de JavaScript, consultez [écrire du code JavaScript client avec gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configurer gRPC-Web avec le client .NET gRPC

Le client .NET gRPC peut être configuré pour effectuer des appels gRPC-Web. Cela est utile pour les [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) applications qui sont hébergées dans le navigateur et qui ont les mêmes limitations http que le code JavaScript. L’appel de gRPC-Web avec un client .NET est [identique à http/2 gRPC](xref:grpc/client). La seule modification est la manière dont le canal est créé.

Pour utiliser gRPC-Web :

* Ajoutez une référence au package [GRPC .net. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Assurez-vous que la référence au package [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) est 2.29.0 ou supérieure.
* Configurez le canal pour utiliser le `GrpcWebHandler` :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Le code précédent :

* Configure un canal pour utiliser gRPC-Web.
* Crée un client et effectue un appel à l’aide du canal.

`GrpcWebHandler` possède les options de configuration suivantes :

* **InnerHandler**: sous-jacent <xref:System.Net.Http.HttpMessageHandler> qui effectue la requête http gRPC, par exemple, `HttpClientHandler` .
* **GrpcWebMode**: type énumération qui spécifie si la requête http gRPC `Content-Type` est `application/grpc-web` ou `application/grpc-web-text` .
    * `GrpcWebMode.GrpcWeb` configure le contenu à envoyer sans encodage. Valeur par défaut.
    * `GrpcWebMode.GrpcWebText` configure le contenu pour qu’il soit encodé en base64. Requis pour les appels de diffusion en continu du serveur dans les navigateurs.
* **HttpVersion**: protocole http `Version` utilisé pour définir [HttpRequestMessage. version](xref:System.Net.Http.HttpRequestMessage.Version) sur la requête http gRPC sous-jacente. gRPC-Web n’a pas besoin d’une version spécifique et ne remplace pas la valeur par défaut, sauf indication contraire.

> [!IMPORTANT]
> Les clients gRPC générés ont des méthodes synchrones et asynchrones pour appeler des méthodes unaires. Par exemple, `SayHello` est Sync et `SayHelloAsync` est Async. L’appel d’une méthode de synchronisation dans une Blazor WebAssembly application entraîne le blocage de l’application. Les méthodes Async doivent toujours être utilisées dans Blazor WebAssembly .

### <a name="use-grpc-client-factory-with-grpc-web"></a>Utiliser la fabrique de clients gRPC avec gRPC-Web

Un client .NET compatible avec le Web gRPC peut être créé à l’aide de l’intégration de gRPC avec [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).

Pour utiliser gRPC-Web avec la fabrique de clients :

* Ajoutez des références de package au fichier projet pour les packages suivants :
  * [GRPC .net. client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* Inscrire un client gRPC avec l’injection de dépendance (DI) à l’aide de la `AddGrpcClient` méthode d’extension générique. Dans une Blazor WebAssembly application, les services sont inscrits auprès de di dans `Program.cs` .
* Configurez `GrpcWebHandler` à l’aide de la <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> méthode d’extension.

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

Pour plus d’informations, consultez <xref:grpc/clientfactory>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [gRPC pour le projet GitHub des clients Web](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
