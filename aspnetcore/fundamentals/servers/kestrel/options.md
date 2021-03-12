---
title: Configurer les options du serveur Web ASP.NET Core Kestrel
author: rick-anderson
description: En savoir plus sur la configuration des options pour Kestrel, le serveur Web multiplateforme pour ASP.NET Core.
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 48b4af2dfc925c4444c2bd0e43d04f2f0f3ddd17
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587006"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="172e9-103">Configurer les options du serveur Web ASP.NET Core Kestrel</span><span class="sxs-lookup"><span data-stu-id="172e9-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="172e9-104">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="172e9-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="172e9-105">Pour fournir une configuration supplémentaire après l’appel de <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A>, utilisez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A> :</span><span class="sxs-lookup"><span data-stu-id="172e9-105">To provide additional configuration after calling <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A>, use <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A>:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="172e9-106">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="172e9-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="172e9-107">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="172e9-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="172e9-108">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="172e9-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="172e9-109">Dans les exemples présentés plus loin dans cet article, les options Kestrel sont configurées dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="172e9-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="172e9-110">Les options Kestrel peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="172e9-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="172e9-111">Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider) peut charger la configuration Kestrel à partir d’un `appsettings.json` `appsettings.{Environment}.json` fichier ou :</span><span class="sxs-lookup"><span data-stu-id="172e9-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an `appsettings.json` or `appsettings.{Environment}.json` file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="172e9-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> et la [configuration du point de terminaison](xref:fundamentals/servers/kestrel/endpoints) sont configurables à partir des fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="172e9-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="172e9-113">La configuration Kestrel restante doit être configurée dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="172e9-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="172e9-114">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="172e9-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="172e9-115">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="172e9-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="172e9-116">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="172e9-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="172e9-117">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="172e9-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="172e9-118">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="172e9-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="172e9-119">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="172e9-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="172e9-120">Dans `Program.cs` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="172e9-120">In `Program.cs`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="172e9-121">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="172e9-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="172e9-122">Limites générales</span><span class="sxs-lookup"><span data-stu-id="172e9-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="172e9-123">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="172e9-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="172e9-124">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="172e9-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="172e9-125">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="172e9-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="172e9-126">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="172e9-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="172e9-127">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="172e9-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="172e9-128">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="172e9-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="172e9-129">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="172e9-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="172e9-130">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="172e9-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="172e9-131">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="172e9-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="172e9-132">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="172e9-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="172e9-133">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="172e9-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="172e9-134">L’exemple suivant montre comment configurer la contrainte pour l’application à chaque demande :</span><span class="sxs-lookup"><span data-stu-id="172e9-134">The following example shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="172e9-135">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="172e9-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="172e9-136">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="172e9-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="172e9-137">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="172e9-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="172e9-138">Quand une application s’exécute [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) en arrière-plan du [module ASP.net Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de la demande Kestrel est désactivée.</span><span class="sxs-lookup"><span data-stu-id="172e9-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="172e9-139">IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="172e9-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="172e9-140">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="172e9-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="172e9-141">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="172e9-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="172e9-142">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale.</span><span class="sxs-lookup"><span data-stu-id="172e9-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="172e9-143">La vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="172e9-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="172e9-144">La période de grâce permet d’éviter la suppression des connexions qui envoient initialement des données à une vitesse lente en raison du démarrage lent TCP.</span><span class="sxs-lookup"><span data-stu-id="172e9-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="172e9-145">Le taux minimal par défaut est de 240 octets par seconde avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="172e9-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="172e9-146">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="172e9-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="172e9-147">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="172e9-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="172e9-148">Voici un exemple qui montre comment configurer les débits minimaux de données dans `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="172e9-148">Here's an example that shows how to configure the minimum data rates in `Program.cs`:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="172e9-149">Remplacer les limites de taux minimum par demande dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="172e9-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="172e9-150">Le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> référencé dans l’exemple précédent n’est pas présent dans <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> pour les requêtes http/2.</span><span class="sxs-lookup"><span data-stu-id="172e9-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> for HTTP/2 requests.</span></span> <span data-ttu-id="172e9-151">La modification des limites de taux de transfert par demande n’est généralement pas prise en charge pour HTTP/2 en raison de la prise en charge du protocole pour le multiplexage des demandes.</span><span class="sxs-lookup"><span data-stu-id="172e9-151">Modifying rate limits on a per-request basis isn't generally supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="172e9-152">Toutefois, le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> est toujours présent `HttpContext.Features` pour les requêtes HTTP/2, car la limite de débit de lecture peut toujours être *désactivée entièrement* sur une base par demande en définissant <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> sur `null` même pour une requête HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="172e9-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="172e9-153">Une tentative de lecture de `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou une tentative de définition sur une valeur autre que `null` entraîne une levée de <xref:System.NotSupportedException> selon une requête HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="172e9-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a <xref:System.NotSupportedException> being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="172e9-154">Les limites de débit à l’échelle du serveur configurées par le biais de <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> s’appliquent encore aux connexions HTTP/1.x et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="172e9-154">Server-wide rate limits configured via <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="172e9-155">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="172e9-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="172e9-156">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="172e9-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="172e9-157">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="172e9-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="172e9-158">Limites HTTP/2</span><span class="sxs-lookup"><span data-stu-id="172e9-158">HTTP/2 limits</span></span>

<span data-ttu-id="172e9-159">Les limites de cette section sont définies sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="172e9-159">The limits in this section are set on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType>.</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="172e9-160">Flux de données maximal par connexion</span><span class="sxs-lookup"><span data-stu-id="172e9-160">Maximum streams per connection</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>

<span data-ttu-id="172e9-161">Limite le nombre de flux de demandes simultanés par connexion HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="172e9-161">Limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="172e9-162">Les flux de données excédentaires sont refusés.</span><span class="sxs-lookup"><span data-stu-id="172e9-162">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="172e9-163">La valeur par défaut est 100.</span><span class="sxs-lookup"><span data-stu-id="172e9-163">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="172e9-164">Taille de la table d’en-tête</span><span class="sxs-lookup"><span data-stu-id="172e9-164">Header table size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.HeaderTableSize>

<span data-ttu-id="172e9-165">Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="172e9-165">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="172e9-166">`HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise.</span><span class="sxs-lookup"><span data-stu-id="172e9-166">`HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="172e9-167">La valeur est fournie en octets et doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="172e9-167">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="172e9-168">La valeur par défaut est 4096.</span><span class="sxs-lookup"><span data-stu-id="172e9-168">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="172e9-169">Taille de trame maximale</span><span class="sxs-lookup"><span data-stu-id="172e9-169">Maximum frame size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxFrameSize>

<span data-ttu-id="172e9-170">Indique la taille maximale autorisée d’une charge utile de trame de connexion HTTP/2 reçue ou envoyée par le serveur.</span><span class="sxs-lookup"><span data-stu-id="172e9-170">Indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="172e9-171">La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).</span><span class="sxs-lookup"><span data-stu-id="172e9-171">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="172e9-172">La valeur par défaut est 2^14 (16,384).</span><span class="sxs-lookup"><span data-stu-id="172e9-172">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="172e9-173">Taille maximale d’en-tête de requête</span><span class="sxs-lookup"><span data-stu-id="172e9-173">Maximum request header size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxRequestHeaderFieldSize>

