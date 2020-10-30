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
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059921"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>Services gRPC fiables avec échéances et annulation

Par [James Newton-King](https://twitter.com/jamesnk)

Les échéances et l’annulation sont des fonctionnalités utilisées par les clients gRPC pour abandonner les appels en cours. Cet article explique pourquoi les échéances et l’annulation sont importantes et comment les utiliser dans les applications .NET gRPC.

## <a name="deadlines"></a>Échéances

Une échéance permet à un client gRPC de spécifier la durée pendant laquelle il doit attendre la fin d’un appel. Lorsqu’une échéance est dépassée, l’appel est annulé. La définition d’une échéance est importante, car elle fournit une limite supérieure de la durée pendant laquelle un appel peut s’exécuter. Il empêche les services incorrects de s’exécuter de façon permanente et d’épuiser les ressources du serveur. Les échéances sont un outil utile pour la création d’applications fiables et doivent être configurées.

Configuration de l’échéance :

* Une échéance est configurée à l’aide `CallOptions.Deadline` de lorsqu’un appel est effectué.
* Il n’y a pas de valeur d’échéance par défaut. les appels gRPC ne sont pas limités dans le temps, sauf si une échéance est spécifiée.
* Une échéance est l’heure UTC de la date à laquelle l’échéance est dépassée. Par exemple, `DateTime.UtcNow.AddSeconds(5)` est une échéance de 5 secondes à partir de maintenant.
* Si une heure passée ou actuelle est utilisée, l’appel est immédiatement supérieur à l’échéance.
* L’échéance est envoyée avec l’appel gRPC au service et est suivie indépendamment par le client et le service. Il est possible qu’un appel gRPC se termine sur un ordinateur, mais au moment où la réponse est renvoyée au client, l’échéance a été dépassée.

Si une échéance est dépassée, le client et le service ont un comportement différent :

* Le client abandonne immédiatement la requête HTTP sous-jacente et génère une `DeadlineExceeded` erreur. L’application cliente peut choisir d’intercepter l’erreur et d’afficher un message de délai d’attente à l’utilisateur.
* Sur le serveur, la requête HTTP en cours d’exécution est abandonnée et [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) est déclenché. Bien que la requête HTTP soit abandonnée, l’appel gRPC continue à s’exécuter sur le serveur jusqu’à ce que la méthode soit terminée. Il est important que le jeton d’annulation soit passé aux méthodes Async afin qu’elles soient annulées avec l’appel. Par exemple, le passage d’un jeton d’annulation aux requêtes de base de données Async et aux requêtes HTTP. Le passage d’un jeton d’annulation permet à l’appel annulé de se terminer rapidement sur le serveur et de libérer des ressources pour d’autres appels.

Configurez `CallOptions.Deadline` pour définir une échéance pour un appel gRPC :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Utilisation `ServerCallContext.CancellationToken` de dans un service gRPC :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>Propagation des échéances

Lorsqu’un appel gRPC est effectué à partir d’un service gRPC en cours d’exécution, l’échéance doit être propagée. Exemple :

1. Appels de l’application cliente `FrontendService.GetUser` avec une échéance.
2. `FrontendService` appelle `UserService.GetUser`. L’échéance spécifiée par le client doit être spécifiée avec le nouvel appel gRPC.
3. `UserService.GetUser` reçoit l’échéance. Il expire correctement si l’échéance de l’application cliente est dépassée.

Le contexte de l’appel fournit l’échéance `ServerCallContext.Deadline` :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

La propagation manuelle des échéances peut s’avérer fastidieuse. L’échéance doit être transmise à chaque appel, et il est facile de le manquer accidentellement. Une solution automatique est disponible avec la fabrique de clients gRPC. Spécification de `EnableCallContextPropagation` :

* Propage automatiquement l’échéance et le jeton d’annulation aux appels enfants.
* Est un excellent moyen de s’assurer que les scénarios gRPC complexes et imbriqués propagent toujours l’échéance et l’annulation.

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

Pour plus d'informations, consultez <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.

## <a name="cancellation"></a>Annulation

L’annulation permet à un client gRPC d’annuler les appels de longue durée qui ne sont plus nécessaires. Par exemple, un appel gRPC qui diffuse des mises à jour en temps réel est démarré lorsque l’utilisateur visite une page sur un site Web. Le flux doit être annulé lorsque l’utilisateur quitte la page.

Un appel gRPC peut être annulé dans le client en passant un jeton d’annulation avec [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) ou en appelant `Dispose` sur l’appel.

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

les services gRPC qui peuvent être annulés doivent :
* Passer `ServerCallContext.CancellationToken` aux méthodes Async. L’annulation des méthodes Async permet l’exécution rapide de l’appel sur le serveur.
* Propage le jeton d’annulation aux appels enfants. La propagation du jeton d’annulation garantit que les appels enfants sont annulés avec leur parent. la [fabrique de clients gRPC](xref:grpc/clientfactory) et `EnableCallContextPropagation()` propage automatiquement le jeton d’annulation.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/client>
* <xref:grpc/clientfactory>
