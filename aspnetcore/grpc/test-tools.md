---
title: Tester gRPC services avec gRPCurl dans ASP.NET Core
author: jamesnk
description: Découvrez comment tester les services avec les outils gRPC. gRPCurl un outil en ligne de commande pour interagir avec les services gRPC. gRPCui est une interface utilisateur Web interactive.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058790"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>Tester gRPC services avec gRPCurl dans ASP.NET Core

Par [James Newton-King](https://twitter.com/jamesnk)

Les outils sont disponibles pour gRPC qui permet aux développeurs de tester les services sans générer d’applications clientes :

* [gRPCurl](https://github.com/fullstorydev/grpcurl) est un outil de ligne de commande qui fournit une interaction avec les services gRPC.
* [gRPCui](https://github.com/fullstorydev/grpcui) s’appuie sur gRPCurl et ajoute une interface utilisateur Web interactive pour gRPC, semblable à des outils tels que le billet et l’interface utilisateur Swagger.

Cet article explique comment :

* Téléchargez et installez gRPCurl et gRPCui.
* Configurez la réflexion gRPC avec une application gRPC ASP.NET Core.
* Découvrez et testez les services gRPC avec `grpcurl` .
* Interagissez avec les services gRPC via un navigateur à l’aide de `grpcui` .

## <a name="about-grpcurl"></a>À propos de gRPCurl

gRPCurl est un outil de ligne de commande créé par la communauté gRPC. Ses fonctionnalités sont les suivantes :

* Appel des services gRPC, y compris les services de streaming.
* Découverte de service à l’aide de la [réflexion gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).
* Répertorier et décrire les services gRPC.
* Fonctionne avec des serveurs sécurisés (TLS) et non sécurisés (texte brut).

Pour plus d’informations sur le téléchargement et l’installation de `grpcurl` , consultez la [page d’accueil gRPCurl GitHub](https://github.com/fullstorydev/grpcurl#installation).

![ligne de commande gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>Configurer la réflexion gRPC

`grpcurl` doit connaître le contrat Protobuf des services avant de pouvoir les appeler. Il existe deux façons d'effectuer cette opération :

* Configurez la [réflexion gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) sur le serveur. gRPCurl Découvre automatiquement les contrats de service.
* Spécifiez `.proto` les fichiers dans les arguments de ligne de commande de gRPCurl.

Il est plus facile d’utiliser gRPCurl avec la réflexion gRPC. la réflexion gRPC ajoute un nouveau service gRPC à l’application que les clients peuvent appeler pour découvrir les services.

gRPC ASP.NET Core offre une prise en charge intégrée de la réflexion gRPC avec le [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) Package. Pour configurer la réflexion dans une application :

* Ajoutez une `Grpc.AspNetCore.Server.Reflection` référence de package.
* Inscrire la réflexion dans `Startup.cs` :
  * `AddGrpcReflection` pour inscrire des services qui activent la réflexion.
  * `MapGrpcReflectionService` pour ajouter un point de terminaison de service de réflexion.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

Quand la réflexion gRPC est configurée :

* Un service de réflexion gRPC est ajouté à l’application serveur.
* Les applications clientes qui prennent en charge la réflexion gRPC peuvent appeler le service de réflexion pour découvrir les services hébergés par le serveur.
* les services gRPC sont toujours appelés depuis le client. La réflexion active uniquement la détection de service et ne contourne pas la sécurité côté serveur. Les points de terminaison protégés par l' [authentification et l’autorisation](xref:grpc/authn-and-authz) requièrent que l’appelant transmette les informations d’identification pour que le point de terminaison soit appelé avec succès.

## <a name="use-grpcurl"></a>Utilisez `grpcurl`.

L' `-help` argument décrit les `grpcurl` options de ligne de commande :

```console
$ grpcurl -help
```

### <a name="discover-services"></a>Découvrir les services

Utilisez le `describe` verbe pour afficher les services définis par le serveur :

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

L’exemple précédent :

* Exécute le `describe` verbe sur le serveur `localhost:5001` .
* Imprime les services et les méthodes retournés par la réflexion gRPC.
  * `Greeter` est un service implémenté par l’application.
  * `ServerReflection` est le service ajouté par le `Grpc.AspNetCore.Server.Reflection` Package.

Combiner `describe` avec un service, une méthode ou un nom de message pour afficher ses détails :

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Appeler gRPC services

Appelez un service gRPC en spécifiant un nom de service et de méthode avec un argument JSON qui représente le message de demande. Le JSON est converti en Protobuf et envoyé au service.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Dans l’exemple précédent :

* L' `-d` argument spécifie un message de demande avec JSON. Cet argument doit précéder l’adresse du serveur et le nom de la méthode.
* Appelle la `SayHello` méthode sur le `greeter.Greeter` service.
* Imprime le message de réponse au format JSON.

## <a name="about-grpcui"></a>À propos de gRPCui

gRPCui est une interface utilisateur Web interactive pour gRPC. Il s’appuie sur gRPCurl et offre une interface graphique utilisateur pour la découverte et le test des services gRPC, à l’instar des outils HTTP tels que l’interface utilisateur poster ou Swagger.

Pour plus d’informations sur le téléchargement et l’installation de `grpcui` , consultez la [page d’accueil gRPCui GitHub](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Utilisation de `grpcui`

Exécutez `grpcui` avec l’adresse de serveur pour interagir comme argument :

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

L’outil lance une fenêtre de navigateur avec l’interface utilisateur Web interactive. les services gRPC sont automatiquement découverts à l’aide de la réflexion gRPC.

![interface utilisateur Web de gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Page d’accueil gRPCurl GitHub](https://github.com/fullstorydev/grpcurl)
* [Page d’accueil gRPCui GitHub](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
