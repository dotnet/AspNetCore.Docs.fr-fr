---
title: Vue d’ensemble de la sécurité ASP.NET Core
author: rick-anderson
description: Découvrez les concepts de base de l’authentification, de l’autorisation et de la sécurité dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051627"
---
# <a name="overview-of-aspnet-core-security"></a>Vue d’ensemble de la sécurité ASP.NET Core

ASP.NET Core permet aux développeurs de configurer et de gérer facilement la sécurité de leurs applications. ASP.NET Core contient des fonctionnalités de gestion de l’authentification, de l’autorisation, de la protection des données, de la contrainte HTTPs, des secrets d’application, de la prévention XSRF/CSRF et de la gestion CORS. Ces fonctionnalités de sécurité vous permettent de générer des applications ASP.NET Core robustes et néanmoins sécurisées.

## <a name="aspnet-core-security-features"></a>Fonctionnalités de sécurité ASP.NET Core

ASP.NET Core fournit de nombreux outils et bibliothèques pour sécuriser vos applications, notamment les fournisseurs d’identité intégrés, mais vous pouvez utiliser des services d’identité tiers tels que Facebook, Twitter et LinkedIn. Avec ASP.NET Core, vous pouvez facilement gérer les secrets des applications, qui sont un moyen de stocker et d’utiliser des informations confidentielles sans avoir à les exposer dans le code.

## <a name="authentication-vs-authorization"></a>Authentification et autorisation

L’authentification est un processus selon lequel un utilisateur fournit des informations d’identification qui sont ensuite comparées à celles stockées dans un système d’exploitation, une base de données, une application ou une ressource. Si elles correspondent, les utilisateurs sont authentifiés et peuvent alors effectuer les actions pour lesquelles ils disposent d’autorisations pendant un processus d’autorisation. L’autorisation désigne le processus qui détermine ce qu’un utilisateur est autorisé à faire.

Vous pouvez aussi vous représenter l’authentification comme un moyen d’entrer dans un espace, tel qu’un serveur, une base de données, une application ou une ressource, tandis que l’autorisation consiste à définir quelles actions l’utilisateur peut effectuer sur quels objets à l’intérieur de cet espace (serveur, base de données ou application).

## <a name="common-vulnerabilities-in-software"></a>Failles de sécurité courantes dans les logiciels

ASP.NET Core et Entity Framework contiennent des fonctionnalités qui vous aident à sécuriser vos applications et à empêcher les violations de sécurité. La liste de liens ci-après vous permet d’accéder à une documentation décrivant en détail des techniques destinées à éviter les failles de sécurité les plus courantes dans les applications web :

* [Attaques de script entre sites (XSS)](xref:security/cross-site-scripting)
* [Attaques par injection de code SQL](/ef/core/querying/raw-sql)
* [Attaques de falsification de requête intersites (XSRF/CSRF)](xref:security/anti-request-forgery)
* [Attaques par redirection ouverte](xref:security/preventing-open-redirects)

Il existe d’autres failles de sécurité que vous devez connaître. Pour plus d’informations, reportez-vous aux autres Articles de la section **sécurité et Identity** de la table des matières.
