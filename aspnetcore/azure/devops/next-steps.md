---
title: Étapes suivantes-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Parcours d’apprentissage supplémentaires pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056593"
---
# <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez créé un pipeline DevOps pour un exemple d’application ASP.NET Core. Félicitations ! Nous espérons que vous avez apprécié l’apprentissage de la publication de ASP.NET Core Web Apps pour Azure App Service et automatiser l’intégration continue des modifications.

Au-delà de l’hébergement Web et de DevOps, Azure dispose d’un vaste éventail de services de plateforme en tant que service (PaaS) utiles pour ASP.NET Core les développeurs. Cette section donne un bref aperçu des services les plus couramment utilisés.

## <a name="storage-and-databases"></a>Stockage et bases de données

Le [cache redims](/azure/redis-cache/) est une mise en cache de données à débit élevé et à faible latence disponible en tant que service. Il peut être utilisé pour la mise en cache de la sortie de page, la réduction des demandes de base de données et la fourniture d’un état de session ASP.NET Core sur plusieurs instances d’une application.

Le [stockage Azure](/azure/storage/) est le stockage cloud à grande échelle d’Azure. Les développeurs peuvent tirer parti du [stockage file d’attente](/azure/storage/queues/storage-queues-introduction) pour la mise en file d’attente de messages fiable et le [stockage table](/azure/storage/tables/table-storage-overview) est un magasin de valeurs clés NoSQL conçu pour un développement rapide à l’aide de jeux de données volumineux et semi-structurés.

[Azure SQL Database](/azure/sql-database/) fournit des fonctionnalités de base de données relationnelles familières en tant que service à l’aide du moteur de Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) service de base de données NoSQL multimodèle distribué à l’échelle mondiale. Plusieurs API sont disponibles, y compris l’API SQL (anciennement appelée DocumentDB), Cassandra et MongoDB.

## Identity

[Azure Active Directory](/azure/active-directory/) et [Azure Active Directory B2C](/azure/active-directory-b2c/) sont tous deux des services d’identité. Azure Active Directory est conçu pour les scénarios d’entreprise et Azure AD permet la collaboration B2B (entreprise-entreprise), tandis que Azure Active Directory B2C est destiné aux scénarios interentreprises, y compris la connexion au réseau social.

## <a name="mobile"></a>Mobile

[Notification hubs](/azure/notification-hubs/) est un moteur de notifications push multi-plateforme et évolutif pour envoyer rapidement des millions de messages à des applications exécutées sur différents types d’appareils.

## <a name="web-infrastructure"></a>Infrastructure Web

[Azure Container Service](/azure/aks/) gère votre environnement Kubernetes hébergé, ce qui vous permet de déployer et de gérer rapidement et facilement des applications en conteneur sans l’expertise de l’orchestration de conteneur.

[Recherche Azure](/azure/search/) est utilisé pour créer une solution de recherche d’entreprise sur du contenu privé et hétérogène.

[Service Fabric](/azure/service-fabric/) est une plateforme de systèmes distribués qui facilite l’empaquetage, le déploiement et la gestion de microservices et de conteneurs évolutifs et fiables.