<span data-ttu-id="172e9-174">Indique la taille maximale autorisée en octets des valeurs d’en-tête de demande.</span><span class="sxs-lookup"><span data-stu-id="172e9-174">Indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="172e9-175">Cette limite s’applique à la fois au nom et à la valeur dans leurs représentations compressées et non compressées.</span><span class="sxs-lookup"><span data-stu-id="172e9-175">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="172e9-176">La valeur doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="172e9-176">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="172e9-177">La valeur par défaut est 8 192.</span><span class="sxs-lookup"><span data-stu-id="172e9-177">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="172e9-178">Taille de fenêtre de connexion initiale</span><span class="sxs-lookup"><span data-stu-id="172e9-178">Initial connection window size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialConnectionWindowSize>

<span data-ttu-id="172e9-179">Indique, en octets, les données de corps de requête maximales que le serveur met en mémoire tampon à un moment agrégé sur toutes les demandes (flux) par connexion.</span><span class="sxs-lookup"><span data-stu-id="172e9-179">Indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="172e9-180">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="172e9-180">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="172e9-181">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="172e9-181">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="172e9-182">La valeur par défaut est 128 Ko (131 072).</span><span class="sxs-lookup"><span data-stu-id="172e9-182">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="172e9-183">Taille de la fenêtre de flux initiale</span><span class="sxs-lookup"><span data-stu-id="172e9-183">Initial stream window size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialStreamWindowSize>

