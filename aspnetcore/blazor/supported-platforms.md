---
title: BlazorPlateformes prises en charge ASP.net Core
author: guardrex
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: fe0734dbf6eb2647fa6c9b6f336063b9ec091139
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054955"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>BlazorPlateformes prises en charge ASP.net Core

Par [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly et Blazor Server sont pris en charge dans les navigateurs présentés dans le tableau suivant.

| Browser                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, y compris iOS      | Actif&dagger; |
| Google Chrome, y compris Android | Actif&dagger; |
| Microsoft Edge                   | Actif&dagger; |
| Mozilla Firefox                  | Actif&dagger; |  

&dagger;*Current* fait référence à la dernière version du navigateur.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Browser                          | Version               |
| -------------------------------- | --------------------- |
| Apple Safari, y compris iOS      | Actif&dagger;       |
| Google Chrome, y compris Android | Actif&dagger;       |
| Microsoft Edge                   | Actif&dagger;       |
| Microsoft Internet Explorer      | Non pris en charge&Dagger; |
| Mozilla Firefox                  | Actif&dagger;       |  

&dagger;*Current* fait référence à la dernière version du navigateur.  
&Dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).

## Blazor Server

| Browser                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, y compris iOS      | Actif&dagger; |
| Google Chrome, y compris Android | Actif&dagger; |
| Microsoft Edge                   | Actif&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | Actif&dagger; |

&dagger;*Current* fait référence à la dernière version du navigateur.  
&Dagger;Des polyremplissages supplémentaires sont nécessaires. Par exemple, les promesses peuvent être ajoutées via un [`Polyfill.io`](https://polyfill.io/v3/) bundle.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
