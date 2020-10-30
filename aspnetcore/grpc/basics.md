---
title: Services gRPC avec C#
author: juntaoluo
description: Découvrez les concepts de base liés à l’écriture de services gRPC avec C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 4968ac889cd3b4e0780ce73dc729d0107a416932
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061013"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="8c6ce-103">services gRPC avec C\#</span><span class="sxs-lookup"><span data-stu-id="8c6ce-103">gRPC services with C\#</span></span>

<span data-ttu-id="8c6ce-104">Ce document décrit les concepts nécessaires pour écrire des applications [gRPC](https://grpc.io/docs/guides/) en C#.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="8c6ce-105">Les rubriques traitées ici s’appliquent à la fois aux applications gRPC basées sur le [noyau C](https://grpc.io/blog/grpc-stacks)et à l’ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="8c6ce-106">fichier proto</span><span class="sxs-lookup"><span data-stu-id="8c6ce-106">proto file</span></span>

<span data-ttu-id="8c6ce-107">gRPC utilise une approche contrat d’abord pour le développement d’API.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="8c6ce-108">Les mémoires tampons de protocole (protobuf) sont utilisées par défaut en tant que langage IDL (Interface Definition Language).</span><span class="sxs-lookup"><span data-stu-id="8c6ce-108">Protocol buffers (protobuf) are used as the Interface Definition Language (IDL) by default.</span></span> <span data-ttu-id="8c6ce-109">Le fichier *\* . proto* contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="8c6ce-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="8c6ce-110">Définition du service gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="8c6ce-111">Messages envoyés entre les clients et les serveurs.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="8c6ce-112">Pour plus d’informations sur la syntaxe des fichiers protobuf, consultez <xref:grpc/protobuf> .</span><span class="sxs-lookup"><span data-stu-id="8c6ce-112">For more information on the syntax of protobuf files, see <xref:grpc/protobuf>.</span></span>

<span data-ttu-id="8c6ce-113">Par exemple, considérez le fichier *Greeter. proto* utilisé dans [prise en main du service gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="8c6ce-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="8c6ce-114">Définit un `Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="8c6ce-115">Le `Greeter` service définit un `SayHello` appel.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="8c6ce-116">`SayHello` envoie un `HelloRequest` message et reçoit un `HelloReply` message :</span><span class="sxs-lookup"><span data-stu-id="8c6ce-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="8c6ce-117">Ajouter un fichier. proto à une \# application C</span><span class="sxs-lookup"><span data-stu-id="8c6ce-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="8c6ce-118">Le fichier *\* . proto* est inclus dans un projet en l’ajoutant au `<Protobuf>` groupe d’éléments :</span><span class="sxs-lookup"><span data-stu-id="8c6ce-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="8c6ce-119">Par défaut, une `<Protobuf>` référence génère un client concret et une classe de base de service.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-119">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="8c6ce-120">L’attribut de l’élément de référence `GrpcServices` peut être utilisé pour limiter la génération d’éléments C#.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-120">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="8c6ce-121">`GrpcServices`Les options valides sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="8c6ce-121">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="8c6ce-122">`Both` (valeur par défaut absente)</span><span class="sxs-lookup"><span data-stu-id="8c6ce-122">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="8c6ce-123">Prise en charge des outils C# pour les fichiers. proto</span><span class="sxs-lookup"><span data-stu-id="8c6ce-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="8c6ce-124">Le package d’outils [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) est requis pour générer les ressources C# à partir des fichiers *\* . proto* .</span><span class="sxs-lookup"><span data-stu-id="8c6ce-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="8c6ce-125">Ressources générées (fichiers) :</span><span class="sxs-lookup"><span data-stu-id="8c6ce-125">The generated assets (files):</span></span>

* <span data-ttu-id="8c6ce-126">Sont générés en fonction des besoins à chaque fois que le projet est généré.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="8c6ce-127">Ne sont pas ajoutés au projet ou archivés dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="8c6ce-128">Est un artefact de build contenu dans le répertoire *obj* .</span><span class="sxs-lookup"><span data-stu-id="8c6ce-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="8c6ce-129">Ce package est requis par les projets serveur et client.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="8c6ce-130">Le `Grpc.AspNetCore` repackage contient une référence à `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="8c6ce-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="8c6ce-131">Les projets serveur peuvent être ajoutés `Grpc.AspNetCore` à l’aide du gestionnaire de package dans Visual Studio ou en ajoutant un `<PackageReference>` au fichier projet :</span><span class="sxs-lookup"><span data-stu-id="8c6ce-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="8c6ce-132">Les projets clients doivent faire directement référence `Grpc.Tools` à côté des autres packages requis pour utiliser le client gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="8c6ce-133">Le package d’outils n’est pas requis lors de l’exécution, donc la dépendance est marquée avec `PrivateAssets="All"` :</span><span class="sxs-lookup"><span data-stu-id="8c6ce-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="8c6ce-134">Ressources C# générées</span><span class="sxs-lookup"><span data-stu-id="8c6ce-134">Generated C# assets</span></span>

<span data-ttu-id="8c6ce-135">Le package d’outils génère les types C# représentant les messages définis dans les fichiers *\* . proto* inclus.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="8c6ce-136">Pour les ressources côté serveur, un type de base de service abstrait est généré.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="8c6ce-137">Le type de base contient les définitions de tous les appels gRPC contenus dans le fichier *. proto* .</span><span class="sxs-lookup"><span data-stu-id="8c6ce-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="8c6ce-138">Créez une implémentation de service concrète qui dérive de ce type de base et implémente la logique pour les appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="8c6ce-139">Pour le `greet.proto` , l’exemple décrit précédemment, un `GreeterBase` type abstrait qui contient une `SayHello` méthode virtuelle est généré.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="8c6ce-140">Une implémentation concrète `GreeterService` remplace la méthode et implémente la logique qui gère l’appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="8c6ce-141">Pour les ressources côté client, un type de client concret est généré.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="8c6ce-142">Les appels gRPC dans le fichier *. proto* sont traduits en méthodes sur le type concret, qui peuvent être appelées.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="8c6ce-143">Pour le `greet.proto` , l’exemple décrit précédemment, un `GreeterClient` type concret est généré.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="8c6ce-144">Appelez `GreeterClient.SayHelloAsync` pour lancer un appel gRPC sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="8c6ce-145">Par défaut, les ressources serveur et client sont générées pour chaque fichier *\* . proto* inclus dans le `<Protobuf>` groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="8c6ce-146">Pour garantir que seules les ressources du serveur sont générées dans un projet serveur, l' `GrpcServices` attribut a la valeur `Server` .</span><span class="sxs-lookup"><span data-stu-id="8c6ce-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="8c6ce-147">De même, l’attribut a la valeur `Client` dans les projets clients.</span><span class="sxs-lookup"><span data-stu-id="8c6ce-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c6ce-148">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8c6ce-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
