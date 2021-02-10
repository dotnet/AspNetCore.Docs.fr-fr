---
title: Cache en mémoire dans ASP.NET Core
author: rick-anderson
description: Découvrez comment mettre en cache les données en mémoire dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
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
uid: performance/caching/memory
ms.openlocfilehash: 19e8dc0ae4d5f8fd28d03d5be87c0b1bbf32d940
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107218"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Cache en mémoire dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo)et [Steve Smith](https://ardalis.com/)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Bases de la mise en cache

La mise en cache peut améliorer considérablement les performances et l’extensibilité d’une application en réduisant le travail requis pour générer du contenu. La mise en cache fonctionne mieux avec les données qui changent rarement **et** qui sont coûteuses à générer. La mise en cache crée une copie des données qui peuvent être retournées beaucoup plus rapidement qu’à partir de la source. Les applications doivent être écrites et testées pour **ne** pas dépendre des données mises en cache.

ASP.NET Core prend en charge plusieurs caches différents. Le cache le plus simple est basé sur [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). `IMemoryCache` représente un cache stocké dans la mémoire du serveur Web. Les applications qui s’exécutent sur une batterie de serveurs (plusieurs serveurs) doivent s’assurer que les sessions sont permanentes lors de l’utilisation du cache en mémoire. Les sessions rémanentes garantissent que les demandes suivantes d’un client sont toutes dirigées vers le même serveur. Par exemple, Azure Web Apps utilise [application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (arr) pour acheminer toutes les requêtes suivantes vers le même serveur.

Les sessions non rémanentes dans une batterie de serveurs Web requièrent un [cache distribué](distributed.md) pour éviter les problèmes de cohérence du cache. Pour certaines applications, un cache distribué peut prendre en charge une montée en puissance parallèle supérieure à celle d’un cache en mémoire. L’utilisation d’un cache distribué décharge la mémoire cache dans un processus externe.

Le cache en mémoire peut stocker n’importe quel objet. L’interface du cache distribué est limitée à `byte[]` . Les éléments du cache de stockage du cache distribué et en mémoire sont des paires clé-valeur.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Package NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) peut être utilisé avec :

