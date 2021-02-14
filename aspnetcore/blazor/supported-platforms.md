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
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280716"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>BlazorPlateformes prises en charge ASP.net Core

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly et Blazor Server sont pris en charge dans les navigateurs présentés dans le tableau suivant.

| Navigateur                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, y compris iOS      | Actif&dagger; |
| Google Chrome, y compris Android | Actif&dagger; |
| Microsoft Edge                   | Actif&dagger; |
| Mozilla Firefox                  | Actif&dagger; |  

&dagger;*Current* fait référence à la dernière version du navigateur.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Navigateur                          | Version               |
| -------------------------------- | --------------------- |
| Apple Safari, y compris iOS      | Actif&dagger;       |
| Google Chrome, y compris Android | Actif&dagger;       |
| Microsoft Edge                   | Actif&dagger;       |
| Microsoft Internet Explorer      | Non pris en charge&Dagger; |
| Mozilla Firefox                  | Actif&dagger;       |  

&dagger;*Current* fait référence à la dernière version du navigateur.  
&Dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).

## Blazor Server

| Navigateur                          | Version         |
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
