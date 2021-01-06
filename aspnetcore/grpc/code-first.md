---
title: Clients et services gRPC code First avec .NET
author: jamesnk
description: Découvrez les concepts de base lors de l’écriture de code-First gRPC avec .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880634"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="6cafd-103">Clients et services gRPC code First avec .NET</span><span class="sxs-lookup"><span data-stu-id="6cafd-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="6cafd-104">Par [James Newton-King](https://twitter.com/jamesnk) et [Marc gravier](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="6cafd-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="6cafd-105">Code First gRPC utilise les types .NET pour définir les contrats de service et de message.</span><span class="sxs-lookup"><span data-stu-id="6cafd-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="6cafd-106">Code-First est un bon choix quand un système entier utilise .NET :</span><span class="sxs-lookup"><span data-stu-id="6cafd-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="6cafd-107">Les types de contrat de données et de service .NET peuvent être partagés entre le serveur .NET et les clients.</span><span class="sxs-lookup"><span data-stu-id="6cafd-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="6cafd-108">Évite de devoir définir des contrats dans les `.proto` fichiers et la génération de code.</span><span class="sxs-lookup"><span data-stu-id="6cafd-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="6cafd-109">Code-First n’est pas recommandé dans les systèmes polyglotte avec plusieurs langues.</span><span class="sxs-lookup"><span data-stu-id="6cafd-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="6cafd-110">Les types de contrat de données et de service .NET ne peuvent pas être utilisés avec les plateformes non-.NET.</span><span class="sxs-lookup"><span data-stu-id="6cafd-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="6cafd-111">Pour appeler un service gRPC écrit à l’aide de code-First, les autres plateformes doivent créer un `.proto` contrat qui correspond au service.</span><span class="sxs-lookup"><span data-stu-id="6cafd-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="6cafd-112">protobuf-net. GRPC</span><span class="sxs-lookup"><span data-stu-id="6cafd-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6cafd-113">Pour obtenir de l’aide sur protobuf-net. GRPC, visitez le [protobuf-net. GRPC site Web](https://protobuf-net.github.io/protobuf-net.Grpc/) ou créez un problème sur le [protobuf-net. Référentiel GitHub GRPC](https://github.com/protobuf-net/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="6cafd-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="6cafd-114">[protobuf-net. GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/) est un projet communautaire et n’est pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6cafd-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="6cafd-115">Elle ajoute la prise en charge du code First à `Grpc.AspNetCore` et `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="6cafd-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="6cafd-116">Il utilise des types .NET annotés avec des attributs pour définir les services et les messages gRPC d’une application.</span><span class="sxs-lookup"><span data-stu-id="6cafd-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="6cafd-117">La première étape de la création d’un service gRPC code First consiste à définir le contrat de code :</span><span class="sxs-lookup"><span data-stu-id="6cafd-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="6cafd-118">Créez un projet qui sera partagé par le serveur et le client.</span><span class="sxs-lookup"><span data-stu-id="6cafd-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="6cafd-119">Ajoutez un [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Référence du package GRPC.</span><span class="sxs-lookup"><span data-stu-id="6cafd-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="6cafd-120">Créer des types de contrat de service et de données.</span><span class="sxs-lookup"><span data-stu-id="6cafd-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="6cafd-121">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="6cafd-121">The preceding code:</span></span>

* <span data-ttu-id="6cafd-122">Définit `HelloRequest` les `HelloReply` messages et.</span><span class="sxs-lookup"><span data-stu-id="6cafd-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="6cafd-123">Définit l' `IGreeterService` interface de contrat avec la `SayHelloAsync` méthode gRPC unaire.</span><span class="sxs-lookup"><span data-stu-id="6cafd-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="6cafd-124">Le contrat de service est implémenté sur le serveur et appelé à partir du client.</span><span class="sxs-lookup"><span data-stu-id="6cafd-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="6cafd-125">Les méthodes définies sur les interfaces de service doivent correspondre à certaines signatures selon qu’elles sont unaires, la diffusion de serveur, la diffusion en continu client ou la diffusion bidirectionnelle.</span><span class="sxs-lookup"><span data-stu-id="6cafd-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="6cafd-126">Pour plus d’informations sur la définition des contrats de service, consultez le [protobuf-net. Documentation de prise](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)en main de GRPC.</span><span class="sxs-lookup"><span data-stu-id="6cafd-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="6cafd-127">Créer un service gRPC code First</span><span class="sxs-lookup"><span data-stu-id="6cafd-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="6cafd-128">Pour ajouter le service gRPC code-First à une application ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="6cafd-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="6cafd-129">Ajoutez un [protobuf-net. Référence du package GRPC. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="6cafd-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="6cafd-130">Ajoutez une référence au projet de contrat de code partagé.</span><span class="sxs-lookup"><span data-stu-id="6cafd-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="6cafd-131">Créez un `GreeterService.cs` fichier et implémentez l' `IGreeterService` interface de service :</span><span class="sxs-lookup"><span data-stu-id="6cafd-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="6cafd-132">Mettez à jour le `Startup.cs` fichier :</span><span class="sxs-lookup"><span data-stu-id="6cafd-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="6cafd-133">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="6cafd-133">In the preceding code:</span></span>

* <span data-ttu-id="6cafd-134">`AddCodeFirstGrpc` inscrit les services qui activent le code en premier.</span><span class="sxs-lookup"><span data-stu-id="6cafd-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="6cafd-135">`MapGrpcService<GreeterService>` Ajoute le point de terminaison de service Code First.</span><span class="sxs-lookup"><span data-stu-id="6cafd-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="6cafd-136">les services gRPC implémentés avec code-First et `.proto` les fichiers peuvent coexister dans la même application.</span><span class="sxs-lookup"><span data-stu-id="6cafd-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="6cafd-137">Tous les services gRPC utilisent la [configuration du service gRPC](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="6cafd-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="6cafd-138">Créer un client gRPC de code First</span><span class="sxs-lookup"><span data-stu-id="6cafd-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="6cafd-139">Un client gRPC code First utilise le contrat de service pour appeler des services gRPC.</span><span class="sxs-lookup"><span data-stu-id="6cafd-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="6cafd-140">Pour appeler un service gRPC à l’aide d’un client Code First :</span><span class="sxs-lookup"><span data-stu-id="6cafd-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="6cafd-141">Ajoutez un [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Référence du package GRPC.</span><span class="sxs-lookup"><span data-stu-id="6cafd-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="6cafd-142">Ajoutez une référence au projet de contrat de code partagé.</span><span class="sxs-lookup"><span data-stu-id="6cafd-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="6cafd-143">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="6cafd-143">The preceding code:</span></span>

* <span data-ttu-id="6cafd-144">Crée un canal gRPC.</span><span class="sxs-lookup"><span data-stu-id="6cafd-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="6cafd-145">Crée un client Code First à partir du canal avec la `CreateGrpcService<IGreeterService>` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="6cafd-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="6cafd-146">Appelle le service gRPC avec `SayHelloAsync` .</span><span class="sxs-lookup"><span data-stu-id="6cafd-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="6cafd-147">Un client gRPC de code First est créé à partir d’un canal.</span><span class="sxs-lookup"><span data-stu-id="6cafd-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="6cafd-148">Tout comme un client standard, un client de code First utilise sa [configuration de canal](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="6cafd-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6cafd-149">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6cafd-149">Additional resources</span></span>

* [<span data-ttu-id="6cafd-150">protobuf-net. Site Web GRPC</span><span class="sxs-lookup"><span data-stu-id="6cafd-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="6cafd-151">protobuf-net. Référentiel GitHub GRPC</span><span class="sxs-lookup"><span data-stu-id="6cafd-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
