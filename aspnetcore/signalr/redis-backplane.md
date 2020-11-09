---
title: 'Panier ReDim pour ASP.NET Core SignalR montée en puissance parallèle'
author: bradygaster
description: 'Découvrez comment configurer un backplane ReDim pour permettre la montée en charge d’une SignalR application ASP.net core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/redis-backplane
ms.openlocfilehash: e92f515b82b8ee76f98eaa1fca51feb9cdd14d5c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059635"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a><span data-ttu-id="b7f96-103">Configurer un backplane ReDim pour ASP.NET Core SignalR montée en puissance parallèle</span><span class="sxs-lookup"><span data-stu-id="b7f96-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="b7f96-104">Par [Andrew Stanton-infirmière](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)et [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="b7f96-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="b7f96-105">Cet article explique SignalR les aspects spécifiques de la configuration d’un serveur [redims](https://redis.io/) à utiliser pour la montée en charge d’une SignalR application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="b7f96-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="b7f96-106">Configurer un backplane ReDim</span><span class="sxs-lookup"><span data-stu-id="b7f96-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="b7f96-107">Déployez un serveur ReDim.</span><span class="sxs-lookup"><span data-stu-id="b7f96-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="b7f96-108">Pour une utilisation en production, un backplane ReDim est recommandé uniquement lorsqu’il s’exécute dans le même centre de données que l' SignalR application.</span><span class="sxs-lookup"><span data-stu-id="b7f96-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="b7f96-109">Dans le cas contraire, la latence du réseau dégrade les performances.</span><span class="sxs-lookup"><span data-stu-id="b7f96-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="b7f96-110">Si votre SignalR application s’exécute dans le Cloud Azure, nous vous recommandons le SignalR service Azure au lieu d’un backplane ReDim.</span><span class="sxs-lookup"><span data-stu-id="b7f96-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="b7f96-111">Vous pouvez utiliser le Cache Service Azure Redims pour les environnements de développement et de test.</span><span class="sxs-lookup"><span data-stu-id="b7f96-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="b7f96-112">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="b7f96-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="b7f96-113">Documentation Redis</span><span class="sxs-lookup"><span data-stu-id="b7f96-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="b7f96-114">Documentation du Cache Redis Azure</span><span class="sxs-lookup"><span data-stu-id="b7f96-114">Azure Redis Cache documentation</span></span>](/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="b7f96-115">Dans l' SignalR application, installez le `Microsoft.AspNetCore.SignalR.Redis` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="b7f96-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="b7f96-116">Dans la `Startup.ConfigureServices` méthode, appelez `AddRedis` après `AddSignalR` :</span><span class="sxs-lookup"><span data-stu-id="b7f96-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="b7f96-117">Configurez les options selon vos besoins :</span><span class="sxs-lookup"><span data-stu-id="b7f96-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="b7f96-118">La plupart des options peuvent être définies dans la chaîne de connexion ou dans l’objet [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="b7f96-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="b7f96-119">Les options spécifiées dans `ConfigurationOptions` remplacent celles définies dans la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="b7f96-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="b7f96-120">L’exemple suivant montre comment définir des options dans l' `ConfigurationOptions` objet.</span><span class="sxs-lookup"><span data-stu-id="b7f96-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="b7f96-121">Cet exemple ajoute un préfixe de canal afin que plusieurs applications puissent partager la même instance de ReDim, comme expliqué à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="b7f96-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="b7f96-122">Dans le code précédent, `options.Configuration` est initialisé avec tout ce qui a été spécifié dans la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="b7f96-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="b7f96-123">Dans l' SignalR application, installez l’un des packages NuGet suivants :</span><span class="sxs-lookup"><span data-stu-id="b7f96-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="b7f96-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` Dépend de StackExchange. Redims 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="b7f96-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="b7f96-125">Il s’agit du package recommandé pour ASP.NET Core 2,2 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="b7f96-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="b7f96-126">`Microsoft.AspNetCore.SignalR.Redis` Dépend de StackExchange. Redims 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="b7f96-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="b7f96-127">Ce package n’est pas inclus dans ASP.NET Core 3,0 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="b7f96-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="b7f96-128">Dans la `Startup.ConfigureServices` méthode, appelez <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="b7f96-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="b7f96-129">Lors de l’utilisation `Microsoft.AspNetCore.SignalR.Redis` de, appelez <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="b7f96-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="b7f96-130">Configurez les options selon vos besoins :</span><span class="sxs-lookup"><span data-stu-id="b7f96-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="b7f96-131">La plupart des options peuvent être définies dans la chaîne de connexion ou dans l’objet [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="b7f96-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="b7f96-132">Les options spécifiées dans `ConfigurationOptions` remplacent celles définies dans la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="b7f96-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="b7f96-133">L’exemple suivant montre comment définir des options dans l' `ConfigurationOptions` objet.</span><span class="sxs-lookup"><span data-stu-id="b7f96-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="b7f96-134">Cet exemple ajoute un préfixe de canal afin que plusieurs applications puissent partager la même instance de ReDim, comme expliqué à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="b7f96-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="b7f96-135">Lors de l’utilisation `Microsoft.AspNetCore.SignalR.Redis` de, appelez <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="b7f96-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="b7f96-136">Dans le code précédent, `options.Configuration` est initialisé avec tout ce qui a été spécifié dans la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="b7f96-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="b7f96-137">Pour plus d’informations sur les options Redims, consultez la [documentation sur StackExchange](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="b7f96-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b7f96-138">Dans l' SignalR application, installez le package NuGet suivant :</span><span class="sxs-lookup"><span data-stu-id="b7f96-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="b7f96-139">Dans la `Startup.ConfigureServices` méthode, appelez <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="b7f96-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="b7f96-140">Configurez les options selon vos besoins :</span><span class="sxs-lookup"><span data-stu-id="b7f96-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="b7f96-141">La plupart des options peuvent être définies dans la chaîne de connexion ou dans l’objet [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="b7f96-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="b7f96-142">Les options spécifiées dans `ConfigurationOptions` remplacent celles définies dans la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="b7f96-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="b7f96-143">L’exemple suivant montre comment définir des options dans l' `ConfigurationOptions` objet.</span><span class="sxs-lookup"><span data-stu-id="b7f96-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="b7f96-144">Cet exemple ajoute un préfixe de canal afin que plusieurs applications puissent partager la même instance de ReDim, comme expliqué à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="b7f96-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="b7f96-145">Dans le code précédent, `options.Configuration` est initialisé avec tout ce qui a été spécifié dans la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="b7f96-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="b7f96-146">Pour plus d’informations sur les options Redims, consultez la [documentation sur StackExchange](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="b7f96-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="b7f96-147">Si vous utilisez un serveur Redims pour plusieurs SignalR applications, utilisez un préfixe de canal différent pour chaque SignalR application.</span><span class="sxs-lookup"><span data-stu-id="b7f96-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="b7f96-148">La définition d’un préfixe de canal isole une SignalR application des autres qui utilisent des préfixes de canal différents.</span><span class="sxs-lookup"><span data-stu-id="b7f96-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="b7f96-149">Si vous n’assignez pas de préfixes différents, un message envoyé à partir d’une application à tous ses propres clients est envoyé à tous les clients de toutes les applications qui utilisent le serveur ReDim comme fond de panier.</span><span class="sxs-lookup"><span data-stu-id="b7f96-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="b7f96-150">Configurez le logiciel d’équilibrage de charge de votre batterie de serveurs pour les sessions rémanentes.</span><span class="sxs-lookup"><span data-stu-id="b7f96-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="b7f96-151">Voici quelques exemples de documentation sur la manière de procéder :</span><span class="sxs-lookup"><span data-stu-id="b7f96-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="b7f96-152">IIS</span><span class="sxs-lookup"><span data-stu-id="b7f96-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="b7f96-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="b7f96-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="b7f96-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="b7f96-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="b7f96-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="b7f96-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="b7f96-156">Erreurs du serveur ReDim</span><span class="sxs-lookup"><span data-stu-id="b7f96-156">Redis server errors</span></span>

<span data-ttu-id="b7f96-157">Lorsqu’un serveur ReDim tombe en panne, SignalR lève des exceptions qui indiquent que les messages ne sont pas remis.</span><span class="sxs-lookup"><span data-stu-id="b7f96-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="b7f96-158">Messages d’exception typiques :</span><span class="sxs-lookup"><span data-stu-id="b7f96-158">Some typical exception messages:</span></span>

* <span data-ttu-id="b7f96-159">*Échec de l’écriture du message*</span><span class="sxs-lookup"><span data-stu-id="b7f96-159">*Failed writing message*</span></span>
* <span data-ttu-id="b7f96-160">*Échec de l’appel de la méthode de concentrateur’MethodName'*</span><span class="sxs-lookup"><span data-stu-id="b7f96-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="b7f96-161">*Échec de la connexion aux ReDim*</span><span class="sxs-lookup"><span data-stu-id="b7f96-161">*Connection to Redis failed*</span></span>

<span data-ttu-id="b7f96-162">SignalR ne met pas en mémoire tampon les messages pour les envoyer lorsque le serveur est rétabli.</span><span class="sxs-lookup"><span data-stu-id="b7f96-162">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="b7f96-163">Tous les messages envoyés pendant que le serveur Redims est défaillant sont perdus.</span><span class="sxs-lookup"><span data-stu-id="b7f96-163">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="b7f96-164">SignalR se reconnecte automatiquement lorsque le serveur Redims est à nouveau disponible.</span><span class="sxs-lookup"><span data-stu-id="b7f96-164">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="b7f96-165">Comportement personnalisé pour les échecs de connexion</span><span class="sxs-lookup"><span data-stu-id="b7f96-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="b7f96-166">Voici un exemple qui montre comment gérer les événements d’échec de connexion Redims.</span><span class="sxs-lookup"><span data-stu-id="b7f96-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="b7f96-167">Clusters ReDim</span><span class="sxs-lookup"><span data-stu-id="b7f96-167">Redis Clustering</span></span>

<span data-ttu-id="b7f96-168">Le [clustering redims](https://redis.io/topics/cluster-spec) est une méthode permettant d’obtenir une haute disponibilité à l’aide de plusieurs serveurs ReDim.</span><span class="sxs-lookup"><span data-stu-id="b7f96-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="b7f96-169">Le clustering n’est pas officiellement pris en charge, mais il peut fonctionner.</span><span class="sxs-lookup"><span data-stu-id="b7f96-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b7f96-170">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="b7f96-170">Next steps</span></span>

<span data-ttu-id="b7f96-171">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="b7f96-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="b7f96-172">Documentation Redis</span><span class="sxs-lookup"><span data-stu-id="b7f96-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="b7f96-173">Documentation sur StackExchange ReDim</span><span class="sxs-lookup"><span data-stu-id="b7f96-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="b7f96-174">Documentation du Cache Redis Azure</span><span class="sxs-lookup"><span data-stu-id="b7f96-174">Azure Redis Cache documentation</span></span>](/azure/redis-cache/)