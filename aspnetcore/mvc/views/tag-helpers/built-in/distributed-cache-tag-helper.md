---
title: Tag Helper Cache distribué dans ASP.NET Core
author: pkellner
description: Découvrez comment utiliser le Tag Helper Cache distribué.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 04ab5be4d9cec066a4b7cd422a1566bcbb5a291a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061156"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="2e85a-103">Tag Helper Cache distribué dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e85a-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="2e85a-104">Par [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="2e85a-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="2e85a-105">Le Tag Helper Cache distribué permet d’améliorer considérablement les performances de votre application ASP.NET Core en mettant en cache son contenu dans une source de cache distribué.</span><span class="sxs-lookup"><span data-stu-id="2e85a-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="2e85a-106">Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="2e85a-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="2e85a-107">Le Tag Helper Cache distribué hérite de la même classe de base que le Tag Helper Cache.</span><span class="sxs-lookup"><span data-stu-id="2e85a-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="2e85a-108">Tous les attributs [Tag Helper Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) sont disponibles pour Tag Helper distribué.</span><span class="sxs-lookup"><span data-stu-id="2e85a-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="2e85a-109">Le Tag Helper Cache distribué utilise [l’injection de constructeurs](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="2e85a-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="2e85a-110">L’interface <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> est passée dans le constructeur du Tag Helper Cache distribué.</span><span class="sxs-lookup"><span data-stu-id="2e85a-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="2e85a-111">Si aucune implémentation concrète de `IDistributedCache` n’est créée dans `Startup.ConfigureServices` ( *Startup.cs* ), le Tag Helper Cache distribué utilise le même fournisseur en mémoire pour le stockage des données mises en cache que le [Tag Helper Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2e85a-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` ( *Startup.cs* ), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="2e85a-112">Attributs de Tag Helper Cache distribué</span><span class="sxs-lookup"><span data-stu-id="2e85a-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="2e85a-113">Attributs partagés avec le Tag Helper Cache</span><span class="sxs-lookup"><span data-stu-id="2e85a-113">Attributes shared with the Cache Tag Helper</span></span>

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

<span data-ttu-id="2e85a-114">Le Tag Helper Cache distribué hérite de la même classe que le Tag Helper Cache.</span><span class="sxs-lookup"><span data-stu-id="2e85a-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="2e85a-115">Pour obtenir une description de ces attributs, consultez le [Tag Helper Cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2e85a-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="2e85a-116">name</span><span class="sxs-lookup"><span data-stu-id="2e85a-116">name</span></span>

| <span data-ttu-id="2e85a-117">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="2e85a-117">Attribute Type</span></span> | <span data-ttu-id="2e85a-118">Exemple</span><span class="sxs-lookup"><span data-stu-id="2e85a-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="2e85a-119">String</span><span class="sxs-lookup"><span data-stu-id="2e85a-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="2e85a-120">`name` est obligatoire.</span><span class="sxs-lookup"><span data-stu-id="2e85a-120">`name` is required.</span></span> <span data-ttu-id="2e85a-121">L’attribut `name` est utilisé en tant que clé pour chaque instance de cache stockée.</span><span class="sxs-lookup"><span data-stu-id="2e85a-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="2e85a-122">Contrairement au tag Helper cache qui affecte une clé de cache à chaque instance en fonction du Razor nom et de l’emplacement de la page dans la Razor page, le tag Helper cache distribué base uniquement sa clé sur l’attribut `name` .</span><span class="sxs-lookup"><span data-stu-id="2e85a-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="2e85a-123">Exemple :</span><span class="sxs-lookup"><span data-stu-id="2e85a-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="2e85a-124">Implémentations IDistributedCache de Tag Helper Cache distribué</span><span class="sxs-lookup"><span data-stu-id="2e85a-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="2e85a-125">Il existe deux implémentations d’<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> intégrées à ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e85a-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="2e85a-126">L’une est basée sur SQL Server et l’autre sur Redis.</span><span class="sxs-lookup"><span data-stu-id="2e85a-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="2e85a-127">Des implémentations tierces sont également disponibles, telles que [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span><span class="sxs-lookup"><span data-stu-id="2e85a-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="2e85a-128">Pour obtenir les détails de ces implémentations, consultez <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="2e85a-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="2e85a-129">Les deux implémentations impliquent la définition d’une instance de `IDistributedCache` dans `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2e85a-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="2e85a-130">Aucun attribut de balise n’est spécifiquement associé à l’utilisation d’une implémentation d’`IDistributedCache`.</span><span class="sxs-lookup"><span data-stu-id="2e85a-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e85a-131">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2e85a-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
