---
title: DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Un guide qui fournit des conseils de bout en bout sur la création d’un pipeline DevOps pour une application ASP.NET Core hébergée dans Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: 10a6bae73743d3063ad2e1ab49fc418ad1d63756
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052290"
---
# <a name="devops-with-aspnet-core-and-azure"></a>DevOps avec ASP.NET Core et Azure

[![Image de couverture](./media/cover-large.png)](https://aka.ms/devopsbook)

Par [Cam Soper](https://twitter.com/camsoper) et [Scott Addie](https://twitter.com/scottaddie)

Ce guide est disponible sous forme de [livre électronique PDF téléchargeable](https://aka.ms/devopsbook).

## <a name="welcome"></a>Bienvenue 

Bienvenue dans le guide du cycle de vie du développement Azure pour .NET ! Ce guide présente les concepts de base de la création d’un cycle de vie du développement autour d’Azure avec les outils et les processus .NET. Après avoir terminé ce guide, vous pourrez tirer parti d’une chaîne d’outils DevOps arrivés à maturation.

## <a name="who-this-guide-is-for"></a>Audience à laquelle ce guide est destiné

Vous devez être un développeur ASP.NET Core expérimenté (niveau 200-300). Vous n’avez pas besoin de connaissances sur Azure, car nous couvrons cet aspect dans cette introduction. Ce guide peut également être utile aux ingénieurs DevOps qui se consacrent davantage aux opérations qu’au développement.

Ce guide cible les développeurs Windows. Cependant, Linux et macOS sont entièrement pris en charge par .NET Core. Pour adapter ce guide à Linux/macOS, consultez les indications faisant état des différences pour Linux/macOS.

## <a name="what-this-guide-doesnt-cover"></a>Ce que ce guide ne couvre pas

Ce guide est centré sur une expérience de déploiement continu de bout en bout pour les développeurs .NET. Il ne s’agit pas d’un guide exhaustif sur Azure, et il n’aborde pas de façon détaillée les API .NET pour les services Azure. L’accent est mis sur tout ce qui a trait à l’intégration continue, au déploiement, à la surveillance et au débogage. Vous pouvez trouver des recommandations pour les étapes suivantes vers la fin du guide. Les services de plateforme Azure qui sont utiles aux développeurs ASP.NET Core figurent dans les suggestions.

## <a name="whats-in-this-guide"></a>Contenu de ce guide

### <a name="tools-and-downloads"></a>[Outils et téléchargements](xref:azure/devops/tools-and-downloads)

Découvrez où vous procurer les outils utilisés dans ce guide.

### <a name="deploy-to-app-service"></a>[Déployer sur App Service](xref:azure/devops/deploy-to-app-service)

Découvrez les différentes méthodes de déploiement d’une application ASP.NET Core sur Azure App Service.

### <a name="continuous-integration-and-deployment"></a>[Intégration et déploiement continus](xref:azure/devops/cicd)

Créez une solution d’intégration et de déploiement continus de bout en bout pour votre application ASP.NET Core avec GitHub, Azure DevOps Services et Azure.

### <a name="monitor-and-debug"></a>[Surveiller et déboguer](xref:azure/devops/monitor)

Utilisez les outils d’Azure pour surveiller, dépanner et optimiser votre application.

### <a name="next-steps"></a>[Étapes suivantes](xref:azure/devops/next-steps)

Autres parcours d’apprentissage pour les développeurs ASP.NET Core découvrant Azure.

## <a name="additional-introductory-reading"></a>Articles d’introduction supplémentaires

S’il s’agit de votre premier contact avec le cloud computing, ces articles expliquent les principes de base.

* [Qu’est-ce que le Cloud Computing ?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Exemples de cloud computing](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [Qu’est-ce que IaaS ?](https://azure.microsoft.com/overview/what-is-iaas/)
* [Qu’est-ce que PaaS ?](https://azure.microsoft.com/overview/what-is-paas/)
