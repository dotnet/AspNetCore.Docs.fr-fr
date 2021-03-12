---
title: Gestion des erreurs temporaires avec les nouvelles tentatives gRPC
author: jamesnk
description: Découvrez comment effectuer des appels gRPC résilients et tolérants aux pannes avec les nouvelles tentatives dans .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/25/2021
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
uid: grpc/retries
ms.openlocfilehash: 613386d1fedd8b1b04081e3240b8a3aaf7b37012
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589803"
---
# <a name="transient-fault-handling-with-grpc-retries"></a>Gestion des erreurs temporaires avec les nouvelles tentatives gRPC

Par [James Newton-King](https://twitter.com/jamesnk)

gRPC nouvelles tentatives est une fonctionnalité qui permet aux clients gRPC de retenter automatiquement les appels ayant échoué. Cet article explique comment configurer une stratégie de nouvelle tentative pour créer des applications gRPC résilientes et tolérantes aux pannes dans .NET.

gRPC nouvelles tentatives requièrent [gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0-PRE1 ou version ultérieure.

## <a name="transient-fault-handling"></a>Gestion des erreurs temporaires

les appels gRPC peuvent être interrompus par des erreurs temporaires. Les erreurs temporaires sont les suivantes :

* Perte momentanée de la connectivité réseau.
* Indisponibilité temporaire d’un service.
* Délais d’attente dus à la charge du serveur.

Lorsqu’un appel gRPC est interrompu, le client lève un `RpcException` avec des détails sur l’erreur. L’application cliente doit intercepter l’exception et choisir comment gérer l’erreur.

```csharp
var client = new Greeter.GreeterClient(channel);
try
{
    var response = await client.SayHelloAsync(
        new HelloRequest { Name = ".NET" });

    Console.WriteLine("From server: " + response.Message);
}
catch (RpcException ex)
{
    // Write logic to inspect the error and retry
    // if the error is from a transient fault.
}
```

La duplication de la logique de nouvelle tentative dans une application est détaillée et sujette aux erreurs. Heureusement, le client .NET gRPC dispose d’une prise en charge intégrée des nouvelles tentatives automatiques.

## <a name="configure-a-grpc-retry-policy"></a>Configurer une stratégie de nouvelle tentative gRPC

Une stratégie de nouvelle tentative est configurée une seule fois lors de la création d’un canal gRPC :

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    RetryPolicy = new RetryPolicy
    {
        MaxAttempts = 5,
        InitialBackoff = TimeSpan.FromSeconds(1),
        MaxBackoff = TimeSpan.FromSeconds(5),
        BackoffMultiplier = 1.5,
        RetryableStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

Le code précédent :

* Crée un `MethodConfig`. Les stratégies de nouvelle tentative peuvent être configurées par méthode et les méthodes sont mises en correspondance à l’aide de la `Names` propriété. Cette méthode étant configurée avec `MethodName.Default` , elle est appliquée à toutes les méthodes gRPC appelées par ce canal.
* Configure une stratégie de nouvelle tentative. Cette stratégie indique aux clients de retenter automatiquement les appels gRPC qui échouent avec le code d’état `Unavailable` .
* Configure le canal créé pour utiliser la stratégie de nouvelle tentative en définissant `GrpcChannelOptions.ServiceConfig` .

les clients gRPC créés avec le canal renouvellent automatiquement les appels ayant échoué :

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a>options de nouvelle tentative gRPC

Le tableau suivant décrit les options de configuration des stratégies de nouvelle tentative gRPC :

| Option | Description |
| ------ | ----------- |
| `MaxAttempts` | Nombre maximal de tentatives d’appel, y compris la tentative d’origine. Cette valeur est limitée par `GrpcChannelOptions.MaxRetryAttempts` la valeur par défaut 5. Une valeur est obligatoire et doit être supérieure à 1. |
| `InitialBackoff` | Délai d’attente initial entre chaque tentative. Un délai aléatoire compris entre 0 et l’interruption actuelle détermine à quel moment la nouvelle tentative suivante est effectuée. Après chaque tentative, l’interruption actuelle est multipliée par `BackoffMultiplier` . Une valeur est obligatoire et doit être supérieure à zéro. |
| `MaxBackoff` | Le nombre maximal d’interruptions augmente la limite supérieure sur la croissance exponentielle. Une valeur est obligatoire et doit être supérieure à zéro. |
| `BackoffMultiplier` | L’interruption est multipliée par cette valeur après chaque nouvelle tentative et augmente de façon exponentielle lorsque le multiplicateur est supérieur à 1. Une valeur est obligatoire et doit être supérieure à zéro. |
| `RetryableStatusCodes` | Collection de codes d’État. Un appel gRPC qui échoue avec un état correspondant sera automatiquement retenté. Pour plus d’informations sur les codes d’État, consultez [codes d’État et leur utilisation dans gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html). Au moins un code d’État renouvelable est requis. |

## <a name="hedging"></a>Couverture

La couverture est une stratégie de nouvelle tentative alternative. La couverture permet d’envoyer de manière agressive plusieurs copies d’un seul appel gRPC sans attendre une réponse. Les appels gRPC de couverture peuvent être exécutés plusieurs fois sur le serveur et le premier résultat réussi est utilisé. Il est important que la couverture soit activée uniquement pour les méthodes qui peuvent être exécutées plusieurs fois sans effet négatif.

La couverture présente des avantages et des inconvénients par rapport aux nouvelles tentatives : 

* L’un des avantages de la couverture est qu’elle peut retourner un résultat positif plus rapidement. Il autorise plusieurs appels gRPC simultanément et se termine lorsque le premier résultat réussi est disponible. 
* L’inconvénient de la couverture est qu’il peut s’agir d’un gaspillage. Plusieurs appels peuvent être effectués et tous ont échoué. Seul le premier résultat est utilisé et le reste est ignoré.

## <a name="configure-a-grpc-hedging-policy"></a>Configurer une stratégie de couverture gRPC

Une stratégie de couverture est configurée comme une stratégie de nouvelle tentative. Notez qu’une stratégie de couverture ne peut pas être combinée avec une stratégie de nouvelle tentative.

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    HedgingPolicy = new HedgingPolicy
    {
        MaxAttempts = 5,
        NonFatalStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

### <a name="grpc-hedging-options"></a>options de couverture gRPC

Le tableau suivant décrit les options de configuration des stratégies de couverture de gRPC :

| Option | Description |
| ------ | ----------- |
| `MaxAttempts` | La stratégie de couverture enverra ce nombre d’appels. `MaxAttempts` représente le nombre total de tentatives, y compris la tentative d’origine. Cette valeur est limitée par `GrpcChannelOptions.MaxRetryAttempts` la valeur par défaut 5. Une valeur est obligatoire et doit être supérieure ou égale à 2. |
| `HedgingDelay` | Le premier appel sera envoyé immédiatement, mais les appels de couverture suivants seront retardés par cette valeur. Lorsque le délai est défini sur zéro ou `null` , tous les appels de couverture sont envoyés immédiatement. La valeur par défaut est zéro. |
| `NonFatalStatusCodes` | Une collection de codes d’État qui indiquent d’autres appels de haies peut encore être établie. Si un code d’État récupérable est retourné par le serveur, les appels de couverture se poursuivront. Dans le cas contraire, les demandes en suspens seront annulées et l’erreur retournée à l’application. Pour plus d’informations sur les codes d’État, consultez [codes d’État et leur utilisation dans gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html). |

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/client>
* [Conseils généraux sur les nouvelles tentatives-meilleures pratiques pour les applications Cloud](/azure/architecture/best-practices/transient-faults)
