---
title: Quand utiliser un proxy inverse avec le serveur Web ASP.NET Core Kestrel
author: rick-anderson
description: En savoir plus sur l’utilisation d’un proxy inverse devant Kestrel, le serveur Web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253939"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Quand utiliser Kestrel avec un proxy inverse ?

Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/). Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.

Kestrel utilisé comme serveur web edge (accessible sur Internet) :

![Kestrel communique directement avec Internet sans serveur proxy inverse](_static/kestrel-to-internet2.png)

Kestrel utilisé dans une configuration de proxy inverse :

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](_static/kestrel-to-internet.png)

L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.

Lorsque Kestrel est utilisé en tant que serveur Edge sans serveur proxy inverse, le partage de la même adresse IP et du même port entre plusieurs processus n’est pas pris en charge. Lorsque Kestrel est configuré pour écouter sur un port, Kestrel gère tout le trafic pour ce port, quels que soient les `Host` en-têtes des demandes. Un proxy inverse pouvant partager des ports peut transférer les demandes à Kestrel sur une adresse IP et un port uniques.

Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.

Un proxy inverse :

* Peut limiter la surface publique exposée des applications qu’il héberge.
* Fournit une couche supplémentaire de configuration et de défense.
* Peut mieux s’intégrer à l’infrastructure existante.
* Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS). Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.

> [!WARNING]
> L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](xref:fundamentals/servers/kestrel/host-filtering).

## <a name="additional-resources"></a>Ressources supplémentaires

<xref:host-and-deploy/proxy-load-balancer>

