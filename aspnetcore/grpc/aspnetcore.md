---
title: Services gRPC avec ASP.NET Core
author: juntaoluo
description: Découvrez les concepts de base liés à l’écriture de services gRPC avec ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: f17ba247747f906cf026fc0f7bc04d51f4c8cb2a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530201"
---
# <a name="grpc-services-with-aspnet-core"></a>Services gRPC avec ASP.NET Core

Ce document montre comment prendre en main les services gRPC à l’aide de ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Bien démarrer avec le service gRPC dans ASP.NET Core

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour obtenir des instructions détaillées sur la création d’un projet gRPC, consultez [prise en main des services gRPC](xref:tutorials/grpc/grpc-start) .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Exécutez `dotnet new grpc -o GrpcGreeter` à partir de la ligne de commande.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Ajouter des services gRPC à une application ASP.NET Core

gRPC requiert le package [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .

### <a name="configure-grpc"></a>Configurer gRPC

Dans *Startup.cs* :

* gRPC est activé avec la `AddGrpc` méthode.
* Chaque service gRPC est ajouté au pipeline de routage via la `MapGrpcService` méthode.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core middleware et les fonctionnalités partagent le pipeline de routage. par conséquent, une application peut être configurée pour servir des gestionnaires de demandes supplémentaires. Les gestionnaires de demandes supplémentaires, tels que les contrôleurs MVC, fonctionnent en parallèle avec les services gRPC configurés.

## <a name="server-options"></a>Options de serveur

les services gRPC peuvent être hébergés par tous les serveurs ASP.NET Core intégrés.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS requiert .NET 5 et Windows 10 Build 20241 ou version ultérieure.

&Dagger;HTTP.sys nécessite .NET 5 et Windows 10 Build 19529 ou version ultérieure.

Pour plus d’informations sur le choix du serveur approprié pour une application ASP.NET Core, consultez <xref:fundamentals/servers/index> .

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) est un serveur Web multiplateforme pour ASP.net core. Kestrel fournit les meilleures performances et l’utilisation de la mémoire, mais il ne dispose pas de certaines des fonctionnalités avancées de HTTP.sys telles que le partage de ports.

Points de terminaison Kestrel gRPC :

* Nécessite HTTP/2.
* Doit être sécurisé avec [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).

### <a name="http2"></a>HTTP/2

gRPC requiert HTTP/2. gRPC pour ASP.NET Core valide [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) est `HTTP/2` .

Kestrel [prend en charge http/2](xref:fundamentals/servers/kestrel/http2) sur la plupart des systèmes d’exploitation modernes. Les points de terminaison Kestrel sont configurés pour prendre en charge les connexions HTTP/1.1 et HTTP/2 par défaut.

### <a name="tls"></a>TLS

Les points de terminaison Kestrel utilisés pour gRPC doivent être sécurisés avec TLS. En cours de développement, un point de terminaison sécurisé avec TLS est automatiquement créé `https://localhost:5001` lorsque le certificat de développement ASP.net Core est présent. Aucune configuration n'est requise. Un `https` préfixe vérifie que le point de terminaison Kestrel utilise TLS.

En production, TLS doit être configuré de manière explicite. Dans l' *appsettings.json* exemple suivant, un point de terminaison http/2 sécurisé avec TLS est fourni :

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Vous pouvez également configurer les points de terminaison Kestrel dans *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Négociation de protocole

TLS est utilisé pour davantage que la sécurisation de la communication. La négociation de [négociation de protocole de couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS est utilisée pour négocier le protocole de connexion entre le client et le serveur lorsqu’un point de terminaison prend en charge plusieurs protocoles. Cette négociation détermine si la connexion utilise HTTP/1.1 ou HTTP/2.

Si un point de terminaison HTTP/2 est configuré sans TLS, le [ListenOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) du point de terminaison doit avoir la valeur `HttpProtocols.Http2` . Un point de terminaison avec plusieurs protocoles (par exemple, `HttpProtocols.Http1AndHttp2` ) ne peut pas être utilisé sans TLS, car il n’y a aucune négociation. Toutes les connexions au point de terminaison non sécurisé par défaut pour les appels HTTP/1.1 et gRPC échouent.

Pour plus d’informations sur l’activation de HTTP/2 et de TLS avec Kestrel, consultez [configuration du point de terminaison Kestrel](xref:fundamentals/servers/kestrel/endpoints).

> [!NOTE]
> MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS. Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS. Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="iis"></a>IIS

[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) est un serveur Web flexible, sécurisé et facile à gérer pour héberger des applications Web, y compris des ASP.net core. .NET 5 et Windows 10 Build 20241 ou version ultérieure sont requis pour héberger les services gRPC avec IIS.

IIS doit être configuré pour utiliser TLS et HTTP/2. Pour plus d’informations, consultez <xref:host-and-deploy/iis/protocols>.

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) est un serveur web pour ASP.NET Core qui s’exécute uniquement sous Windows. .NET 5 et Windows 10 Build 20241 ou version ultérieure sont requis pour héberger les services gRPC avec HTTP.sys.

