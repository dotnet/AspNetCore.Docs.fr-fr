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
ms.openlocfilehash: 64d18114e2fe9ee10edb902a98a281c3cd9f3393
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417576"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="44ae9-103">Créer des API web JSON à partir de gRPC</span><span class="sxs-lookup"><span data-stu-id="44ae9-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="44ae9-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="44ae9-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="44ae9-105">l’API HTTP gRPC est un projet expérimental, et non un produit validé.</span><span class="sxs-lookup"><span data-stu-id="44ae9-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="44ae9-106">Nous voulons :</span><span class="sxs-lookup"><span data-stu-id="44ae9-106">We want to:</span></span>
>
> * <span data-ttu-id="44ae9-107">Testez le fonctionnement de notre approche de création d’API Web JSON pour les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="44ae9-108">Obtenir des commentaires sur si cette approche est utile pour les développeurs .NET.</span><span class="sxs-lookup"><span data-stu-id="44ae9-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="44ae9-109">N’hésitez pas à nous faire [part de vos commentaires](https://github.com/grpc/grpc-dotnet/issues/167) pour nous assurer que nous créons des éléments dont les développeurs aiment et sont productifs.</span><span class="sxs-lookup"><span data-stu-id="44ae9-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="44ae9-110">gRPC est un moyen moderne de communiquer entre les applications.</span><span class="sxs-lookup"><span data-stu-id="44ae9-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="44ae9-111">gRPC utilise les contrats HTTP/2, streaming, Protobuf et message pour créer des services haute performance et en temps réel.</span><span class="sxs-lookup"><span data-stu-id="44ae9-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="44ae9-112">Une limitation avec gRPC n’est pas possible pour chaque plateforme de l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="44ae9-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="44ae9-113">Les navigateurs ne prennent pas entièrement en charge HTTP/2, ce qui fait de REST et de JSON le moyen principal d’extraire des données dans des applications de navigateur.</span><span class="sxs-lookup"><span data-stu-id="44ae9-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="44ae9-114">Malgré les avantages que gRPC apporte, REST et JSON ont un emplacement important dans les applications modernes.</span><span class="sxs-lookup"><span data-stu-id="44ae9-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="44ae9-115">La génération d’API Web gRPC \***et** _ JSON ajoute une surcharge indésirable au développement d’applications.</span><span class="sxs-lookup"><span data-stu-id="44ae9-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="44ae9-116">Ce document explique comment créer des API Web JSON à l’aide des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="44ae9-117">API HTTP gRPC</span><span class="sxs-lookup"><span data-stu-id="44ae9-117">gRPC HTTP API</span></span>

<span data-ttu-id="44ae9-118">gRPC HTTP API est une extension expérimentale de ASP.NET Core qui crée des API JSON RESTful pour gRPC services.</span><span class="sxs-lookup"><span data-stu-id="44ae9-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="44ae9-119">Une fois configuré, l’API HTTP gRPC permet aux applications d’appeler des services gRPC avec des concepts HTTP familiers :</span><span class="sxs-lookup"><span data-stu-id="44ae9-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="44ae9-120">_ Verbes HTTP</span><span class="sxs-lookup"><span data-stu-id="44ae9-120">_ HTTP verbs</span></span>
* <span data-ttu-id="44ae9-121">Liaison de paramètre d’URL</span><span class="sxs-lookup"><span data-stu-id="44ae9-121">URL parameter binding</span></span>
* <span data-ttu-id="44ae9-122">Demandes/réponses JSON</span><span class="sxs-lookup"><span data-stu-id="44ae9-122">JSON requests/responses</span></span>

<span data-ttu-id="44ae9-123">gRPC peut toujours être utilisé pour appeler des services.</span><span class="sxs-lookup"><span data-stu-id="44ae9-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="44ae9-124">Usage</span><span class="sxs-lookup"><span data-stu-id="44ae9-124">Usage</span></span>

