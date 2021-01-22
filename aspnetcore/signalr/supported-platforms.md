---
title: SignalRPlateformes prises en charge ASP.net Core
author: bradygaster
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
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
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689225"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>SignalRPlateformes prises en charge ASP.net Core

## <a name="server-system-requirements"></a>Configuration requise pour le serveur

SignalR pour ASP.NET Core prend en charge toutes les plateformes de serveur prises en charge par ASP.NET Core.

## <a name="javascript-client"></a>Client JavaScript

Le [client JavaScript](xref:signalr/javascript-client) s’exécute sur NodeJS 8 et versions ultérieures, ainsi que sur les navigateurs suivants :

| Browser                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, y compris iOS      | Actif&dagger; |
| Google Chrome, y compris Android | Actif&dagger; |
| Microsoft Edge                   | Actif&dagger; |
| Mozilla Firefox                  | Actif&dagger; |

&dagger;*Current* fait référence à la dernière version du navigateur.

Le client JavaScript ne prend pas en charge Internet Explorer et d’autres navigateurs plus anciens. Le client peut avoir un comportement inattendu et des erreurs sur les navigateurs non pris en charge.

## <a name="net-client"></a>Client .NET

Le [client .net](xref:signalr/dotnet-client) s’exécute sur n’importe quelle plateforme prise en charge par ASP.net core. Par exemple, [les développeurs Xamarin peuvent SignalR utiliser](https://github.com/aspnet/Announcements/issues/305) pour créer des applications Android à l’aide de Xamarin. Android 8.4.0.1 et versions ultérieures et des applications iOS à l’aide de Xamarin. iOS 11.14.0.4 et versions ultérieures.

Si le serveur exécute IIS, le transport WebSockets requiert IIS 8,0 ou une version ultérieure sur Windows Server 2012 ou version ultérieure. D’autres transports sont pris en charge sur toutes les plateformes.

## <a name="java-client"></a>Client Java

Le [client Java](xref:signalr/java-client) prend en charge Java 8 et versions ultérieures.

## <a name="unsupported-clients"></a>Clients non pris en charge

Les clients suivants sont disponibles, mais sont expérimentaux ou non officiels. Ils ne sont actuellement pas pris en charge et peuvent ne jamais être.

* [Client C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Client SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