Les HTTP.sys doivent être configurés pour utiliser TLS et HTTP/2. Pour plus d’informations, consultez  [HTTP.sys la prise en charge http/2 du serveur Web](xref:fundamentals/servers/httpsys#http2-support).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) est un serveur Web multiplateforme pour ASP.net core. Kestrel fournit les meilleures performances et l’utilisation de la mémoire, mais il ne dispose pas de certaines des fonctionnalités avancées de HTTP.sys telles que le partage de ports.

Points de terminaison Kestrel gRPC :

* Nécessite HTTP/2.
* Doit être sécurisé avec [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).

### <a name="http2"></a>HTTP/2

gRPC requiert HTTP/2. gRPC pour ASP.NET Core valide [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) est `HTTP/2` .

Kestrel [prend en charge http/2](xref:fundamentals/servers/kestrel#http2-support) sur la plupart des systèmes d’exploitation modernes. Les points de terminaison Kestrel sont configurés pour prendre en charge les connexions HTTP/1.1 et HTTP/2 par défaut.

### <a name="tls"></a>TLS

Les points de terminaison Kestrel utilisés pour gRPC doivent être sécurisés avec TLS. En cours de développement, un point de terminaison sécurisé avec TLS est automatiquement créé `https://localhost:5001` lorsque le certificat de développement ASP.net Core est présent. Aucune configuration n'est requise. Un `https` préfixe vérifie que le point de terminaison Kestrel utilise TLS.

En production, TLS doit être configuré de manière explicite. Dans l' *appsettings.json* exemple suivant, un point de terminaison http/2 sécurisé avec TLS est fourni :

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Vous pouvez également configurer les points de terminaison Kestrel dans *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Négociation de protocole

TLS est utilisé pour davantage que la sécurisation de la communication. La négociation de [négociation de protocole de couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS est utilisée pour négocier le protocole de connexion entre le client et le serveur lorsqu’un point de terminaison prend en charge plusieurs protocoles. Cette négociation détermine si la connexion utilise HTTP/1.1 ou HTTP/2.

Si un point de terminaison HTTP/2 est configuré sans TLS, le [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) du point de terminaison doit avoir la valeur `HttpProtocols.Http2` . Un point de terminaison avec plusieurs protocoles (par exemple, `HttpProtocols.Http1AndHttp2` ) ne peut pas être utilisé sans TLS, car il n’y a aucune négociation. Toutes les connexions au point de terminaison non sécurisé par défaut pour les appels HTTP/1.1 et gRPC échouent.

Pour plus d’informations sur l’activation de HTTP/2 et de TLS avec Kestrel, consultez [configuration du point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS. Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS. Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a>Intégration avec les API ASP.NET Core

les services gRPC ont un accès complet aux fonctionnalités de ASP.NET Core telles que l' [injection de dépendances](xref:fundamentals/dependency-injection) (di) et la [journalisation](xref:fundamentals/logging/index). Par exemple, l’implémentation de service peut résoudre un service d’enregistreur d’événements à partir du conteneur DI via le constructeur :

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Par défaut, l’implémentation du service gRPC peut résoudre d’autres services DI avec n’importe quelle durée de vie (Singleton, étendue ou transitoire).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Résoudre HttpContext dans les méthodes gRPC

L’API gRPC permet d’accéder à certaines données de message HTTP/2, telles que la méthode, l’hôte, l’en-tête et les codes de fin. L’accès s’effectue par le biais de l' `ServerCallContext` argument passé à chaque méthode gRPC :

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` ne fournit pas un accès complet à `HttpContext` dans toutes les api ASP.net. La `GetHttpContext` méthode d’extension fournit un accès complet au `HttpContext` représentant le message http/2 sous-jacent dans les API ASP.net :

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
