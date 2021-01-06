---
title: Implémentation du serveur web Kestrel dans ASP.NET Core
author: rick-anderson
description: Découvrez plus d’informations sur Kestrel, serveur web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 5c9e1717ad603687343f015826a113e6945e4a41
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854611"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="7c883-103">Implémentation du serveur web Kestrel dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c883-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="7c883-104">Par [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) et [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="7c883-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c883-105">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="7c883-106">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c883-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="7c883-107">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="7c883-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="7c883-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="7c883-108">HTTPS</span></span>
* <span data-ttu-id="7c883-109">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="7c883-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="7c883-110">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="7c883-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="7c883-111">HTTP/2 (sauf sur macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="7c883-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="7c883-112">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="7c883-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="7c883-113">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c883-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="7c883-114">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c883-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="7c883-115">Assistance HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7c883-115">HTTP/2 support</span></span>

<span data-ttu-id="7c883-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="7c883-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="7c883-117">Système d’exploitation&dagger;</span><span class="sxs-lookup"><span data-stu-id="7c883-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="7c883-118">Windows Server 2016/Windows 10 ou version ultérieure&Dagger;</span><span class="sxs-lookup"><span data-stu-id="7c883-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="7c883-119">Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="7c883-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="7c883-120">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="7c883-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="7c883-121">Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="7c883-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="7c883-122">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="7c883-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7c883-123">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="7c883-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="7c883-124">&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="7c883-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="7c883-125">La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée.</span><span class="sxs-lookup"><span data-stu-id="7c883-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="7c883-126">Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="7c883-127">Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="7c883-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="7c883-128">HTTP/2 est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="7c883-129">Pour plus d’informations sur la configuration, consultez les sections [Options Kestrel](#kestrel-options) et [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="7c883-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="7c883-130">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="7c883-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="7c883-131">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7c883-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7c883-132">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="7c883-133">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="7c883-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="7c883-135">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="7c883-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7c883-137">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7c883-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="7c883-138">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="7c883-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="7c883-139">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="7c883-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="7c883-140">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="7c883-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="7c883-141">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="7c883-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="7c883-142">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="7c883-142">A reverse proxy:</span></span>

* <span data-ttu-id="7c883-143">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="7c883-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="7c883-144">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="7c883-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="7c883-145">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="7c883-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="7c883-146">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7c883-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="7c883-147">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="7c883-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="7c883-148">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7c883-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="7c883-149">Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c883-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="7c883-150">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="7c883-151">Dans *Program.cs*, la <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> méthode appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="7c883-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="7c883-152">Pour plus d’informations sur la génération de l’hôte, consultez les sections *configurer un hôte* et les *paramètres du générateur par défaut* de <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="7c883-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="7c883-153">Pour fournir une configuration supplémentaire après l’appel de `ConfigureWebHostDefaults`, utilisez `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="7c883-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="7c883-154">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="7c883-154">Kestrel options</span></span>

<span data-ttu-id="7c883-155">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="7c883-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="7c883-156">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="7c883-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="7c883-157">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="7c883-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="7c883-158">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="7c883-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="7c883-159">Dans les exemples présentés plus loin dans cet article, les options Kestrel sont configurées dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="7c883-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="7c883-160">Les options Kestrel peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="7c883-161">Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider) peut charger la configuration Kestrel à partir d’un *appsettings.json* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7c883-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="7c883-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> et la [configuration du point de terminaison](#endpoint-configuration) sont configurables à partir des fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="7c883-163">La configuration Kestrel restante doit être configurée dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="7c883-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="7c883-164">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="7c883-165">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7c883-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="7c883-166">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="7c883-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="7c883-167">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="7c883-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="7c883-168">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="7c883-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="7c883-169">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="7c883-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="7c883-170">Dans *Program.cs*, chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="7c883-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="7c883-171">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="7c883-172">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="7c883-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="7c883-173">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="7c883-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="7c883-174">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="7c883-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="7c883-175">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="7c883-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="7c883-176">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7c883-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="7c883-177">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="7c883-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="7c883-178">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="7c883-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="7c883-179">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="7c883-180">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="7c883-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="7c883-181">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="7c883-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="7c883-182">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="7c883-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7c883-183">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="7c883-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="7c883-184">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="7c883-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="7c883-185">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="7c883-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="7c883-186">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="7c883-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7c883-187">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="7c883-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="7c883-188">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="7c883-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="7c883-189">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="7c883-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="7c883-190">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="7c883-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="7c883-191">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="7c883-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="7c883-192">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="7c883-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="7c883-193">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="7c883-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="7c883-194">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="7c883-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="7c883-195">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="7c883-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="7c883-196">Remplacer les limites de taux minimum par demande dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="7c883-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="7c883-197">Le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>débit référencé dans l’exemple précédent n’est pas présent dans `HttpContext.Features` pour les requêtes HTTP/2, car la modification des limites de débit par requête n’est généralement pas prise en charge pour HTTP/2 (le protocole prend en charge le multiplexage de requête).</span><span class="sxs-lookup"><span data-stu-id="7c883-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="7c883-198">Toutefois, le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> est toujours présent `HttpContext.Features` pour les requêtes HTTP/2, car la limite de débit de lecture peut toujours être *désactivée entièrement* sur une base par demande en définissant `IHttpMinRequestBodyDataRateFeature.MinDataRate` sur `null` même pour une requête HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="7c883-199">Une tentative de lecture de `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou une tentative de définition sur une valeur autre que `null` entraîne une levée de `NotSupportedException` selon une requête HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="7c883-200">Les limites de débit à l’échelle du serveur configurées par le biais de `KestrelServerOptions.Limits` s’appliquent encore aux connexions HTTP/1.x et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="7c883-201">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="7c883-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="7c883-202">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="7c883-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="7c883-203">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="7c883-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="7c883-204">Flux de données maximal par connexion</span><span class="sxs-lookup"><span data-stu-id="7c883-204">Maximum streams per connection</span></span>

<span data-ttu-id="7c883-205">`Http2.MaxStreamsPerConnection` limite le nombre de flux de requête simultanée par connexion HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="7c883-206">Les flux de données excédentaires sont refusés.</span><span class="sxs-lookup"><span data-stu-id="7c883-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="7c883-207">La valeur par défaut est 100.</span><span class="sxs-lookup"><span data-stu-id="7c883-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="7c883-208">Taille de la table d’en-tête</span><span class="sxs-lookup"><span data-stu-id="7c883-208">Header table size</span></span>

<span data-ttu-id="7c883-209">Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="7c883-210">`Http2.HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise.</span><span class="sxs-lookup"><span data-stu-id="7c883-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="7c883-211">La valeur est fournie en octets et doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="7c883-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="7c883-212">La valeur par défaut est 4096.</span><span class="sxs-lookup"><span data-stu-id="7c883-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="7c883-213">Taille de trame maximale</span><span class="sxs-lookup"><span data-stu-id="7c883-213">Maximum frame size</span></span>

<span data-ttu-id="7c883-214">`Http2.MaxFrameSize` indique la taille maximale autorisée d’une charge utile de trame de connexion HTTP/2 reçue ou envoyée par le serveur.</span><span class="sxs-lookup"><span data-stu-id="7c883-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="7c883-215">La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).</span><span class="sxs-lookup"><span data-stu-id="7c883-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="7c883-216">La valeur par défaut est 2^14 (16,384).</span><span class="sxs-lookup"><span data-stu-id="7c883-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="7c883-217">Taille maximale d’en-tête de requête</span><span class="sxs-lookup"><span data-stu-id="7c883-217">Maximum request header size</span></span>

<span data-ttu-id="7c883-218">`Http2.MaxRequestHeaderFieldSize` indique la taille maximale autorisée en octets des valeurs d’en-tête de requête.</span><span class="sxs-lookup"><span data-stu-id="7c883-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="7c883-219">Cette limite s’applique à la fois au nom et à la valeur dans leurs représentations compressées et non compressées.</span><span class="sxs-lookup"><span data-stu-id="7c883-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="7c883-220">La valeur doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="7c883-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="7c883-221">La valeur par défaut est 8 192.</span><span class="sxs-lookup"><span data-stu-id="7c883-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="7c883-222">Taille de fenêtre de connexion initiale</span><span class="sxs-lookup"><span data-stu-id="7c883-222">Initial connection window size</span></span>

<span data-ttu-id="7c883-223">`Http2.InitialConnectionWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné pour toutes les requêtes (flux) par connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="7c883-224">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="7c883-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="7c883-225">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="7c883-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="7c883-226">La valeur par défaut est 128 Ko (131 072).</span><span class="sxs-lookup"><span data-stu-id="7c883-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="7c883-227">Taille de la fenêtre de flux initiale</span><span class="sxs-lookup"><span data-stu-id="7c883-227">Initial stream window size</span></span>

<span data-ttu-id="7c883-228">`Http2.InitialStreamWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné par requête (flux).</span><span class="sxs-lookup"><span data-stu-id="7c883-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="7c883-229">Les requêtes sont également limitées par `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="7c883-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="7c883-230">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="7c883-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="7c883-231">La valeur par défaut est 96 Ko (98 304).</span><span class="sxs-lookup"><span data-stu-id="7c883-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="7c883-232">Configuration du ping Keep Alive HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7c883-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="7c883-233">Kestrel peut être configuré pour envoyer des pings HTTP/2 à des clients connectés.</span><span class="sxs-lookup"><span data-stu-id="7c883-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="7c883-234">Les pings HTTP/2 servent plusieurs objectifs :</span><span class="sxs-lookup"><span data-stu-id="7c883-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="7c883-235">Conserver les connexions inactives actives.</span><span class="sxs-lookup"><span data-stu-id="7c883-235">Keep idle connections alive.</span></span> <span data-ttu-id="7c883-236">Certains clients et serveurs proxy ferment les connexions inactives.</span><span class="sxs-lookup"><span data-stu-id="7c883-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="7c883-237">Les pings HTTP/2 sont considérés comme des activités sur une connexion et empêchent la fermeture de la connexion comme étant inactive.</span><span class="sxs-lookup"><span data-stu-id="7c883-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="7c883-238">Fermez les connexions défectueuses.</span><span class="sxs-lookup"><span data-stu-id="7c883-238">Close unhealthy connections.</span></span> <span data-ttu-id="7c883-239">Les connexions où le client ne répond pas au ping Keep Alive dans l’heure configurée sont fermées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="7c883-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="7c883-240">Il existe deux options de configuration relatives aux pings de maintien actifs HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="7c883-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="7c883-241">`Http2.KeepAlivePingInterval` est un `TimeSpan` qui configure le ping interne.</span><span class="sxs-lookup"><span data-stu-id="7c883-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="7c883-242">Le serveur envoie un ping Keep Alive au client s’il ne reçoit aucune trame pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="7c883-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="7c883-243">Les commandes ping Keep Alive sont désactivées lorsque cette option a la valeur `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="7c883-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="7c883-244">La valeur par défaut est `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="7c883-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="7c883-245">`Http2.KeepAlivePingTimeout` est un `TimeSpan` qui configure le délai d’exécution de la commande ping.</span><span class="sxs-lookup"><span data-stu-id="7c883-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="7c883-246">Si le serveur ne reçoit aucune trame, telle qu’un ping de réponse, pendant ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="7c883-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="7c883-247">Le délai d’attente Keep Alive est désactivé lorsque cette option a la valeur `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="7c883-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="7c883-248">La valeur par défaut est 20 secondes.</span><span class="sxs-lookup"><span data-stu-id="7c883-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="7c883-249">Bandes-annonce</span><span class="sxs-lookup"><span data-stu-id="7c883-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="7c883-250">Réinitialiser</span><span class="sxs-lookup"><span data-stu-id="7c883-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="7c883-251">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="7c883-251">Synchronous I/O</span></span>

<span data-ttu-id="7c883-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="7c883-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="7c883-253">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="7c883-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="7c883-254">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="7c883-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="7c883-255">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="7c883-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="7c883-256">L’exemple suivant active des e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="7c883-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="7c883-257">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="7c883-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="7c883-258">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="7c883-258">Endpoint configuration</span></span>

<span data-ttu-id="7c883-259">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="7c883-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="7c883-260">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="7c883-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="7c883-261">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="7c883-261">Specify URLs using the:</span></span>

* <span data-ttu-id="7c883-262">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="7c883-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="7c883-263">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="7c883-264">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="7c883-265">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="7c883-266">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="7c883-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="7c883-267">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="7c883-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="7c883-268">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="7c883-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="7c883-269">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="7c883-269">A development certificate is created:</span></span>

* <span data-ttu-id="7c883-270">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="7c883-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="7c883-271">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="7c883-272">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="7c883-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="7c883-273">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="7c883-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7c883-274">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="7c883-275">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7c883-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="7c883-276">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="7c883-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="7c883-277">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="7c883-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="7c883-278">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="7c883-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="7c883-279">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="7c883-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="7c883-280"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="7c883-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="7c883-281">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="7c883-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="7c883-282">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c883-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="7c883-283">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="7c883-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="7c883-284"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="7c883-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="7c883-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="7c883-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="7c883-286">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="7c883-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="7c883-287">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="7c883-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="7c883-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="7c883-289">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c883-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="7c883-290">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="7c883-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="7c883-291">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="7c883-292">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="7c883-292">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="7c883-293">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="7c883-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="7c883-294">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="7c883-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="7c883-295">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="7c883-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="7c883-296">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="7c883-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="7c883-297">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="7c883-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="7c883-298">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="7c883-299">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="7c883-300">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="7c883-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="7c883-301">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="7c883-302">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="7c883-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="7c883-303">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="7c883-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="7c883-304">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="7c883-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="7c883-305">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="7c883-305">Supported configurations described next:</span></span>

* <span data-ttu-id="7c883-306">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="7c883-306">No configuration</span></span>
* <span data-ttu-id="7c883-307">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="7c883-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="7c883-308">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="7c883-308">Change the defaults in code</span></span>

<span data-ttu-id="7c883-309">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="7c883-309">*No configuration*</span></span>

<span data-ttu-id="7c883-310">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="7c883-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="7c883-311">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="7c883-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="7c883-312">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="7c883-313">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="7c883-314">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="7c883-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="7c883-315">Dans l' *appsettings.json* exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7c883-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="7c883-316">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="7c883-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="7c883-317">Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut** ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="7c883-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="7c883-318">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="7c883-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="7c883-319">Par exemple, le   >  certificat **par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="7c883-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="7c883-320">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="7c883-320">Schema notes:</span></span>

* <span data-ttu-id="7c883-321">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="7c883-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="7c883-322">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="7c883-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="7c883-323">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7c883-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="7c883-324">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="7c883-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="7c883-325">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="7c883-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="7c883-326">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="7c883-327">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="7c883-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="7c883-328">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="7c883-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="7c883-329">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="7c883-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="7c883-330">La `Certificate` section prend en charge les &ndash; certificats **de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="7c883-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="7c883-331">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="7c883-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="7c883-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="7c883-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="7c883-333">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="7c883-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="7c883-334">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="7c883-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="7c883-335">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="7c883-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="7c883-336">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="7c883-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="7c883-337">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="7c883-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="7c883-338">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="7c883-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="7c883-339">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="7c883-340">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="7c883-340">*Change the defaults in code*</span></span>

<span data-ttu-id="7c883-341">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="7c883-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="7c883-342">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="7c883-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="7c883-343">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="7c883-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="7c883-344">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="7c883-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="7c883-345">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="7c883-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="7c883-346">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="7c883-347">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="7c883-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="7c883-348">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="7c883-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="7c883-349">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="7c883-349">SNI support requires:</span></span>

* <span data-ttu-id="7c883-350">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="7c883-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="7c883-351">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="7c883-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="7c883-352">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="7c883-353">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="7c883-354">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="7c883-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="7c883-355">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="7c883-355">Connection logging</span></span>

<span data-ttu-id="7c883-356">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="7c883-357">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="7c883-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="7c883-358">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="7c883-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="7c883-359">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="7c883-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="7c883-360">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="7c883-360">Bind to a TCP socket</span></span>

<span data-ttu-id="7c883-361">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="7c883-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="7c883-362">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="7c883-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="7c883-363">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7c883-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="7c883-364">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="7c883-364">Bind to a Unix socket</span></span>

<span data-ttu-id="7c883-365">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="7c883-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="7c883-366">Dans le fichier de configuration nginx, définissez l' `server`  >  `location`  >  `proxy_pass` entrée sur `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="7c883-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="7c883-367">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="7c883-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="7c883-368">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="7c883-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="7c883-369">Port 0</span><span class="sxs-lookup"><span data-stu-id="7c883-369">Port 0</span></span>

<span data-ttu-id="7c883-370">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="7c883-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="7c883-371">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="7c883-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="7c883-372">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="7c883-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="7c883-373">Limites</span><span class="sxs-lookup"><span data-stu-id="7c883-373">Limitations</span></span>

<span data-ttu-id="7c883-374">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="7c883-375">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="7c883-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="7c883-376">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-376">`urls` host configuration key</span></span>
* <span data-ttu-id="7c883-377">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="7c883-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="7c883-378">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="7c883-379">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="7c883-380">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="7c883-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="7c883-381">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="7c883-382">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="7c883-382">IIS endpoint configuration</span></span>

<span data-ttu-id="7c883-383">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="7c883-384">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7c883-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="7c883-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="7c883-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="7c883-386">La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur.</span><span class="sxs-lookup"><span data-stu-id="7c883-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="7c883-387">Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="7c883-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="7c883-388">Valeur enum `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="7c883-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="7c883-389">Protocole de connexion autorisé</span><span class="sxs-lookup"><span data-stu-id="7c883-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="7c883-390">HTTP/1.1 uniquement.</span><span class="sxs-lookup"><span data-stu-id="7c883-390">HTTP/1.1 only.</span></span> <span data-ttu-id="7c883-391">Peut être utilisé avec ou sans TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="7c883-392">HTTP/2 uniquement.</span><span class="sxs-lookup"><span data-stu-id="7c883-392">HTTP/2 only.</span></span> <span data-ttu-id="7c883-393">Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="7c883-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="7c883-394">HTTP/1.1 et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="7c883-395">HTTP/2 nécessite que le client sélectionne HTTP/2 dans le protocole de transfert de [négociation de protocole de couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS. dans le cas contraire, la connexion par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7c883-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="7c883-396">La valeur par défaut `ListenOptions.Protocols` d’un point de terminaison est `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="7c883-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="7c883-397">Restrictions TLS pour HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="7c883-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="7c883-398">TLS version 1.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="7c883-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="7c883-399">Renégociation désactivée</span><span class="sxs-lookup"><span data-stu-id="7c883-399">Renegotiation disabled</span></span>
* <span data-ttu-id="7c883-400">Compression désactivée</span><span class="sxs-lookup"><span data-stu-id="7c883-400">Compression disabled</span></span>
* <span data-ttu-id="7c883-401">Tailles minimales de l’échange de clé éphémère :</span><span class="sxs-lookup"><span data-stu-id="7c883-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="7c883-402">Elliptic Curve Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bits minimum</span><span class="sxs-lookup"><span data-stu-id="7c883-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="7c883-403">Diffie-Hellman de champ fini (dhe) &lbrack; `TLS12` &rbrack; : 2048 bits minimum</span><span class="sxs-lookup"><span data-stu-id="7c883-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="7c883-404">Suite de chiffrement non interdite.</span><span class="sxs-lookup"><span data-stu-id="7c883-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="7c883-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; avec la courbe elliptique P-256 &lbrack; `FIPS186` &rbrack; est pris en charge par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="7c883-406">L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000.</span><span class="sxs-lookup"><span data-stu-id="7c883-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="7c883-407">Les connexions sont sécurisées par TLS avec un certificat fourni :</span><span class="sxs-lookup"><span data-stu-id="7c883-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="7c883-408">Utilisez l’intergiciel de connexion pour filtrer les négociations TLS en fonction de la connexion pour des chiffrements spécifiques, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7c883-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="7c883-409">L’exemple suivant lève <xref:System.NotSupportedException> une exception pour tous les algorithmes de chiffrement que l’application ne prend pas en charge.</span><span class="sxs-lookup"><span data-stu-id="7c883-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="7c883-410">Vous pouvez également définir et comparer [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) à une liste de suites de chiffrement acceptables.</span><span class="sxs-lookup"><span data-stu-id="7c883-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="7c883-411">Aucun chiffrement n’est utilisé avec un algorithme de chiffrement [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="7c883-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="7c883-412">Le filtrage des connexions peut également être configuré par le biais d’une expression <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda :</span><span class="sxs-lookup"><span data-stu-id="7c883-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="7c883-413">Sur Linux, <xref:System.Net.Security.CipherSuitesPolicy> peut être utilisé pour filtrer les négociations TLS en fonction de la connexion :</span><span class="sxs-lookup"><span data-stu-id="7c883-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="7c883-414">*Définir le protocole à partir de la configuration*</span><span class="sxs-lookup"><span data-stu-id="7c883-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="7c883-415">Par défaut, `CreateDefaultBuilder` appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="7c883-416">L' *appsettings.json* exemple suivant établit http/1.1 comme protocole de connexion par défaut pour tous les points de terminaison :</span><span class="sxs-lookup"><span data-stu-id="7c883-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="7c883-417">L' *appsettings.json* exemple suivant établit le protocole de connexion http/1.1 pour un point de terminaison spécifique :</span><span class="sxs-lookup"><span data-stu-id="7c883-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="7c883-418">Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-418">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="7c883-419">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="7c883-419">URL prefixes</span></span>

<span data-ttu-id="7c883-420">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="7c883-420">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="7c883-421">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="7c883-421">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="7c883-422">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-422">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="7c883-423">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-423">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="7c883-424">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="7c883-424">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="7c883-425">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-425">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="7c883-426">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="7c883-426">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="7c883-427">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-427">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="7c883-428">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="7c883-428">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="7c883-429">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="7c883-429">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="7c883-430">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="7c883-430">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="7c883-431">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7c883-431">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="7c883-432">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-432">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="7c883-433">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="7c883-433">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="7c883-434">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="7c883-434">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="7c883-435">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="7c883-435">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="7c883-436">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="7c883-436">Host filtering</span></span>

<span data-ttu-id="7c883-437">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="7c883-437">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="7c883-438">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="7c883-438">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="7c883-439">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="7c883-439">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="7c883-440">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="7c883-440">`Host` headers aren't validated.</span></span>

<span data-ttu-id="7c883-441">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="7c883-441">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="7c883-442">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est fourni implicitement pour les applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="7c883-442">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="7c883-443">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="7c883-443">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="7c883-444">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-444">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="7c883-445">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *appsettings.json* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="7c883-445">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="7c883-446">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="7c883-446">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="7c883-447">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7c883-447">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="7c883-448">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="7c883-448">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="7c883-449">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="7c883-449">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="7c883-450">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="7c883-450">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="7c883-451">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="7c883-451">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="7c883-452">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7c883-452">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="7c883-453">Configuration du transport Libuv</span><span class="sxs-lookup"><span data-stu-id="7c883-453">Libuv transport configuration</span></span>

<span data-ttu-id="7c883-454">À partir de ASP.NET Core 5,0, le transport Libuv de Kestrel est obsolète.</span><span class="sxs-lookup"><span data-stu-id="7c883-454">As of ASP.NET Core 5.0, Kestrel's Libuv transport is obsolete.</span></span> <span data-ttu-id="7c883-455">Le transport Libuv ne reçoit pas de mises à jour pour prendre en charge de nouvelles plateformes de système d’exploitation, telles que Windows ARM64, et sera supprimé dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="7c883-455">Libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="7c883-456">Supprimez tous les appels à la <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> méthode obsolète et utilisez le transport de socket par défaut de Kestrel à la place.</span><span class="sxs-lookup"><span data-stu-id="7c883-456">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="7c883-457">Configuration du transport Libuv</span><span class="sxs-lookup"><span data-stu-id="7c883-457">Libuv transport configuration</span></span>

<span data-ttu-id="7c883-458">Pour les projets qui requièrent l’utilisation de Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> ) :</span><span class="sxs-lookup"><span data-stu-id="7c883-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="7c883-459">Ajoutez une dépendance pour le [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="7c883-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="7c883-460">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> sur le `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="7c883-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

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

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7c883-461">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-461">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="7c883-462">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c883-462">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="7c883-463">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="7c883-463">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="7c883-464">HTTPS</span><span class="sxs-lookup"><span data-stu-id="7c883-464">HTTPS</span></span>
* <span data-ttu-id="7c883-465">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="7c883-465">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="7c883-466">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="7c883-466">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="7c883-467">HTTP/2 (sauf sur macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="7c883-467">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="7c883-468">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="7c883-468">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="7c883-469">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c883-469">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="7c883-470">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c883-470">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="7c883-471">Assistance HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7c883-471">HTTP/2 support</span></span>

<span data-ttu-id="7c883-472">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="7c883-472">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="7c883-473">Système d’exploitation&dagger;</span><span class="sxs-lookup"><span data-stu-id="7c883-473">Operating system&dagger;</span></span>
  * <span data-ttu-id="7c883-474">Windows Server 2016/Windows 10 ou version ultérieure&Dagger;</span><span class="sxs-lookup"><span data-stu-id="7c883-474">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="7c883-475">Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="7c883-475">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="7c883-476">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="7c883-476">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="7c883-477">Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="7c883-477">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="7c883-478">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="7c883-478">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7c883-479">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="7c883-479">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="7c883-480">&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="7c883-480">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="7c883-481">La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée.</span><span class="sxs-lookup"><span data-stu-id="7c883-481">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="7c883-482">Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-482">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="7c883-483">Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="7c883-483">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="7c883-484">HTTP/2 est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-484">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="7c883-485">Pour plus d’informations sur la configuration, consultez les sections [Options Kestrel](#kestrel-options) et [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="7c883-485">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="7c883-486">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="7c883-486">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="7c883-487">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7c883-487">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7c883-488">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-488">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="7c883-489">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="7c883-489">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="7c883-491">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="7c883-491">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7c883-493">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7c883-493">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="7c883-494">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="7c883-494">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="7c883-495">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="7c883-495">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="7c883-496">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="7c883-496">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="7c883-497">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="7c883-497">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="7c883-498">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="7c883-498">A reverse proxy:</span></span>

* <span data-ttu-id="7c883-499">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="7c883-499">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="7c883-500">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="7c883-500">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="7c883-501">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="7c883-501">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="7c883-502">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7c883-502">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="7c883-503">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="7c883-503">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="7c883-504">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7c883-504">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="7c883-505">Comment utiliser Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c883-505">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="7c883-506">Le package [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7c883-506">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7c883-507">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-507">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="7c883-508">Dans *Program.cs*, le modèle de code appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="7c883-508">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="7c883-509">Pour plus d’informations sur `CreateDefaultBuilder` et la génération de l’hôte, consultez la section *configurer un hôte* de la rubrique <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="7c883-509">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="7c883-510">Pour fournir une configuration supplémentaire après l’appel de `CreateDefaultBuilder`, utilisez `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="7c883-510">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="7c883-511">Si l’application n’appelle pas `CreateDefaultBuilder` pour configurer l’hôte, appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **avant** d’appeler `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="7c883-511">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="7c883-512">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="7c883-512">Kestrel options</span></span>

<span data-ttu-id="7c883-513">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="7c883-513">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="7c883-514">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="7c883-514">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="7c883-515">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="7c883-515">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="7c883-516">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="7c883-516">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="7c883-517">Les options Kestrel, qui sont configurées dans le code C# dans les exemples suivants, peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-517">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="7c883-518">Par exemple, le fournisseur de configuration de fichier peut charger la configuration Kestrel à partir d’un *appsettings.json* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7c883-518">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="7c883-519">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-519">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="7c883-520">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7c883-520">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="7c883-521">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="7c883-521">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="7c883-522">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="7c883-522">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="7c883-523">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="7c883-523">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="7c883-524">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="7c883-524">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="7c883-525">Dans *Program.cs*, chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="7c883-525">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="7c883-526">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-526">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="7c883-527">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="7c883-527">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="7c883-528">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="7c883-528">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="7c883-529">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="7c883-529">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="7c883-530">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="7c883-530">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="7c883-531">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7c883-531">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="7c883-532">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="7c883-532">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="7c883-533">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="7c883-533">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="7c883-534">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-534">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="7c883-535">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="7c883-535">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="7c883-536">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="7c883-536">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="7c883-537">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="7c883-537">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7c883-538">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="7c883-538">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="7c883-539">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="7c883-539">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="7c883-540">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="7c883-540">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="7c883-541">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="7c883-541">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7c883-542">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="7c883-542">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="7c883-543">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="7c883-543">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="7c883-544">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="7c883-544">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="7c883-545">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="7c883-545">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="7c883-546">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="7c883-546">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="7c883-547">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="7c883-547">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="7c883-548">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="7c883-548">A minimum rate also applies to the response.</span></span> <span data-ttu-id="7c883-549">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="7c883-549">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="7c883-550">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="7c883-550">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="7c883-551">Remplacer les limites de taux minimum par demande dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="7c883-551">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="7c883-552">Aucune des fonctionnalités de débit référencées dans l’exemple précédent n’est présente dans `HttpContext.Features` pour les requêtes HTTP/2, car la modification des limites de débit par requête n’est pas prise en charge pour HTTP/2 (le protocole prend en charge le multiplexage de requête).</span><span class="sxs-lookup"><span data-stu-id="7c883-552">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="7c883-553">Les limites de débit à l’échelle du serveur configurées par le biais de `KestrelServerOptions.Limits` s’appliquent encore aux connexions HTTP/1.x et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-553">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="7c883-554">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="7c883-554">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="7c883-555">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="7c883-555">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="7c883-556">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="7c883-556">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="7c883-557">Flux de données maximal par connexion</span><span class="sxs-lookup"><span data-stu-id="7c883-557">Maximum streams per connection</span></span>

<span data-ttu-id="7c883-558">`Http2.MaxStreamsPerConnection` limite le nombre de flux de requête simultanée par connexion HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-558">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="7c883-559">Les flux de données excédentaires sont refusés.</span><span class="sxs-lookup"><span data-stu-id="7c883-559">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="7c883-560">La valeur par défaut est 100.</span><span class="sxs-lookup"><span data-stu-id="7c883-560">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="7c883-561">Taille de la table d’en-tête</span><span class="sxs-lookup"><span data-stu-id="7c883-561">Header table size</span></span>

<span data-ttu-id="7c883-562">Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-562">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="7c883-563">`Http2.HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise.</span><span class="sxs-lookup"><span data-stu-id="7c883-563">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="7c883-564">La valeur est fournie en octets et doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="7c883-564">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="7c883-565">La valeur par défaut est 4096.</span><span class="sxs-lookup"><span data-stu-id="7c883-565">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="7c883-566">Taille de trame maximale</span><span class="sxs-lookup"><span data-stu-id="7c883-566">Maximum frame size</span></span>

