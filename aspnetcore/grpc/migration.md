---
title: Migration des services gRPC de C Core vers ASP.NET Core
author: juntaoluo
description: Découvrez comment déplacer une application gRPC basée sur un noyau C existante pour qu’elle s’exécute sur ASP.NET Core pile.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 1a230e470fa666b2aa6761b4d5dabd09264d2aae
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059830"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migration des services gRPC de C Core vers ASP.NET Core

Par [John Luo](https://github.com/juntaoluo)

En raison de l’implémentation de la pile sous-jacente, certaines fonctionnalités ne fonctionnent pas de la même façon entre les applications [gRPC basées sur le langage C](https://grpc.io/blog/grpc-stacks) et les applications basées sur les ASP.net core. Ce document met en évidence les principales différences en matière de migration entre les deux piles.

## <a name="grpc-service-implementation-lifetime"></a>durée de vie de l’implémentation du service gRPC

Dans la pile ASP.NET Core, les services gRPC, par défaut, sont créés avec une [durée de vie limitée](xref:fundamentals/dependency-injection#service-lifetimes). En revanche, gRPC C-Core est lié par défaut à un service avec une [durée de vie Singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Une durée de vie limitée permet à l’implémentation de service de résoudre d’autres services avec des durées de vie délimitées. Par exemple, une durée de vie délimitée peut également être résolue `DbContext` à partir du conteneur di via l’injection de constructeur. Utilisation de la durée de vie limitée :

* Une nouvelle instance de l’implémentation de service est construite pour chaque requête.
* Il n’est pas possible de partager l’état entre les demandes via des membres d’instance sur le type d’implémentation.
* L’objectif est de stocker les États partagés dans un service Singleton dans le conteneur DI. Les États partagés stockés sont résolus dans le constructeur de l’implémentation du service gRPC.

Pour plus d’informations sur les durées de vie des services, consultez <xref:fundamentals/dependency-injection#service-lifetimes> .

### <a name="add-a-singleton-service"></a>Ajouter un service Singleton

Pour faciliter la transition d’une implémentation gRPC C-Core à ASP.NET Core, il est possible de modifier la durée de vie du service de l’implémentation de service de l’étendue à Singleton. Cela implique l’ajout d’une instance de l’implémentation de service au conteneur DI :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Toutefois, une implémentation de service avec une durée de vie Singleton n’est plus en mesure de résoudre les services délimités via l’injection de constructeur.

## <a name="configure-grpc-services-options"></a>Configurer les options des services gRPC

Dans les applications basées sur C-Core, les paramètres tels que `grpc.max_receive_message_length` et `grpc.max_send_message_length` sont configurés avec `ChannelOption` lors de [la construction de l’instance de serveur](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

Dans ASP.NET Core, gRPC fournit la configuration par le biais du `GrpcServiceOptions` type. Par exemple, la taille maximale des messages entrants peut être configurée par le biais du service gRPC `AddGrpc` . L’exemple suivant modifie la valeur par défaut `MaxReceiveMessageSize` de 4 Mo à 16 Mo :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Pour plus d’informations sur la configuration, consultez <xref:grpc/configuration> .

## <a name="logging"></a>Journalisation

Les applications basées sur le langage C s’appuient sur le `GrpcEnvironment` pour [configurer l’enregistreur](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) d’événements à des fins de débogage. La pile ASP.NET Core fournit cette fonctionnalité par le biais de l' [API de journalisation](xref:fundamentals/logging/index). Par exemple, un enregistreur d’événements peut être ajouté au service gRPC via l’injection de constructeur :

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Les applications basées sur le langage C configurent le protocole HTTPs via la [propriété Server. ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Un concept similaire est utilisé pour configurer des serveurs dans ASP.NET Core. Par exemple, Kestrel utilise la [configuration de point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration) pour cette fonctionnalité.

## <a name="grpc-interceptors-vs-middleware"></a>intercepteurs gRPC vs intergiciel

ASP.NET Core [intergiciel](xref:fundamentals/middleware/index) offre des fonctionnalités similaires par rapport aux intercepteurs des applications gRPC basées sur le langage C. ASP.NET Core l’intergiciel et les intercepteurs sont similaires d’un plan conceptuel. Les deux :

* Sont utilisés pour construire un pipeline qui gère une demande gRPC.
* Autorisez l’exécution du travail avant ou après le composant suivant dans le pipeline.
* Fournir un accès à `HttpContext` :
  * Dans l’intergiciel (middleware), `HttpContext` est un paramètre.
  * Dans les intercepteurs, le `HttpContext` est accessible à l’aide du `ServerCallContext` paramètre avec la `ServerCallContext.GetHttpContext` méthode d’extension. Notez que cette fonctionnalité est spécifique aux intercepteurs s’exécutant dans ASP.NET Core.

différences entre l’intercepteur gRPC et l’intergiciel (middleware) ASP.NET Core :

* Intercepteurs
  * Opérer sur la couche d’abstraction gRPC à l’aide de [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Fournir un accès à :
    * Message désérialisé envoyé à un appel.
    * Message retourné par l’appel avant qu’il ne soit sérialisé.
  * Peut intercepter et gérer les exceptions levées à partir des services gRPC.
* Intergiciel
  * S’exécute avant les intercepteurs gRPC.
  * Opère sur les messages HTTP/2 sous-jacents.
  * Peut uniquement accéder aux octets des flux de requête et de réponse.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
