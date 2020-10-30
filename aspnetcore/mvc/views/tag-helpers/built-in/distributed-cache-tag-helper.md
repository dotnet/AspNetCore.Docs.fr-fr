---
title: Tag Helper Cache distribué dans ASP.NET Core
author: pkellner
description: Découvrez comment utiliser le Tag Helper Cache distribué.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
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
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 04ab5be4d9cec066a4b7cd422a1566bcbb5a291a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061156"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>Tag Helper Cache distribué dans ASP.NET Core

Par [Peter Kellner](https://peterkellner.net)

Le Tag Helper Cache distribué permet d’améliorer considérablement les performances de votre application ASP.NET Core en mettant en cache son contenu dans une source de cache distribué.

Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.

Le Tag Helper Cache distribué hérite de la même classe de base que le Tag Helper Cache. Tous les attributs [Tag Helper Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) sont disponibles pour Tag Helper distribué.

Le Tag Helper Cache distribué utilise [l’injection de constructeurs](xref:fundamentals/dependency-injection#constructor-injection-behavior). L’interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> est passée dans le constructeur du Tag Helper Cache distribué. Si aucune implémentation concrète de `IDistributedCache` n’est créée dans `Startup.ConfigureServices` ( *Startup.cs* ), le Tag Helper Cache distribué utilise le même fournisseur en mémoire pour le stockage des données mises en cache que le [Tag Helper Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

## <a name="distributed-cache-tag-helper-attributes"></a>Attributs de Tag Helper Cache distribué

### <a name="attributes-shared-with-the-cache-tag-helper"></a>Attributs partagés avec le Tag Helper Cache

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

Le Tag Helper Cache distribué hérite de la même classe que le Tag Helper Cache. Pour obtenir une description de ces attributs, consultez le [Tag Helper Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="name"></a>name

| Type d’attribut | Exemple                               |
| -------------- | ------------------------------------- |
| String         | `my-distributed-cache-unique-key-101` |

`name` est obligatoire. L’attribut `name` est utilisé en tant que clé pour chaque instance de cache stockée. Contrairement au tag Helper cache qui affecte une clé de cache à chaque instance en fonction du Razor nom et de l’emplacement de la page dans la Razor page, le tag Helper cache distribué base uniquement sa clé sur l’attribut `name` .

Exemple :

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>Implémentations IDistributedCache de Tag Helper Cache distribué

Il existe deux implémentations d’<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> intégrées à ASP.NET Core. L’une est basée sur SQL Server et l’autre sur Redis. Des implémentations tierces sont également disponibles, telles que [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html). Pour obtenir les détails de ces implémentations, consultez <xref:performance/caching/distributed>. Les deux implémentations impliquent la définition d’une instance de `IDistributedCache` dans `Startup`.

Aucun attribut de balise n’est spécifiquement associé à l’utilisation d’une implémentation d’`IDistributedCache`.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