<span data-ttu-id="7c883-567">`Http2.MaxFrameSize` Indique la taille maximale de charge utile dans la trame de connexion HTTP/2 à recevoir.</span><span class="sxs-lookup"><span data-stu-id="7c883-567">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="7c883-568">La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).</span><span class="sxs-lookup"><span data-stu-id="7c883-568">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="7c883-569">La valeur par défaut est 2^14 (16,384).</span><span class="sxs-lookup"><span data-stu-id="7c883-569">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="7c883-570">Taille maximale d’en-tête de requête</span><span class="sxs-lookup"><span data-stu-id="7c883-570">Maximum request header size</span></span>

<span data-ttu-id="7c883-571">`Http2.MaxRequestHeaderFieldSize` indique la taille maximale autorisée en octets des valeurs d’en-tête de requête.</span><span class="sxs-lookup"><span data-stu-id="7c883-571">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="7c883-572">Cette limite s’applique au nom et à la valeur dans leurs représentations compressées et non compressées.</span><span class="sxs-lookup"><span data-stu-id="7c883-572">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="7c883-573">La valeur doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="7c883-573">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="7c883-574">La valeur par défaut est 8 192.</span><span class="sxs-lookup"><span data-stu-id="7c883-574">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="7c883-575">Taille de fenêtre de connexion initiale</span><span class="sxs-lookup"><span data-stu-id="7c883-575">Initial connection window size</span></span>