1. <span data-ttu-id="44ae9-125">Ajoutez une référence de package à [Microsoft. AspNetCore. GRPC. HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="44ae9-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="44ae9-126">Inscrire des services dans *Startup.cs* avec `AddGrpcHttpApi` .</span><span class="sxs-lookup"><span data-stu-id="44ae9-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="44ae9-127">Ajoutez des fichiers [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) et [Google/API/annotations. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) à votre projet.</span><span class="sxs-lookup"><span data-stu-id="44ae9-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="44ae9-128">Annotez les méthodes gRPC dans vos fichiers *. proto* avec des liaisons et des itinéraires http :</span><span class="sxs-lookup"><span data-stu-id="44ae9-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

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

<span data-ttu-id="44ae9-129">La `SayHello` méthode gRPC peut désormais être appelée gRPC + Protobuf et comme API http :</span><span class="sxs-lookup"><span data-stu-id="44ae9-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="44ae9-130">Demande : `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="44ae9-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="44ae9-131">Réponse : `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="44ae9-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="44ae9-132">Les journaux du serveur indiquent que l’appel HTTP est exécuté par un service gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="44ae9-133">l’API HTTP gRPC mappe la requête HTTP entrante à un message gRPC, puis convertit le message de réponse au format JSON.</span><span class="sxs-lookup"><span data-stu-id="44ae9-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

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

<span data-ttu-id="44ae9-134">Il s’agit d’un exemple de base.</span><span class="sxs-lookup"><span data-stu-id="44ae9-134">This is a basic example.</span></span> <span data-ttu-id="44ae9-135">Pour plus d’options de personnalisation, consultez [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) .</span><span class="sxs-lookup"><span data-stu-id="44ae9-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="44ae9-136">API HTTP gRPC et gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="44ae9-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="44ae9-137">L’API HTTP gRPC et gRPC-Web autorisent l’appel des services gRPC à partir d’un navigateur.</span><span class="sxs-lookup"><span data-stu-id="44ae9-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="44ae9-138">Toutefois, le mode de fonctionnement est différent :</span><span class="sxs-lookup"><span data-stu-id="44ae9-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="44ae9-139">gRPC-Web permet aux applications de navigateur d’appeler des services gRPC à partir du navigateur avec le client gRPC-Web et Protobuf.</span><span class="sxs-lookup"><span data-stu-id="44ae9-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="44ae9-140">gRPC-Web nécessite que l’application de navigateur génère un client gRPC et présente l’avantage d’envoyer des messages Protobuf courts et rapides.</span><span class="sxs-lookup"><span data-stu-id="44ae9-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="44ae9-141">l’API HTTP gRPC permet aux applications de navigateur d’appeler des services gRPC comme s’il s’agissait d’API RESTful avec JSON.</span><span class="sxs-lookup"><span data-stu-id="44ae9-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="44ae9-142">L’application de navigateur n’a pas besoin de générer un client gRPC ni de connaître quoi que ce soit sur gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="44ae9-143">Aucun client généré n’est créé pour l’API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="44ae9-144">Le `Greeter` service précédent peut être appelé à l’aide des API JavaScript du navigateur :</span><span class="sxs-lookup"><span data-stu-id="44ae9-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="44ae9-145">État expérimental</span><span class="sxs-lookup"><span data-stu-id="44ae9-145">Experimental status</span></span>

<span data-ttu-id="44ae9-146">l’API HTTP gRPC est une expérience.</span><span class="sxs-lookup"><span data-stu-id="44ae9-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="44ae9-147">Elle n’est pas complète et n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="44ae9-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="44ae9-148">Nous nous intéressons à cette technologie et à la capacité qu’elle permet aux développeurs d’applications de créer rapidement des services gRPC et JSON en même temps.</span><span class="sxs-lookup"><span data-stu-id="44ae9-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="44ae9-149">Il n’y a aucun engagement envers l’exécution de l’API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="44ae9-150">Nous souhaitons évaluer l’intérêt pour les développeurs dans l’API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="44ae9-151">Si vous avez un intérêt pour l’API HTTP gRPC, faites-nous [part de vos commentaires](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="44ae9-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="44ae9-152">GRPC-passerelle</span><span class="sxs-lookup"><span data-stu-id="44ae9-152">grpc-gateway</span></span>

<span data-ttu-id="44ae9-153">[GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/) est une autre technologie permettant de créer des API JSON RESTful à partir des services GRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="44ae9-154">Il utilise les mêmes annotations *. proto* pour mapper les concepts http aux services gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="44ae9-155">La plus grande différence entre GRPC-Gateway et gRPC HTTP API est GRPC-Gateway utilise la génération de code pour créer un serveur proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="44ae9-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="44ae9-156">Le proxy inverse traduit les appels RESTful en gRPC, puis les envoie au service gRPC.</span><span class="sxs-lookup"><span data-stu-id="44ae9-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="44ae9-157">Pour l’installation et l’utilisation de GRPC-Gateway, consultez le [fichier Lisez-moi de la passerelle GRPC](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span><span class="sxs-lookup"><span data-stu-id="44ae9-157">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44ae9-158">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="44ae9-158">Additional resources</span></span>

* [<span data-ttu-id="44ae9-159">documentation Google. API. HttpRule</span><span class="sxs-lookup"><span data-stu-id="44ae9-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
