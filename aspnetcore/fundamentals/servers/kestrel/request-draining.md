---
title: Demander le drainage avec ASP.NET Core serveur Web Kestrel
author: rick-anderson
description: En savoir plus sur la demande de drainage avec Kestrel, le serveur Web multiplateforme pour ASP.NET Core.
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253949"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>Demander le drainage avec ASP.NET Core serveur Web Kestrel

L’ouverture de connexions HTTP prend beaucoup de temps. Pour le protocole HTTPs, il s’agit également d’une utilisation intensive des ressources. Par conséquent, Kestrel tente de réutiliser les connexions par protocole HTTP/1.1. Un corps de demande doit être entièrement consommé pour permettre la réutilisation de la connexion. L’application ne consomme pas toujours le corps de la requête, par exemple les requêtes HTTP Après, où le serveur retourne une réponse de redirection ou 404. Dans le cas de redirection HTTP, procédez comme suit :

* Le client a peut-être déjà envoyé une partie des données de publication.
* Le serveur écrit la réponse 301.
* La connexion ne peut pas être utilisée pour une nouvelle demande tant que les données de publication du corps de la requête précédente n’ont pas été entièrement lues.
* Kestrel tente de vider le corps de la requête. Le drainage du corps de la demande signifie lire et ignorer les données sans les traiter.

Le processus de drainage fait un compromis entre l’autorisation de réutilisation de la connexion et le temps nécessaire pour décharger les données restantes :

* La vidange a un délai d’expiration de cinq secondes, ce qui n’est pas configurable.
* Si toutes les données spécifiées par l' `Content-Length` `Transfer-Encoding` en-tête ou n’ont pas été lues avant le délai d’attente, la connexion est fermée.

Il peut arriver que vous souhaitiez mettre fin à la demande immédiatement, avant ou après l’écriture de la réponse. Par exemple, les clients peuvent avoir des limites de données restrictives. La limitation des données chargées peut être une priorité. Dans ce cas, pour mettre fin à une demande, appelez [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) à partir d’un contrôleur, d’une Razor page ou d’un intergiciel (middleware).

Il existe des inconvénients à l’appel de `Abort` :

* La création de nouvelles connexions peut être lente et coûteuse.
* Il n’y a aucune garantie que le client a lu la réponse avant la fermeture de la connexion.
* L’appel `Abort` de doit être rare et réservé aux cas d’erreur graves, et non aux erreurs courantes.
  * Appelez uniquement `Abort` lorsqu’un problème spécifique doit être résolu. Par exemple, appelez `Abort` si des clients malveillants essaient de poster des données ou lorsqu’il y a un bogue dans le code client qui provoque des demandes volumineuses ou multiples.
  * N’appelez pas `Abort` pour les situations d’erreur courantes, telles que HTTP 404 (introuvable).

L’appel de [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) avant d’appeler `Abort` garantit que le serveur a terminé l’écriture de la réponse. Toutefois, le comportement du client n’est pas prévisible et il est possible qu’il ne lise pas la réponse avant l’abandon de la connexion.

Ce processus est différent pour HTTP/2, car le protocole prend en charge l’abandon des flux de demande individuels sans fermer la connexion. Le délai d’attente de drainage de cinq secondes ne s’applique pas. S’il existe des données de corps de demande non lues après avoir complété une réponse, le serveur envoie une trame HTTP/2 RST. Les trames de données de corps de requête supplémentaires sont ignorées.

Si possible, il est préférable pour les clients d’utiliser l’en-tête de demande [expect : 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) et d’attendre que le serveur réponde avant de commencer à envoyer le corps de la demande. Cela donne au client la possibilité d’examiner la réponse et de l’abandonner avant d’envoyer des données inutiles.
