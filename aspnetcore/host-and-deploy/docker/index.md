---
title: Héberger ASP.NET Core dans des conteneurs Docker
author: rick-anderson
description: Découvrez des liens vers des ressources pour savoir comment héberger des applications ASP.NET Core dans des conteneurs Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 6b4b011314be2481e6e71d7782fff6ee99cedb9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059778"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Héberger ASP.NET Core dans des conteneurs Docker

Les articles suivants sont disponibles pour en savoir plus sur l’hébergement d’applications ASP.NET Core dans Docker :

[Introduction aux conteneurs et à Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Découvrez dans quelle mesure la mise en conteneur est une approche de développement de logiciels qui consiste à empaqueter une application ou un service, ses dépendances et sa configuration sous forme d’image de conteneur. L’image peut être testée, puis déployée sur un hôte.

[Qu’est-ce que l’ancrage](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Découvrez Docker, projet open source permettant d’automatiser le déploiement d’applications en tant que conteneurs portables et autonomes exécutables sur le cloud ou localement.

[Terminologie de l’arrimeur](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Découvrez les termes et définitions de la technologie Docker.

[Conteneurs, images et registres Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Découvrez comment les images de conteneur Docker sont stockées dans un registre d’image pour un déploiement cohérent entre les environnements.

<xref:host-and-deploy/docker/building-net-docker-images>Découvrez comment générer et dockeriser une application ASP.NET Core. Explorez les images Docker gérées par Microsoft et examinez des cas d’usage.

[Outils de conteneur Visual Studio](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Découvrez dans quelle mesure Visual Studio prend en charge la création, le débogage et l’exécution d’applications ASP.NET Core ciblant le .NET Framework ou .NET Core sur Docker pour Windows. Les conteneurs Windows et Linux sont pris en charge.

[Publier sur Azure Container Registry](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Découvrez comment utiliser l’extension Outils de conteneur Visual pour déployer une application ASP.NET Core sur un hôte Docker sur Azure avec PowerShell.

[Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer)  
Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge. Le passage des requêtes à travers un proxy masque souvent des informations sur la requête d’origine, comme le schéma et l’adresse IP du client. Il peut être nécessaire de transférer manuellement à l’application certaines informations sur la requête.

[GC avec l’ancrage et les petits conteneurs](xref:performance/memory#sc) Décrit la sélection de GC avec de petits conteneurs.