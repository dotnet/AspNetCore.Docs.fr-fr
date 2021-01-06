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
# <a name="code-first-grpc-services-and-clients-with-net"></a>Clients et services gRPC code First avec .NET

Par [James Newton-King](https://twitter.com/jamesnk) et [Marc gravier](https://twitter.com/marcgravell)

Code First gRPC utilise les types .NET pour définir les contrats de service et de message.

Code-First est un bon choix quand un système entier utilise .NET :

* Les types de contrat de données et de service .NET peuvent être partagés entre le serveur .NET et les clients.
* Évite de devoir définir des contrats dans les `.proto` fichiers et la génération de code.

Code-First n’est pas recommandé dans les systèmes polyglotte avec plusieurs langues. Les types de contrat de données et de service .NET ne peuvent pas être utilisés avec les plateformes non-.NET. Pour appeler un service gRPC écrit à l’aide de code-First, les autres plateformes doivent créer un `.proto` contrat qui correspond au service.

## <a name="protobuf-netgrpc"></a>protobuf-net. GRPC

> [!IMPORTANT]
> Pour obtenir de l’aide sur protobuf-net. GRPC, visitez le [protobuf-net. GRPC site Web](https://protobuf-net.github.io/protobuf-net.Grpc/) ou créez un problème sur le [protobuf-net. Référentiel GitHub GRPC](https://github.com/protobuf-net/protobuf-net.Grpc).

[protobuf-net. GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/) est un projet communautaire et n’est pas pris en charge par Microsoft. Elle ajoute la prise en charge du code First à `Grpc.AspNetCore` et `Grpc.Net.Client` . Il utilise des types .NET annotés avec des attributs pour définir les services et les messages gRPC d’une application.

La première étape de la création d’un service gRPC code First consiste à définir le contrat de code :

* Créez un projet qui sera partagé par le serveur et le client.
* Ajoutez un [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Référence du package GRPC.
* Créer des types de contrat de service et de données.

[!code-csharp[](code-first/Contracts.cs)]

Le code précédent :

* Définit `HelloRequest` les `HelloReply` messages et.
* Définit l' `IGreeterService` interface de contrat avec la `SayHelloAsync` méthode gRPC unaire.

Le contrat de service est implémenté sur le serveur et appelé à partir du client. Les méthodes définies sur les interfaces de service doivent correspondre à certaines signatures selon qu’elles sont unaires, la diffusion de serveur, la diffusion en continu client ou la diffusion bidirectionnelle.

Pour plus d’informations sur la définition des contrats de service, consultez le [protobuf-net. Documentation de prise](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)en main de GRPC.

## <a name="create-a-code-first-grpc-service"></a>Créer un service gRPC code First

Pour ajouter le service gRPC code-First à une application ASP.NET Core :

* Ajoutez un [protobuf-net. Référence du package GRPC. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) .
* Ajoutez une référence au projet de contrat de code partagé.

Créez un `GreeterService.cs` fichier et implémentez l' `IGreeterService` interface de service :

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

Mettez à jour le `Startup.cs` fichier :

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

Dans le code précédent :

* `AddCodeFirstGrpc` inscrit les services qui activent le code en premier.
* `MapGrpcService<GreeterService>` Ajoute le point de terminaison de service Code First.

les services gRPC implémentés avec code-First et `.proto` les fichiers peuvent coexister dans la même application. Tous les services gRPC utilisent la [configuration du service gRPC](xref:grpc/configuration#configure-services-options).

## <a name="create-a-code-first-grpc-client"></a>Créer un client gRPC de code First

Un client gRPC code First utilise le contrat de service pour appeler des services gRPC. Pour appeler un service gRPC à l’aide d’un client Code First :

* Ajoutez un [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Référence du package GRPC.
* Ajoutez une référence au projet de contrat de code partagé.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

Le code précédent :

* Crée un canal gRPC.
* Crée un client Code First à partir du canal avec la `CreateGrpcService<IGreeterService>` méthode d’extension.
* Appelle le service gRPC avec `SayHelloAsync` .

Un client gRPC de code First est créé à partir d’un canal. Tout comme un client standard, un client de code First utilise sa [configuration de canal](xref:grpc/configuration#configure-client-options).

## <a name="additional-resources"></a>Ressources supplémentaires

* [protobuf-net. Site Web GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net. Référentiel GitHub GRPC](https://github.com/protobuf-net/protobuf-net.Grpc)
