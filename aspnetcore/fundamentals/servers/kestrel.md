---
title: Implémentation du serveur web Kestrel dans ASP.NET Core
author: rick-anderson
description: Découvrez plus d’informations sur Kestrel, serveur web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 56ac6635639eed93a84f47fc915c7013c6ed2381
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052329"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="c6e3c-103">Implémentation du serveur web Kestrel dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6e3c-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="c6e3c-104">Par [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) et [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6e3c-105">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c6e3c-106">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c6e3c-107">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c6e3c-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c6e3c-108">HTTPS</span></span>
* <span data-ttu-id="c6e3c-109">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c6e3c-110">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="c6e3c-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="c6e3c-111">HTTP/2 (sauf sur macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="c6e3c-112">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c6e3c-113">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c6e3c-114">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c6e3c-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="c6e3c-115">Assistance HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c6e3c-115">HTTP/2 support</span></span>

<span data-ttu-id="c6e3c-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c6e3c-117">Système d’exploitation&dagger;</span><span class="sxs-lookup"><span data-stu-id="c6e3c-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="c6e3c-118">Windows Server 2016/Windows 10 ou version ultérieure&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c6e3c-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="c6e3c-119">Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="c6e3c-120">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="c6e3c-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="c6e3c-121">Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c6e3c-122">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="c6e3c-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c6e3c-123">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="c6e3c-124">&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c6e3c-125">La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c6e3c-126">Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="c6e3c-127">Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c6e3c-128">HTTP/2 est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="c6e3c-129">Pour plus d’informations sur la configuration, consultez les sections [Options Kestrel](#kestrel-options) et [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c6e3c-130">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="c6e3c-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c6e3c-131">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse* , comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-131">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c6e3c-132">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c6e3c-133">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c6e3c-135">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c6e3c-137">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c6e3c-138">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c6e3c-139">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c6e3c-140">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c6e3c-141">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c6e3c-142">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-142">A reverse proxy:</span></span>

* <span data-ttu-id="c6e3c-143">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c6e3c-144">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c6e3c-145">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c6e3c-146">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c6e3c-147">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c6e3c-148">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c6e3c-149">Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6e3c-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c6e3c-150">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c6e3c-151">Dans *Program.cs* , la <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> méthode appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-151">In *Program.cs* , the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="c6e3c-152">Pour plus d’informations sur la génération de l’hôte, consultez les sections *configurer un hôte* et les *paramètres du générateur par défaut* de <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="c6e3c-153">Pour fournir une configuration supplémentaire après l’appel de `ConfigureWebHostDefaults`, utilisez `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="c6e3c-154">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="c6e3c-154">Kestrel options</span></span>

<span data-ttu-id="c6e3c-155">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c6e3c-156">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c6e3c-157">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c6e3c-158">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c6e3c-159">Dans les exemples présentés plus loin dans cet article, les options Kestrel sont configurées dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="c6e3c-160">Les options Kestrel peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c6e3c-161">Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider) peut charger la configuration Kestrel à partir d’un *:::no-loc(appsettings.json):::* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *:::no-loc(appsettings.json):::* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="c6e3c-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> et la [configuration du point de terminaison](#endpoint-configuration) sont configurables à partir des fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="c6e3c-163">La configuration Kestrel restante doit être configurée dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="c6e3c-164">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c6e3c-165">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c6e3c-166">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c6e3c-167">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c6e3c-168">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c6e3c-169">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c6e3c-170">Dans *Program.cs* , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-170">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="c6e3c-171">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c6e3c-172">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="c6e3c-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c6e3c-173">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c6e3c-174">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="c6e3c-175">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="c6e3c-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c6e3c-176">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="c6e3c-177">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c6e3c-178">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="c6e3c-179">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c6e3c-180">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c6e3c-181">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c6e3c-182">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c6e3c-183">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="c6e3c-184">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c6e3c-185">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c6e3c-186">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c6e3c-187">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c6e3c-188">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c6e3c-189">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c6e3c-190">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c6e3c-191">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c6e3c-192">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c6e3c-193">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c6e3c-194">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c6e3c-195">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-195">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="c6e3c-196">Remplacer les limites de taux minimum par demande dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="c6e3c-197">Le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>débit référencé dans l’exemple précédent n’est pas présent dans `HttpContext.Features` pour les requêtes HTTP/2, car la modification des limites de débit par requête n’est généralement pas prise en charge pour HTTP/2 (le protocole prend en charge le multiplexage de requête).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="c6e3c-198">Toutefois, le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> est toujours présent `HttpContext.Features` pour les requêtes HTTP/2, car la limite de débit de lecture peut toujours être *désactivée entièrement* sur une base par demande en définissant `IHttpMinRequestBodyDataRateFeature.MinDataRate` sur `null` même pour une requête HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="c6e3c-199">Une tentative de lecture de `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou une tentative de définition sur une valeur autre que `null` entraîne une levée de `NotSupportedException` selon une requête HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="c6e3c-200">Les limites de débit à l’échelle du serveur configurées par le biais de `KestrelServerOptions.Limits` s’appliquent encore aux connexions HTTP/1.x et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="c6e3c-201">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c6e3c-202">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c6e3c-203">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="c6e3c-204">Flux de données maximal par connexion</span><span class="sxs-lookup"><span data-stu-id="c6e3c-204">Maximum streams per connection</span></span>

<span data-ttu-id="c6e3c-205">`Http2.MaxStreamsPerConnection` limite le nombre de flux de requête simultanée par connexion HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="c6e3c-206">Les flux de données excédentaires sont refusés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="c6e3c-207">La valeur par défaut est 100.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="c6e3c-208">Taille de la table d’en-tête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-208">Header table size</span></span>

<span data-ttu-id="c6e3c-209">Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="c6e3c-210">`Http2.HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="c6e3c-211">La valeur est fournie en octets et doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="c6e3c-212">La valeur par défaut est 4096.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="c6e3c-213">Taille de trame maximale</span><span class="sxs-lookup"><span data-stu-id="c6e3c-213">Maximum frame size</span></span>

<span data-ttu-id="c6e3c-214">`Http2.MaxFrameSize` indique la taille maximale autorisée d’une charge utile de trame de connexion HTTP/2 reçue ou envoyée par le serveur.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="c6e3c-215">La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="c6e3c-216">La valeur par défaut est 2^14 (16,384).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="c6e3c-217">Taille maximale d’en-tête de requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-217">Maximum request header size</span></span>

<span data-ttu-id="c6e3c-218">`Http2.MaxRequestHeaderFieldSize` indique la taille maximale autorisée en octets des valeurs d’en-tête de requête.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="c6e3c-219">Cette limite s’applique à la fois au nom et à la valeur dans leurs représentations compressées et non compressées.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="c6e3c-220">La valeur doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="c6e3c-221">La valeur par défaut est 8 192.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="c6e3c-222">Taille de fenêtre de connexion initiale</span><span class="sxs-lookup"><span data-stu-id="c6e3c-222">Initial connection window size</span></span>

<span data-ttu-id="c6e3c-223">`Http2.InitialConnectionWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné pour toutes les requêtes (flux) par connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="c6e3c-224">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c6e3c-225">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="c6e3c-226">La valeur par défaut est 128 Ko (131 072).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="c6e3c-227">Taille de la fenêtre de flux initiale</span><span class="sxs-lookup"><span data-stu-id="c6e3c-227">Initial stream window size</span></span>

<span data-ttu-id="c6e3c-228">`Http2.InitialStreamWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné par requête (flux).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="c6e3c-229">Les requêtes sont également limitées par `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="c6e3c-230">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="c6e3c-231">La valeur par défaut est 96 Ko (98 304).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="c6e3c-232">Configuration du ping Keep Alive HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c6e3c-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="c6e3c-233">Kestrel peut être configuré pour envoyer des pings HTTP/2 à des clients connectés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="c6e3c-234">Les pings HTTP/2 servent plusieurs objectifs :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="c6e3c-235">Conserver les connexions inactives actives.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-235">Keep idle connections alive.</span></span> <span data-ttu-id="c6e3c-236">Certains clients et serveurs proxy ferment les connexions inactives.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="c6e3c-237">Les pings HTTP/2 sont considérés comme des activités sur une connexion et empêchent la fermeture de la connexion comme étant inactive.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="c6e3c-238">Fermez les connexions défectueuses.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-238">Close unhealthy connections.</span></span> <span data-ttu-id="c6e3c-239">Les connexions où le client ne répond pas au ping Keep Alive dans l’heure configurée sont fermées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="c6e3c-240">Il existe deux options de configuration relatives aux pings de maintien actifs HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="c6e3c-241">`Http2.KeepAlivePingInterval` est un `TimeSpan` qui configure le ping interne.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="c6e3c-242">Le serveur envoie un ping Keep Alive au client s’il ne reçoit aucune trame pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="c6e3c-243">Les commandes ping Keep Alive sont désactivées lorsque cette option a la valeur `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="c6e3c-244">La valeur par défaut est `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="c6e3c-245">`Http2.KeepAlivePingTimeout` est un `TimeSpan` qui configure le délai d’exécution de la commande ping.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="c6e3c-246">Si le serveur ne reçoit aucune trame, telle qu’un ping de réponse, pendant ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="c6e3c-247">Le délai d’attente Keep Alive est désactivé lorsque cette option a la valeur `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="c6e3c-248">La valeur par défaut est 20 secondes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="c6e3c-249">Bandes-annonce</span><span class="sxs-lookup"><span data-stu-id="c6e3c-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="c6e3c-250">Réinitialiser</span><span class="sxs-lookup"><span data-stu-id="c6e3c-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="c6e3c-251">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="c6e3c-251">Synchronous I/O</span></span>

<span data-ttu-id="c6e3c-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="c6e3c-253">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="c6e3c-254">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c6e3c-255">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="c6e3c-256">L’exemple suivant active des e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="c6e3c-257">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c6e3c-258">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="c6e3c-258">Endpoint configuration</span></span>

<span data-ttu-id="c6e3c-259">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c6e3c-260">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c6e3c-261">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-261">Specify URLs using the:</span></span>

* <span data-ttu-id="c6e3c-262">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c6e3c-263">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c6e3c-264">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="c6e3c-265">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="c6e3c-266">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c6e3c-267">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c6e3c-268">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c6e3c-269">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-269">A development certificate is created:</span></span>

* <span data-ttu-id="c6e3c-270">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c6e3c-271">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c6e3c-272">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c6e3c-273">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c6e3c-274">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c6e3c-275">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c6e3c-276">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="c6e3c-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c6e3c-277">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="c6e3c-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c6e3c-278">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c6e3c-279">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="c6e3c-280"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c6e3c-281">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="c6e3c-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c6e3c-282">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c6e3c-283">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="c6e3c-284"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="c6e3c-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="c6e3c-286">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c6e3c-287">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c6e3c-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c6e3c-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c6e3c-289">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c6e3c-290">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c6e3c-291">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c6e3c-292">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-292">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c6e3c-293">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c6e3c-294">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c6e3c-295">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c6e3c-296">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c6e3c-297">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c6e3c-298">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c6e3c-299">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c6e3c-300">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c6e3c-301">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c6e3c-302">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c6e3c-303">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c6e3c-304">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c6e3c-305">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-305">Supported configurations described next:</span></span>

* <span data-ttu-id="c6e3c-306">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="c6e3c-306">No configuration</span></span>
* <span data-ttu-id="c6e3c-307">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="c6e3c-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c6e3c-308">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="c6e3c-308">Change the defaults in code</span></span>

<span data-ttu-id="c6e3c-309">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-309">*No configuration*</span></span>

<span data-ttu-id="c6e3c-310">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c6e3c-311">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c6e3c-312">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c6e3c-313">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c6e3c-314">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c6e3c-315">Dans l' *:::no-loc(appsettings.json):::* exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-315">In the following *:::no-loc(appsettings.json):::* example:</span></span>

* <span data-ttu-id="c6e3c-316">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c6e3c-317">Tout point de terminaison HTTPS qui ne spécifie pas de certificat ( **HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut** ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-317">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c6e3c-318">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c6e3c-319">Par exemple, le **Certificates**  >  certificat **par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c6e3c-320">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-320">Schema notes:</span></span>

* <span data-ttu-id="c6e3c-321">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c6e3c-322">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c6e3c-323">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c6e3c-324">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c6e3c-325">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c6e3c-326">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c6e3c-327">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="c6e3c-328">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c6e3c-329">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c6e3c-330">La `Certificate` section prend en **Path** charge les &ndash; certificats **de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c6e3c-331">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c6e3c-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="c6e3c-333">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c6e3c-334">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c6e3c-335">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c6e3c-336">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c6e3c-337">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c6e3c-338">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c6e3c-339">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c6e3c-340">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-340">*Change the defaults in code*</span></span>

<span data-ttu-id="c6e3c-341">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c6e3c-342">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="c6e3c-343">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c6e3c-344">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c6e3c-345">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c6e3c-346">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c6e3c-347">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c6e3c-348">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c6e3c-349">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-349">SNI support requires:</span></span>

* <span data-ttu-id="c6e3c-350">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c6e3c-351">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c6e3c-352">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c6e3c-353">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c6e3c-354">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="c6e3c-355">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="c6e3c-355">Connection logging</span></span>

<span data-ttu-id="c6e3c-356">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c6e3c-357">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c6e3c-358">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c6e3c-359">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c6e3c-360">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="c6e3c-360">Bind to a TCP socket</span></span>

<span data-ttu-id="c6e3c-361">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="c6e3c-362">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c6e3c-363">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c6e3c-364">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="c6e3c-364">Bind to a Unix socket</span></span>

<span data-ttu-id="c6e3c-365">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="c6e3c-366">Dans le fichier de configuration nginx, définissez l' `server`  >  `location`  >  `proxy_pass` entrée sur `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="c6e3c-367">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="c6e3c-368">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="c6e3c-369">Port 0</span><span class="sxs-lookup"><span data-stu-id="c6e3c-369">Port 0</span></span>

<span data-ttu-id="c6e3c-370">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c6e3c-371">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c6e3c-372">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c6e3c-373">Limites</span><span class="sxs-lookup"><span data-stu-id="c6e3c-373">Limitations</span></span>

<span data-ttu-id="c6e3c-374">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c6e3c-375">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="c6e3c-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="c6e3c-376">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-376">`urls` host configuration key</span></span>
* <span data-ttu-id="c6e3c-377">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="c6e3c-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c6e3c-378">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c6e3c-379">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c6e3c-380">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c6e3c-381">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c6e3c-382">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="c6e3c-382">IIS endpoint configuration</span></span>

<span data-ttu-id="c6e3c-383">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c6e3c-384">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="c6e3c-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="c6e3c-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="c6e3c-386">La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="c6e3c-387">Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="c6e3c-388">Valeur enum `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="c6e3c-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="c6e3c-389">Protocole de connexion autorisé</span><span class="sxs-lookup"><span data-stu-id="c6e3c-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="c6e3c-390">HTTP/1.1 uniquement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-390">HTTP/1.1 only.</span></span> <span data-ttu-id="c6e3c-391">Peut être utilisé avec ou sans TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="c6e3c-392">HTTP/2 uniquement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-392">HTTP/2 only.</span></span> <span data-ttu-id="c6e3c-393">Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="c6e3c-394">HTTP/1.1 et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="c6e3c-395">HTTP/2 nécessite que le client sélectionne HTTP/2 dans le protocole de transfert de [négociation de protocole de couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS. dans le cas contraire, la connexion par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="c6e3c-396">La valeur par défaut `ListenOptions.Protocols` d’un point de terminaison est `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="c6e3c-397">Restrictions TLS pour HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="c6e3c-398">TLS version 1.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="c6e3c-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="c6e3c-399">Renégociation désactivée</span><span class="sxs-lookup"><span data-stu-id="c6e3c-399">Renegotiation disabled</span></span>
* <span data-ttu-id="c6e3c-400">Compression désactivée</span><span class="sxs-lookup"><span data-stu-id="c6e3c-400">Compression disabled</span></span>
* <span data-ttu-id="c6e3c-401">Tailles minimales de l’échange de clé éphémère :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="c6e3c-402">Elliptic Curve Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bits minimum</span><span class="sxs-lookup"><span data-stu-id="c6e3c-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="c6e3c-403">Diffie-Hellman de champ fini (dhe) &lbrack; `TLS12` &rbrack; : 2048 bits minimum</span><span class="sxs-lookup"><span data-stu-id="c6e3c-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="c6e3c-404">Suite de chiffrement non interdite.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="c6e3c-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; avec la courbe elliptique P-256 &lbrack; `FIPS186` &rbrack; est pris en charge par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="c6e3c-406">L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="c6e3c-407">Les connexions sont sécurisées par TLS avec un certificat fourni :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="c6e3c-408">Utilisez l’intergiciel de connexion pour filtrer les négociations TLS en fonction de la connexion pour des chiffrements spécifiques, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="c6e3c-409">L’exemple suivant lève <xref:System.NotSupportedException> une exception pour tous les algorithmes de chiffrement que l’application ne prend pas en charge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="c6e3c-410">Vous pouvez également définir et comparer [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) à une liste de suites de chiffrement acceptables.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="c6e3c-411">Aucun chiffrement n’est utilisé avec un algorithme de chiffrement [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="c6e3c-412">Le filtrage des connexions peut également être configuré par le biais d’une expression <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="c6e3c-413">Sur Linux, <xref:System.Net.Security.CipherSuitesPolicy> peut être utilisé pour filtrer les négociations TLS en fonction de la connexion :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="c6e3c-414">*Définir le protocole à partir de la configuration*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="c6e3c-415">Par défaut, `CreateDefaultBuilder` appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="c6e3c-416">L' *:::no-loc(appsettings.json):::* exemple suivant établit http/1.1 comme protocole de connexion par défaut pour tous les points de terminaison :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-416">The following *:::no-loc(appsettings.json):::* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="c6e3c-417">L' *:::no-loc(appsettings.json):::* exemple suivant établit le protocole de connexion http/1.1 pour un point de terminaison spécifique :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-417">The following *:::no-loc(appsettings.json):::* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="c6e3c-418">Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-418">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c6e3c-419">Configuration du transport</span><span class="sxs-lookup"><span data-stu-id="c6e3c-419">Transport configuration</span></span>

<span data-ttu-id="c6e3c-420">Pour les projets qui requièrent l’utilisation de Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-420">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="c6e3c-421">Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-421">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="c6e3c-422">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> sur le `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-422">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="c6e3c-423">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="c6e3c-423">URL prefixes</span></span>

<span data-ttu-id="c6e3c-424">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c6e3c-425">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c6e3c-426">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c6e3c-427">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c6e3c-428">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c6e3c-429">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c6e3c-430">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c6e3c-431">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c6e3c-432">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c6e3c-433">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c6e3c-434">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c6e3c-435">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c6e3c-436">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c6e3c-437">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c6e3c-438">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c6e3c-439">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c6e3c-440">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="c6e3c-440">Host filtering</span></span>

<span data-ttu-id="c6e3c-441">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c6e3c-442">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c6e3c-443">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c6e3c-444">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c6e3c-445">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c6e3c-446">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est fourni implicitement pour les applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="c6e3c-447">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c6e3c-448">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c6e3c-449">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *:::no-loc(appsettings.json):::* / *appSettings. \<EnvironmentName> JSON* .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-449">To enable the middleware, define an `AllowedHosts` key in *:::no-loc(appsettings.json):::*/*appsettings.\<EnvironmentName>.json* .</span></span> <span data-ttu-id="c6e3c-450">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c6e3c-451">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-451">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c6e3c-452">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c6e3c-453">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c6e3c-454">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c6e3c-455">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c6e3c-456">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c6e3c-457">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-457">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c6e3c-458">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-458">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c6e3c-459">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-459">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c6e3c-460">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c6e3c-460">HTTPS</span></span>
* <span data-ttu-id="c6e3c-461">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-461">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c6e3c-462">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="c6e3c-462">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="c6e3c-463">HTTP/2 (sauf sur macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-463">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="c6e3c-464">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-464">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c6e3c-465">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-465">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c6e3c-466">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c6e3c-466">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="c6e3c-467">Assistance HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c6e3c-467">HTTP/2 support</span></span>

<span data-ttu-id="c6e3c-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c6e3c-469">Système d’exploitation&dagger;</span><span class="sxs-lookup"><span data-stu-id="c6e3c-469">Operating system&dagger;</span></span>
  * <span data-ttu-id="c6e3c-470">Windows Server 2016/Windows 10 ou version ultérieure&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c6e3c-470">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="c6e3c-471">Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-471">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="c6e3c-472">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="c6e3c-472">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="c6e3c-473">Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-473">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c6e3c-474">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="c6e3c-474">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c6e3c-475">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-475">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="c6e3c-476">&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-476">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c6e3c-477">La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-477">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c6e3c-478">Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-478">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="c6e3c-479">Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-479">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c6e3c-480">HTTP/2 est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-480">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="c6e3c-481">Pour plus d’informations sur la configuration, consultez les sections [Options Kestrel](#kestrel-options) et [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-481">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c6e3c-482">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="c6e3c-482">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c6e3c-483">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse* , comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-483">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c6e3c-484">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-484">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c6e3c-485">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-485">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c6e3c-487">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-487">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c6e3c-489">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-489">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c6e3c-490">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-490">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c6e3c-491">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-491">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c6e3c-492">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-492">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c6e3c-493">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-493">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c6e3c-494">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-494">A reverse proxy:</span></span>

* <span data-ttu-id="c6e3c-495">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-495">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c6e3c-496">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-496">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c6e3c-497">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-497">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c6e3c-498">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-498">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c6e3c-499">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-499">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c6e3c-500">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-500">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c6e3c-501">Comment utiliser Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6e3c-501">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c6e3c-502">Le package [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-502">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c6e3c-503">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-503">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c6e3c-504">Dans *Program.cs* , le modèle de code appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-504">In *Program.cs* , the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="c6e3c-505">Pour plus d’informations sur `CreateDefaultBuilder` et la génération de l’hôte, consultez la section *configurer un hôte* de la rubrique <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-505">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="c6e3c-506">Pour fournir une configuration supplémentaire après l’appel de `CreateDefaultBuilder`, utilisez `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-506">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="c6e3c-507">Si l’application n’appelle pas `CreateDefaultBuilder` pour configurer l’hôte, appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **avant** d’appeler `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-507">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="c6e3c-508">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="c6e3c-508">Kestrel options</span></span>

<span data-ttu-id="c6e3c-509">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-509">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c6e3c-510">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-510">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c6e3c-511">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-511">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c6e3c-512">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-512">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c6e3c-513">Les options Kestrel, qui sont configurées dans le code C# dans les exemples suivants, peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-513">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c6e3c-514">Par exemple, le fournisseur de configuration de fichier peut charger la configuration Kestrel à partir d’un *:::no-loc(appsettings.json):::* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-514">For example, the File Configuration Provider can load Kestrel configuration from an *:::no-loc(appsettings.json):::* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="c6e3c-515">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-515">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c6e3c-516">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-516">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c6e3c-517">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-517">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c6e3c-518">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-518">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c6e3c-519">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-519">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c6e3c-520">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-520">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c6e3c-521">Dans *Program.cs* , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-521">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="c6e3c-522">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-522">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c6e3c-523">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="c6e3c-523">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c6e3c-524">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-524">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c6e3c-525">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-525">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="c6e3c-526">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="c6e3c-526">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c6e3c-527">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-527">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="c6e3c-528">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-528">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c6e3c-529">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-529">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="c6e3c-530">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-530">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c6e3c-531">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-531">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c6e3c-532">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-532">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c6e3c-533">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-533">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c6e3c-534">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-534">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="c6e3c-535">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-535">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c6e3c-536">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-536">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c6e3c-537">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-537">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c6e3c-538">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-538">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c6e3c-539">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-539">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c6e3c-540">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-540">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c6e3c-541">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-541">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c6e3c-542">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-542">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c6e3c-543">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-543">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c6e3c-544">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-544">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c6e3c-545">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-545">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c6e3c-546">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-546">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="c6e3c-547">Remplacer les limites de taux minimum par demande dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-547">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="c6e3c-548">Aucune des fonctionnalités de débit référencées dans l’exemple précédent n’est présente dans `HttpContext.Features` pour les requêtes HTTP/2, car la modification des limites de débit par requête n’est pas prise en charge pour HTTP/2 (le protocole prend en charge le multiplexage de requête).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-548">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="c6e3c-549">Les limites de débit à l’échelle du serveur configurées par le biais de `KestrelServerOptions.Limits` s’appliquent encore aux connexions HTTP/1.x et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-549">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="c6e3c-550">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-550">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c6e3c-551">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-551">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c6e3c-552">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-552">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="c6e3c-553">Flux de données maximal par connexion</span><span class="sxs-lookup"><span data-stu-id="c6e3c-553">Maximum streams per connection</span></span>

<span data-ttu-id="c6e3c-554">`Http2.MaxStreamsPerConnection` limite le nombre de flux de requête simultanée par connexion HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-554">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="c6e3c-555">Les flux de données excédentaires sont refusés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-555">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="c6e3c-556">La valeur par défaut est 100.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-556">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="c6e3c-557">Taille de la table d’en-tête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-557">Header table size</span></span>

<span data-ttu-id="c6e3c-558">Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-558">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="c6e3c-559">`Http2.HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-559">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="c6e3c-560">La valeur est fournie en octets et doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-560">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="c6e3c-561">La valeur par défaut est 4096.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-561">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="c6e3c-562">Taille de trame maximale</span><span class="sxs-lookup"><span data-stu-id="c6e3c-562">Maximum frame size</span></span>

<span data-ttu-id="c6e3c-563">`Http2.MaxFrameSize` Indique la taille maximale de charge utile dans la trame de connexion HTTP/2 à recevoir.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-563">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="c6e3c-564">La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-564">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="c6e3c-565">La valeur par défaut est 2^14 (16,384).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-565">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="c6e3c-566">Taille maximale d’en-tête de requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-566">Maximum request header size</span></span>

<span data-ttu-id="c6e3c-567">`Http2.MaxRequestHeaderFieldSize` indique la taille maximale autorisée en octets des valeurs d’en-tête de requête.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-567">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="c6e3c-568">Cette limite s’applique au nom et à la valeur dans leurs représentations compressées et non compressées.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-568">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="c6e3c-569">La valeur doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-569">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="c6e3c-570">La valeur par défaut est 8 192.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-570">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="c6e3c-571">Taille de fenêtre de connexion initiale</span><span class="sxs-lookup"><span data-stu-id="c6e3c-571">Initial connection window size</span></span>

<span data-ttu-id="c6e3c-572">`Http2.InitialConnectionWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné pour toutes les requêtes (flux) par connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-572">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="c6e3c-573">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-573">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c6e3c-574">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-574">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="c6e3c-575">La valeur par défaut est 128 Ko (131 072).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-575">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="c6e3c-576">Taille de la fenêtre de flux initiale</span><span class="sxs-lookup"><span data-stu-id="c6e3c-576">Initial stream window size</span></span>

<span data-ttu-id="c6e3c-577">`Http2.InitialStreamWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné par requête (flux).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-577">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="c6e3c-578">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-578">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c6e3c-579">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-579">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="c6e3c-580">La valeur par défaut est 96 Ko (98 304).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-580">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="c6e3c-581">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="c6e3c-581">Synchronous I/O</span></span>

<span data-ttu-id="c6e3c-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="c6e3c-583">La valeur par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-583">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c6e3c-584">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-584">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c6e3c-585">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-585">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="c6e3c-586">L’exemple suivant active des e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-586">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="c6e3c-587">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-587">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c6e3c-588">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="c6e3c-588">Endpoint configuration</span></span>

<span data-ttu-id="c6e3c-589">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-589">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c6e3c-590">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-590">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c6e3c-591">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-591">Specify URLs using the:</span></span>

* <span data-ttu-id="c6e3c-592">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-592">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c6e3c-593">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-593">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c6e3c-594">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-594">`urls` host configuration key.</span></span>
* <span data-ttu-id="c6e3c-595">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-595">`UseUrls` extension method.</span></span>

<span data-ttu-id="c6e3c-596">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-596">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c6e3c-597">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-597">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c6e3c-598">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-598">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c6e3c-599">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-599">A development certificate is created:</span></span>

* <span data-ttu-id="c6e3c-600">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-600">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c6e3c-601">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-601">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c6e3c-602">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-602">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c6e3c-603">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-603">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c6e3c-604">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-604">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c6e3c-605">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-605">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c6e3c-606">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="c6e3c-606">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c6e3c-607">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="c6e3c-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c6e3c-608">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-608">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c6e3c-609">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-609">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="c6e3c-610"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-610">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c6e3c-611">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="c6e3c-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c6e3c-612">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-612">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c6e3c-613">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-613">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="c6e3c-614"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="c6e3c-615">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-615">Configure(IConfiguration)</span></span>

<span data-ttu-id="c6e3c-616">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-616">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c6e3c-617">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-617">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c6e3c-618">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c6e3c-618">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c6e3c-619">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-619">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c6e3c-620">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-620">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c6e3c-621">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-621">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c6e3c-622">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-622">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c6e3c-623">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-623">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c6e3c-624">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-624">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c6e3c-625">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-625">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c6e3c-626">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-626">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c6e3c-627">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-627">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c6e3c-628">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-628">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c6e3c-629">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-629">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c6e3c-630">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-630">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c6e3c-631">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-631">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c6e3c-632">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-632">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c6e3c-633">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-633">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c6e3c-634">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-634">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c6e3c-635">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-635">Supported configurations described next:</span></span>

* <span data-ttu-id="c6e3c-636">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="c6e3c-636">No configuration</span></span>
* <span data-ttu-id="c6e3c-637">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="c6e3c-637">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c6e3c-638">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="c6e3c-638">Change the defaults in code</span></span>

<span data-ttu-id="c6e3c-639">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-639">*No configuration*</span></span>

<span data-ttu-id="c6e3c-640">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-640">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c6e3c-641">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-641">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c6e3c-642">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-642">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c6e3c-643">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-643">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c6e3c-644">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-644">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c6e3c-645">Dans l' *:::no-loc(appsettings.json):::* exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-645">In the following *:::no-loc(appsettings.json):::* example:</span></span>

* <span data-ttu-id="c6e3c-646">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-646">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c6e3c-647">Tout point de terminaison HTTPS qui ne spécifie pas de certificat ( **HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut** ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-647">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c6e3c-648">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-648">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c6e3c-649">Par exemple, le **Certificates**  >  certificat **par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-649">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c6e3c-650">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-650">Schema notes:</span></span>

* <span data-ttu-id="c6e3c-651">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-651">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c6e3c-652">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-652">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c6e3c-653">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-653">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c6e3c-654">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-654">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c6e3c-655">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-655">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c6e3c-656">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-656">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c6e3c-657">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-657">The `Certificate` section is optional.</span></span> <span data-ttu-id="c6e3c-658">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-658">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c6e3c-659">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-659">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c6e3c-660">La `Certificate` section prend en **Path** charge les &ndash; certificats **de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-660">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c6e3c-661">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-661">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c6e3c-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="c6e3c-663">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-663">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c6e3c-664">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-664">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c6e3c-665">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-665">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c6e3c-666">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-666">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c6e3c-667">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-667">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c6e3c-668">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-668">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c6e3c-669">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-669">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c6e3c-670">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-670">*Change the defaults in code*</span></span>

<span data-ttu-id="c6e3c-671">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-671">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c6e3c-672">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-672">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="c6e3c-673">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-673">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c6e3c-674">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-674">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c6e3c-675">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-675">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c6e3c-676">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-676">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c6e3c-677">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-677">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c6e3c-678">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-678">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c6e3c-679">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-679">SNI support requires:</span></span>

* <span data-ttu-id="c6e3c-680">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-680">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c6e3c-681">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-681">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c6e3c-682">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-682">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c6e3c-683">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-683">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c6e3c-684">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-684">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="c6e3c-685">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="c6e3c-685">Connection logging</span></span>

<span data-ttu-id="c6e3c-686">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-686">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c6e3c-687">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-687">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c6e3c-688">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-688">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c6e3c-689">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-689">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c6e3c-690">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="c6e3c-690">Bind to a TCP socket</span></span>

<span data-ttu-id="c6e3c-691">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-691">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="c6e3c-692">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-692">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c6e3c-693">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-693">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c6e3c-694">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="c6e3c-694">Bind to a Unix socket</span></span>

<span data-ttu-id="c6e3c-695">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-695">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="c6e3c-696">Dans le fichier Nginx confiuguration, affectez `server`  >  `location`  >  `proxy_pass` à l’entrée `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-696">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="c6e3c-697">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-697">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="c6e3c-698">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-698">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="c6e3c-699">Port 0</span><span class="sxs-lookup"><span data-stu-id="c6e3c-699">Port 0</span></span>

<span data-ttu-id="c6e3c-700">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-700">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c6e3c-701">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-701">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c6e3c-702">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-702">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c6e3c-703">Limites</span><span class="sxs-lookup"><span data-stu-id="c6e3c-703">Limitations</span></span>

<span data-ttu-id="c6e3c-704">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-704">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c6e3c-705">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="c6e3c-705">`--urls` command-line argument</span></span>
* <span data-ttu-id="c6e3c-706">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-706">`urls` host configuration key</span></span>
* <span data-ttu-id="c6e3c-707">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="c6e3c-707">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c6e3c-708">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-708">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c6e3c-709">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-709">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c6e3c-710">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-710">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c6e3c-711">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-711">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c6e3c-712">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="c6e3c-712">IIS endpoint configuration</span></span>

<span data-ttu-id="c6e3c-713">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-713">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c6e3c-714">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-714">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="c6e3c-715">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="c6e3c-715">ListenOptions.Protocols</span></span>

<span data-ttu-id="c6e3c-716">La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-716">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="c6e3c-717">Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-717">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="c6e3c-718">Valeur enum `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="c6e3c-718">`HttpProtocols` enum value</span></span> | <span data-ttu-id="c6e3c-719">Protocole de connexion autorisé</span><span class="sxs-lookup"><span data-stu-id="c6e3c-719">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="c6e3c-720">HTTP/1.1 uniquement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-720">HTTP/1.1 only.</span></span> <span data-ttu-id="c6e3c-721">Peut être utilisé avec ou sans TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-721">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="c6e3c-722">HTTP/2 uniquement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-722">HTTP/2 only.</span></span> <span data-ttu-id="c6e3c-723">Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-723">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="c6e3c-724">HTTP/1.1 et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-724">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="c6e3c-725">HTTP/2 requiert une connexion TLS et la [négociation de protocole de couche application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ; dans le cas contraire, la connexion par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-725">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="c6e3c-726">Le protocole par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-726">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="c6e3c-727">Restrictions TLS pour HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-727">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="c6e3c-728">TLS version 1.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="c6e3c-728">TLS version 1.2 or later</span></span>
* <span data-ttu-id="c6e3c-729">Renégociation désactivée</span><span class="sxs-lookup"><span data-stu-id="c6e3c-729">Renegotiation disabled</span></span>
* <span data-ttu-id="c6e3c-730">Compression désactivée</span><span class="sxs-lookup"><span data-stu-id="c6e3c-730">Compression disabled</span></span>
* <span data-ttu-id="c6e3c-731">Tailles minimales de l’échange de clé éphémère :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-731">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="c6e3c-732">Elliptic Curve Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bits minimum</span><span class="sxs-lookup"><span data-stu-id="c6e3c-732">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="c6e3c-733">Diffie-Hellman de champ fini (dhe) &lbrack; `TLS12` &rbrack; : 2048 bits minimum</span><span class="sxs-lookup"><span data-stu-id="c6e3c-733">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="c6e3c-734">Suite de chiffrement non bloquée</span><span class="sxs-lookup"><span data-stu-id="c6e3c-734">Cipher suite not blocked</span></span>

<span data-ttu-id="c6e3c-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; avec la courbe elliptique P-256 &lbrack; `FIPS186` &rbrack; est pris en charge par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="c6e3c-736">L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-736">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="c6e3c-737">Les connexions sont sécurisées par TLS avec un certificat fourni :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-737">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="c6e3c-738">Le cas échéant, créez une implémentation `IConnectionAdapter` pour filtrer les négociations TLS par connexion pour des chiffrements spécifiques :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-738">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="c6e3c-739">*Définir le protocole à partir de la configuration*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-739">*Set the protocol from configuration*</span></span>

<span data-ttu-id="c6e3c-740">Par défaut, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="c6e3c-741">Dans l' *:::no-loc(appsettings.json):::* exemple suivant, un protocole de connexion par défaut (http/1.1 et http/2) est établi pour tous les points de terminaison de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-741">In the following *:::no-loc(appsettings.json):::* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="c6e3c-742">L’exemple de fichier de configuration suivant établit un protocole de connexion pour un point de terminaison spécifique :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-742">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="c6e3c-743">Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-743">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c6e3c-744">Configuration du transport</span><span class="sxs-lookup"><span data-stu-id="c6e3c-744">Transport configuration</span></span>

<span data-ttu-id="c6e3c-745">Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-745">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c6e3c-746">Il s’agit d’un changement cassant pour les applications ASP.NET Core 2.0 mises à niveau vers la version 2.1 qui appellent <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> et qui dépendent d’un des packages suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-746">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="c6e3c-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (référence de package directe)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="c6e3c-748">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c6e3c-748">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="c6e3c-749">Pour les projets qui requièrent l’utilisation de Libuv :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-749">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="c6e3c-750">Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-750">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c6e3c-751">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-751">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="c6e3c-752">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="c6e3c-752">URL prefixes</span></span>

<span data-ttu-id="c6e3c-753">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-753">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c6e3c-754">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-754">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c6e3c-755">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-755">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c6e3c-756">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-756">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c6e3c-757">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-757">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c6e3c-758">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-758">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c6e3c-759">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-759">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c6e3c-760">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-760">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c6e3c-761">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-761">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c6e3c-762">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-762">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c6e3c-763">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-763">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c6e3c-764">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-764">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c6e3c-765">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-765">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c6e3c-766">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-766">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c6e3c-767">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-767">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c6e3c-768">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-768">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c6e3c-769">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="c6e3c-769">Host filtering</span></span>

<span data-ttu-id="c6e3c-770">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-770">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c6e3c-771">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-771">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c6e3c-772">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-772">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c6e3c-773">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-773">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c6e3c-774">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-774">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c6e3c-775">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est inclus dans le de [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.net Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-775">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="c6e3c-776">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-776">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c6e3c-777">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-777">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c6e3c-778">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *:::no-loc(appsettings.json):::* / *appSettings. \<EnvironmentName> JSON* .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-778">To enable the middleware, define an `AllowedHosts` key in *:::no-loc(appsettings.json):::*/*appsettings.\<EnvironmentName>.json* .</span></span> <span data-ttu-id="c6e3c-779">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-779">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c6e3c-780">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-780">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c6e3c-781">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-781">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c6e3c-782">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-782">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c6e3c-783">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-783">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c6e3c-784">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-784">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c6e3c-785">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-785">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c6e3c-786">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-786">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c6e3c-787">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-787">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c6e3c-788">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-788">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c6e3c-789">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c6e3c-789">HTTPS</span></span>
* <span data-ttu-id="c6e3c-790">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-790">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c6e3c-791">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="c6e3c-791">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="c6e3c-792">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-792">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c6e3c-793">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c6e3c-793">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c6e3c-794">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="c6e3c-794">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c6e3c-795">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse* , comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-795">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c6e3c-796">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-796">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c6e3c-797">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-797">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c6e3c-799">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-799">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c6e3c-801">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-801">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c6e3c-802">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-802">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c6e3c-803">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-803">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c6e3c-804">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-804">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c6e3c-805">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-805">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c6e3c-806">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-806">A reverse proxy:</span></span>

* <span data-ttu-id="c6e3c-807">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-807">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c6e3c-808">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-808">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c6e3c-809">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-809">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c6e3c-810">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-810">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c6e3c-811">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-811">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c6e3c-812">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-812">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c6e3c-813">Comment utiliser Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6e3c-813">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c6e3c-814">Le package [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-814">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c6e3c-815">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-815">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c6e3c-816">Dans *Program.cs* , le modèle de code appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-816">In *Program.cs* , the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="c6e3c-817">Pour fournir une configuration supplémentaire après l’appel de `CreateDefaultBuilder`, appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-817">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="c6e3c-818">Pour plus d’informations sur `CreateDefaultBuilder` et la génération de l’hôte, consultez la section *configurer un hôte* de la rubrique <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-818">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="c6e3c-819">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="c6e3c-819">Kestrel options</span></span>

<span data-ttu-id="c6e3c-820">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-820">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c6e3c-821">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-821">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c6e3c-822">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-822">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c6e3c-823">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-823">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c6e3c-824">Les options Kestrel, qui sont configurées dans le code C# dans les exemples suivants, peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-824">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c6e3c-825">Par exemple, le fournisseur de configuration de fichier peut charger la configuration Kestrel à partir d’un *:::no-loc(appsettings.json):::* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-825">For example, the File Configuration Provider can load Kestrel configuration from an *:::no-loc(appsettings.json):::* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="c6e3c-826">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-826">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c6e3c-827">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-827">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c6e3c-828">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-828">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c6e3c-829">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-829">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c6e3c-830">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-830">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c6e3c-831">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-831">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c6e3c-832">Dans *Program.cs* , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-832">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="c6e3c-833">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-833">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c6e3c-834">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="c6e3c-834">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c6e3c-835">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-835">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c6e3c-836">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-836">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="c6e3c-837">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="c6e3c-837">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c6e3c-838">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-838">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="c6e3c-839">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-839">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c6e3c-840">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-840">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="c6e3c-841">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-841">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c6e3c-842">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-842">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c6e3c-843">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-843">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c6e3c-844">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-844">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c6e3c-845">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-845">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="c6e3c-846">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-846">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c6e3c-847">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-847">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c6e3c-848">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-848">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c6e3c-849">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-849">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c6e3c-850">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-850">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c6e3c-851">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-851">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c6e3c-852">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-852">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c6e3c-853">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-853">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c6e3c-854">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-854">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c6e3c-855">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-855">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c6e3c-856">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-856">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c6e3c-857">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-857">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="c6e3c-858">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="c6e3c-858">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c6e3c-859">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-859">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c6e3c-860">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-860">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="c6e3c-861">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="c6e3c-861">Synchronous I/O</span></span>

<span data-ttu-id="c6e3c-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="c6e3c-863">La valeur par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-863">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c6e3c-864">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-864">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c6e3c-865">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-865">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="c6e3c-866">L’exemple suivant désactive les e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-866">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="c6e3c-867">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-867">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c6e3c-868">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="c6e3c-868">Endpoint configuration</span></span>

<span data-ttu-id="c6e3c-869">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-869">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c6e3c-870">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-870">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c6e3c-871">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-871">Specify URLs using the:</span></span>

* <span data-ttu-id="c6e3c-872">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-872">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c6e3c-873">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-873">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c6e3c-874">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-874">`urls` host configuration key.</span></span>
* <span data-ttu-id="c6e3c-875">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-875">`UseUrls` extension method.</span></span>

<span data-ttu-id="c6e3c-876">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-876">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c6e3c-877">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-877">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c6e3c-878">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-878">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c6e3c-879">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-879">A development certificate is created:</span></span>

* <span data-ttu-id="c6e3c-880">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-880">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c6e3c-881">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-881">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c6e3c-882">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-882">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c6e3c-883">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-883">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c6e3c-884">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-884">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c6e3c-885">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-885">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c6e3c-886">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="c6e3c-886">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c6e3c-887">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="c6e3c-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c6e3c-888">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-888">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c6e3c-889">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-889">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="c6e3c-890"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-890">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c6e3c-891">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="c6e3c-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c6e3c-892">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-892">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c6e3c-893">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-893">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="c6e3c-894"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="c6e3c-895">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-895">Configure(IConfiguration)</span></span>

<span data-ttu-id="c6e3c-896">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-896">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c6e3c-897">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-897">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c6e3c-898">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c6e3c-898">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c6e3c-899">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-899">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c6e3c-900">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-900">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c6e3c-901">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-901">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c6e3c-902">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-902">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c6e3c-903">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-903">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c6e3c-904">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-904">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c6e3c-905">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-905">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c6e3c-906">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-906">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c6e3c-907">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-907">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c6e3c-908">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-908">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c6e3c-909">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-909">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c6e3c-910">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-910">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c6e3c-911">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-911">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c6e3c-912">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-912">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c6e3c-913">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-913">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c6e3c-914">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-914">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c6e3c-915">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-915">Supported configurations described next:</span></span>

* <span data-ttu-id="c6e3c-916">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="c6e3c-916">No configuration</span></span>
* <span data-ttu-id="c6e3c-917">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="c6e3c-917">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c6e3c-918">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="c6e3c-918">Change the defaults in code</span></span>

<span data-ttu-id="c6e3c-919">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-919">*No configuration*</span></span>

<span data-ttu-id="c6e3c-920">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-920">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c6e3c-921">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-921">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c6e3c-922">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-922">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c6e3c-923">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-923">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c6e3c-924">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-924">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c6e3c-925">Dans l' *:::no-loc(appsettings.json):::* exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-925">In the following *:::no-loc(appsettings.json):::* example:</span></span>

* <span data-ttu-id="c6e3c-926">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-926">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c6e3c-927">Tout point de terminaison HTTPS qui ne spécifie pas de certificat ( **HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut** ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-927">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c6e3c-928">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-928">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c6e3c-929">Par exemple, le **Certificates**  >  certificat **par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-929">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c6e3c-930">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-930">Schema notes:</span></span>

* <span data-ttu-id="c6e3c-931">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-931">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c6e3c-932">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-932">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c6e3c-933">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-933">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c6e3c-934">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-934">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c6e3c-935">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-935">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c6e3c-936">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-936">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c6e3c-937">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-937">The `Certificate` section is optional.</span></span> <span data-ttu-id="c6e3c-938">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-938">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c6e3c-939">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-939">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c6e3c-940">La `Certificate` section prend en **Path** charge les &ndash; certificats **de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-940">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c6e3c-941">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-941">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c6e3c-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="c6e3c-943">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-943">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c6e3c-944">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-944">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c6e3c-945">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-945">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c6e3c-946">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-946">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c6e3c-947">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-947">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c6e3c-948">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-948">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c6e3c-949">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-949">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c6e3c-950">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-950">*Change the defaults in code*</span></span>

<span data-ttu-id="c6e3c-951">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-951">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c6e3c-952">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-952">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="c6e3c-953">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="c6e3c-953">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c6e3c-954">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-954">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c6e3c-955">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-955">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c6e3c-956">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-956">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c6e3c-957">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-957">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c6e3c-958">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-958">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c6e3c-959">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-959">SNI support requires:</span></span>

* <span data-ttu-id="c6e3c-960">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-960">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c6e3c-961">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-961">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c6e3c-962">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-962">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c6e3c-963">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-963">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c6e3c-964">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-964">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="c6e3c-965">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="c6e3c-965">Connection logging</span></span>

<span data-ttu-id="c6e3c-966">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-966">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c6e3c-967">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-967">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c6e3c-968">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-968">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c6e3c-969">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-969">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c6e3c-970">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="c6e3c-970">Bind to a TCP socket</span></span>

<span data-ttu-id="c6e3c-971">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-971">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="c6e3c-972">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-972">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c6e3c-973">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-973">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c6e3c-974">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="c6e3c-974">Bind to a Unix socket</span></span>

<span data-ttu-id="c6e3c-975">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-975">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="c6e3c-976">Dans le fichier Nginx confiuguration, affectez `server`  >  `location`  >  `proxy_pass` à l’entrée `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-976">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="c6e3c-977">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-977">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="c6e3c-978">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-978">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="c6e3c-979">Port 0</span><span class="sxs-lookup"><span data-stu-id="c6e3c-979">Port 0</span></span>

<span data-ttu-id="c6e3c-980">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-980">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c6e3c-981">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-981">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c6e3c-982">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-982">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c6e3c-983">Limites</span><span class="sxs-lookup"><span data-stu-id="c6e3c-983">Limitations</span></span>

<span data-ttu-id="c6e3c-984">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-984">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c6e3c-985">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="c6e3c-985">`--urls` command-line argument</span></span>
* <span data-ttu-id="c6e3c-986">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-986">`urls` host configuration key</span></span>
* <span data-ttu-id="c6e3c-987">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="c6e3c-987">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c6e3c-988">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-988">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c6e3c-989">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-989">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c6e3c-990">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-990">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c6e3c-991">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-991">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c6e3c-992">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="c6e3c-992">IIS endpoint configuration</span></span>

<span data-ttu-id="c6e3c-993">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-993">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c6e3c-994">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-994">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c6e3c-995">Configuration du transport</span><span class="sxs-lookup"><span data-stu-id="c6e3c-995">Transport configuration</span></span>

<span data-ttu-id="c6e3c-996">Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-996">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c6e3c-997">Il s’agit d’un changement cassant pour les applications ASP.NET Core 2.0 mises à niveau vers la version 2.1 qui appellent <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> et qui dépendent d’un des packages suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-997">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="c6e3c-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (référence de package directe)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="c6e3c-999">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c6e3c-999">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="c6e3c-1000">Pour les projets qui requièrent l’utilisation de Libuv :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1000">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="c6e3c-1001">Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1001">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c6e3c-1002">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1002">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="c6e3c-1003">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1003">URL prefixes</span></span>

<span data-ttu-id="c6e3c-1004">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1004">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c6e3c-1005">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1005">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c6e3c-1006">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1006">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c6e3c-1007">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1007">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c6e3c-1008">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1008">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c6e3c-1009">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1009">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c6e3c-1010">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1010">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c6e3c-1011">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1011">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c6e3c-1012">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1012">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c6e3c-1013">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1013">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c6e3c-1014">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1014">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c6e3c-1015">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1015">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c6e3c-1016">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1016">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c6e3c-1017">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1017">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c6e3c-1018">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1018">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c6e3c-1019">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1019">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c6e3c-1020">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1020">Host filtering</span></span>

<span data-ttu-id="c6e3c-1021">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1021">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c6e3c-1022">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1022">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c6e3c-1023">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1023">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c6e3c-1024">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1024">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c6e3c-1025">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1025">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c6e3c-1026">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est inclus dans le de [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.net Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1026">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="c6e3c-1027">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1027">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c6e3c-1028">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1028">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c6e3c-1029">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *:::no-loc(appsettings.json):::* / *appSettings. \<EnvironmentName> JSON* .</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1029">To enable the middleware, define an `AllowedHosts` key in *:::no-loc(appsettings.json):::*/*appsettings.\<EnvironmentName>.json* .</span></span> <span data-ttu-id="c6e3c-1030">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1030">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c6e3c-1031">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1031">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c6e3c-1032">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1032">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c6e3c-1033">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1033">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c6e3c-1034">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1034">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c6e3c-1035">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1035">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c6e3c-1036">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1036">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="c6e3c-1037">Demande de drainage HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1037">HTTP/1.1 request draining</span></span>

<span data-ttu-id="c6e3c-1038">L’ouverture de connexions HTTP prend beaucoup de temps.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1038">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="c6e3c-1039">Pour le protocole HTTPs, il s’agit également d’une utilisation intensive des ressources.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1039">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="c6e3c-1040">Par conséquent, Kestrel tente de réutiliser les connexions par protocole HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1040">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="c6e3c-1041">Un corps de demande doit être entièrement consommé pour permettre la réutilisation de la connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1041">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="c6e3c-1042">L’application ne consomme pas toujours le corps de la requête, par exemple les `POST` demandes où le serveur retourne une réponse de redirection ou 404.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1042">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="c6e3c-1043">Dans le `POST` cas de redirection :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1043">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="c6e3c-1044">Le client a peut-être déjà envoyé une partie des `POST` données.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1044">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="c6e3c-1045">Le serveur écrit la réponse 301.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1045">The server writes the 301 response.</span></span>
* <span data-ttu-id="c6e3c-1046">La connexion ne peut pas être utilisée pour une nouvelle demande tant que les `POST` données du corps de la requête précédente n’ont pas été entièrement lues.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1046">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="c6e3c-1047">Kestrel tente de vider le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1047">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="c6e3c-1048">Le drainage du corps de la demande signifie lire et ignorer les données sans les traiter.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1048">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="c6e3c-1049">Le processus de drainage rend un tradoff entre l’autorisation de réutilisation de la connexion et le temps nécessaire pour décharger les données restantes :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1049">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="c6e3c-1050">La vidange a un délai d’expiration de cinq secondes, ce qui n’est pas configurable.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1050">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="c6e3c-1051">Si toutes les données spécifiées par l' `Content-Length` `Transfer-Encoding` en-tête ou n’ont pas été lues avant le délai d’attente, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1051">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="c6e3c-1052">Il peut arriver que vous souhaitiez mettre fin à la demande immédiatement, avant ou après l’écriture de la réponse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1052">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="c6e3c-1053">Par exemple, les clients peuvent avoir des limites de données restrictives, de sorte que la limitation des données chargées peut être une priorité.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1053">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="c6e3c-1054">Dans ce cas, pour mettre fin à une demande, appelez [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) à partir d’un contrôleur, d’une :::no-loc(Razor)::: page ou d’un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1054">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, :::no-loc(Razor)::: Page, or middleware.</span></span>

<span data-ttu-id="c6e3c-1055">Il existe des inconvénients à l’appel de `Abort` :</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1055">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="c6e3c-1056">La création de nouvelles connexions peut être lente et coûteuse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1056">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="c6e3c-1057">Il n’y a aucune garantie que le client a lu la réponse avant la fermeture de la connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1057">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="c6e3c-1058">L’appel `Abort` de doit être rare et réservé aux cas d’erreur graves, et non aux erreurs courantes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1058">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="c6e3c-1059">Appelez uniquement `Abort` lorsqu’un problème spécifique doit être résolu.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1059">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="c6e3c-1060">Par exemple, appelez `Abort` si des clients malveillants essaient des `POST` données ou s’il existe un bogue dans le code client qui génère des demandes volumineuses ou de nombreuses requêtes.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1060">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="c6e3c-1061">N’appelez pas `Abort` pour les situations d’erreur courantes, telles que HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1061">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="c6e3c-1062">L’appel de [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) avant d’appeler `Abort` garantit que le serveur a terminé l’écriture de la réponse.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1062">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="c6e3c-1063">Toutefois, le comportement du client n’est pas prévisible et il est possible qu’il ne lise pas la réponse avant l’abandon de la connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1063">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="c6e3c-1064">Ce processus est différent pour HTTP/2, car le protocole prend en charge l’abandon des flux de demande individuels sans fermer la connexion.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1064">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="c6e3c-1065">Le délai d’attente de drainage de cinq secondes ne s’applique pas.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1065">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="c6e3c-1066">S’il existe des données de corps de demande non lues après avoir complété une réponse, le serveur envoie une trame HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1066">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="c6e3c-1067">Les trames de données de corps de requête supplémentaires sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1067">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="c6e3c-1068">Si possible, il est préférable que les clients utilisent l’en-tête de demande [expect : 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) et attendent que le serveur réponde avant de commencer à envoyer le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1068">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="c6e3c-1069">Cela donne au client la possibilité d’examiner la réponse et de l’abandonner avant d’envoyer des données inutiles.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1069">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c6e3c-1070">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1070">Additional resources</span></span>

* <span data-ttu-id="c6e3c-1071">Lorsque vous utilisez des sockets UNIX sur Linux, le socket n’est pas supprimé automatiquement lors de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1071">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="c6e3c-1072">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1072">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="c6e3c-1073">Document RFC 7230 : syntaxe et routage des messages (section 5.4 : Hôte)</span><span class="sxs-lookup"><span data-stu-id="c6e3c-1073">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
