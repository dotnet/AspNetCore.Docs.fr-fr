---
title: Mise en cache distribuée dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser un cache ASP.NET Core distribué pour améliorer les performances et l’évolutivité des applications, en particulier dans un environnement de batterie de serveurs ou de Cloud.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 6d87c8de66bf5600189465b96dee903841106b6f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061143"
---
# <a name="distributed-caching-in-aspnet-core"></a>Mise en cache distribuée dans ASP.NET Core

Par [mohsin Nasir](https://github.com/mohsinnasir) et [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

Un cache distribué est un cache partagé par plusieurs serveurs d’applications, généralement géré comme un service externe pour les serveurs d’applications qui y accèdent. Un cache distribué peut améliorer les performances et l’extensibilité d’une application ASP.NET Core, en particulier lorsque l’application est hébergée par un service Cloud ou une batterie de serveurs.

Un cache distribué présente plusieurs avantages par rapport à d’autres scénarios de mise en cache où les données mises en cache sont stockées sur des serveurs d’applications individuels.

Lorsque les données mises en cache sont distribuées, les données :

* Est *cohérente* (cohérente) entre les demandes adressées à plusieurs serveurs.
* Survit aux redémarrages du serveur et aux déploiements d’applications.
* N’utilise pas la mémoire locale.

La configuration du cache distribué est spécifique à l’implémentation. Cet article explique comment configurer des caches distribués SQL Server et Redims. Des implémentations tierces sont également disponibles, telles que [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache sur GitHub](https://github.com/Alachisoft/NCache)). Quelle que soit l’implémentation sélectionnée, l’application interagit avec le cache à l’aide de l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Pour utiliser un SQL Server cache distribué, ajoutez une référence de package au package [Microsoft. extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Pour utiliser un cache redistribuable ReDim, ajoutez une référence de package au package [Microsoft. extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .

Pour utiliser le cache distribué NCache, ajoutez une référence de package au package [NCache. Microsoft. extensions. Caching. Open source](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .

## <a name="idistributedcache-interface"></a>Interface IDistributedCache

L' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fournit les méthodes suivantes pour manipuler des éléments dans l’implémentation de cache distribué :

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Accepte une clé de chaîne et récupère un élément mis en cache en tant que `byte[]` tableau s’il est trouvé dans le cache.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Ajoute un élément (en tant que `byte[]` tableau) au cache à l’aide d’une clé de chaîne.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Actualise un élément dans le cache en fonction de sa clé, en réinitialisant son délai d’expiration décalé (le cas échéant).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Supprime un élément de cache en fonction de sa clé de chaîne.

## <a name="establish-distributed-caching-services"></a>Établir des services de mise en cache distribuée

Inscrire une implémentation de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dans `Startup.ConfigureServices` . Les implémentations fournies par le Framework décrites dans cette rubrique sont les suivantes :

* [Cache mémoire distribuée](#distributed-memory-cache)
* [Cache de SQL Server distribué](#distributed-sql-server-cache)
* [Cache Redims distribué](#distributed-redis-cache)
* [Cache NCache distribué](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Cache mémoire distribuée

Le cache mémoire distribuée ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) est une implémentation fournie par le Framework de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> qui stocke des éléments en mémoire. Le cache mémoire distribuée n’est pas un cache distribué réel. Les éléments mis en cache sont stockés par l’instance de l’application sur le serveur sur lequel l’application s’exécute.

Le cache mémoire distribuée est une implémentation utile :

* Dans les scénarios de développement et de test.
* Lorsqu’un serveur unique est utilisé en production et que la consommation de mémoire n’est pas un problème. L’implémentation du cache de mémoire distribuée résume le stockage des données mises en cache. Il permet d’implémenter une solution de mise en cache distribuée réelle à l’avenir si plusieurs nœuds ou une tolérance de panne sont nécessaires.

L’exemple d’application utilise le cache de mémoire distribuée lorsque l’application est exécutée dans l’environnement de développement dans `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Cache de SQL Server distribué

L’implémentation du cache SQL Server distribué ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permet au cache distribué d’utiliser une base de données SQL Server comme magasin de stockage. Pour créer un SQL Server table d’éléments mis en cache dans une instance de SQL Server, vous pouvez utiliser l' `sql-cache` outil. L’outil crée une table avec le nom et le schéma que vous spécifiez.

Créez une table dans SQL Server en exécutant la `sql-cache create` commande. Indiquez l’instance de SQL Server ( `Data Source` ), la base de données ( `Initial Catalog` ), le schéma (par exemple, `dbo` ) et le nom de la table (par exemple, `TestCache` ) :

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Un message est consigné pour indiquer la réussite de l’outil :

```console
Table and index were created successfully.
```

La table créée par l' `sql-cache` outil a le schéma suivant :

![Table de cache SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Une application doit manipuler les valeurs de cache à l’aide d’une instance de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , et non d’un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

L’exemple d’application implémente <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> dans un environnement de non-développement dans `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Et éventuellement <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> et <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) sont généralement stockés en dehors du contrôle de code source (par exemple, stocké par le [Gestionnaire de secret](xref:security/app-secrets) ou dans *appsettings.json* / *appSettings. { Fichiers ENVIRONMENT}. JSON* ). La chaîne de connexion peut contenir des informations d’identification qui doivent être conservées hors des systèmes de contrôle de code source.

### <a name="distributed-redis-cache"></a>Cache Redims distribué

[Redims](https://redis.io/) est un magasin de données en mémoire Open source, qui est souvent utilisé comme un cache distribué.  Vous pouvez configurer un [cache redims Azure](https://azure.microsoft.com/services/cache/) pour une application ASP.net Core hébergée sur Azure et utiliser un cache redims Azure pour le développement local.

Une application configure l’implémentation du cache à l’aide d’une <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ).

Pour plus d’informations, consultez [Cache Azure pour Redis](/azure/azure-cache-for-redis/cache-overview).

Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/19542) pour une discussion sur les autres approches d’un cache redims local.

### <a name="distributed-ncache-cache"></a>Cache NCache distribué

[NCache](https://github.com/Alachisoft/NCache) est un cache distribué en mémoire Open source développé en mode natif dans .NET et .net core. NCache fonctionne localement et est configuré en tant que cluster de cache distribué pour une application ASP.NET Core s’exécutant dans Azure ou sur d’autres plateformes d’hébergement.

Pour installer et configurer NCache sur votre ordinateur local, consultez le [Guide de prise en main NCache pour Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Pour configurer NCache :

1. Installez [NuGet Open source de NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Configurez le cluster de cache dans [client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Ajoutez le code suivant à `Startup.ConfigureServices` :

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Utiliser le cache distribué

Pour utiliser l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, demandez une instance de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> à partir de n’importe quel constructeur de l’application. L’instance est fournie par l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).

Lorsque l’exemple d’application démarre, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> est injecté dans `Startup.Configure` . L’heure actuelle est mise en cache à l’aide <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> de (pour plus d’informations, consultez [hôte générique : IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)) :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

L’exemple d’application injecte <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dans le `IndexModel` pour une utilisation par la page d’index.

Chaque fois que la page d’index est chargée, la durée de mise en cache est vérifiée dans le cache `OnGetAsync` . Si l’heure mise en cache n’a pas expiré, l’heure est affichée. Si 20 secondes se sont écoulées depuis le dernier accès à l’heure de mise en cache (la dernière fois que cette page a été chargée), la page affiche le *temps mis en cache expiré* .

Mettez immédiatement à jour l’heure de mise en cache à l’heure actuelle en sélectionnant le bouton **réinitialisation du temps mis en cache** . Le bouton déclenche la `OnPostResetCachedTime` méthode de gestionnaire.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Il n’est pas nécessaire d’utiliser un singleton ou une durée de vie limitée pour <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> les instances (au moins pour les implémentations intégrées).
>
> Vous pouvez également créer une <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance de chaque fois que vous en aurez besoin au lieu d’utiliser l’injection de dépendances, mais la création d’une instance dans du code peut compliquer le test et violer le [principe des dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Recommandations

Lorsque vous décidez de l’implémentation qui convient <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> le mieux à votre application, tenez compte des points suivants :

* Infrastructure existante
* Exigences en matière de performances
* Coût
* Expérience de l’équipe

Les solutions de mise en cache s’appuient généralement sur le stockage en mémoire pour fournir une récupération rapide des données mises en cache, mais la mémoire est une ressource limitée et coûteuse à développer. Stocke uniquement les données couramment utilisées dans un cache.

En règle générale, un cache Redims fournit un débit plus élevé et une latence inférieure à celle d’un cache de SQL Server. Toutefois, l’évaluation est généralement requise pour déterminer les caractéristiques de performances des stratégies de mise en cache.

Lorsque SQL Server est utilisé en tant que magasin de stockage de cache distribué, l’utilisation de la même base de données pour le cache et du stockage et de la récupération de données ordinaires de l’application peut avoir un impact négatif sur les performances des deux. Nous vous recommandons d’utiliser une instance de SQL Server dédiée pour le magasin de stockage de cache distribué.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Cache redims sur Azure](/azure/azure-cache-for-redis/)
* [SQL Database sur Azure](/azure/sql-database/)
* [ASP.net Core fournisseur IDistributedCache pour NCache dans les batteries de serveurs Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache sur GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Un cache distribué est un cache partagé par plusieurs serveurs d’applications, généralement géré comme un service externe pour les serveurs d’applications qui y accèdent. Un cache distribué peut améliorer les performances et l’extensibilité d’une application ASP.NET Core, en particulier lorsque l’application est hébergée par un service Cloud ou une batterie de serveurs.

Un cache distribué présente plusieurs avantages par rapport à d’autres scénarios de mise en cache où les données mises en cache sont stockées sur des serveurs d’applications individuels.

Lorsque les données mises en cache sont distribuées, les données :

* Est *cohérente* (cohérente) entre les demandes adressées à plusieurs serveurs.
* Survit aux redémarrages du serveur et aux déploiements d’applications.
* N’utilise pas la mémoire locale.

La configuration du cache distribué est spécifique à l’implémentation. Cet article explique comment configurer des caches distribués SQL Server et Redims. Des implémentations tierces sont également disponibles, telles que [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache sur GitHub](https://github.com/Alachisoft/NCache)). Quelle que soit l’implémentation sélectionnée, l’application interagit avec le cache à l’aide de l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Pour utiliser un SQL Server cache distribué, référencez le [AspNetCore. app. app](xref:fundamentals/metapackage-app) ou ajoutez une référence de package au package [Microsoft. extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Pour utiliser un cache distribué Redims, référencez le package [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) et ajoutez une référence de package au package [Microsoft. extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) . Le package Redims n’étant pas inclus dans le `Microsoft.AspNetCore.App` package, vous devez référencer le package redims séparément dans votre fichier projet.

Pour utiliser le cache distribué NCache, référencez le logiciel [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) et ajoutez une référence de package au package [NCache. Microsoft. extensions. Caching. Open source](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . Le package NCache n’étant pas inclus dans le `Microsoft.AspNetCore.App` package, vous devez référencer le package NCache séparément dans votre fichier projet.

## <a name="idistributedcache-interface"></a>Interface IDistributedCache

L' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fournit les méthodes suivantes pour manipuler des éléments dans l’implémentation de cache distribué :

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Accepte une clé de chaîne et récupère un élément mis en cache en tant que `byte[]` tableau s’il est trouvé dans le cache.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Ajoute un élément (en tant que `byte[]` tableau) au cache à l’aide d’une clé de chaîne.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Actualise un élément dans le cache en fonction de sa clé, en réinitialisant son délai d’expiration décalé (le cas échéant).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Supprime un élément de cache en fonction de sa clé de chaîne.

## <a name="establish-distributed-caching-services"></a>Établir des services de mise en cache distribuée

Inscrire une implémentation de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dans `Startup.ConfigureServices` . Les implémentations fournies par le Framework décrites dans cette rubrique sont les suivantes :

* [Cache mémoire distribuée](#distributed-memory-cache)
* [Cache de SQL Server distribué](#distributed-sql-server-cache)
* [Cache Redims distribué](#distributed-redis-cache)
* [Cache NCache distribué](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Cache mémoire distribuée

Le cache mémoire distribuée ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) est une implémentation fournie par le Framework de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> qui stocke des éléments en mémoire. Le cache mémoire distribuée n’est pas un cache distribué réel. Les éléments mis en cache sont stockés par l’instance de l’application sur le serveur sur lequel l’application s’exécute.

Le cache mémoire distribuée est une implémentation utile :

* Dans les scénarios de développement et de test.
* Lorsqu’un serveur unique est utilisé en production et que la consommation de mémoire n’est pas un problème. L’implémentation du cache de mémoire distribuée résume le stockage des données mises en cache. Il permet d’implémenter une solution de mise en cache distribuée réelle à l’avenir si plusieurs nœuds ou une tolérance de panne sont nécessaires.

L’exemple d’application utilise le cache de mémoire distribuée lorsque l’application est exécutée dans l’environnement de développement dans `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Cache de SQL Server distribué

L’implémentation du cache SQL Server distribué ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permet au cache distribué d’utiliser une base de données SQL Server comme magasin de stockage. Pour créer un SQL Server table d’éléments mis en cache dans une instance de SQL Server, vous pouvez utiliser l' `sql-cache` outil. L’outil crée une table avec le nom et le schéma que vous spécifiez.

Créez une table dans SQL Server en exécutant la `sql-cache create` commande. Indiquez l’instance de SQL Server ( `Data Source` ), la base de données ( `Initial Catalog` ), le schéma (par exemple, `dbo` ) et le nom de la table (par exemple, `TestCache` ) :

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Un message est consigné pour indiquer la réussite de l’outil :

```console
Table and index were created successfully.
```

La table créée par l' `sql-cache` outil a le schéma suivant :

![Table de cache SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Une application doit manipuler les valeurs de cache à l’aide d’une instance de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , et non d’un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

L’exemple d’application implémente <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> dans un environnement de non-développement dans `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Et éventuellement <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> et <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) sont généralement stockés en dehors du contrôle de code source (par exemple, stocké par le [Gestionnaire de secret](xref:security/app-secrets) ou dans *appsettings.json* / *appSettings. { Fichiers ENVIRONMENT}. JSON* ). La chaîne de connexion peut contenir des informations d’identification qui doivent être conservées hors des systèmes de contrôle de code source.

### <a name="distributed-redis-cache"></a>Cache Redims distribué

[Redims](https://redis.io/) est un magasin de données en mémoire Open source, qui est souvent utilisé comme un cache distribué. Vous pouvez utiliser des ReDim localement, et vous pouvez configurer un [cache redims Azure](https://azure.microsoft.com/services/cache/) pour une application ASP.net Core hébergée sur Azure.

Une application configure l’implémentation du cache à l’aide d’une <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) dans un environnement de non-développement dans `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Pour installer les éléments ReDim sur votre ordinateur local :

1. Installez le [package redims en chocolat](https://chocolatey.org/packages/redis-64/).
1. Exécutez `redis-server` à partir d’une invite de commandes.

### <a name="distributed-ncache-cache"></a>Cache NCache distribué

[NCache](https://github.com/Alachisoft/NCache) est un cache distribué en mémoire Open source développé en mode natif dans .NET et .net core. NCache fonctionne localement et est configuré en tant que cluster de cache distribué pour une application ASP.NET Core s’exécutant dans Azure ou sur d’autres plateformes d’hébergement.

Pour installer et configurer NCache sur votre ordinateur local, consultez le [Guide de prise en main NCache pour Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Pour configurer NCache :

1. Installez [NuGet Open source de NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Configurez le cluster de cache dans [client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Ajoutez le code suivant à `Startup.ConfigureServices` :

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Utiliser le cache distribué

Pour utiliser l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, demandez une instance de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> à partir de n’importe quel constructeur de l’application. L’instance est fournie par l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).

Lorsque l’exemple d’application démarre, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> est injecté dans `Startup.Configure` . L’heure actuelle est mise en cache à l’aide <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> de (pour plus d’informations, consultez [hôte Web : IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

L’exemple d’application injecte <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dans le `IndexModel` pour une utilisation par la page d’index.

Chaque fois que la page d’index est chargée, la durée de mise en cache est vérifiée dans le cache `OnGetAsync` . Si l’heure mise en cache n’a pas expiré, l’heure est affichée. Si 20 secondes se sont écoulées depuis le dernier accès à l’heure de mise en cache (la dernière fois que cette page a été chargée), la page affiche le *temps mis en cache expiré* .

Mettez immédiatement à jour l’heure de mise en cache à l’heure actuelle en sélectionnant le bouton **réinitialisation du temps mis en cache** . Le bouton déclenche la `OnPostResetCachedTime` méthode de gestionnaire.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Il n’est pas nécessaire d’utiliser un singleton ou une durée de vie limitée pour <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> les instances (au moins pour les implémentations intégrées).
>
> Vous pouvez également créer une <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance de chaque fois que vous en aurez besoin au lieu d’utiliser l’injection de dépendances, mais la création d’une instance dans du code peut compliquer le test et violer le [principe des dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Recommandations

Lorsque vous décidez de l’implémentation qui convient <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> le mieux à votre application, tenez compte des points suivants :

* Infrastructure existante
* Exigences en matière de performances
* Coût
* Expérience de l’équipe

Les solutions de mise en cache s’appuient généralement sur le stockage en mémoire pour fournir une récupération rapide des données mises en cache, mais la mémoire est une ressource limitée et coûteuse à développer. Stocke uniquement les données couramment utilisées dans un cache.

En règle générale, un cache Redims fournit un débit plus élevé et une latence inférieure à celle d’un cache de SQL Server. Toutefois, l’évaluation est généralement requise pour déterminer les caractéristiques de performances des stratégies de mise en cache.

Lorsque SQL Server est utilisé en tant que magasin de stockage de cache distribué, l’utilisation de la même base de données pour le cache et du stockage et de la récupération de données ordinaires de l’application peut avoir un impact négatif sur les performances des deux. Nous vous recommandons d’utiliser une instance de SQL Server dédiée pour le magasin de stockage de cache distribué.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Cache redims sur Azure](/azure/azure-cache-for-redis/)
* [SQL Database sur Azure](/azure/sql-database/)
* [ASP.net Core fournisseur IDistributedCache pour NCache dans les batteries de serveurs Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache sur GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Un cache distribué est un cache partagé par plusieurs serveurs d’applications, généralement géré comme un service externe pour les serveurs d’applications qui y accèdent. Un cache distribué peut améliorer les performances et l’extensibilité d’une application ASP.NET Core, en particulier lorsque l’application est hébergée par un service Cloud ou une batterie de serveurs.

Un cache distribué présente plusieurs avantages par rapport à d’autres scénarios de mise en cache où les données mises en cache sont stockées sur des serveurs d’applications individuels.

Lorsque les données mises en cache sont distribuées, les données :

* Est *cohérente* (cohérente) entre les demandes adressées à plusieurs serveurs.
* Survit aux redémarrages du serveur et aux déploiements d’applications.
* N’utilise pas la mémoire locale.

La configuration du cache distribué est spécifique à l’implémentation. Cet article explique comment configurer des caches distribués SQL Server et Redims. Des implémentations tierces sont également disponibles, telles que [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache sur GitHub](https://github.com/Alachisoft/NCache)). Quelle que soit l’implémentation sélectionnée, l’application interagit avec le cache à l’aide de l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Pour utiliser un SQL Server cache distribué, référencez le [AspNetCore. app. app](xref:fundamentals/metapackage-app) ou ajoutez une référence de package au package [Microsoft. extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Pour utiliser un cache redistribuable ReDim, référencez le package [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) et ajoutez une référence de package au package [Microsoft. extensions. Caching. redims](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) . Le package Redims n’étant pas inclus dans le `Microsoft.AspNetCore.App` package, vous devez référencer le package redims séparément dans votre fichier projet.

Pour utiliser le cache distribué NCache, référencez le logiciel [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) et ajoutez une référence de package au package [NCache. Microsoft. extensions. Caching. Open source](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . Le package NCache n’étant pas inclus dans le `Microsoft.AspNetCore.App` package, vous devez référencer le package NCache séparément dans votre fichier projet.

## <a name="idistributedcache-interface"></a>Interface IDistributedCache

L' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fournit les méthodes suivantes pour manipuler des éléments dans l’implémentation de cache distribué :

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Accepte une clé de chaîne et récupère un élément mis en cache en tant que `byte[]` tableau s’il est trouvé dans le cache.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Ajoute un élément (en tant que `byte[]` tableau) au cache à l’aide d’une clé de chaîne.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Actualise un élément dans le cache en fonction de sa clé, en réinitialisant son délai d’expiration décalé (le cas échéant).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Supprime un élément de cache en fonction de sa clé de chaîne.

## <a name="establish-distributed-caching-services"></a>Établir des services de mise en cache distribuée

Inscrire une implémentation de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dans `Startup.ConfigureServices` . Les implémentations fournies par le Framework décrites dans cette rubrique sont les suivantes :

* [Cache mémoire distribuée](#distributed-memory-cache)
* [Cache de SQL Server distribué](#distributed-sql-server-cache)
* [Cache Redims distribué](#distributed-redis-cache)
* [Cache NCache distribué](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Cache mémoire distribuée

Le cache mémoire distribuée ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) est une implémentation fournie par le Framework de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> qui stocke des éléments en mémoire. Le cache mémoire distribuée n’est pas un cache distribué réel. Les éléments mis en cache sont stockés par l’instance de l’application sur le serveur sur lequel l’application s’exécute.

Le cache mémoire distribuée est une implémentation utile :

* Dans les scénarios de développement et de test.
* Lorsqu’un serveur unique est utilisé en production et que la consommation de mémoire n’est pas un problème. L’implémentation du cache de mémoire distribuée résume le stockage des données mises en cache. Il permet d’implémenter une solution de mise en cache distribuée réelle à l’avenir si plusieurs nœuds ou une tolérance de panne sont nécessaires.

L’exemple d’application utilise le cache de mémoire distribuée lorsque l’application est exécutée dans l’environnement de développement dans `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Cache de SQL Server distribué

L’implémentation du cache SQL Server distribué ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permet au cache distribué d’utiliser une base de données SQL Server comme magasin de stockage. Pour créer un SQL Server table d’éléments mis en cache dans une instance de SQL Server, vous pouvez utiliser l' `sql-cache` outil. L’outil crée une table avec le nom et le schéma que vous spécifiez.

Créez une table dans SQL Server en exécutant la `sql-cache create` commande. Indiquez l’instance de SQL Server ( `Data Source` ), la base de données ( `Initial Catalog` ), le schéma (par exemple, `dbo` ) et le nom de la table (par exemple, `TestCache` ) :

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Un message est consigné pour indiquer la réussite de l’outil :

```console
Table and index were created successfully.
```

La table créée par l' `sql-cache` outil a le schéma suivant :

![Table de cache SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Une application doit manipuler les valeurs de cache à l’aide d’une instance de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , et non d’un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

L’exemple d’application implémente <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> dans un environnement de non-développement dans `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Et éventuellement <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> et <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) sont généralement stockés en dehors du contrôle de code source (par exemple, stocké par le [Gestionnaire de secret](xref:security/app-secrets) ou dans *appsettings.json* / *appSettings. { Fichiers ENVIRONMENT}. JSON* ). La chaîne de connexion peut contenir des informations d’identification qui doivent être conservées hors des systèmes de contrôle de code source.

### <a name="distributed-redis-cache"></a>Cache Redims distribué

[Redims](https://redis.io/) est un magasin de données en mémoire Open source, qui est souvent utilisé comme un cache distribué. Vous pouvez utiliser des ReDim localement, et vous pouvez configurer un [cache redims Azure](https://azure.microsoft.com/services/cache/) pour une application ASP.net Core hébergée sur Azure.

Une application configure l’implémentation du cache à l’aide d’une <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ) :

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

Pour installer les éléments ReDim sur votre ordinateur local :

1. Installez le [package redims en chocolat](https://chocolatey.org/packages/redis-64/).
1. Exécutez `redis-server` à partir d’une invite de commandes.

### <a name="distributed-ncache-cache"></a>Cache NCache distribué

[NCache](https://github.com/Alachisoft/NCache) est un cache distribué en mémoire Open source développé en mode natif dans .NET et .net core. NCache fonctionne localement et est configuré en tant que cluster de cache distribué pour une application ASP.NET Core s’exécutant dans Azure ou sur d’autres plateformes d’hébergement.

Pour installer et configurer NCache sur votre ordinateur local, consultez le [Guide de prise en main NCache pour Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Pour configurer NCache :

1. Installez [NuGet Open source de NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Configurez le cluster de cache dans [client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Ajoutez le code suivant à `Startup.ConfigureServices` :

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Utiliser le cache distribué

Pour utiliser l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, demandez une instance de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> à partir de n’importe quel constructeur de l’application. L’instance est fournie par l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).

Lorsque l’exemple d’application démarre, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> est injecté dans `Startup.Configure` . L’heure actuelle est mise en cache à l’aide <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> de (pour plus d’informations, consultez [hôte Web : IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

L’exemple d’application injecte <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dans le `IndexModel` pour une utilisation par la page d’index.

Chaque fois que la page d’index est chargée, la durée de mise en cache est vérifiée dans le cache `OnGetAsync` . Si l’heure mise en cache n’a pas expiré, l’heure est affichée. Si 20 secondes se sont écoulées depuis le dernier accès à l’heure de mise en cache (la dernière fois que cette page a été chargée), la page affiche le *temps mis en cache expiré* .

Mettez immédiatement à jour l’heure de mise en cache à l’heure actuelle en sélectionnant le bouton **réinitialisation du temps mis en cache** . Le bouton déclenche la `OnPostResetCachedTime` méthode de gestionnaire.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Il n’est pas nécessaire d’utiliser un singleton ou une durée de vie limitée pour <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> les instances (au moins pour les implémentations intégrées).
>
> Vous pouvez également créer une <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance de chaque fois que vous en aurez besoin au lieu d’utiliser l’injection de dépendances, mais la création d’une instance dans du code peut compliquer le test et violer le [principe des dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Recommandations

Lorsque vous décidez de l’implémentation qui convient <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> le mieux à votre application, tenez compte des points suivants :

* Infrastructure existante
* Exigences en matière de performances
* Coût
* Expérience de l’équipe

Les solutions de mise en cache s’appuient généralement sur le stockage en mémoire pour fournir une récupération rapide des données mises en cache, mais la mémoire est une ressource limitée et coûteuse à développer. Stocke uniquement les données couramment utilisées dans un cache.

En règle générale, un cache Redims fournit un débit plus élevé et une latence inférieure à celle d’un cache de SQL Server. Toutefois, l’évaluation est généralement requise pour déterminer les caractéristiques de performances des stratégies de mise en cache.

Lorsque SQL Server est utilisé en tant que magasin de stockage de cache distribué, l’utilisation de la même base de données pour le cache et du stockage et de la récupération de données ordinaires de l’application peut avoir un impact négatif sur les performances des deux. Nous vous recommandons d’utiliser une instance de SQL Server dédiée pour le magasin de stockage de cache distribué.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Cache redims sur Azure](/azure/azure-cache-for-redis/)
* [SQL Database sur Azure](/azure/sql-database/)
* [ASP.net Core fournisseur IDistributedCache pour NCache dans les batteries de serveurs Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache sur GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
