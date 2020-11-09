---
title: Étapes suivantes-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Parcours d’apprentissage supplémentaires pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056593"
---
# <a name="next-steps"></a><span data-ttu-id="909c6-103">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="909c6-103">Next steps</span></span>

<span data-ttu-id="909c6-104">Dans ce guide, vous avez créé un pipeline DevOps pour un exemple d’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="909c6-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="909c6-105">Félicitations !</span><span class="sxs-lookup"><span data-stu-id="909c6-105">Congratulations!</span></span> <span data-ttu-id="909c6-106">Nous espérons que vous avez apprécié l’apprentissage de la publication de ASP.NET Core Web Apps pour Azure App Service et automatiser l’intégration continue des modifications.</span><span class="sxs-lookup"><span data-stu-id="909c6-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="909c6-107">Au-delà de l’hébergement Web et de DevOps, Azure dispose d’un vaste éventail de services de plateforme en tant que service (PaaS) utiles pour ASP.NET Core les développeurs.</span><span class="sxs-lookup"><span data-stu-id="909c6-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="909c6-108">Cette section donne un bref aperçu des services les plus couramment utilisés.</span><span class="sxs-lookup"><span data-stu-id="909c6-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="909c6-109">Stockage et bases de données</span><span class="sxs-lookup"><span data-stu-id="909c6-109">Storage and databases</span></span>

<span data-ttu-id="909c6-110">Le [cache redims](/azure/redis-cache/) est une mise en cache de données à débit élevé et à faible latence disponible en tant que service.</span><span class="sxs-lookup"><span data-stu-id="909c6-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="909c6-111">Il peut être utilisé pour la mise en cache de la sortie de page, la réduction des demandes de base de données et la fourniture d’un état de session ASP.NET Core sur plusieurs instances d’une application.</span><span class="sxs-lookup"><span data-stu-id="909c6-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="909c6-112">Le [stockage Azure](/azure/storage/) est le stockage cloud à grande échelle d’Azure.</span><span class="sxs-lookup"><span data-stu-id="909c6-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="909c6-113">Les développeurs peuvent tirer parti du [stockage file d’attente](/azure/storage/queues/storage-queues-introduction) pour la mise en file d’attente de messages fiable et le [stockage table](/azure/storage/tables/table-storage-overview) est un magasin de valeurs clés NoSQL conçu pour un développement rapide à l’aide de jeux de données volumineux et semi-structurés.</span><span class="sxs-lookup"><span data-stu-id="909c6-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="909c6-114">[Azure SQL Database](/azure/sql-database/) fournit des fonctionnalités de base de données relationnelles familières en tant que service à l’aide du moteur de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="909c6-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="909c6-115">[Cosmos DB](/azure/cosmos-db/) service de base de données NoSQL multimodèle distribué à l’échelle mondiale.</span><span class="sxs-lookup"><span data-stu-id="909c6-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="909c6-116">Plusieurs API sont disponibles, y compris l’API SQL (anciennement appelée DocumentDB), Cassandra et MongoDB.</span><span class="sxs-lookup"><span data-stu-id="909c6-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="909c6-117">[Azure Active Directory](/azure/active-directory/) et [Azure Active Directory B2C](/azure/active-directory-b2c/) sont tous deux des services d’identité.</span><span class="sxs-lookup"><span data-stu-id="909c6-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="909c6-118">Azure Active Directory est conçu pour les scénarios d’entreprise et Azure AD permet la collaboration B2B (entreprise-entreprise), tandis que Azure Active Directory B2C est destiné aux scénarios interentreprises, y compris la connexion au réseau social.</span><span class="sxs-lookup"><span data-stu-id="909c6-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="909c6-119">Mobile</span><span class="sxs-lookup"><span data-stu-id="909c6-119">Mobile</span></span>

<span data-ttu-id="909c6-120">[Notification hubs](/azure/notification-hubs/) est un moteur de notifications push multi-plateforme et évolutif pour envoyer rapidement des millions de messages à des applications exécutées sur différents types d’appareils.</span><span class="sxs-lookup"><span data-stu-id="909c6-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="909c6-121">Infrastructure Web</span><span class="sxs-lookup"><span data-stu-id="909c6-121">Web infrastructure</span></span>

<span data-ttu-id="909c6-122">[Azure Container Service](/azure/aks/) gère votre environnement Kubernetes hébergé, ce qui vous permet de déployer et de gérer rapidement et facilement des applications en conteneur sans l’expertise de l’orchestration de conteneur.</span><span class="sxs-lookup"><span data-stu-id="909c6-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="909c6-123">[Recherche Azure](/azure/search/) est utilisé pour créer une solution de recherche d’entreprise sur du contenu privé et hétérogène.</span><span class="sxs-lookup"><span data-stu-id="909c6-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="909c6-124">[Service Fabric](/azure/service-fabric/) est une plateforme de systèmes distribués qui facilite l’empaquetage, le déploiement et la gestion de microservices et de conteneurs évolutifs et fiables.</span><span class="sxs-lookup"><span data-stu-id="909c6-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