<span data-ttu-id="7c883-576">`Http2.InitialConnectionWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné pour toutes les requêtes (flux) par connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-576">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="7c883-577">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="7c883-577">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="7c883-578">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="7c883-578">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="7c883-579">La valeur par défaut est 128 Ko (131 072).</span><span class="sxs-lookup"><span data-stu-id="7c883-579">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="7c883-580">Taille de la fenêtre de flux initiale</span><span class="sxs-lookup"><span data-stu-id="7c883-580">Initial stream window size</span></span>

<span data-ttu-id="7c883-581">`Http2.InitialStreamWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné par requête (flux).</span><span class="sxs-lookup"><span data-stu-id="7c883-581">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="7c883-582">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="7c883-582">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="7c883-583">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="7c883-583">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="7c883-584">La valeur par défaut est 96 Ko (98 304).</span><span class="sxs-lookup"><span data-stu-id="7c883-584">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="7c883-585">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="7c883-585">Synchronous I/O</span></span>

<span data-ttu-id="7c883-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="7c883-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="7c883-587">La valeur par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="7c883-587">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="7c883-588">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="7c883-588">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="7c883-589">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="7c883-589">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="7c883-590">L’exemple suivant active des e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="7c883-590">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="7c883-591">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="7c883-591">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="7c883-592">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="7c883-592">Endpoint configuration</span></span>

