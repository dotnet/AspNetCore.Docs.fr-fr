---
title: SignalRClients ASP.net Core
author: bradygaster
description: Découvrez les fonctionnalités prises en charge par les différents SignalR clients ASP.net core.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/client-features
ms.openlocfilehash: 65ad619bb943a0e5ffbaec7ec80243972dafc49f
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024663"
---
# <a name="aspnet-core-no-locsignalr-clients"></a>SignalRClients ASP.net Core

## <a name="versioning-support-and-compatibility"></a>Contrôle de version, prise en charge et compatibilité

Les SignalR clients sont livrés avec les composants serveur et les versions sont mises en correspondance. Tout client pris en charge peut se connecter en toute sécurité à n’importe quel serveur pris en charge, et les problèmes de compatibilité seraient considérés comme des bogues à résoudre. SignalR les clients sont pris en charge dans le même cycle de vie du support que le reste de .NET Core. Pour plus d’informations, consultez [la stratégie de prise en charge de .net Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .

De nombreuses fonctionnalités requièrent un client **et** un serveur compatibles. Reportez-vous à la section ci-dessous pour un tableau présentant les versions minimales de différentes fonctionnalités.

Les versions 1. x de SignalR correspondent aux versions 2,1 et 2,2 .net Core et ont la même durée de vie. Pour la version 3. x et les versions ultérieures, la SignalR version correspond exactement au reste de .net et a le même cycle de vie de support.

| Version SignalR | Version .NET Core | Niveau de support | Fin de la prise en charge |
| - | - | - | - |
| 1.0. x | 2.1.x | Support à long terme | 21 août, 2021 |
| 1.1. x | 2.2.x | Fin de vie | 23 décembre 2019 |
| 3. x ou supérieur | *identique à la SignalR version* | Consultez la [stratégie de prise en charge de .net Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) |

**Remarque :** Dans ASP.NET Core 3,0, le client JavaScript a *été déplacé* vers le `@microsoft/signalr` package NPM.

## <a name="feature-distribution"></a>Distribution des fonctionnalités

Le tableau ci-dessous présente les fonctionnalités et la prise en charge des clients qui offrent une prise en charge en temps réel. Pour chaque fonctionnalité, la version *minimale* qui prend en charge cette fonctionnalité est indiquée. Si aucune version n’est indiquée, la fonctionnalité n’est pas prise en charge.

| Fonctionnalité | Serveur | Client .NET | Client JavaScript | Client Java |
| ---- | :-: | :-: | :-: | :-: |
| SignalRSupport du service Azure |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming de serveur à client](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming client à serveur](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Reconnexion automatique ([.net](./dotnet-client.md?tabs=visual-studio&view=aspnetcore-3.0#handle-lost-connection), [JavaScript](./javascript-client.md?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Transport WebSockets |2.1.0|1.0.0|1.0.0|1.0.0|
| Transport d’événements de Server-Sent |2.1.0|1.0.0|1.0.0|❌|
| Transport d’interrogation longue |2.1.0|1.0.0|1.0.0|3.0.0|
| Protocole JSON Hub |2.1.0|1.0.0|1.0.0|1.0.0|
| Protocole MessagePack Hub |2.1.0|1.0.0|1.0.0|5.0.0|

La prise en charge de l’activation de fonctionnalités supplémentaires du client est suivie dans [notre suivi des problèmes](https://github.com/dotnet/AspNetCore/issues).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Prise en main SignalR de pour ASP.net Core](xref:tutorials/signalr)
* [Plateformes prises en charge](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [Client JavaScript](xref:signalr/javascript-client)