---
title: Créer des API web JSON à partir de gRPC
author: jamesnk
description: Découvrez comment créer des API JSON HTTP pour les services gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: cb2855f0293a6bc800bb5758cd1a8400d4434a24
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855458"
---
# <a name="create-json-web-apis-from-grpc"></a>Créer des API web JSON à partir de gRPC

Par [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> l’API HTTP gRPC est un projet expérimental, et non un produit validé. Nous voulons :
>
> * Testez le fonctionnement de notre approche de création d’API Web JSON pour les services gRPC.
> * Obtenir des commentaires sur si cette approche est utile pour les développeurs .NET.
>
> N’hésitez pas à nous faire [part de vos commentaires](https://github.com/grpc/grpc-dotnet/issues/167) pour nous assurer que nous créons des éléments dont les développeurs aiment et sont productifs.

gRPC est un moyen moderne de communiquer entre les applications. gRPC utilise les contrats HTTP/2, streaming, Protobuf et message pour créer des services haute performance et en temps réel.

Une limitation avec gRPC n’est pas possible pour chaque plateforme de l’utiliser. Les navigateurs ne prennent pas entièrement en charge HTTP/2, ce qui fait de REST et de JSON le moyen principal d’extraire des données dans des applications de navigateur. Malgré les avantages que gRPC apporte, REST et JSON ont un emplacement important dans les applications modernes. La génération d’API Web gRPC ***et** _ JSON ajoute une surcharge indésirable au développement d’applications.

Ce document explique comment créer des API Web JSON à l’aide des services gRPC.

## <a name="grpc-http-api"></a>API HTTP gRPC

gRPC HTTP API est une extension expérimentale de ASP.NET Core qui crée des API JSON RESTful pour gRPC services. Une fois configuré, l’API HTTP gRPC permet aux applications d’appeler des services gRPC avec des concepts HTTP familiers :

_ Verbes HTTP
* Liaison de paramètre d’URL
* Demandes/réponses JSON

gRPC peut toujours être utilisé pour appeler des services.

### <a name="usage"></a>Usage

1. Ajoutez une référence de package à [Microsoft. AspNetCore. GRPC. HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).
1. Inscrire des services dans *Startup.cs* avec `AddGrpcHttpApi` .
1. Ajoutez des fichiers [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) et [Google/API/annotations. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) à votre projet.
1. Annotez les méthodes gRPC dans vos fichiers *. proto* avec des liaisons et des itinéraires http :

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

La `SayHello` méthode gRPC peut désormais être appelée gRPC + Protobuf et comme API http :

* Demande : `HTTP/1.1 GET /v1/greeter/world`
* Réponse : `{ "message": "Hello world" }`

Les journaux du serveur indiquent que l’appel HTTP est exécuté par un service gRPC. l’API HTTP gRPC mappe la requête HTTP entrante à un message gRPC, puis convertit le message de réponse au format JSON.

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

Il s’agit d’un exemple de base. Pour plus d’options de personnalisation, consultez [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) .

### <a name="enable-swaggeropenapi-support"></a>Activer la prise en charge de Swagger/OpenAPI

Swagger (OpenAPI) est une spécification indépendante du langage pour décrire les API REST. l’API HTTP gRPC peut s’intégrer à [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) pour générer un point de terminaison Swagger pour les services gRPC RESTful. Le point de terminaison Swagger peut ensuite être utilisé avec l' [interface utilisateur Swagger](https://swagger.io/swagger-ui/) et d’autres outils.

Pour activer Swagger avec l’API HTTP gRPC :

1. Ajoutez une référence de package à [Microsoft. AspNetCore. GRPC. Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).
2. Configurez Swashbuckle dans *Startup.cs*. La `AddGrpcSwagger` méthode Configure Swashbuckle pour inclure les points de terminaison de l’API http gRPC.

[!code-csharp[](~/grpc/httpapi/Startup.cs?name=snippet_1&highlight=6-10,15-19)]

Pour confirmer que Swashbuckle génère des Swagger pour les services gRPC RESTful, démarrez l’application et accédez à la page d’interface utilisateur Swagger :

![Interface utilisateur Swagger](~/grpc/httpapi/static/swaggerui.png)

### <a name="grpc-http-api-vs-grpc-web"></a>API HTTP gRPC et gRPC-Web

L’API HTTP gRPC et gRPC-Web autorisent l’appel des services gRPC à partir d’un navigateur. Toutefois, le mode de fonctionnement est différent :

* gRPC-Web permet aux applications de navigateur d’appeler des services gRPC à partir du navigateur avec le client gRPC-Web et Protobuf. gRPC-Web nécessite que l’application de navigateur génère un client gRPC et présente l’avantage d’envoyer des messages Protobuf courts et rapides.
* l’API HTTP gRPC permet aux applications de navigateur d’appeler des services gRPC comme s’il s’agissait d’API RESTful avec JSON. L’application de navigateur n’a pas besoin de générer un client gRPC ni de connaître quoi que ce soit sur gRPC.

Aucun client généré n’est créé pour l’API HTTP gRPC. Le `Greeter` service précédent peut être appelé à l’aide des API JavaScript du navigateur :

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>État expérimental

l’API HTTP gRPC est une expérience. Elle n’est pas complète et n’est pas prise en charge. Nous nous intéressons à cette technologie et à la capacité qu’elle permet aux développeurs d’applications de créer rapidement des services gRPC et JSON en même temps. Il n’y a aucun engagement envers l’exécution de l’API HTTP gRPC.

Nous souhaitons évaluer l’intérêt pour les développeurs dans l’API HTTP gRPC. Si vous avez un intérêt pour l’API HTTP gRPC, faites-nous [part de vos commentaires](https://github.com/grpc/grpc-dotnet/issues/167).

## <a name="grpc-gateway"></a>GRPC-passerelle

[GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/) est une autre technologie permettant de créer des API JSON RESTful à partir des services GRPC. Il utilise les mêmes annotations *. proto* pour mapper les concepts http aux services gRPC.

La plus grande différence entre GRPC-Gateway et gRPC HTTP API est GRPC-Gateway utilise la génération de code pour créer un serveur proxy inverse. Le proxy inverse traduit les appels RESTful en gRPC, puis les envoie au service gRPC.

Pour l’installation et l’utilisation de GRPC-Gateway, consultez le [fichier Lisez-moi de la passerelle GRPC](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).

## <a name="additional-resources"></a>Ressources supplémentaires

* [documentation Google. API. HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
