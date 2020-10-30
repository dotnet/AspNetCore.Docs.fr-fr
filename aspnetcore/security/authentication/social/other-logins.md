---
title: Fournisseurs d’authentification OAuth externes
author: rick-anderson
description: Découvrez les fournisseurs d’authentification OAuth externes qui fonctionnent avec les applications ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
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
uid: security/authentication/otherlogins
ms.openlocfilehash: 11f770b752a6654ffe73b4fe005c09711515ac74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053265"
---
# <a name="external-oauth-authentication-providers"></a>Fournisseurs d’authentification OAuth externes

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd)et [Valeriy Novytskyy](https://github.com/01binary)

La liste suivante répertorie les fournisseurs d’authentification OAuth externes communs qui fonctionnent avec les applications ASP.NET Core. Des packages NuGet tiers, tels que ceux gérés par [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), peuvent être utilisés pour compléter les fournisseurs d’authentification implémentés par l’équipe ASP.net core.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([instructions](https://developer.linkedin.com/docs/oauth2))

* [Instagram](https://www.instagram.com/developer/register/) ([instructions](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([instructions](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([instructions](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([instructions](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([instructions](https://developers.pinterest.com/docs/api/overview/?))

* [Pocket](https://getpocket.com/developer/apps/new) ([instructions](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([instructions](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribble](https://dribbble.com/signup) ([instructions](https://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([instructions](https://developer.vimeo.com/api/authentication))

* [SoundCloud](https://soundcloud.com/you/apps/new) ([instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