* .NET Standard 2,0 ou version ultérieure.
* Toute [implémentation .net](/dotnet/standard/net-standard#net-implementation-support) qui cible .NET standard 2,0 ou version ultérieure. Par exemple, ASP.NET Core 2,0 ou version ultérieure.
* .NET Framework 4.5 ou ultérieur.

[Microsoft. extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (décrit dans cet article) est recommandé `System.Runtime.Caching` / `MemoryCache` , car il est mieux intégré à ASP.net core. Par exemple, `IMemoryCache` fonctionne en mode natif avec ASP.net Core [injection de dépendances](xref:fundamentals/dependency-injection).

`System.Runtime.Caching` / `MemoryCache` À utiliser en tant que pont de compatibilité lors du Portage du code de ASP.net 4. x vers ASP.net core.

## <a name="cache-guidelines"></a>Instructions du cache

* Le code doit toujours avoir une option de secours pour extraire les données et **ne pas** dépendre d’une valeur mise en cache disponible.
* Le cache utilise une ressource rare, la mémoire. Limiter la croissance du cache :
  * N’utilisez **pas** d’entrée externe comme clés de cache.
  * Utilisez des expirations pour limiter la croissance du cache.
  * [Utilisez les paramètres, size et SizeLimit pour limiter la taille du cache](#use-setsize-size-and-sizelimit-to-limit-cache-size). Le runtime ASP.NET Core ne limite **pas** la taille du cache en fonction de la sollicitation de la mémoire. C’est au développeur de limiter la taille du cache.

## <a name="use-imemorycache"></a>Utiliser IMemoryCache

> [!WARNING]
> L’utilisation d’un cache mémoire *partagé* à partir d’une [injection de dépendance](xref:fundamentals/dependency-injection) et l’appel `SetSize` `Size` de, ou `SizeLimit` pour limiter la taille du cache peuvent entraîner l’échec de l’application. Quand une limite de taille est définie sur un cache, toutes les entrées doivent spécifier une taille lors de leur ajout. Cela peut entraîner des problèmes, car les développeurs n’ont peut-être pas un contrôle total sur ce qui utilise le cache partagé. Par exemple, Entity Framework Core utilise le cache partagé et ne spécifie pas de taille. Si une application définit une limite de taille de cache et utilise EF Core, l’application lève une exception `InvalidOperationException` .
> Lorsque `SetSize` vous utilisez, `Size` ou `SizeLimit` pour limiter le cache, créez un singleton de cache pour la mise en cache. Pour plus d’informations et pour obtenir un exemple, consultez utiliser la configuration, la [taille et SizeLimit pour limiter la taille du cache](#use-setsize-size-and-sizelimit-to-limit-cache-size).
> Un cache partagé est un cache partagé par d’autres infrastructures ou bibliothèques. Par exemple, EF Core utilise le cache partagé et ne spécifie pas de taille. 

La mise en cache en mémoire est un *service* référencé à partir d’une application à l’aide de l' [injection de dépendances](xref:fundamentals/dependency-injection). Demandez l' `IMemoryCache` instance dans le constructeur :

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

Le code suivant utilise [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) pour vérifier si une heure se trouve dans le cache. Si une heure n’est pas mise en cache, une nouvelle entrée est créée et ajoutée au cache avec [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_). La `CacheKeys` classe fait partie de l’exemple Download.

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

L’heure actuelle et l’heure de mise en cache sont affichées :

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

Le code suivant utilise la méthode d’extension [Set pour mettre](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_System_TimeSpan_) en cache des données pour une heure relative sans créer l' `MemoryCacheEntryOptions` objet.
[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_set)]

La valeur mise en cache `DateTime` reste dans le cache pendant qu’il y a des requêtes dans le délai imparti.

Le code suivant utilise [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) et [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) pour mettre en cache des données.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Le code [suivant appelle la commande pour récupérer le](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) temps mis en cache :

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

Le code suivant obtient ou crée un élément mis en cache avec une expiration absolue :

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

Un ensemble d’éléments mis en cache avec une expiration décalée est à risque de devenir obsolète. Si l’accès est plus fréquent que l’intervalle d’expiration décalé, l’élément n’expire jamais. Combinez une expiration décalée avec une expiration absolue pour garantir que l’élément expire une fois que son heure d’expiration absolue s’est écoulée. L’expiration absolue définit une limite supérieure à la durée pendant laquelle l’élément peut être mis en cache tout en autorisant l’expiration antérieure de l’élément s’il n’est pas demandé dans l’intervalle d’expiration décalé. Lorsque les expirations absolues et décalées sont spécifiées, les expirations sont logiquement associées. Si l’intervalle d’expiration décalé *ou* le délai d’expiration absolu est écoulé, l’élément est supprimé du cache.

Le code suivant obtient ou crée un élément mis en cache avec l’expiration décalée *et* absolue :

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

Le code précédent garantit que les données ne seront pas mises en cache plus longtemps que l’heure absolue.

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> et <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> sont des méthodes d’extension dans la <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> classe. Ces méthodes étendent la capacité de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

L’exemple suivant :

* Définit une heure d’expiration décalée. Les requêtes qui accèdent à cet élément mis en cache réinitialisent l’horloge d’expiration décalée.
* Définit la priorité du cache sur [CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).
* Définit un [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) qui sera appelé une fois que l’entrée est supprimée du cache. Le rappel est exécuté sur un thread différent du code qui supprime l’élément du cache.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Utiliser la valeur de Size, size et SizeLimit pour limiter la taille du cache

Une `MemoryCache` instance peut éventuellement spécifier et appliquer une limite de taille. La limite de taille du cache n’a pas d’unité de mesure définie car le cache n’a pas de mécanisme pour mesurer la taille des entrées. Si la limite de taille du cache est définie, toutes les entrées doivent spécifier la taille. Le runtime ASP.NET Core ne limite pas la taille du cache en fonction de la sollicitation de la mémoire. C’est au développeur de limiter la taille du cache. La taille spécifiée est dans les unités choisies par le développeur.

Par exemple :

* Si l’application Web a principalement mis en cache des chaînes, chaque taille d’entrée de cache peut être la longueur de chaîne.
* L’application peut spécifier la taille de toutes les entrées en tant que 1, et la limite de taille est le nombre d’entrées.

Si <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> n’est pas défini, le cache croît sans limite. Le runtime ASP.NET Core ne supprime pas le cache lorsque la mémoire système est insuffisante. Les applications doivent être conçues pour :

* Limiter la croissance du cache.
* Appelez <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> ou <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> lorsque la mémoire disponible est limitée :

Le code suivant crée une taille fixe sans unité <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible par [injection de dépendances](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` n’a pas d’unités. Les entrées mises en cache doivent spécifier la taille des unités qu’elles estiment le plus approprié si la limite de taille du cache a été définie. Tous les utilisateurs d’une instance de cache doivent utiliser le même système d’unité. Une entrée ne sera pas mise en cache si la somme des tailles d’entrée mises en cache dépasse la valeur spécifiée par `SizeLimit` . Si aucune limite de taille du cache n’est définie, la taille du cache définie sur l’entrée sera ignorée.

Le code suivant s’inscrit `MyMemoryCache` auprès du conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

`MyMemoryCache` est créé en tant que cache mémoire indépendant pour les composants qui connaissent ce cache de taille limitée et savent comment définir la taille d’entrée du cache de manière appropriée.

Le code suivant utilise `MyMemoryCache` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

La taille de l’entrée de cache peut être définie par <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> ou par les <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> méthodes d’extension :

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. compact

`MemoryCache.Compact` tente de supprimer le pourcentage spécifié du cache dans l’ordre suivant :

* Tous les éléments arrivés à expiration.
* Éléments par priorité. Les éléments de priorité la plus basse sont supprimés en premier.
* Objets utilisés le moins récemment.
* Éléments avec l’expiration absolue la plus ancienne.
* Éléments avec l’expiration décalée la plus ancienne.

Les éléments épinglés avec priorité <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> ne sont jamais supprimés. Le code suivant supprime un élément de cache et appelle `Compact` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Pour plus d’informations, consultez [source compact sur GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Dépendances de cache

L’exemple suivant montre comment faire expirer une entrée de cache en cas d’expiration d’une entrée dépendante. Un <xref:Microsoft.Extensions.Primitives.CancellationChangeToken> est ajouté à l’élément mis en cache. Lorsque `Cancel` est appelé sur `CancellationTokenSource` , les deux entrées du cache sont supprimées.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

L’utilisation de <xref:System.Threading.CancellationTokenSource> permet de supprimer plusieurs entrées de cache en tant que groupe. Avec le `using` modèle dans le code ci-dessus, les entrées de cache créées à l’intérieur du `using` bloc héritent des déclencheurs et des paramètres d’expiration.

## <a name="additional-notes"></a>Remarques supplémentaires

* L’expiration ne se produit pas en arrière-plan. Il n’existe pas de minuteur qui analyse activement le cache à la recherche d’éléments arrivés à expiration. Toute activité sur le cache ( `Get` , `Set` , `Remove` ) peut déclencher une analyse en arrière-plan des éléments arrivés à expiration. Un minuteur sur `CancellationTokenSource` ( <xref:System.Threading.CancellationTokenSource.CancelAfter*> ) supprime également l’entrée et déclenche une analyse des éléments arrivés à expiration. L’exemple suivant utilise [CancellationTokenSource (TimeSpan)](/dotnet/api/system.threading.cancellationtokensource.-ctor) pour le jeton inscrit. Quand ce jeton est activé, il supprime immédiatement l’entrée et déclenche les rappels d’éviction :

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* Lors de l’utilisation d’un rappel pour remplir à nouveau un élément de cache :

  * Plusieurs demandes peuvent trouver la valeur de clé mise en cache vide, car le rappel n’est pas terminé.
  * Cela peut entraîner le remplissage de plusieurs threads de l’élément mis en cache.

* Lorsqu’une entrée de cache est utilisée pour en créer une autre, l’enfant copie les jetons d’expiration de l’entrée parente et les paramètres d’expiration basés sur la durée. L’enfant n’a pas expiré par la suppression ou la mise à jour manuelle de l’entrée parente.

* Utilisez <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> pour définir les rappels qui seront déclenchés après que l’entrée du cache a été supprimée du cache.
* Pour la plupart des applications, `IMemoryCache` est activé. Par exemple, l’appel de `AddMvc` , `AddControllersWithViews` , `AddRazorPages` , `AddMvcCore().AddRazorViewEngine` , et `Add{Service}` de nombreuses autres méthodes dans `ConfigureServices` , active `IMemoryCache` . Pour les applications qui n’appellent pas l’une des `Add{Service}` méthodes précédentes, il peut être nécessaire d’appeler <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> dans `ConfigureServices` .

## <a name="background-cache-update"></a>Mise à jour du cache en arrière-plan

Utilisez un [service d’arrière-plan](xref:fundamentals/host/hosted-services) tel que <xref:Microsoft.Extensions.Hosting.IHostedService> pour mettre à jour le cache. Le service d’arrière-plan peut recalculer les entrées, puis les assigner au cache uniquement lorsqu’elles sont prêtes.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
Par [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo)et [Steve Smith](https://ardalis.com/)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Bases de la mise en cache

La mise en cache peut améliorer considérablement les performances et l’extensibilité d’une application en réduisant le travail requis pour générer du contenu. La mise en cache fonctionne mieux avec les données qui changent rarement. La mise en cache crée une copie des données qui peuvent être retournées beaucoup plus rapidement qu’à partir de la source d’origine. Le code doit être écrit et testé pour **ne** pas dépendre des données mises en cache.

ASP.NET Core prend en charge plusieurs caches différents. Le cache le plus simple est basé sur [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), qui représente un cache stocké dans la mémoire du serveur Web. Les applications qui s’exécutent sur une batterie de serveurs (plusieurs serveurs) doivent s’assurer que les sessions sont permanentes lors de l’utilisation du cache en mémoire. Les sessions rémanentes garantissent que les demandes ultérieures provenant d’un client sont toutes dirigées vers le même serveur. Par exemple, Azure Web Apps utilise [application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (arr) pour acheminer toutes les demandes d’un agent utilisateur vers le même serveur.

Les sessions non rémanentes dans une batterie de serveurs Web requièrent un [cache distribué](distributed.md) pour éviter les problèmes de cohérence du cache. Pour certaines applications, un cache distribué peut prendre en charge une montée en puissance parallèle supérieure à celle d’un cache en mémoire. L’utilisation d’un cache distribué décharge la mémoire cache dans un processus externe.

Le cache en mémoire peut stocker n’importe quel objet. L’interface du cache distribué est limitée à `byte[]` . Les éléments du cache de stockage du cache distribué et en mémoire sont des paires clé-valeur.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Package NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) peut être utilisé avec :

* .NET Standard 2,0 ou version ultérieure.
* Toute [implémentation .net](/dotnet/standard/net-standard#net-implementation-support) qui cible .NET standard 2,0 ou version ultérieure. Par exemple, ASP.NET Core 2,0 ou version ultérieure.
* .NET Framework 4.5 ou ultérieur.

[Microsoft. extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (décrit dans cet article) est recommandé `System.Runtime.Caching` / `MemoryCache` , car il est mieux intégré à ASP.net core. Par exemple, `IMemoryCache` fonctionne en mode natif avec ASP.net Core [injection de dépendances](xref:fundamentals/dependency-injection).

`System.Runtime.Caching` / `MemoryCache` À utiliser en tant que pont de compatibilité lors du Portage du code de ASP.net 4. x vers ASP.net core.

## <a name="cache-guidelines"></a>Instructions du cache

* Le code doit toujours avoir une option de secours pour extraire les données et **ne pas** dépendre d’une valeur mise en cache disponible.
* Le cache utilise une ressource rare, la mémoire. Limiter la croissance du cache :
  * N’utilisez **pas** d’entrée externe comme clés de cache.
  * Utilisez des expirations pour limiter la croissance du cache.
  * [Utilisez les paramètres, size et SizeLimit pour limiter la taille du cache](#use-setsize-size-and-sizelimit-to-limit-cache-size). Le runtime ASP.NET Core ne limite pas la taille du cache en fonction de la sollicitation de la mémoire. C’est au développeur de limiter la taille du cache.

## <a name="using-imemorycache"></a>Utilisation de IMemoryCache

> [!WARNING]
> L’utilisation d’un cache mémoire *partagé* à partir d’une [injection de dépendance](xref:fundamentals/dependency-injection) et l’appel `SetSize` `Size` de, ou `SizeLimit` pour limiter la taille du cache peuvent entraîner l’échec de l’application. Quand une limite de taille est définie sur un cache, toutes les entrées doivent spécifier une taille lors de leur ajout. Cela peut entraîner des problèmes, car les développeurs n’ont peut-être pas un contrôle total sur ce qui utilise le cache partagé. Par exemple, Entity Framework Core utilise le cache partagé et ne spécifie pas de taille. Si une application définit une limite de taille de cache et utilise EF Core, l’application lève une exception `InvalidOperationException` .
> Lorsque `SetSize` vous utilisez, `Size` ou `SizeLimit` pour limiter le cache, créez un singleton de cache pour la mise en cache. Pour plus d’informations et pour obtenir un exemple, consultez utiliser la configuration, la [taille et SizeLimit pour limiter la taille du cache](#use-setsize-size-and-sizelimit-to-limit-cache-size).

La mise en cache en mémoire est un *service* qui est référencé à partir de votre application à l’aide de l' [injection de dépendances](../../fundamentals/dependency-injection.md). Appeler `AddMemoryCache` dans `ConfigureServices` :

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Demandez l' `IMemoryCache` instance dans le constructeur :

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

`IMemoryCache` requiert le package NuGet [Microsoft. extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), qui est disponible dans le [produit Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Le code suivant utilise [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) pour vérifier si une heure se trouve dans le cache. Si une heure n’est pas mise en cache, une nouvelle entrée est créée et ajoutée au cache avec [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

L’heure actuelle et l’heure de mise en cache sont affichées :

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

La valeur mise en cache `DateTime` reste dans le cache pendant qu’il y a des requêtes dans le délai imparti. L’illustration suivante montre l’heure actuelle et une ancienne heure extraite du cache :

![Vue d’index avec deux heures différentes affichées](memory/_static/time.png)

Le code suivant utilise [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) et [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) pour mettre en cache des données.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Le code [suivant appelle la commande pour récupérer le](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) temps mis en cache :

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> et sont [](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) des méthodes d’extension qui font partie de la classe [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) qui étend la fonctionnalité de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Pour obtenir une description des autres méthodes de cache, consultez [méthodes IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) et [méthodes CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) .

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

L’exemple suivant :

* Définit une heure d’expiration décalée. Les requêtes qui accèdent à cet élément mis en cache réinitialisent l’horloge d’expiration décalée.
* Affecte à la priorité de cache la valeur `CacheItemPriority.NeverRemove` .
* Définit un [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) qui sera appelé une fois que l’entrée est supprimée du cache. Le rappel est exécuté sur un thread différent du code qui supprime l’élément du cache.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Utiliser la valeur de Size, size et SizeLimit pour limiter la taille du cache

Une `MemoryCache` instance peut éventuellement spécifier et appliquer une limite de taille. La limite de taille du cache n’a pas d’unité de mesure définie car le cache n’a pas de mécanisme pour mesurer la taille des entrées. Si la limite de taille du cache est définie, toutes les entrées doivent spécifier la taille. Le runtime ASP.NET Core ne limite pas la taille du cache en fonction de la sollicitation de la mémoire. C’est au développeur de limiter la taille du cache. La taille spécifiée est dans les unités choisies par le développeur.

Par exemple :

* Si l’application Web a principalement mis en cache des chaînes, chaque taille d’entrée de cache peut être la longueur de chaîne.
* L’application peut spécifier la taille de toutes les entrées en tant que 1, et la limite de taille est le nombre d’entrées.

Si <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> n’est pas défini, le cache croît sans limite. Le runtime ASP.NET Core ne supprime pas le cache lorsque la mémoire système est insuffisante. Les applications sont en grande partie conçues pour :

* Limiter la croissance du cache.
* Appelez <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> ou <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> lorsque la mémoire disponible est limitée :

Le code suivant crée une taille fixe sans unité <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible par [injection de dépendances](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` n’a pas d’unités. Les entrées mises en cache doivent spécifier la taille des unités qu’elles estiment le plus approprié si la limite de taille du cache a été définie. Tous les utilisateurs d’une instance de cache doivent utiliser le même système d’unité. Une entrée ne sera pas mise en cache si la somme des tailles d’entrée mises en cache dépasse la valeur spécifiée par `SizeLimit` . Si aucune limite de taille du cache n’est définie, la taille du cache définie sur l’entrée sera ignorée.

Le code suivant s’inscrit `MyMemoryCache` auprès du conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` est créé en tant que cache mémoire indépendant pour les composants qui connaissent ce cache de taille limitée et savent comment définir la taille d’entrée du cache de manière appropriée.

Le code suivant utilise `MyMemoryCache` :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

La taille de l’entrée de cache peut être définie par [taille](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) ou par la méthode d’extension de [définition](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. compact

`MemoryCache.Compact` tente de supprimer le pourcentage spécifié du cache dans l’ordre suivant :

* Tous les éléments arrivés à expiration.
* Éléments par priorité. Les éléments de priorité la plus basse sont supprimés en premier.
* Objets utilisés le moins récemment.
* Éléments avec l’expiration absolue la plus ancienne.
* Éléments avec l’expiration décalée la plus ancienne.

Les éléments épinglés avec priorité <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> ne sont jamais supprimés.

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Pour plus d’informations, consultez [source compact sur GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Dépendances de cache

L’exemple suivant montre comment faire expirer une entrée de cache en cas d’expiration d’une entrée dépendante. Un <xref:Microsoft.Extensions.Primitives.CancellationChangeToken> est ajouté à l’élément mis en cache. Lorsque `Cancel` est appelé sur `CancellationTokenSource` , les deux entrées du cache sont supprimées.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

L’utilisation de `CancellationTokenSource` permet de supprimer plusieurs entrées de cache en tant que groupe. Avec le `using` modèle dans le code ci-dessus, les entrées de cache créées à l’intérieur du `using` bloc héritent des déclencheurs et des paramètres d’expiration.

## <a name="additional-notes"></a>Remarques supplémentaires

* Lors de l’utilisation d’un rappel pour remplir à nouveau un élément de cache :

  * Plusieurs demandes peuvent trouver la valeur de clé mise en cache vide, car le rappel n’est pas terminé.
  * Cela peut entraîner le remplissage de plusieurs threads de l’élément mis en cache.

* Lorsqu’une entrée de cache est utilisée pour en créer une autre, l’enfant copie les jetons d’expiration de l’entrée parente et les paramètres d’expiration basés sur la durée. L’enfant n’a pas expiré par la suppression ou la mise à jour manuelle de l’entrée parente.

* Utilisez [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) pour définir les rappels qui seront déclenchés après que l’entrée du cache a été supprimée du cache.

## <a name="background-cache-update"></a>Mise à jour du cache en arrière-plan

Utilisez un [service d’arrière-plan](xref:fundamentals/host/hosted-services) tel que <xref:Microsoft.Extensions.Hosting.IHostedService> pour mettre à jour le cache. Le service d’arrière-plan peut recalculer les entrées, puis les assigner au cache uniquement lorsqu’elles sont prêtes.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
