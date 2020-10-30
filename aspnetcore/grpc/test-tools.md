---
title: Tester gRPC services avec gRPCurl dans ASP.NET Core
author: jamesnk
description: Découvrez comment tester les services avec les outils gRPC. gRPCurl un outil en ligne de commande pour interagir avec les services gRPC. gRPCui est une interface utilisateur Web interactive.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058790"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="c6d8b-105">Tester gRPC services avec gRPCurl dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6d8b-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="c6d8b-106">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c6d8b-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c6d8b-107">Les outils sont disponibles pour gRPC qui permet aux développeurs de tester les services sans générer d’applications clientes :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="c6d8b-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) est un outil de ligne de commande qui fournit une interaction avec les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="c6d8b-109">[gRPCui](https://github.com/fullstorydev/grpcui) s’appuie sur gRPCurl et ajoute une interface utilisateur Web interactive pour gRPC, semblable à des outils tels que le billet et l’interface utilisateur Swagger.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="c6d8b-110">Cet article explique comment :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-110">This article discusses how to:</span></span>

* <span data-ttu-id="c6d8b-111">Téléchargez et installez gRPCurl et gRPCui.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="c6d8b-112">Configurez la réflexion gRPC avec une application gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="c6d8b-113">Découvrez et testez les services gRPC avec `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="c6d8b-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="c6d8b-114">Interagissez avec les services gRPC via un navigateur à l’aide de `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="c6d8b-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="c6d8b-115">À propos de gRPCurl</span><span class="sxs-lookup"><span data-stu-id="c6d8b-115">About gRPCurl</span></span>

<span data-ttu-id="c6d8b-116">gRPCurl est un outil de ligne de commande créé par la communauté gRPC.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="c6d8b-117">Ses fonctionnalités sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-117">Its features include:</span></span>

