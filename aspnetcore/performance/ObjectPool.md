---
title: Réutilisation d’objets avec ObjectPool dans ASP.NET Core
author: rick-anderson
description: Conseils pour améliorer les performances dans les applications de ASP.NET Core à l’aide de ObjectPool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: performance/ObjectPool
ms.openlocfilehash: 3bbfde6f68a238131149e67ce72f0ddc68a9ea0f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056905"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="946bc-103">Réutilisation d’objets avec ObjectPool dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="946bc-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="946bc-104">Par [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)et [Günther Foidl](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="946bc-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="946bc-105"><xref:Microsoft.Extensions.ObjectPool> fait partie de l’infrastructure ASP.NET Core qui prend en charge la conservation d’un groupe d’objets en mémoire à des fins de réutilisation, au lieu d’autoriser les objets à être récupérés par le garbage collector.</span><span class="sxs-lookup"><span data-stu-id="946bc-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="946bc-106">Vous souhaiterez peut-être utiliser le pool d’objets si les objets gérés sont :</span><span class="sxs-lookup"><span data-stu-id="946bc-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="946bc-107">Coûteuse à allouer/initialiser.</span><span class="sxs-lookup"><span data-stu-id="946bc-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="946bc-108">Représentent une ressource limitée.</span><span class="sxs-lookup"><span data-stu-id="946bc-108">Represent some limited resource.</span></span>
- <span data-ttu-id="946bc-109">Utilisé de manière prévisible et fréquente.</span><span class="sxs-lookup"><span data-stu-id="946bc-109">Used predictably and frequently.</span></span>

<span data-ttu-id="946bc-110">Par exemple, l’infrastructure de ASP.NET Core utilise le pool d’objets à certains endroits pour réutiliser des <xref:System.Text.StringBuilder> instances.</span><span class="sxs-lookup"><span data-stu-id="946bc-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="946bc-111">`StringBuilder` alloue et gère ses propres mémoires tampons pour contenir les données de caractères.</span><span class="sxs-lookup"><span data-stu-id="946bc-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="946bc-112">ASP.NET Core utilise régulièrement `StringBuilder` pour implémenter des fonctionnalités, et les réutiliser améliorent les performances.</span><span class="sxs-lookup"><span data-stu-id="946bc-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="946bc-113">La mise en pool d’objets n’améliore pas toujours les performances :</span><span class="sxs-lookup"><span data-stu-id="946bc-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="946bc-114">À moins que le coût d’initialisation d’un objet ne soit élevé, il est généralement plus lent de récupérer l’objet à partir du pool.</span><span class="sxs-lookup"><span data-stu-id="946bc-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="946bc-115">Les objets gérés par le pool ne sont pas désalloués tant que le pool n’est pas libéré.</span><span class="sxs-lookup"><span data-stu-id="946bc-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="946bc-116">Utilisez le mise en pool d’objets uniquement après avoir collecté les données de performances à l’aide de scénarios réalistes pour votre application ou bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="946bc-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="946bc-117">**AVERTISSEMENT : `ObjectPool` n’implémente pas `IDisposable` . Nous vous déconseillons de l’utiliser avec des types nécessitant une suppression.**</span><span class="sxs-lookup"><span data-stu-id="946bc-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="946bc-118">`ObjectPool` dans ASP.NET Core 3,0 et versions ultérieures prend en charge `IDisposable` .</span><span class="sxs-lookup"><span data-stu-id="946bc-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="946bc-119">**Remarque : le ObjectPool n’impose aucune limite quant au nombre d’objets qu’il va allouer, il limite le nombre d’objets qu’il va conserver.**</span><span class="sxs-lookup"><span data-stu-id="946bc-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="946bc-120">Concepts</span><span class="sxs-lookup"><span data-stu-id="946bc-120">Concepts</span></span>

<span data-ttu-id="946bc-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> : abstraction du pool d’objets de base.</span><span class="sxs-lookup"><span data-stu-id="946bc-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="946bc-122">Permet d’obtenir et de retourner des objets.</span><span class="sxs-lookup"><span data-stu-id="946bc-122">Used to get and return objects.</span></span>

<span data-ttu-id="946bc-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -Implémentez ceci pour personnaliser la façon dont un objet est créé et la façon dont il est *réinitialisé* lorsqu’il est retourné au pool.</span><span class="sxs-lookup"><span data-stu-id="946bc-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="946bc-124">Cela peut être passé dans un pool d’objets que vous créez directement... NI</span><span class="sxs-lookup"><span data-stu-id="946bc-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="946bc-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> agit comme une fabrique pour la création de pools d’objets.</span><span class="sxs-lookup"><span data-stu-id="946bc-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="946bc-126">Le ObjectPool peut être utilisé dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="946bc-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="946bc-127">Instanciation d’un pool.</span><span class="sxs-lookup"><span data-stu-id="946bc-127">Instantiating a pool.</span></span>
* <span data-ttu-id="946bc-128">Inscription d’un pool dans une [injection de dépendance](xref:fundamentals/dependency-injection) (di) en tant qu’instance.</span><span class="sxs-lookup"><span data-stu-id="946bc-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="946bc-129">Inscription du `ObjectPoolProvider<>` dans di et utilisation de celui-ci comme fabrique.</span><span class="sxs-lookup"><span data-stu-id="946bc-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="946bc-130">Utilisation de ObjectPool</span><span class="sxs-lookup"><span data-stu-id="946bc-130">How to use ObjectPool</span></span>

<span data-ttu-id="946bc-131">Appelez <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> pour obtenir un objet et <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> pour retourner l’objet.</span><span class="sxs-lookup"><span data-stu-id="946bc-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="946bc-132">Vous n’avez pas besoin de retourner chaque objet.</span><span class="sxs-lookup"><span data-stu-id="946bc-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="946bc-133">Si vous ne renvoyez pas d’objet, il sera récupéré par le garbage collector.</span><span class="sxs-lookup"><span data-stu-id="946bc-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="946bc-134">Lorsque <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> est utilisé et `T` implémente `IDisposable` :</span><span class="sxs-lookup"><span data-stu-id="946bc-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="946bc-135">Les éléments qui ne sont **pas** _ retournés au pool seront supprimés.</span><span class="sxs-lookup"><span data-stu-id="946bc-135">Items that are \* **not** _ returned to the pool will be disposed.</span></span>
<span data-ttu-id="946bc-136">_ Lorsque le pool est supprimé par DI, tous les éléments du pool sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="946bc-136">_ When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="946bc-137">Remarque : une fois le pool supprimé :</span><span class="sxs-lookup"><span data-stu-id="946bc-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="946bc-138">L’appel `Get` de lève une `ObjectDisposedException` .</span><span class="sxs-lookup"><span data-stu-id="946bc-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="946bc-139">`return` supprime l’élément donné.</span><span class="sxs-lookup"><span data-stu-id="946bc-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="946bc-140">Exemple ObjectPool</span><span class="sxs-lookup"><span data-stu-id="946bc-140">ObjectPool sample</span></span>

<span data-ttu-id="946bc-141">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="946bc-141">The following code:</span></span>

* <span data-ttu-id="946bc-142">Ajoute `ObjectPoolProvider` au conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="946bc-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="946bc-143">Ajoute et configure `ObjectPool<StringBuilder>` le conteneur di.</span><span class="sxs-lookup"><span data-stu-id="946bc-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="946bc-144">Ajoute le `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="946bc-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="946bc-145">Le code suivant implémente `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="946bc-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