<span data-ttu-id="172e9-184">Indique, en octets, les données de corps de requête maximales que le serveur met en mémoire tampon à un moment donné par demande (flux).</span><span class="sxs-lookup"><span data-stu-id="172e9-184">Indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="172e9-185">Les demandes sont également limitées par [`InitialConnectionWindowSize`](#initial-connection-window-size) .</span><span class="sxs-lookup"><span data-stu-id="172e9-185">Requests are also limited by [`InitialConnectionWindowSize`](#initial-connection-window-size).</span></span> <span data-ttu-id="172e9-186">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="172e9-186">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="172e9-187">La valeur par défaut est 96 Ko (98 304).</span><span class="sxs-lookup"><span data-stu-id="172e9-187">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="172e9-188">Configuration du ping Keep Alive HTTP/2</span><span class="sxs-lookup"><span data-stu-id="172e9-188">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="172e9-189">Kestrel peut être configuré pour envoyer des pings HTTP/2 à des clients connectés.</span><span class="sxs-lookup"><span data-stu-id="172e9-189">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="172e9-190">Les pings HTTP/2 servent plusieurs objectifs :</span><span class="sxs-lookup"><span data-stu-id="172e9-190">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="172e9-191">Conserver les connexions inactives actives.</span><span class="sxs-lookup"><span data-stu-id="172e9-191">Keep idle connections alive.</span></span> <span data-ttu-id="172e9-192">Certains clients et serveurs proxy ferment les connexions inactives.</span><span class="sxs-lookup"><span data-stu-id="172e9-192">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="172e9-193">Les pings HTTP/2 sont considérés comme des activités sur une connexion et empêchent la fermeture de la connexion comme étant inactive.</span><span class="sxs-lookup"><span data-stu-id="172e9-193">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="172e9-194">Fermez les connexions défectueuses.</span><span class="sxs-lookup"><span data-stu-id="172e9-194">Close unhealthy connections.</span></span> <span data-ttu-id="172e9-195">Les connexions où le client ne répond pas au ping Keep Alive dans l’heure configurée sont fermées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="172e9-195">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="172e9-196">Il existe deux options de configuration relatives aux pings de maintien actifs HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="172e9-196">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="172e9-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay> est un <xref:System.TimeSpan> qui configure l’intervalle de test ping.</span><span class="sxs-lookup"><span data-stu-id="172e9-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay> is a <xref:System.TimeSpan> that configures the ping interval.</span></span> <span data-ttu-id="172e9-198">Le serveur envoie un ping Keep Alive au client s’il ne reçoit aucune trame pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="172e9-198">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="172e9-199">Les commandes ping Keep Alive sont désactivées lorsque cette option a la valeur <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="172e9-199">Keep alive pings are disabled when this option is set to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span> <span data-ttu-id="172e9-200">La valeur par défaut est <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="172e9-200">The default value is <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="172e9-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout> est un <xref:System.TimeSpan> qui configure le délai d’exécution de la commande ping.</span><span class="sxs-lookup"><span data-stu-id="172e9-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout> is a <xref:System.TimeSpan> that configures the ping timeout.</span></span> <span data-ttu-id="172e9-202">Si le serveur ne reçoit aucune trame, telle qu’un ping de réponse, pendant ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="172e9-202">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="172e9-203">Le délai d’attente Keep Alive est désactivé lorsque cette option a la valeur <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="172e9-203">Keep alive timeout is disabled when this option is set to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span> <span data-ttu-id="172e9-204">La valeur par défaut est 20 secondes.</span><span class="sxs-lookup"><span data-stu-id="172e9-204">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingDelay = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="172e9-205">Autres options</span><span class="sxs-lookup"><span data-stu-id="172e9-205">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="172e9-206">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="172e9-206">Synchronous I/O</span></span>

<span data-ttu-id="172e9-207"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="172e9-207"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="172e9-208">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="172e9-208">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="172e9-209">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="172e9-209">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="172e9-210">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="172e9-210">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="172e9-211">L’exemple suivant active des e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="172e9-211">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="172e9-212">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="172e9-212">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
