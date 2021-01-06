---
title: Services gRPC fiables avec échéances et annulation
author: jamesnk
description: Découvrez comment créer des services gRPC fiables avec des échéances et des annulations dans .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059921"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="4e819-103">Services gRPC fiables avec échéances et annulation</span><span class="sxs-lookup"><span data-stu-id="4e819-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="4e819-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="4e819-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4e819-105">Les échéances et l’annulation sont des fonctionnalités utilisées par les clients gRPC pour abandonner les appels en cours.</span><span class="sxs-lookup"><span data-stu-id="4e819-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="4e819-106">Cet article explique pourquoi les échéances et l’annulation sont importantes et comment les utiliser dans les applications .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e819-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="4e819-107">Échéances</span><span class="sxs-lookup"><span data-stu-id="4e819-107">Deadlines</span></span>

<span data-ttu-id="4e819-108">Une échéance permet à un client gRPC de spécifier la durée pendant laquelle il doit attendre la fin d’un appel.</span><span class="sxs-lookup"><span data-stu-id="4e819-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="4e819-109">Lorsqu’une échéance est dépassée, l’appel est annulé.</span><span class="sxs-lookup"><span data-stu-id="4e819-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="4e819-110">La définition d’une échéance est importante, car elle fournit une limite supérieure de la durée pendant laquelle un appel peut s’exécuter.</span><span class="sxs-lookup"><span data-stu-id="4e819-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="4e819-111">Il empêche les services incorrects de s’exécuter de façon permanente et d’épuiser les ressources du serveur.</span><span class="sxs-lookup"><span data-stu-id="4e819-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="4e819-112">Les échéances sont un outil utile pour la création d’applications fiables et doivent être configurées.</span><span class="sxs-lookup"><span data-stu-id="4e819-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="4e819-113">Configuration de l’échéance :</span><span class="sxs-lookup"><span data-stu-id="4e819-113">Deadline configuration:</span></span>

* <span data-ttu-id="4e819-114">Une échéance est configurée à l’aide `CallOptions.Deadline` de lorsqu’un appel est effectué.</span><span class="sxs-lookup"><span data-stu-id="4e819-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="4e819-115">Il n’y a pas de valeur d’échéance par défaut.</span><span class="sxs-lookup"><span data-stu-id="4e819-115">There is no default deadline value.</span></span> <span data-ttu-id="4e819-116">les appels gRPC ne sont pas limités dans le temps, sauf si une échéance est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="4e819-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="4e819-117">Une échéance est l’heure UTC de la date à laquelle l’échéance est dépassée.</span><span class="sxs-lookup"><span data-stu-id="4e819-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="4e819-118">Par exemple, `DateTime.UtcNow.AddSeconds(5)` est une échéance de 5 secondes à partir de maintenant.</span><span class="sxs-lookup"><span data-stu-id="4e819-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="4e819-119">Si une heure passée ou actuelle est utilisée, l’appel est immédiatement supérieur à l’échéance.</span><span class="sxs-lookup"><span data-stu-id="4e819-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="4e819-120">L’échéance est envoyée avec l’appel gRPC au service et est suivie indépendamment par le client et le service.</span><span class="sxs-lookup"><span data-stu-id="4e819-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="4e819-121">Il est possible qu’un appel gRPC se termine sur un ordinateur, mais au moment où la réponse est renvoyée au client, l’échéance a été dépassée.</span><span class="sxs-lookup"><span data-stu-id="4e819-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="4e819-122">Si une échéance est dépassée, le client et le service ont un comportement différent :</span><span class="sxs-lookup"><span data-stu-id="4e819-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="4e819-123">Le client abandonne immédiatement la requête HTTP sous-jacente et génère une `DeadlineExceeded` erreur.</span><span class="sxs-lookup"><span data-stu-id="4e819-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="4e819-124">L’application cliente peut choisir d’intercepter l’erreur et d’afficher un message de délai d’attente à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4e819-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="4e819-125">Sur le serveur, la requête HTTP en cours d’exécution est abandonnée et [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="4e819-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="4e819-126">Bien que la requête HTTP soit abandonnée, l’appel gRPC continue à s’exécuter sur le serveur jusqu’à ce que la méthode soit terminée.</span><span class="sxs-lookup"><span data-stu-id="4e819-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="4e819-127">Il est important que le jeton d’annulation soit passé aux méthodes Async afin qu’elles soient annulées avec l’appel.</span><span class="sxs-lookup"><span data-stu-id="4e819-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="4e819-128">Par exemple, le passage d’un jeton d’annulation aux requêtes de base de données Async et aux requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4e819-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="4e819-129">Le passage d’un jeton d’annulation permet à l’appel annulé de se terminer rapidement sur le serveur et de libérer des ressources pour d’autres appels.</span><span class="sxs-lookup"><span data-stu-id="4e819-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="4e819-130">Configurez `CallOptions.Deadline` pour définir une échéance pour un appel gRPC :</span><span class="sxs-lookup"><span data-stu-id="4e819-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="4e819-131">Utilisation `ServerCallContext.CancellationToken` de dans un service gRPC :</span><span class="sxs-lookup"><span data-stu-id="4e819-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="4e819-132">Propagation des échéances</span><span class="sxs-lookup"><span data-stu-id="4e819-132">Propagating deadlines</span></span>

