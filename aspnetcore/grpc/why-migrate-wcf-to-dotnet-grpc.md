---
title: Pourquoi migrer WCF vers ASP.NET Core gRPC
author: markrendle
description: Cet article fournit un résumé de la raison pour laquelle ASP.NET Core gRPC est adapté aux développeurs Windows Communication Foundation (WCF) qui souhaitent migrer vers des architectures et des plateformes modernes.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058686"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>gRPC pour les développeurs Windows Communication Foundation (WCF)

Cet article fournit un résumé de la raison pour laquelle ASP.NET Core gRPC est adapté aux développeurs Windows Communication Foundation (WCF) qui souhaitent migrer vers des architectures et des plateformes modernes.

## <a name="comparison-to-wcf"></a>Comparaison avec WCF

Bien que l’implémentation et l’approche soient différentes pour gRPC, l’expérience du développement et de l’utilisation des services avec gRPC doit être intuitive pour les développeurs WCF. WCF et gRPC sont des infrastructures RPC (Remote Procedure Call, appel de procédure distante) ayant les mêmes objectifs :

* Rendre possible le code comme si le client et le serveur se trouvent sur la même plate-forme.
* Fournir une API réseau portable simplifiée.

Les deux plateformes partagent la nécessité de déclarer et d’implémenter une interface, bien que le processus de déclaration de l’interface soit différent. Les nombreux types d’appels RPC que gRPC prend en charge mappent bien aux liaisons disponibles pour les services WCF. Pour plus d’informations et d’exemples, consultez [migrer une solution WCF vers gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).

## <a name="benefits-of-grpc"></a>Avantages de gRPC

gRPC fournit une meilleure infrastructure que les autres approches pour les raisons suivantes.

### <a name="performance"></a>Performances

gRPC utilise HTTP/2. Contrairement à HTTP/1.1, HTTP/2 :

* Est un protocole binaire plus petit et plus rapide.
* Est plus efficace pour l’analyse des ordinateurs.
* Prend en charge le multiplexage des requêtes sur une seule connexion. Le multiplexage permet d’envoyer plusieurs demandes sur une connexion sans que les demandes se bloquent mutuellement. Dans HTTP/1.1, le blocage est appelé « blocage de la tête de ligne (HOL) ».

gRPC utilise Protobuf, un format binaire efficace, pour sérialiser les messages. Les messages Protobuf sont les suivants :
* Rapidité de sérialisation et de désérialisation.
* Utilisez moins de bande passante que les formats textuels. 

gRPC est une bonne solution pour les appareils mobiles et les réseaux avec des restrictions de bande passante.

### <a name="interoperability"></a>Interopérabilité

Il existe des outils et des bibliothèques gRPC pour tous les principaux langages de programmation et plateformes, notamment .NET, Java, Python, Go, C++, Node.js, SWIFT, DART, Ruby et PHP. Grâce au format de câble binaire Protobuf et à la génération de code efficace pour chaque plateforme, les développeurs peuvent créer des applications performantes, multiplateformes.

### <a name="usability-and-productivity"></a>Convivialité et productivité

gRPC est une solution RPC complète. Il fonctionne de manière cohérente sur plusieurs langages et plateformes. Il fournit également d’excellents outils, avec une grande partie du code réutilisable généré automatiquement. Comme WCF, gRPC génère automatiquement des messages et un client fortement typé. Le temps du développeur est libéré pour se concentrer sur la logique métier.

### <a name="streaming"></a>Diffusion en continu

gRPC dispose d’une diffusion en continu bidirectionnelle complète qui offre des fonctionnalités similaires à celles des services duplex complets de WCF. la diffusion en continu gRPC peut fonctionner sur des connexions Internet régulières, des équilibrages de charge et des maillages de service.

### <a name="deadlines-timeouts-and-cancellation"></a>Échéances, délais d’attente et annulation

gRPC permet aux clients de spécifier la durée maximale pendant laquelle un RPC se termine. Si l’échéance spécifiée est dépassée, le serveur peut annuler l’opération indépendamment du client. Les échéances et les annulations peuvent être propagées par le biais des appels gRPC suivants pour aider à appliquer les limites d’utilisation des ressources. Les clients peuvent arrêter des opérations lorsqu’une échéance est dépassée, ou plus tôt si nécessaire. Par exemple, les clients peuvent arrêter des opérations en raison d’une interaction avec l’utilisateur.

### <a name="security"></a>Sécurité

gRPC peut utiliser TLS et HTTP/2 pour fournir une connexion chiffrée de bout en bout entre le client et le serveur. La prise en charge de l’authentification par certificat client augmente davantage la sécurité et l’approbation entre le client et le serveur.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>gRPC comme chemin de migration pour WCF vers .NET Core et .NET 5

.NET Core et .NET 5 marquent une évolution de la façon dont Microsoft fournit des solutions de communication à distance pour les développeurs qui souhaitent fournir des services sur toute une gamme de plateformes. .NET Core et .NET 5 prennent en charge l' [appel des services WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), mais n’offrent pas de prise en charge côté serveur pour l’hébergement de WCF.

Il existe deux chemins d’accès recommandés pour la modernisation des applications WCF :

* gRPC repose sur les technologies modernes et est apparu comme le choix le plus populaire dans la communauté des développeurs pour les applications RPC. À compter de .NET Core 3,0, les plateformes .NET modernes offrent une excellente prise en charge de gRPC. La migration des services WCF pour utiliser gRPC permet de fournir les fonctionnalités RPC, les performances et l’interopérabilité nécessaires dans les applications modernes.

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) est un effort de la Communauté visant à prendre en charge l’hébergement des services WCF sur .net Core et .net 5. Une version préliminaire est disponible et le projet est prêt pour la production. CoreWCF prend uniquement en charge un sous-ensemble des fonctionnalités de WCF et .NET Framework les applications qui migrent pour l’utiliser nécessitent des modifications de code et des tests pour réussir. CoreWCF est un bon choix si une application doit assurer la compatibilité avec les clients existants qui appellent les services WCF.

## <a name="get-started"></a>Bien démarrer

Pour obtenir des instructions détaillées sur la création de services gRPC dans ASP.NET Core pour les développeurs WCF, consultez [ASP.net Core gRPC pour les développeurs WCF](/dotnet/architecture/grpc-for-wcf-developers) .