* <span data-ttu-id="c6d8b-118">Appel des services gRPC, y compris les services de streaming.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="c6d8b-119">Découverte de service à l’aide de la [réflexion gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="c6d8b-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="c6d8b-120">Répertorier et décrire les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="c6d8b-121">Fonctionne avec des serveurs sécurisés (TLS) et non sécurisés (texte brut).</span><span class="sxs-lookup"><span data-stu-id="c6d8b-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="c6d8b-122">Pour plus d’informations sur le téléchargement et l’installation de `grpcurl` , consultez la [page d’accueil gRPCurl GitHub](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="c6d8b-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![ligne de commande gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="c6d8b-124">Configurer la réflexion gRPC</span><span class="sxs-lookup"><span data-stu-id="c6d8b-124">Set up gRPC reflection</span></span>

<span data-ttu-id="c6d8b-125">`grpcurl` doit connaître le contrat Protobuf des services avant de pouvoir les appeler.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="c6d8b-126">Il existe deux façons d'effectuer cette opération :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-126">There are two ways to do this:</span></span>

* <span data-ttu-id="c6d8b-127">Configurez la [réflexion gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="c6d8b-128">gRPCurl Découvre automatiquement les contrats de service.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="c6d8b-129">Spécifiez `.proto` les fichiers dans les arguments de ligne de commande de gRPCurl.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="c6d8b-130">Il est plus facile d’utiliser gRPCurl avec la réflexion gRPC.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="c6d8b-131">la réflexion gRPC ajoute un nouveau service gRPC à l’application que les clients peuvent appeler pour découvrir les services.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="c6d8b-132">gRPC ASP.NET Core offre une prise en charge intégrée de la réflexion gRPC avec le [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) Package.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="c6d8b-133">Pour configurer la réflexion dans une application :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="c6d8b-134">Ajoutez une `Grpc.AspNetCore.Server.Reflection` référence de package.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="c6d8b-135">Inscrire la réflexion dans `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="c6d8b-136">`AddGrpcReflection` pour inscrire des services qui activent la réflexion.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="c6d8b-137">`MapGrpcReflectionService` pour ajouter un point de terminaison de service de réflexion.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="c6d8b-138">Quand la réflexion gRPC est configurée :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="c6d8b-139">Un service de réflexion gRPC est ajouté à l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="c6d8b-140">Les applications clientes qui prennent en charge la réflexion gRPC peuvent appeler le service de réflexion pour découvrir les services hébergés par le serveur.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="c6d8b-141">les services gRPC sont toujours appelés depuis le client.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="c6d8b-142">La réflexion active uniquement la détection de service et ne contourne pas la sécurité côté serveur.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="c6d8b-143">Les points de terminaison protégés par l' [authentification et l’autorisation](xref:grpc/authn-and-authz) requièrent que l’appelant transmette les informations d’identification pour que le point de terminaison soit appelé avec succès.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="c6d8b-144">Utilisez `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-144">Use `grpcurl`</span></span>

<span data-ttu-id="c6d8b-145">L' `-help` argument décrit les `grpcurl` options de ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="c6d8b-146">Découvrir les services</span><span class="sxs-lookup"><span data-stu-id="c6d8b-146">Discover services</span></span>

<span data-ttu-id="c6d8b-147">Utilisez le `describe` verbe pour afficher les services définis par le serveur :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-147">Use the `describe` verb to view the services defined by the server:</span></span>

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

<span data-ttu-id="c6d8b-148">L’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-148">The preceding example:</span></span>

* <span data-ttu-id="c6d8b-149">Exécute le `describe` verbe sur le serveur `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c6d8b-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="c6d8b-150">Imprime les services et les méthodes retournés par la réflexion gRPC.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="c6d8b-151">`Greeter` est un service implémenté par l’application.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="c6d8b-152">`ServerReflection` est le service ajouté par le `Grpc.AspNetCore.Server.Reflection` Package.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="c6d8b-153">Combiner `describe` avec un service, une méthode ou un nom de message pour afficher ses détails :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="c6d8b-154">Appeler gRPC services</span><span class="sxs-lookup"><span data-stu-id="c6d8b-154">Call gRPC services</span></span>

<span data-ttu-id="c6d8b-155">Appelez un service gRPC en spécifiant un nom de service et de méthode avec un argument JSON qui représente le message de demande.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="c6d8b-156">Le JSON est converti en Protobuf et envoyé au service.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="c6d8b-157">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-157">In the preceding example:</span></span>

* <span data-ttu-id="c6d8b-158">L' `-d` argument spécifie un message de demande avec JSON.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="c6d8b-159">Cet argument doit précéder l’adresse du serveur et le nom de la méthode.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="c6d8b-160">Appelle la `SayHello` méthode sur le `greeter.Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="c6d8b-161">Imprime le message de réponse au format JSON.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="c6d8b-162">À propos de gRPCui</span><span class="sxs-lookup"><span data-stu-id="c6d8b-162">About gRPCui</span></span>

<span data-ttu-id="c6d8b-163">gRPCui est une interface utilisateur Web interactive pour gRPC.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="c6d8b-164">Il s’appuie sur gRPCurl et offre une interface graphique utilisateur pour la découverte et le test des services gRPC, à l’instar des outils HTTP tels que l’interface utilisateur poster ou Swagger.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="c6d8b-165">Pour plus d’informations sur le téléchargement et l’installation de `grpcui` , consultez la [page d’accueil gRPCui GitHub](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="c6d8b-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="c6d8b-166">Utilisation de `grpcui`</span><span class="sxs-lookup"><span data-stu-id="c6d8b-166">Using `grpcui`</span></span>

<span data-ttu-id="c6d8b-167">Exécutez `grpcui` avec l’adresse de serveur pour interagir comme argument :</span><span class="sxs-lookup"><span data-stu-id="c6d8b-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="c6d8b-168">L’outil lance une fenêtre de navigateur avec l’interface utilisateur Web interactive.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="c6d8b-169">les services gRPC sont automatiquement découverts à l’aide de la réflexion gRPC.</span><span class="sxs-lookup"><span data-stu-id="c6d8b-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![interface utilisateur Web de gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="c6d8b-171">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c6d8b-171">Additional resources</span></span>

* [<span data-ttu-id="c6d8b-172">Page d’accueil gRPCurl GitHub</span><span class="sxs-lookup"><span data-stu-id="c6d8b-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="c6d8b-173">Page d’accueil gRPCui GitHub</span><span class="sxs-lookup"><span data-stu-id="c6d8b-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
