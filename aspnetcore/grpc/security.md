---
title: Considérations sur la sécurité dans gRPC pour ASP.NET Core
author: jamesnk
description: En savoir plus sur les considérations de sécurité pour gRPC pour ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: 4a70cb16d8397dbc69a626435fb72a0512788b14
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308752"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="85816-103">Considérations sur la sécurité dans gRPC pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85816-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="85816-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="85816-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="85816-105">Cet article fournit des informations sur la sécurisation de gRPC avec .NET Core.</span><span class="sxs-lookup"><span data-stu-id="85816-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="85816-106">Sécurité de transport</span><span class="sxs-lookup"><span data-stu-id="85816-106">Transport security</span></span>

<span data-ttu-id="85816-107">les messages gRPC sont envoyés et reçus à l’aide de HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="85816-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="85816-108">Nous vous recommandons d’utiliser les éléments suivants:</span><span class="sxs-lookup"><span data-stu-id="85816-108">We recommend:</span></span>

* <span data-ttu-id="85816-109">[TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) permet de sécuriser les messages dans les applications gRPC de production.</span><span class="sxs-lookup"><span data-stu-id="85816-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="85816-110">les services gRPC doivent uniquement écouter les ports sécurisés et y répondre.</span><span class="sxs-lookup"><span data-stu-id="85816-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="85816-111">TLS est configuré dans Kestrel.</span><span class="sxs-lookup"><span data-stu-id="85816-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="85816-112">Pour plus d’informations sur la configuration des points de terminaison Kestrel, consultez [configuration du point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="85816-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="85816-113">Exceptions</span><span class="sxs-lookup"><span data-stu-id="85816-113">Exceptions</span></span>

<span data-ttu-id="85816-114">Les messages d’exception sont généralement considérés comme des données sensibles qui ne doivent pas être révélées à un client.</span><span class="sxs-lookup"><span data-stu-id="85816-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="85816-115">Par défaut, gRPC n’envoie pas les détails d’une exception levée par un service gRPC au client.</span><span class="sxs-lookup"><span data-stu-id="85816-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="85816-116">Au lieu de cela, le client reçoit un message générique indiquant qu’une erreur s’est produite.</span><span class="sxs-lookup"><span data-stu-id="85816-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="85816-117">La remise de message d’exception au client peut être remplacée (par exemple, dans le développement ou le test) par [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="85816-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="85816-118">Les messages d’exception ne doivent pas être exposés au client dans les applications de production.</span><span class="sxs-lookup"><span data-stu-id="85816-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="85816-119">Limites de taille des messages</span><span class="sxs-lookup"><span data-stu-id="85816-119">Message size limits</span></span>

<span data-ttu-id="85816-120">Les messages entrants aux clients et services gRPC sont chargés en mémoire.</span><span class="sxs-lookup"><span data-stu-id="85816-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="85816-121">Les limites de taille des messages sont un mécanisme qui permet d’empêcher gRPC de consommer trop de ressources.</span><span class="sxs-lookup"><span data-stu-id="85816-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="85816-122">gRPC utilise des limites de taille par message pour gérer les messages entrants et sortants.</span><span class="sxs-lookup"><span data-stu-id="85816-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="85816-123">Par défaut, gRPC limite les messages entrants à 4 Mo.</span><span class="sxs-lookup"><span data-stu-id="85816-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="85816-124">Il n’existe aucune limite pour les messages sortants.</span><span class="sxs-lookup"><span data-stu-id="85816-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="85816-125">Sur le serveur, les limites de messages gRPC peuvent être configurées pour tous les services `AddGrpc`d’une application avec:</span><span class="sxs-lookup"><span data-stu-id="85816-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 1 * 1024 * 1024;  // 1 megabyte
        options.SendMaxMessageSize = 1 * 1024 * 1024;     // 1 megabyte
    });
}
```

<span data-ttu-id="85816-126">Les limites peuvent également être configurées pour un service `AddServiceOptions<TService>`individuel à l’aide de.</span><span class="sxs-lookup"><span data-stu-id="85816-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="85816-127">Pour plus d’informations sur la configuration des limites de taille des messages, consultez [configuration de gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="85816-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="85816-128">Validation du certificat client</span><span class="sxs-lookup"><span data-stu-id="85816-128">Client certificate validation</span></span>

<span data-ttu-id="85816-129">Les [certificats clients](https://tools.ietf.org/html/rfc5246#section-7.4.4) sont initialement validés lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="85816-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="85816-130">Par défaut, Kestrel n’effectue pas de validation supplémentaire du certificat client d’une connexion.</span><span class="sxs-lookup"><span data-stu-id="85816-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="85816-131">Nous recommandons que les services gRPC sécurisés par les certificats clients utilisent le package [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) .</span><span class="sxs-lookup"><span data-stu-id="85816-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="85816-132">ASP.NET Core l’authentification de certification effectue une validation supplémentaire sur un certificat client, notamment:</span><span class="sxs-lookup"><span data-stu-id="85816-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="85816-133">Le certificat a une utilisation avancée de la clé (EKU) valide</span><span class="sxs-lookup"><span data-stu-id="85816-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="85816-134">Est dans sa période de validité</span><span class="sxs-lookup"><span data-stu-id="85816-134">Is within its validity period</span></span>
* <span data-ttu-id="85816-135">Vérifier la révocation des certificats</span><span class="sxs-lookup"><span data-stu-id="85816-135">Check certificate revocation</span></span>