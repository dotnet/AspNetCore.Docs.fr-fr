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
# <a name="transient-fault-handling-with-grpc-retries"></a><span data-ttu-id="bb8e6-103">Gestion des erreurs temporaires avec les nouvelles tentatives gRPC</span><span class="sxs-lookup"><span data-stu-id="bb8e6-103">Transient fault handling with gRPC retries</span></span>

<span data-ttu-id="bb8e6-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="bb8e6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="bb8e6-105">gRPC nouvelles tentatives est une fonctionnalité qui permet aux clients gRPC de retenter automatiquement les appels ayant échoué.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-105">gRPC retries is a feature that allows gRPC clients to automatically retry failed calls.</span></span> <span data-ttu-id="bb8e6-106">Cet article explique comment configurer une stratégie de nouvelle tentative pour créer des applications gRPC résilientes et tolérantes aux pannes dans .NET.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-106">This article discusses how to configure a retry policy to make resilient, fault tolerant gRPC apps in .NET.</span></span>

<span data-ttu-id="bb8e6-107">gRPC nouvelles tentatives requièrent [gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0-PRE1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-107">gRPC retries requires [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0-pre1 or later.</span></span>

## <a name="transient-fault-handling"></a><span data-ttu-id="bb8e6-108">Gestion des erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="bb8e6-108">Transient fault handling</span></span>

<span data-ttu-id="bb8e6-109">les appels gRPC peuvent être interrompus par des erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-109">gRPC calls can be interrupted by transient faults.</span></span> <span data-ttu-id="bb8e6-110">Les erreurs temporaires sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="bb8e6-110">Transient faults include:</span></span>

* <span data-ttu-id="bb8e6-111">Perte momentanée de la connectivité réseau.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-111">Momentary loss of network connectivity.</span></span>
* <span data-ttu-id="bb8e6-112">Indisponibilité temporaire d’un service.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-112">Temporary unavailability of a service.</span></span>
* <span data-ttu-id="bb8e6-113">Délais d’attente dus à la charge du serveur.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-113">Timeouts due to server load.</span></span>

<span data-ttu-id="bb8e6-114">Lorsqu’un appel gRPC est interrompu, le client lève un `RpcException` avec des détails sur l’erreur.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-114">When a gRPC call is interrupted, the client throws an `RpcException` with details about the error.</span></span> <span data-ttu-id="bb8e6-115">L’application cliente doit intercepter l’exception et choisir comment gérer l’erreur.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-115">The client app must catch the exception and choose how to handle the error.</span></span>

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

<span data-ttu-id="bb8e6-116">La duplication de la logique de nouvelle tentative dans une application est détaillée et sujette aux erreurs.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-116">Duplicating retry logic throughout an app is verbose and error prone.</span></span> <span data-ttu-id="bb8e6-117">Heureusement, le client .NET gRPC dispose d’une prise en charge intégrée des nouvelles tentatives automatiques.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-117">Fortunately the .NET gRPC client has a built-in support for automatic retries.</span></span>

## <a name="configure-a-grpc-retry-policy"></a><span data-ttu-id="bb8e6-118">Configurer une stratégie de nouvelle tentative gRPC</span><span class="sxs-lookup"><span data-stu-id="bb8e6-118">Configure a gRPC retry policy</span></span>

<span data-ttu-id="bb8e6-119">Une stratégie de nouvelle tentative est configurée une seule fois lors de la création d’un canal gRPC :</span><span class="sxs-lookup"><span data-stu-id="bb8e6-119">A retry policy is configured once when a gRPC channel is created:</span></span>

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

<span data-ttu-id="bb8e6-120">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="bb8e6-120">The preceding code:</span></span>

* <span data-ttu-id="bb8e6-121">Crée un `MethodConfig`.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-121">Creates a `MethodConfig`.</span></span> <span data-ttu-id="bb8e6-122">Les stratégies de nouvelle tentative peuvent être configurées par méthode et les méthodes sont mises en correspondance à l’aide de la `Names` propriété.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-122">Retry policies can be configured per-method and methods are matched using the `Names` property.</span></span> <span data-ttu-id="bb8e6-123">Cette méthode étant configurée avec `MethodName.Default` , elle est appliquée à toutes les méthodes gRPC appelées par ce canal.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-123">This method is configured with `MethodName.Default`, so it's applied to all gRPC methods called by this channel.</span></span>
* <span data-ttu-id="bb8e6-124">Configure une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-124">Configures a retry policy.</span></span> <span data-ttu-id="bb8e6-125">Cette stratégie indique aux clients de retenter automatiquement les appels gRPC qui échouent avec le code d’état `Unavailable` .</span><span class="sxs-lookup"><span data-stu-id="bb8e6-125">This policy instructs clients to automatically retry gRPC calls that fail with the status code `Unavailable`.</span></span>
* <span data-ttu-id="bb8e6-126">Configure le canal créé pour utiliser la stratégie de nouvelle tentative en définissant `GrpcChannelOptions.ServiceConfig` .</span><span class="sxs-lookup"><span data-stu-id="bb8e6-126">Configures the created channel to use the retry policy by setting `GrpcChannelOptions.ServiceConfig`.</span></span>

<span data-ttu-id="bb8e6-127">les clients gRPC créés avec le canal renouvellent automatiquement les appels ayant échoué :</span><span class="sxs-lookup"><span data-stu-id="bb8e6-127">gRPC clients created with the channel will automatically retry failed calls:</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a><span data-ttu-id="bb8e6-128">options de nouvelle tentative gRPC</span><span class="sxs-lookup"><span data-stu-id="bb8e6-128">gRPC retry options</span></span>

<span data-ttu-id="bb8e6-129">Le tableau suivant décrit les options de configuration des stratégies de nouvelle tentative gRPC :</span><span class="sxs-lookup"><span data-stu-id="bb8e6-129">The following table describes options for configuring gRPC retry policies:</span></span>

| <span data-ttu-id="bb8e6-130">Option</span><span class="sxs-lookup"><span data-stu-id="bb8e6-130">Option</span></span> | <span data-ttu-id="bb8e6-131">Description</span><span class="sxs-lookup"><span data-stu-id="bb8e6-131">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="bb8e6-132">Nombre maximal de tentatives d’appel, y compris la tentative d’origine.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-132">The maximum number of call attempts, including the original attempt.</span></span> <span data-ttu-id="bb8e6-133">Cette valeur est limitée par `GrpcChannelOptions.MaxRetryAttempts` la valeur par défaut 5.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-133">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="bb8e6-134">Une valeur est obligatoire et doit être supérieure à 1.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-134">A value is required and must be greater than 1.</span></span> |
| `InitialBackoff` | <span data-ttu-id="bb8e6-135">Délai d’attente initial entre chaque tentative.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-135">The initial backoff delay between retry attempts.</span></span> <span data-ttu-id="bb8e6-136">Un délai aléatoire compris entre 0 et l’interruption actuelle détermine à quel moment la nouvelle tentative suivante est effectuée.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-136">A randomized delay between 0 and the current backoff determines when the next retry attempt is made.</span></span> <span data-ttu-id="bb8e6-137">Après chaque tentative, l’interruption actuelle est multipliée par `BackoffMultiplier` .</span><span class="sxs-lookup"><span data-stu-id="bb8e6-137">After each attempt, the current backoff is multiplied by `BackoffMultiplier`.</span></span> <span data-ttu-id="bb8e6-138">Une valeur est obligatoire et doit être supérieure à zéro.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-138">A value is required and must be greater than zero.</span></span> |
| `MaxBackoff` | <span data-ttu-id="bb8e6-139">Le nombre maximal d’interruptions augmente la limite supérieure sur la croissance exponentielle.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-139">The maximum backoff places an upper limit on exponential backoff growth.</span></span> <span data-ttu-id="bb8e6-140">Une valeur est obligatoire et doit être supérieure à zéro.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-140">A value is required and must be greater than zero.</span></span> |
| `BackoffMultiplier` | <span data-ttu-id="bb8e6-141">L’interruption est multipliée par cette valeur après chaque nouvelle tentative et augmente de façon exponentielle lorsque le multiplicateur est supérieur à 1.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-141">The backoff will be multiplied by this value after each retry attempt and will increase exponentially when the multiplier is greater than 1.</span></span> <span data-ttu-id="bb8e6-142">Une valeur est obligatoire et doit être supérieure à zéro.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-142">A value is required and must be greater than zero.</span></span> |
| `RetryableStatusCodes` | <span data-ttu-id="bb8e6-143">Collection de codes d’État.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-143">A collection of status codes.</span></span> <span data-ttu-id="bb8e6-144">Un appel gRPC qui échoue avec un état correspondant sera automatiquement retenté.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-144">A gRPC call that fails with a matching status will be automatically retried.</span></span> <span data-ttu-id="bb8e6-145">Pour plus d’informations sur les codes d’État, consultez [codes d’État et leur utilisation dans gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span><span class="sxs-lookup"><span data-stu-id="bb8e6-145">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> <span data-ttu-id="bb8e6-146">Au moins un code d’État renouvelable est requis.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-146">At least one retryable status code is required.</span></span> |

## <a name="hedging"></a><span data-ttu-id="bb8e6-147">Couverture</span><span class="sxs-lookup"><span data-stu-id="bb8e6-147">Hedging</span></span>

<span data-ttu-id="bb8e6-148">La couverture est une stratégie de nouvelle tentative alternative.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-148">Hedging is an alternative retry strategy.</span></span> <span data-ttu-id="bb8e6-149">La couverture permet d’envoyer de manière agressive plusieurs copies d’un seul appel gRPC sans attendre une réponse.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-149">Hedging enables aggressively sending multiple copies of a single gRPC call without waiting for a response.</span></span> <span data-ttu-id="bb8e6-150">Les appels gRPC de couverture peuvent être exécutés plusieurs fois sur le serveur et le premier résultat réussi est utilisé.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-150">Hedged gRPC calls may be executed multiple times on the server and the first successful result is used.</span></span> <span data-ttu-id="bb8e6-151">Il est important que la couverture soit activée uniquement pour les méthodes qui peuvent être exécutées plusieurs fois sans effet négatif.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-151">It's important that hedging is only enabled for methods that are safe to execute multiple times without adverse effect.</span></span>

<span data-ttu-id="bb8e6-152">La couverture présente des avantages et des inconvénients par rapport aux nouvelles tentatives :</span><span class="sxs-lookup"><span data-stu-id="bb8e6-152">Hedging has pros and cons when compared to retries:</span></span> 

* <span data-ttu-id="bb8e6-153">L’un des avantages de la couverture est qu’elle peut retourner un résultat positif plus rapidement.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-153">An advantage to hedging is it might return a successful result faster.</span></span> <span data-ttu-id="bb8e6-154">Il autorise plusieurs appels gRPC simultanément et se termine lorsque le premier résultat réussi est disponible.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-154">It allows for multiple simultaneously gRPC calls and will complete when the first successful result is available.</span></span> 
* <span data-ttu-id="bb8e6-155">L’inconvénient de la couverture est qu’il peut s’agir d’un gaspillage.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-155">A disadvantage to hedging is it can be wasteful.</span></span> <span data-ttu-id="bb8e6-156">Plusieurs appels peuvent être effectués et tous ont échoué.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-156">Multiple calls could be made and all succeed.</span></span> <span data-ttu-id="bb8e6-157">Seul le premier résultat est utilisé et le reste est ignoré.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-157">Only the first result is used and the rest are discarded.</span></span>

## <a name="configure-a-grpc-hedging-policy"></a><span data-ttu-id="bb8e6-158">Configurer une stratégie de couverture gRPC</span><span class="sxs-lookup"><span data-stu-id="bb8e6-158">Configure a gRPC hedging policy</span></span>

<span data-ttu-id="bb8e6-159">Une stratégie de couverture est configurée comme une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-159">A hedging policy is configured like a retry policy.</span></span> <span data-ttu-id="bb8e6-160">Notez qu’une stratégie de couverture ne peut pas être combinée avec une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-160">Note that a hedging policy can't be combined with a retry policy.</span></span>

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

### <a name="grpc-hedging-options"></a><span data-ttu-id="bb8e6-161">options de couverture gRPC</span><span class="sxs-lookup"><span data-stu-id="bb8e6-161">gRPC hedging options</span></span>

<span data-ttu-id="bb8e6-162">Le tableau suivant décrit les options de configuration des stratégies de couverture de gRPC :</span><span class="sxs-lookup"><span data-stu-id="bb8e6-162">The following table describes options for configuring gRPC hedging policies:</span></span>

| <span data-ttu-id="bb8e6-163">Option</span><span class="sxs-lookup"><span data-stu-id="bb8e6-163">Option</span></span> | <span data-ttu-id="bb8e6-164">Description</span><span class="sxs-lookup"><span data-stu-id="bb8e6-164">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="bb8e6-165">La stratégie de couverture enverra ce nombre d’appels.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-165">The hedging policy will send up to this number of calls.</span></span> <span data-ttu-id="bb8e6-166">`MaxAttempts` représente le nombre total de tentatives, y compris la tentative d’origine.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-166">`MaxAttempts` represents the total number of all attempts, including the original attempt.</span></span> <span data-ttu-id="bb8e6-167">Cette valeur est limitée par `GrpcChannelOptions.MaxRetryAttempts` la valeur par défaut 5.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-167">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="bb8e6-168">Une valeur est obligatoire et doit être supérieure ou égale à 2.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-168">A value is required and must be 2 or greater.</span></span> |
| `HedgingDelay` | <span data-ttu-id="bb8e6-169">Le premier appel sera envoyé immédiatement, mais les appels de couverture suivants seront retardés par cette valeur.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-169">The first call will be sent immediately, but the subsequent hedging calls will be delayed by this value.</span></span> <span data-ttu-id="bb8e6-170">Lorsque le délai est défini sur zéro ou `null` , tous les appels de couverture sont envoyés immédiatement.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-170">When the delay is set to zero or `null`, all hedged calls are sent immediately.</span></span> <span data-ttu-id="bb8e6-171">La valeur par défaut est zéro.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-171">Default value is zero.</span></span> |
| `NonFatalStatusCodes` | <span data-ttu-id="bb8e6-172">Une collection de codes d’État qui indiquent d’autres appels de haies peut encore être établie.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-172">A collection of status codes which indicate other hedge calls may still succeed.</span></span> <span data-ttu-id="bb8e6-173">Si un code d’État récupérable est retourné par le serveur, les appels de couverture se poursuivront.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-173">If a non-fatal status code is returned by the server, hedged calls will continue.</span></span> <span data-ttu-id="bb8e6-174">Dans le cas contraire, les demandes en suspens seront annulées et l’erreur retournée à l’application.</span><span class="sxs-lookup"><span data-stu-id="bb8e6-174">Otherwise, outstanding requests will be canceled and the error returned to the app.</span></span> <span data-ttu-id="bb8e6-175">Pour plus d’informations sur les codes d’État, consultez [codes d’État et leur utilisation dans gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span><span class="sxs-lookup"><span data-stu-id="bb8e6-175">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="bb8e6-176">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="bb8e6-176">Additional resources</span></span>

* <xref:grpc/client>
* [<span data-ttu-id="bb8e6-177">Conseils généraux sur les nouvelles tentatives-meilleures pratiques pour les applications Cloud</span><span class="sxs-lookup"><span data-stu-id="bb8e6-177">Retry general guidance - Best practices for cloud applications</span></span>](/azure/architecture/best-practices/transient-faults)
