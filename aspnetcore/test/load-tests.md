---
title: ASP.NET Core le test de charge/stress
author: Jeremy-Meng
description: Découvrez plusieurs outils et approches notables pour le test de charge et les tests de stress ASP.NET Core les applications.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: test/loadtests
ms.openlocfilehash: 5a860fe398dff2e12671b6854a80bbab3120499b
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109479"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core le test de charge/stress

Les tests de charge et les tests de stress sont importants pour garantir qu’une application Web est performante et évolutive. Leurs objectifs sont différents même s’ils partagent souvent des tests similaires.

**Tests de charge**: Testez si l’application peut gérer une charge d’utilisateurs spécifiée pour un certain scénario tout en répondant toujours à l’objectif de réponse. L’application est exécutée dans des conditions normales.

**Tests de stress**: stabilité de l’application de test lors de l’exécution dans des conditions extrêmes, souvent pendant une longue période de temps. Les tests mettent en place une charge utilisateur élevée, des pics ou une augmentation progressive de la charge, sur l’application, ou ils limitent les ressources informatiques de l’application.

Les tests de contrainte déterminent si une application en contrainte peut récupérer après une défaillance et retourner normalement au comportement attendu. En cas de stress, l’application n’est pas exécutée dans des conditions normales.

Visual Studio 2019 a annoncé des plans pour [déprécier le test de charge](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/). Le service de test de charge basé sur le Cloud Azure DevOps correspondant a été fermé.

## <a name="third-party-tools"></a>Outils tiers

La liste suivante contient des outils de performances Web tiers avec différents ensembles de fonctionnalités :

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [K6](https://k6.io)
* [Caroubes](https://locust.io/)
* [Surtension de l’ouest du vent](https://websurge.west-wind.com/)
* [Netlingue](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
* [NBomber](https://github.com/PragmaticFlow/NBomber)
