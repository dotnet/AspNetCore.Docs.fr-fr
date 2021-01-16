---
title: Utiliser HTTP/2 avec le serveur Web ASP.NET Core Kestrel
author: rick-anderson
description: En savoir plus sur l’utilisation de HTTP/2 avec Kestrel, le serveur Web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253955"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>Utiliser HTTP/2 avec le serveur Web ASP.NET Core Kestrel

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :

* Système d’exploitation&dagger;
  * Windows Server 2016/Windows 10 ou version ultérieure&Dagger;
  * Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)
* Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure
* Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* TLS 1.2 ou connexion ultérieure

&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.
&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1. La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée. Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.

Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) retourne `HTTP/2`.

À compter de .NET Core 3,0, HTTP/2 est activé par défaut. Pour plus d’informations sur la configuration, consultez les sections [limites KESTREL http/2](xref:fundamentals/servers/kestrel/options#http2-limits) et [ListenOptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) .

## <a name="advanced-http2-features"></a>Fonctionnalités HTTP/2 avancées

Des fonctionnalités HTTP/2 supplémentaires dans Kestrel prennent en charge gRPC, notamment la prise en charge des codes de fin de réponse et l’envoi d’images de réinitialisation.

### <a name="trailers"></a>Bandes-annonce

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Réinitialiser

[!INCLUDE[](~/includes/reset.md)]
