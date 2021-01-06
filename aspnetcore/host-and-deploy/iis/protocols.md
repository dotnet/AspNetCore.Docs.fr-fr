---
title: Utiliser ASP.NET Core avec HTTP/2 sur IIS
author: rick-anderson
description: Découvrez comment utiliser les fonctionnalités HTTP/2 avec IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057412"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>Utiliser ASP.NET Core avec HTTP/2 sur IIS

Par [Justin Kotalik](https://github.com/jkotalik)

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est pris en charge avec ASP.NET Core dans les scénarios de déploiement IIS suivants :

* Windows Server 2016 ou version ultérieure/Windows 10 ou version ultérieure
* IIS 10 ou version ultérieure
* TLS 1.2 ou connexion ultérieure
* Lors [de l’hébergement out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): les connexions au serveur Edge public utilisent http/2, mais la connexion du proxy inverse au [serveur Kestrel](xref:fundamentals/servers/kestrel) utilise http/1.1.

Pour un déploiement in-process lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/2` . Pour un déploiement hors processus lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/1.1` .

Pour plus d’informations sur les modèles d’hébergement in-process et out-of-process, consultez <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 est activé par défaut pour les connexions HTTPs/TLS. Les connexions reviennent à HTTP/1.1 si une connexion HTTP/2 n’est pas établie. Pour plus d’informations sur la configuration de HTTP/2 avec les déploiements IIS, consultez [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="advanced-http2-features-to-support-grpc"></a>Fonctionnalités HTTP/2 avancées pour la prise en charge de gRPC

Des fonctionnalités HTTP/2 supplémentaires dans IIS prennent en charge gRPC, y compris la prise en charge des codes de fin de réponse et l’envoi d’images de réinitialisation

Configuration requise pour exécuter gRPC sur IIS :

* Hébergement in-process.
* Windows 10, version 20300,1000 ou ultérieure du système d’exploitation. Peut nécessiter l’utilisation de builds Windows Insider.
* TLS 1.2 ou connexion ultérieure

### <a name="trailers"></a>Bandes-annonce

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Réinitialiser

[!INCLUDE[](~/includes/reset.md)]