<span data-ttu-id="7c883-593">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="7c883-593">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="7c883-594">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="7c883-594">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="7c883-595">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="7c883-595">Specify URLs using the:</span></span>

* <span data-ttu-id="7c883-596">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="7c883-596">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="7c883-597">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-597">`--urls` command-line argument.</span></span>
* <span data-ttu-id="7c883-598">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-598">`urls` host configuration key.</span></span>
* <span data-ttu-id="7c883-599">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-599">`UseUrls` extension method.</span></span>

<span data-ttu-id="7c883-600">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="7c883-600">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="7c883-601">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="7c883-601">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="7c883-602">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="7c883-602">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="7c883-603">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="7c883-603">A development certificate is created:</span></span>

* <span data-ttu-id="7c883-604">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="7c883-604">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="7c883-605">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-605">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="7c883-606">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="7c883-606">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="7c883-607">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="7c883-607">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7c883-608">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-608">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="7c883-609">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7c883-609">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="7c883-610">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="7c883-610">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="7c883-611">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="7c883-611">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="7c883-612">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="7c883-612">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="7c883-613">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="7c883-613">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="7c883-614"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="7c883-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="7c883-615">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="7c883-615">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="7c883-616">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c883-616">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="7c883-617">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="7c883-617">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="7c883-618"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="7c883-618">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="7c883-619">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="7c883-619">Configure(IConfiguration)</span></span>

