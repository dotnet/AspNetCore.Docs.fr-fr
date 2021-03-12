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
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>Configurer les options du serveur Web ASP.NET Core Kestrel

Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.

Pour fournir une configuration supplémentaire après l’appel de <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A>, utilisez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A> :

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

Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Dans les exemples présentés plus loin dans cet article, les options Kestrel sont configurées dans le code C#. Les options Kestrel peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index). Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider) peut charger la configuration Kestrel à partir d’un `appsettings.json` `appsettings.{Environment}.json` fichier ou :

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> et la [configuration du point de terminaison](xref:fundamentals/servers/kestrel/endpoints) sont configurables à partir des fournisseurs de configuration. La configuration Kestrel restante doit être configurée dans le code C#.

Utilisez l' **une** des approches suivantes :

* Configurer Kestrel dans `Startup.ConfigureServices` :

  1. Injecte une instance de `IConfiguration` dans la `Startup` classe. L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.
  2. Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :

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

* Configurez Kestrel lors de la génération de l’hôte :

  Dans `Program.cs` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :

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

Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).

## <a name="general-limits"></a>Limites générales

### <a name="keep-alive-timeout"></a>Délai d’expiration toujours actif

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5). La valeur par défaut est de 2 minutes.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Nombre maximale de connexions client

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket). Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Le nombre maximal de connexions est illimité (null) par défaut.

### <a name="maximum-request-body-size"></a>Taille maximale du corps de la requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.

Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

L’exemple suivant montre comment configurer la contrainte pour l’application à chaque demande :

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande. Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.

Quand une application s’exécute [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) en arrière-plan du [module ASP.net Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de la demande Kestrel est désactivée. IIS définit déjà la limite.

### <a name="minimum-request-body-data-rate"></a>Débit données minimal du corps de la requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde. Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. La vitesse n’est pas vérifiée pendant cette période. La période de grâce permet d’éviter la suppression des connexions qui envoient initialement des données à une vitesse lente en raison du démarrage lent TCP.

Le taux minimal par défaut est de 240 octets par seconde avec une période de grâce de 5 secondes.

Un débit minimal s’applique également à la réponse. Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.

Voici un exemple qui montre comment configurer les débits minimaux de données dans `Program.cs` :

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Remplacer les limites de taux minimum par demande dans l’intergiciel (middleware) :

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> référencé dans l’exemple précédent n’est pas présent dans <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> pour les requêtes http/2. La modification des limites de taux de transfert par demande n’est généralement pas prise en charge pour HTTP/2 en raison de la prise en charge du protocole pour le multiplexage des demandes. Toutefois, le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> est toujours présent `HttpContext.Features` pour les requêtes HTTP/2, car la limite de débit de lecture peut toujours être *désactivée entièrement* sur une base par demande en définissant <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> sur `null` même pour une requête HTTP/2. Une tentative de lecture de `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou une tentative de définition sur une valeur autre que `null` entraîne une levée de <xref:System.NotSupportedException> selon une requête HTTP/2.

Les limites de débit à l’échelle du serveur configurées par le biais de <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> s’appliquent encore aux connexions HTTP/1.x et HTTP/2.

### <a name="request-headers-timeout"></a>Délai d’expiration des en-têtes de requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête. La valeur par défaut est de 30 secondes.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>Limites HTTP/2

Les limites de cette section sont définies sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType> .

### <a name="maximum-streams-per-connection"></a>Flux de données maximal par connexion

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>

Limite le nombre de flux de demandes simultanés par connexion HTTP/2. Les flux de données excédentaires sont refusés.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

La valeur par défaut est 100.

### <a name="header-table-size"></a>Taille de la table d’en-tête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.HeaderTableSize>

Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2. `HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise. La valeur est fournie en octets et doit être supérieure à zéro (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

La valeur par défaut est 4096.

### <a name="maximum-frame-size"></a>Taille de trame maximale

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxFrameSize>

Indique la taille maximale autorisée d’une charge utile de trame de connexion HTTP/2 reçue ou envoyée par le serveur. La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

La valeur par défaut est 2^14 (16,384).

### <a name="maximum-request-header-size"></a>Taille maximale d’en-tête de requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxRequestHeaderFieldSize>

Indique la taille maximale autorisée en octets des valeurs d’en-tête de demande. Cette limite s’applique à la fois au nom et à la valeur dans leurs représentations compressées et non compressées. La valeur doit être supérieure à zéro (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

La valeur par défaut est 8 192.

### <a name="initial-connection-window-size"></a>Taille de fenêtre de connexion initiale

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialConnectionWindowSize>

Indique, en octets, les données de corps de requête maximales que le serveur met en mémoire tampon à un moment agrégé sur toutes les demandes (flux) par connexion. Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`. La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

La valeur par défaut est 128 Ko (131 072).

### <a name="initial-stream-window-size"></a>Taille de la fenêtre de flux initiale

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialStreamWindowSize>

Indique, en octets, les données de corps de requête maximales que le serveur met en mémoire tampon à un moment donné par demande (flux). Les demandes sont également limitées par [`InitialConnectionWindowSize`](#initial-connection-window-size) . La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

La valeur par défaut est 96 Ko (98 304).

### <a name="http2-keep-alive-ping-configuration"></a>Configuration du ping Keep Alive HTTP/2

Kestrel peut être configuré pour envoyer des pings HTTP/2 à des clients connectés. Les pings HTTP/2 servent plusieurs objectifs :

* Conserver les connexions inactives actives. Certains clients et serveurs proxy ferment les connexions inactives. Les pings HTTP/2 sont considérés comme des activités sur une connexion et empêchent la fermeture de la connexion comme étant inactive.
* Fermez les connexions défectueuses. Les connexions où le client ne répond pas au ping Keep Alive dans l’heure configurée sont fermées par le serveur.

Il existe deux options de configuration relatives aux pings de maintien actifs HTTP/2 :

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay> est un <xref:System.TimeSpan> qui configure l’intervalle de test ping. Le serveur envoie un ping Keep Alive au client s’il ne reçoit aucune trame pendant cette période. Les commandes ping Keep Alive sont désactivées lorsque cette option a la valeur <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> . La valeur par défaut est <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout> est un <xref:System.TimeSpan> qui configure le délai d’exécution de la commande ping. Si le serveur ne reçoit aucune trame, telle qu’un ping de réponse, pendant ce délai, la connexion est fermée. Le délai d’attente Keep Alive est désactivé lorsque cette option a la valeur <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> . La valeur par défaut est 20 secondes.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingDelay = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>Autres options

### <a name="synchronous-io"></a>E/S synchrone

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse. La valeur par défaut est `false`.

> [!WARNING]
> Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre. Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.

L’exemple suivant active des e/s synchrones :

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Pour plus d’informations sur les autres options et limites de Kestrel, consultez :

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