<span data-ttu-id="4e819-133">Lorsqu’un appel gRPC est effectué à partir d’un service gRPC en cours d’exécution, l’échéance doit être propagée.</span><span class="sxs-lookup"><span data-stu-id="4e819-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="4e819-134">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4e819-134">For example:</span></span>

1. <span data-ttu-id="4e819-135">Appels de l’application cliente `FrontendService.GetUser` avec une échéance.</span><span class="sxs-lookup"><span data-stu-id="4e819-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="4e819-136">`FrontendService` appelle `UserService.GetUser`.</span><span class="sxs-lookup"><span data-stu-id="4e819-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="4e819-137">L’échéance spécifiée par le client doit être spécifiée avec le nouvel appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e819-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="4e819-138">`UserService.GetUser` reçoit l’échéance.</span><span class="sxs-lookup"><span data-stu-id="4e819-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="4e819-139">Il expire correctement si l’échéance de l’application cliente est dépassée.</span><span class="sxs-lookup"><span data-stu-id="4e819-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="4e819-140">Le contexte de l’appel fournit l’échéance `ServerCallContext.Deadline` :</span><span class="sxs-lookup"><span data-stu-id="4e819-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="4e819-141">La propagation manuelle des échéances peut s’avérer fastidieuse.</span><span class="sxs-lookup"><span data-stu-id="4e819-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="4e819-142">L’échéance doit être transmise à chaque appel, et il est facile de le manquer accidentellement.</span><span class="sxs-lookup"><span data-stu-id="4e819-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="4e819-143">Une solution automatique est disponible avec la fabrique de clients gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e819-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="4e819-144">Spécification de `EnableCallContextPropagation` :</span><span class="sxs-lookup"><span data-stu-id="4e819-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="4e819-145">Propage automatiquement l’échéance et le jeton d’annulation aux appels enfants.</span><span class="sxs-lookup"><span data-stu-id="4e819-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="4e819-146">Est un excellent moyen de s’assurer que les scénarios gRPC complexes et imbriqués propagent toujours l’échéance et l’annulation.</span><span class="sxs-lookup"><span data-stu-id="4e819-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="4e819-147">Pour plus d’informations, consultez <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="4e819-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="4e819-148">Annulation</span><span class="sxs-lookup"><span data-stu-id="4e819-148">Cancellation</span></span>

<span data-ttu-id="4e819-149">L’annulation permet à un client gRPC d’annuler les appels de longue durée qui ne sont plus nécessaires.</span><span class="sxs-lookup"><span data-stu-id="4e819-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="4e819-150">Par exemple, un appel gRPC qui diffuse des mises à jour en temps réel est démarré lorsque l’utilisateur visite une page sur un site Web.</span><span class="sxs-lookup"><span data-stu-id="4e819-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="4e819-151">Le flux doit être annulé lorsque l’utilisateur quitte la page.</span><span class="sxs-lookup"><span data-stu-id="4e819-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="4e819-152">Un appel gRPC peut être annulé dans le client en passant un jeton d’annulation avec [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) ou en appelant `Dispose` sur l’appel.</span><span class="sxs-lookup"><span data-stu-id="4e819-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="4e819-153">les services gRPC qui peuvent être annulés doivent :</span><span class="sxs-lookup"><span data-stu-id="4e819-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="4e819-154">Passer `ServerCallContext.CancellationToken` aux méthodes Async.</span><span class="sxs-lookup"><span data-stu-id="4e819-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="4e819-155">L’annulation des méthodes Async permet l’exécution rapide de l’appel sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="4e819-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="4e819-156">Propage le jeton d’annulation aux appels enfants.</span><span class="sxs-lookup"><span data-stu-id="4e819-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="4e819-157">La propagation du jeton d’annulation garantit que les appels enfants sont annulés avec leur parent.</span><span class="sxs-lookup"><span data-stu-id="4e819-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="4e819-158">la [fabrique de clients gRPC](xref:grpc/clientfactory) et `EnableCallContextPropagation()` propage automatiquement le jeton d’annulation.</span><span class="sxs-lookup"><span data-stu-id="4e819-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e819-159">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4e819-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