<span data-ttu-id="7c883-620">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="7c883-620">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="7c883-621">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-621">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="7c883-622">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="7c883-622">ListenOptions.UseHttps</span></span>

<span data-ttu-id="7c883-623">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c883-623">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="7c883-624">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="7c883-624">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="7c883-625">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-625">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="7c883-626">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="7c883-626">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="7c883-627">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="7c883-627">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="7c883-628">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="7c883-628">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="7c883-629">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="7c883-629">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="7c883-630">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="7c883-630">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="7c883-631">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="7c883-631">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="7c883-632">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-632">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="7c883-633">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-633">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="7c883-634">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="7c883-634">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="7c883-635">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-635">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="7c883-636">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="7c883-636">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="7c883-637">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="7c883-637">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="7c883-638">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="7c883-638">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="7c883-639">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="7c883-639">Supported configurations described next:</span></span>

* <span data-ttu-id="7c883-640">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="7c883-640">No configuration</span></span>
* <span data-ttu-id="7c883-641">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="7c883-641">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="7c883-642">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="7c883-642">Change the defaults in code</span></span>

<span data-ttu-id="7c883-643">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="7c883-643">*No configuration*</span></span>

<span data-ttu-id="7c883-644">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="7c883-644">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="7c883-645">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="7c883-645">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="7c883-646">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-646">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="7c883-647">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-647">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="7c883-648">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="7c883-648">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="7c883-649">Dans l' *appsettings.json* exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7c883-649">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="7c883-650">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="7c883-650">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="7c883-651">Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut** ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="7c883-651">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="7c883-652">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="7c883-652">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="7c883-653">Par exemple, le   >  certificat **par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="7c883-653">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="7c883-654">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="7c883-654">Schema notes:</span></span>

* <span data-ttu-id="7c883-655">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="7c883-655">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="7c883-656">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="7c883-656">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="7c883-657">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7c883-657">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="7c883-658">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="7c883-658">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="7c883-659">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="7c883-659">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="7c883-660">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-660">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="7c883-661">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="7c883-661">The `Certificate` section is optional.</span></span> <span data-ttu-id="7c883-662">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="7c883-662">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="7c883-663">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="7c883-663">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="7c883-664">La `Certificate` section prend en charge les &ndash; certificats **de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="7c883-664">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="7c883-665">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="7c883-665">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="7c883-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="7c883-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="7c883-667">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="7c883-667">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="7c883-668">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="7c883-668">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="7c883-669">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="7c883-669">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="7c883-670">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="7c883-670">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="7c883-671">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="7c883-671">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="7c883-672">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="7c883-672">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="7c883-673">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-673">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="7c883-674">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="7c883-674">*Change the defaults in code*</span></span>

<span data-ttu-id="7c883-675">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="7c883-675">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="7c883-676">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="7c883-676">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="7c883-677">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="7c883-677">*Kestrel support for SNI*</span></span>

<span data-ttu-id="7c883-678">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="7c883-678">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="7c883-679">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="7c883-679">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="7c883-680">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-680">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="7c883-681">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="7c883-681">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="7c883-682">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="7c883-682">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="7c883-683">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="7c883-683">SNI support requires:</span></span>

* <span data-ttu-id="7c883-684">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="7c883-684">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="7c883-685">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="7c883-685">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="7c883-686">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-686">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="7c883-687">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-687">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="7c883-688">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="7c883-688">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="7c883-689">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="7c883-689">Connection logging</span></span>

<span data-ttu-id="7c883-690">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-690">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="7c883-691">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="7c883-691">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="7c883-692">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="7c883-692">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="7c883-693">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="7c883-693">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="7c883-694">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="7c883-694">Bind to a TCP socket</span></span>

<span data-ttu-id="7c883-695">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="7c883-695">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="7c883-696">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="7c883-696">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="7c883-697">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7c883-697">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="7c883-698">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="7c883-698">Bind to a Unix socket</span></span>

<span data-ttu-id="7c883-699">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="7c883-699">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="7c883-700">Dans le fichier Nginx confiuguration, affectez `server`  >  `location`  >  `proxy_pass` à l’entrée `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="7c883-700">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="7c883-701">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="7c883-701">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="7c883-702">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="7c883-702">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="7c883-703">Port 0</span><span class="sxs-lookup"><span data-stu-id="7c883-703">Port 0</span></span>

<span data-ttu-id="7c883-704">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="7c883-704">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="7c883-705">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="7c883-705">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="7c883-706">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="7c883-706">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="7c883-707">Limites</span><span class="sxs-lookup"><span data-stu-id="7c883-707">Limitations</span></span>

<span data-ttu-id="7c883-708">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-708">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="7c883-709">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="7c883-709">`--urls` command-line argument</span></span>
* <span data-ttu-id="7c883-710">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-710">`urls` host configuration key</span></span>
* <span data-ttu-id="7c883-711">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="7c883-711">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="7c883-712">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-712">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="7c883-713">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-713">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="7c883-714">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="7c883-714">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="7c883-715">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-715">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="7c883-716">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="7c883-716">IIS endpoint configuration</span></span>

<span data-ttu-id="7c883-717">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-717">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="7c883-718">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7c883-718">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="7c883-719">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="7c883-719">ListenOptions.Protocols</span></span>

<span data-ttu-id="7c883-720">La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur.</span><span class="sxs-lookup"><span data-stu-id="7c883-720">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="7c883-721">Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="7c883-721">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="7c883-722">Valeur enum `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="7c883-722">`HttpProtocols` enum value</span></span> | <span data-ttu-id="7c883-723">Protocole de connexion autorisé</span><span class="sxs-lookup"><span data-stu-id="7c883-723">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="7c883-724">HTTP/1.1 uniquement.</span><span class="sxs-lookup"><span data-stu-id="7c883-724">HTTP/1.1 only.</span></span> <span data-ttu-id="7c883-725">Peut être utilisé avec ou sans TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-725">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="7c883-726">HTTP/2 uniquement.</span><span class="sxs-lookup"><span data-stu-id="7c883-726">HTTP/2 only.</span></span> <span data-ttu-id="7c883-727">Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="7c883-727">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="7c883-728">HTTP/1.1 et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7c883-728">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="7c883-729">HTTP/2 requiert une connexion TLS et la [négociation de protocole de couche application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ; dans le cas contraire, la connexion par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7c883-729">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="7c883-730">Le protocole par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7c883-730">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="7c883-731">Restrictions TLS pour HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="7c883-731">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="7c883-732">TLS version 1.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="7c883-732">TLS version 1.2 or later</span></span>
* <span data-ttu-id="7c883-733">Renégociation désactivée</span><span class="sxs-lookup"><span data-stu-id="7c883-733">Renegotiation disabled</span></span>
* <span data-ttu-id="7c883-734">Compression désactivée</span><span class="sxs-lookup"><span data-stu-id="7c883-734">Compression disabled</span></span>
* <span data-ttu-id="7c883-735">Tailles minimales de l’échange de clé éphémère :</span><span class="sxs-lookup"><span data-stu-id="7c883-735">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="7c883-736">Elliptic Curve Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bits minimum</span><span class="sxs-lookup"><span data-stu-id="7c883-736">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="7c883-737">Diffie-Hellman de champ fini (dhe) &lbrack; `TLS12` &rbrack; : 2048 bits minimum</span><span class="sxs-lookup"><span data-stu-id="7c883-737">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="7c883-738">Suite de chiffrement non bloquée</span><span class="sxs-lookup"><span data-stu-id="7c883-738">Cipher suite not blocked</span></span>

<span data-ttu-id="7c883-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; avec la courbe elliptique P-256 &lbrack; `FIPS186` &rbrack; est pris en charge par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="7c883-740">L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000.</span><span class="sxs-lookup"><span data-stu-id="7c883-740">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="7c883-741">Les connexions sont sécurisées par TLS avec un certificat fourni :</span><span class="sxs-lookup"><span data-stu-id="7c883-741">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="7c883-742">Le cas échéant, créez une implémentation `IConnectionAdapter` pour filtrer les négociations TLS par connexion pour des chiffrements spécifiques :</span><span class="sxs-lookup"><span data-stu-id="7c883-742">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="7c883-743">*Définir le protocole à partir de la configuration*</span><span class="sxs-lookup"><span data-stu-id="7c883-743">*Set the protocol from configuration*</span></span>

<span data-ttu-id="7c883-744">Par défaut, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="7c883-745">Dans l' *appsettings.json* exemple suivant, un protocole de connexion par défaut (http/1.1 et http/2) est établi pour tous les points de terminaison de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="7c883-745">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="7c883-746">L’exemple de fichier de configuration suivant établit un protocole de connexion pour un point de terminaison spécifique :</span><span class="sxs-lookup"><span data-stu-id="7c883-746">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="7c883-747">Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-747">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="7c883-748">Configuration du transport Libuv</span><span class="sxs-lookup"><span data-stu-id="7c883-748">Libuv transport configuration</span></span>

<span data-ttu-id="7c883-749">Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés.</span><span class="sxs-lookup"><span data-stu-id="7c883-749">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="7c883-750">Il s’agit d’un changement cassant pour les applications ASP.NET Core 2.0 mises à niveau vers la version 2.1 qui appellent <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> et qui dépendent d’un des packages suivants :</span><span class="sxs-lookup"><span data-stu-id="7c883-750">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="7c883-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (référence de package directe)</span><span class="sxs-lookup"><span data-stu-id="7c883-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="7c883-752">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="7c883-752">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="7c883-753">Pour les projets qui requièrent l’utilisation de Libuv :</span><span class="sxs-lookup"><span data-stu-id="7c883-753">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="7c883-754">Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="7c883-754">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="7c883-755">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="7c883-755">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="7c883-756">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="7c883-756">URL prefixes</span></span>

<span data-ttu-id="7c883-757">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="7c883-757">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="7c883-758">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="7c883-758">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="7c883-759">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-759">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="7c883-760">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-760">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="7c883-761">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="7c883-761">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="7c883-762">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-762">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="7c883-763">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="7c883-763">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="7c883-764">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-764">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="7c883-765">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="7c883-765">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="7c883-766">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="7c883-766">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="7c883-767">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="7c883-767">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="7c883-768">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7c883-768">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="7c883-769">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-769">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="7c883-770">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="7c883-770">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="7c883-771">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="7c883-771">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="7c883-772">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="7c883-772">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="7c883-773">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="7c883-773">Host filtering</span></span>

<span data-ttu-id="7c883-774">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="7c883-774">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="7c883-775">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="7c883-775">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="7c883-776">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="7c883-776">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="7c883-777">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="7c883-777">`Host` headers aren't validated.</span></span>

<span data-ttu-id="7c883-778">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="7c883-778">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="7c883-779">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est inclus dans le de [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.net Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="7c883-779">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="7c883-780">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="7c883-780">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="7c883-781">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-781">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="7c883-782">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *appsettings.json* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="7c883-782">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="7c883-783">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="7c883-783">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="7c883-784">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7c883-784">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="7c883-785">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="7c883-785">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="7c883-786">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="7c883-786">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="7c883-787">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="7c883-787">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="7c883-788">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="7c883-788">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="7c883-789">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7c883-789">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7c883-790">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-790">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="7c883-791">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c883-791">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="7c883-792">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="7c883-792">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="7c883-793">HTTPS</span><span class="sxs-lookup"><span data-stu-id="7c883-793">HTTPS</span></span>
* <span data-ttu-id="7c883-794">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="7c883-794">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="7c883-795">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="7c883-795">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="7c883-796">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c883-796">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="7c883-797">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c883-797">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="7c883-798">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="7c883-798">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="7c883-799">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="7c883-799">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7c883-800">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-800">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="7c883-801">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="7c883-801">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="7c883-803">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="7c883-803">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7c883-805">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7c883-805">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="7c883-806">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="7c883-806">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="7c883-807">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="7c883-807">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="7c883-808">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="7c883-808">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="7c883-809">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="7c883-809">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="7c883-810">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="7c883-810">A reverse proxy:</span></span>

* <span data-ttu-id="7c883-811">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="7c883-811">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="7c883-812">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="7c883-812">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="7c883-813">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="7c883-813">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="7c883-814">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7c883-814">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="7c883-815">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="7c883-815">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="7c883-816">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7c883-816">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="7c883-817">Comment utiliser Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c883-817">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="7c883-818">Le package [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7c883-818">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7c883-819">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-819">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="7c883-820">Dans *Program.cs*, le modèle de code appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="7c883-820">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="7c883-821">Pour fournir une configuration supplémentaire après l’appel de `CreateDefaultBuilder`, appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="7c883-821">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="7c883-822">Pour plus d’informations sur `CreateDefaultBuilder` et la génération de l’hôte, consultez la section *configurer un hôte* de la rubrique <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="7c883-822">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="7c883-823">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="7c883-823">Kestrel options</span></span>

<span data-ttu-id="7c883-824">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="7c883-824">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="7c883-825">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="7c883-825">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="7c883-826">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="7c883-826">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="7c883-827">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="7c883-827">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="7c883-828">Les options Kestrel, qui sont configurées dans le code C# dans les exemples suivants, peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-828">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="7c883-829">Par exemple, le fournisseur de configuration de fichier peut charger la configuration Kestrel à partir d’un *appsettings.json* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7c883-829">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="7c883-830">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-830">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="7c883-831">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7c883-831">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="7c883-832">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="7c883-832">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="7c883-833">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="7c883-833">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="7c883-834">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="7c883-834">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="7c883-835">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="7c883-835">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="7c883-836">Dans *Program.cs*, chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="7c883-836">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="7c883-837">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7c883-837">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="7c883-838">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="7c883-838">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="7c883-839">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="7c883-839">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="7c883-840">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="7c883-840">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="7c883-841">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="7c883-841">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="7c883-842">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7c883-842">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="7c883-843">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="7c883-843">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="7c883-844">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="7c883-844">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="7c883-845">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-845">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="7c883-846">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="7c883-846">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="7c883-847">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="7c883-847">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="7c883-848">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="7c883-848">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7c883-849">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="7c883-849">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="7c883-850">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="7c883-850">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="7c883-851">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="7c883-851">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="7c883-852">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="7c883-852">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7c883-853">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="7c883-853">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="7c883-854">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="7c883-854">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="7c883-855">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="7c883-855">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="7c883-856">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="7c883-856">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="7c883-857">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="7c883-857">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="7c883-858">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="7c883-858">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="7c883-859">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="7c883-859">A minimum rate also applies to the response.</span></span> <span data-ttu-id="7c883-860">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="7c883-860">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="7c883-861">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="7c883-861">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="7c883-862">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="7c883-862">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="7c883-863">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="7c883-863">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="7c883-864">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="7c883-864">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="7c883-865">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="7c883-865">Synchronous I/O</span></span>

<span data-ttu-id="7c883-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="7c883-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="7c883-867">La valeur par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="7c883-867">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="7c883-868">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="7c883-868">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="7c883-869">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="7c883-869">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="7c883-870">L’exemple suivant désactive les e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="7c883-870">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="7c883-871">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="7c883-871">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="7c883-872">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="7c883-872">Endpoint configuration</span></span>

<span data-ttu-id="7c883-873">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="7c883-873">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="7c883-874">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="7c883-874">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="7c883-875">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="7c883-875">Specify URLs using the:</span></span>

* <span data-ttu-id="7c883-876">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="7c883-876">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="7c883-877">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-877">`--urls` command-line argument.</span></span>
* <span data-ttu-id="7c883-878">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-878">`urls` host configuration key.</span></span>
* <span data-ttu-id="7c883-879">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-879">`UseUrls` extension method.</span></span>

<span data-ttu-id="7c883-880">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="7c883-880">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="7c883-881">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="7c883-881">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="7c883-882">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="7c883-882">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="7c883-883">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="7c883-883">A development certificate is created:</span></span>

* <span data-ttu-id="7c883-884">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="7c883-884">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="7c883-885">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-885">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="7c883-886">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="7c883-886">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="7c883-887">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="7c883-887">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7c883-888">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-888">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="7c883-889">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7c883-889">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="7c883-890">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="7c883-890">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="7c883-891">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="7c883-891">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="7c883-892">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="7c883-892">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="7c883-893">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="7c883-893">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="7c883-894"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="7c883-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="7c883-895">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="7c883-895">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="7c883-896">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c883-896">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="7c883-897">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="7c883-897">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="7c883-898"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="7c883-898">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="7c883-899">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="7c883-899">Configure(IConfiguration)</span></span>

<span data-ttu-id="7c883-900">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="7c883-900">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="7c883-901">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-901">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="7c883-902">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="7c883-902">ListenOptions.UseHttps</span></span>

<span data-ttu-id="7c883-903">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c883-903">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="7c883-904">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="7c883-904">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="7c883-905">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-905">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="7c883-906">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="7c883-906">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="7c883-907">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="7c883-907">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="7c883-908">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="7c883-908">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="7c883-909">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="7c883-909">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="7c883-910">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="7c883-910">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="7c883-911">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="7c883-911">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="7c883-912">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-912">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="7c883-913">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-913">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="7c883-914">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="7c883-914">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="7c883-915">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="7c883-915">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="7c883-916">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="7c883-916">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="7c883-917">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="7c883-917">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="7c883-918">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="7c883-918">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="7c883-919">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="7c883-919">Supported configurations described next:</span></span>

* <span data-ttu-id="7c883-920">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="7c883-920">No configuration</span></span>
* <span data-ttu-id="7c883-921">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="7c883-921">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="7c883-922">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="7c883-922">Change the defaults in code</span></span>

<span data-ttu-id="7c883-923">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="7c883-923">*No configuration*</span></span>

<span data-ttu-id="7c883-924">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="7c883-924">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="7c883-925">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="7c883-925">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="7c883-926">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-926">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="7c883-927">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-927">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="7c883-928">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="7c883-928">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="7c883-929">Dans l' *appsettings.json* exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7c883-929">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="7c883-930">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="7c883-930">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="7c883-931">Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut** ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="7c883-931">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="7c883-932">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="7c883-932">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="7c883-933">Par exemple, le   >  certificat **par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="7c883-933">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="7c883-934">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="7c883-934">Schema notes:</span></span>

* <span data-ttu-id="7c883-935">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="7c883-935">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="7c883-936">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="7c883-936">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="7c883-937">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7c883-937">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="7c883-938">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="7c883-938">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="7c883-939">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="7c883-939">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="7c883-940">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-940">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="7c883-941">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="7c883-941">The `Certificate` section is optional.</span></span> <span data-ttu-id="7c883-942">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="7c883-942">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="7c883-943">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="7c883-943">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="7c883-944">La `Certificate` section prend en charge les &ndash; certificats **de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash;  .</span><span class="sxs-lookup"><span data-stu-id="7c883-944">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="7c883-945">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="7c883-945">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="7c883-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="7c883-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="7c883-947">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="7c883-947">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="7c883-948">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="7c883-948">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="7c883-949">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="7c883-949">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="7c883-950">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="7c883-950">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="7c883-951">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="7c883-951">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="7c883-952">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="7c883-952">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="7c883-953">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="7c883-953">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="7c883-954">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="7c883-954">*Change the defaults in code*</span></span>

<span data-ttu-id="7c883-955">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="7c883-955">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="7c883-956">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="7c883-956">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="7c883-957">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="7c883-957">*Kestrel support for SNI*</span></span>

<span data-ttu-id="7c883-958">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="7c883-958">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="7c883-959">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="7c883-959">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="7c883-960">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-960">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="7c883-961">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="7c883-961">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="7c883-962">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="7c883-962">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="7c883-963">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="7c883-963">SNI support requires:</span></span>

* <span data-ttu-id="7c883-964">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="7c883-964">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="7c883-965">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="7c883-965">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="7c883-966">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="7c883-966">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="7c883-967">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-967">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="7c883-968">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="7c883-968">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="7c883-969">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="7c883-969">Connection logging</span></span>

<span data-ttu-id="7c883-970">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-970">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="7c883-971">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="7c883-971">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="7c883-972">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="7c883-972">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="7c883-973">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="7c883-973">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="7c883-974">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="7c883-974">Bind to a TCP socket</span></span>

<span data-ttu-id="7c883-975">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="7c883-975">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="7c883-976">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="7c883-976">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="7c883-977">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7c883-977">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="7c883-978">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="7c883-978">Bind to a Unix socket</span></span>

<span data-ttu-id="7c883-979">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="7c883-979">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="7c883-980">Dans le fichier Nginx confiuguration, affectez `server`  >  `location`  >  `proxy_pass` à l’entrée `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="7c883-980">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="7c883-981">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="7c883-981">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="7c883-982">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="7c883-982">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="7c883-983">Port 0</span><span class="sxs-lookup"><span data-stu-id="7c883-983">Port 0</span></span>

<span data-ttu-id="7c883-984">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="7c883-984">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="7c883-985">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="7c883-985">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="7c883-986">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="7c883-986">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="7c883-987">Limites</span><span class="sxs-lookup"><span data-stu-id="7c883-987">Limitations</span></span>

<span data-ttu-id="7c883-988">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-988">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="7c883-989">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="7c883-989">`--urls` command-line argument</span></span>
* <span data-ttu-id="7c883-990">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-990">`urls` host configuration key</span></span>
* <span data-ttu-id="7c883-991">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="7c883-991">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="7c883-992">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="7c883-992">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="7c883-993">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-993">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="7c883-994">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="7c883-994">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="7c883-995">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-995">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="7c883-996">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="7c883-996">IIS endpoint configuration</span></span>

<span data-ttu-id="7c883-997">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-997">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="7c883-998">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7c883-998">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="7c883-999">Configuration du transport Libuv</span><span class="sxs-lookup"><span data-stu-id="7c883-999">Libuv transport configuration</span></span>

<span data-ttu-id="7c883-1000">Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés.</span><span class="sxs-lookup"><span data-stu-id="7c883-1000">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="7c883-1001">Il s’agit d’un changement cassant pour les applications ASP.NET Core 2.0 mises à niveau vers la version 2.1 qui appellent <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> et qui dépendent d’un des packages suivants :</span><span class="sxs-lookup"><span data-stu-id="7c883-1001">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="7c883-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (référence de package directe)</span><span class="sxs-lookup"><span data-stu-id="7c883-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="7c883-1003">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="7c883-1003">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="7c883-1004">Pour les projets qui requièrent l’utilisation de Libuv :</span><span class="sxs-lookup"><span data-stu-id="7c883-1004">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="7c883-1005">Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="7c883-1005">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="7c883-1006">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="7c883-1006">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="7c883-1007">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="7c883-1007">URL prefixes</span></span>

<span data-ttu-id="7c883-1008">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="7c883-1008">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="7c883-1009">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="7c883-1009">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="7c883-1010">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="7c883-1010">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="7c883-1011">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-1011">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="7c883-1012">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="7c883-1012">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="7c883-1013">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-1013">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="7c883-1014">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="7c883-1014">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="7c883-1015">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-1015">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="7c883-1016">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="7c883-1016">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="7c883-1017">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="7c883-1017">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="7c883-1018">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="7c883-1018">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="7c883-1019">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="7c883-1019">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="7c883-1020">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="7c883-1020">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="7c883-1021">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="7c883-1021">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="7c883-1022">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="7c883-1022">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="7c883-1023">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="7c883-1023">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="7c883-1024">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="7c883-1024">Host filtering</span></span>

<span data-ttu-id="7c883-1025">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="7c883-1025">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="7c883-1026">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="7c883-1026">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="7c883-1027">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="7c883-1027">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="7c883-1028">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="7c883-1028">`Host` headers aren't validated.</span></span>

<span data-ttu-id="7c883-1029">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="7c883-1029">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="7c883-1030">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est inclus dans le de [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.net Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="7c883-1030">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="7c883-1031">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="7c883-1031">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="7c883-1032">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="7c883-1032">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="7c883-1033">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *appsettings.json* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="7c883-1033">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="7c883-1034">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="7c883-1034">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="7c883-1035">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7c883-1035">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="7c883-1036">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="7c883-1036">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="7c883-1037">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="7c883-1037">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="7c883-1038">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="7c883-1038">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="7c883-1039">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="7c883-1039">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="7c883-1040">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7c883-1040">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="7c883-1041">Demande de drainage HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="7c883-1041">HTTP/1.1 request draining</span></span>

<span data-ttu-id="7c883-1042">L’ouverture de connexions HTTP prend beaucoup de temps.</span><span class="sxs-lookup"><span data-stu-id="7c883-1042">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="7c883-1043">Pour le protocole HTTPs, il s’agit également d’une utilisation intensive des ressources.</span><span class="sxs-lookup"><span data-stu-id="7c883-1043">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="7c883-1044">Par conséquent, Kestrel tente de réutiliser les connexions par protocole HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7c883-1044">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="7c883-1045">Un corps de demande doit être entièrement consommé pour permettre la réutilisation de la connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-1045">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="7c883-1046">L’application ne consomme pas toujours le corps de la requête, par exemple les `POST` demandes où le serveur retourne une réponse de redirection ou 404.</span><span class="sxs-lookup"><span data-stu-id="7c883-1046">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="7c883-1047">Dans le `POST` cas de redirection :</span><span class="sxs-lookup"><span data-stu-id="7c883-1047">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="7c883-1048">Le client a peut-être déjà envoyé une partie des `POST` données.</span><span class="sxs-lookup"><span data-stu-id="7c883-1048">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="7c883-1049">Le serveur écrit la réponse 301.</span><span class="sxs-lookup"><span data-stu-id="7c883-1049">The server writes the 301 response.</span></span>
* <span data-ttu-id="7c883-1050">La connexion ne peut pas être utilisée pour une nouvelle demande tant que les `POST` données du corps de la requête précédente n’ont pas été entièrement lues.</span><span class="sxs-lookup"><span data-stu-id="7c883-1050">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="7c883-1051">Kestrel tente de vider le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="7c883-1051">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="7c883-1052">Le drainage du corps de la demande signifie lire et ignorer les données sans les traiter.</span><span class="sxs-lookup"><span data-stu-id="7c883-1052">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="7c883-1053">Le processus de drainage rend un tradoff entre l’autorisation de réutilisation de la connexion et le temps nécessaire pour décharger les données restantes :</span><span class="sxs-lookup"><span data-stu-id="7c883-1053">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="7c883-1054">La vidange a un délai d’expiration de cinq secondes, ce qui n’est pas configurable.</span><span class="sxs-lookup"><span data-stu-id="7c883-1054">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="7c883-1055">Si toutes les données spécifiées par l' `Content-Length` `Transfer-Encoding` en-tête ou n’ont pas été lues avant le délai d’attente, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="7c883-1055">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="7c883-1056">Il peut arriver que vous souhaitiez mettre fin à la demande immédiatement, avant ou après l’écriture de la réponse.</span><span class="sxs-lookup"><span data-stu-id="7c883-1056">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="7c883-1057">Par exemple, les clients peuvent avoir des limites de données restrictives, de sorte que la limitation des données chargées peut être une priorité.</span><span class="sxs-lookup"><span data-stu-id="7c883-1057">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="7c883-1058">Dans ce cas, pour mettre fin à une demande, appelez [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) à partir d’un contrôleur, d’une Razor page ou d’un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="7c883-1058">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="7c883-1059">Il existe des inconvénients à l’appel de `Abort` :</span><span class="sxs-lookup"><span data-stu-id="7c883-1059">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="7c883-1060">La création de nouvelles connexions peut être lente et coûteuse.</span><span class="sxs-lookup"><span data-stu-id="7c883-1060">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="7c883-1061">Il n’y a aucune garantie que le client a lu la réponse avant la fermeture de la connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-1061">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="7c883-1062">L’appel `Abort` de doit être rare et réservé aux cas d’erreur graves, et non aux erreurs courantes.</span><span class="sxs-lookup"><span data-stu-id="7c883-1062">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="7c883-1063">Appelez uniquement `Abort` lorsqu’un problème spécifique doit être résolu.</span><span class="sxs-lookup"><span data-stu-id="7c883-1063">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="7c883-1064">Par exemple, appelez `Abort` si des clients malveillants essaient des `POST` données ou s’il existe un bogue dans le code client qui génère des demandes volumineuses ou de nombreuses requêtes.</span><span class="sxs-lookup"><span data-stu-id="7c883-1064">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="7c883-1065">N’appelez pas `Abort` pour les situations d’erreur courantes, telles que HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="7c883-1065">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="7c883-1066">L’appel de [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) avant d’appeler `Abort` garantit que le serveur a terminé l’écriture de la réponse.</span><span class="sxs-lookup"><span data-stu-id="7c883-1066">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="7c883-1067">Toutefois, le comportement du client n’est pas prévisible et il est possible qu’il ne lise pas la réponse avant l’abandon de la connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-1067">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="7c883-1068">Ce processus est différent pour HTTP/2, car le protocole prend en charge l’abandon des flux de demande individuels sans fermer la connexion.</span><span class="sxs-lookup"><span data-stu-id="7c883-1068">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="7c883-1069">Le délai d’attente de drainage de cinq secondes ne s’applique pas.</span><span class="sxs-lookup"><span data-stu-id="7c883-1069">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="7c883-1070">S’il existe des données de corps de demande non lues après avoir complété une réponse, le serveur envoie une trame HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="7c883-1070">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="7c883-1071">Les trames de données de corps de requête supplémentaires sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="7c883-1071">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="7c883-1072">Si possible, il est préférable que les clients utilisent l’en-tête de demande [expect : 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) et attendent que le serveur réponde avant de commencer à envoyer le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="7c883-1072">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="7c883-1073">Cela donne au client la possibilité d’examiner la réponse et de l’abandonner avant d’envoyer des données inutiles.</span><span class="sxs-lookup"><span data-stu-id="7c883-1073">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c883-1074">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7c883-1074">Additional resources</span></span>

* <span data-ttu-id="7c883-1075">Lorsque vous utilisez des sockets UNIX sur Linux, le socket n’est pas supprimé automatiquement lors de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="7c883-1075">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="7c883-1076">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="7c883-1076">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="7c883-1077">Document RFC 7230 : syntaxe et routage des messages (section 5.4 : Hôte)</span><span class="sxs-lookup"><span data-stu-id="7c883-1077">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
