---
title: Héberger ASP.NET Core sur Linux avec Nginx
author: rick-anderson
description: Découvrez comment configurer Nginx comme un proxy inverse sur Ubuntu 16,04 pour transférer le trafic HTTP vers une application Web ASP.NET Core s’exécutant sur Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 8a593654fa31e643e7c239f361f035589c75ce98
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395251"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="9668b-103">Héberger ASP.NET Core sur Linux avec Nginx</span><span class="sxs-lookup"><span data-stu-id="9668b-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="9668b-104">De [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="9668b-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="9668b-105">Ce guide explique comment configurer un environnement ASP.NET Core prêt pour la production sur un serveur Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="9668b-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="9668b-106">Ces instructions fonctionnent normalement avec les versions d’Ubuntu plus récentes, mais elles n’ont pas fait l’objet de tests avec ces versions.</span><span class="sxs-lookup"><span data-stu-id="9668b-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="9668b-107">Pour plus d’informations sur les autres distributions Linux prises en charge par ASP.NET Core, consultez [Prérequis pour .NET Core sur Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="9668b-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="9668b-108">Pour Ubuntu 14,04, `supervisord` est recommandé en tant que solution pour surveiller le processus Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9668b-108">For Ubuntu 14.04, `supervisord` is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="9668b-109">`systemd` n’est pas disponible sur Ubuntu 14,04.</span><span class="sxs-lookup"><span data-stu-id="9668b-109">`systemd` isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="9668b-110">Pour obtenir des instructions pour Ubuntu 14.04, consultez la [version précédente de cette rubrique](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="9668b-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="9668b-111">Ce guide montre comment effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="9668b-111">This guide:</span></span>

* <span data-ttu-id="9668b-112">Placer une application ASP.NET Core existante derrière un serveur proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="9668b-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="9668b-113">Configurer le serveur proxy inverse pour transférer les requêtes au serveur web Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9668b-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="9668b-114">S’assurer que l’application web s’exécute au démarrage en tant que démon.</span><span class="sxs-lookup"><span data-stu-id="9668b-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="9668b-115">Configurer un outil de gestion des processus pour aider à redémarrer l’application web.</span><span class="sxs-lookup"><span data-stu-id="9668b-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9668b-116">Prérequis</span><span class="sxs-lookup"><span data-stu-id="9668b-116">Prerequisites</span></span>

* <span data-ttu-id="9668b-117">Accédez à un serveur Ubuntu 16.04 avec un compte d’utilisateur standard disposant de privilèges sudo.</span><span class="sxs-lookup"><span data-stu-id="9668b-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="9668b-118">Le [Runtime .net](/dotnet/core/install/linux) non-preview le plus récent installé sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9668b-118">The latest non-preview [.NET runtime installed](/dotnet/core/install/linux) on the server.</span></span>
* <span data-ttu-id="9668b-119">Une application ASP.NET Core existante.</span><span class="sxs-lookup"><span data-stu-id="9668b-119">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="9668b-120">À tout moment après la mise à niveau de l’infrastructure partagée, redémarrez le ASP.NET Core les applications hébergées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="9668b-120">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="9668b-121">Publier et copier sur l’application</span><span class="sxs-lookup"><span data-stu-id="9668b-121">Publish and copy over the app</span></span>

<span data-ttu-id="9668b-122">Configurez l’application pour un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="9668b-122">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="9668b-123">Si l’application est exécutée localement et n’est pas configurée pour établir des connexions sécurisées (HTTPS), adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="9668b-123">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="9668b-124">Configurez l’application pour gérer les connexions locales sécurisées.</span><span class="sxs-lookup"><span data-stu-id="9668b-124">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="9668b-125">Pour plus d’informations, consultez la section [Configuration HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="9668b-125">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="9668b-126">Supprimez `https://localhost:5001` (le cas échéant) de la `applicationUrl` propriété dans le `Properties/launchSettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="9668b-126">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the `Properties/launchSettings.json` file.</span></span>

<span data-ttu-id="9668b-127">Exécutez [dotnet Publish](/dotnet/core/tools/dotnet-publish) à partir de l’environnement de développement pour empaqueter une application dans un répertoire (par exemple, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish` , où l’espace réservé `{TARGET FRAMEWORK MONIKER}` est le moniker du Framework cible/TFM) qui peut s’exécuter sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="9668b-127">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, where the placeholder `{TARGET FRAMEWORK MONIKER}` is the Target Framework Moniker/TFM) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="9668b-128">L’application peut également être publiée en tant que [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) si vous préférez ne pas gérer le runtime .NET Core sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9668b-128">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="9668b-129">Copiez l’application ASP.NET Core sur le serveur à l’aide d’un outil qui s’intègre dans le flux de travail de l’organisation (par exemple, `SCP` `SFTP` ).</span><span class="sxs-lookup"><span data-stu-id="9668b-129">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, `SCP`, `SFTP`).</span></span> <span data-ttu-id="9668b-130">Il est courant de rechercher des applications Web dans le `var` répertoire (par exemple, `var/www/helloapp` ).</span><span class="sxs-lookup"><span data-stu-id="9668b-130">It's common to locate web apps under the `var` directory (for example, `var/www/helloapp`).</span></span>

> [!NOTE]
> <span data-ttu-id="9668b-131">Dans un scénario de déploiement en production, un workflow d’intégration continue effectue le travail de publication de l’application et de copie des composants sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9668b-131">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="9668b-132">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="9668b-132">Test the app:</span></span>

1. <span data-ttu-id="9668b-133">À partir de la ligne de commande, exécutez l’application : `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="9668b-133">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="9668b-134">Dans un navigateur, accédez à `http://<serveraddress>:<port>` pour vérifier que l’application fonctionne sur Linux localement.</span><span class="sxs-lookup"><span data-stu-id="9668b-134">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="9668b-135">Configurer un serveur proxy inverse</span><span class="sxs-lookup"><span data-stu-id="9668b-135">Configure a reverse proxy server</span></span>

<span data-ttu-id="9668b-136">Un proxy inverse est une configuration courante pour traiter les applications web dynamiques.</span><span class="sxs-lookup"><span data-stu-id="9668b-136">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="9668b-137">Un proxy inverse met fin à la requête HTTP et la transfère à l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9668b-137">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="9668b-138">Utiliser un serveur proxy inverse</span><span class="sxs-lookup"><span data-stu-id="9668b-138">Use a reverse proxy server</span></span>

<span data-ttu-id="9668b-139">Kestrel est idéal pour servir du contenu dynamique à partir d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9668b-139">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="9668b-140">Toutefois, les fonctionnalités de service web ne sont pas aussi complètes que les serveurs tels qu’IIS, Apache ou Nginx.</span><span class="sxs-lookup"><span data-stu-id="9668b-140">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="9668b-141">Un serveur proxy inverse peut décharger du travail tel que le traitement du contenu statique, la mise en cache des requêtes, la compression des requêtes et l’arrêt HTTPS à partir du serveur HTTP.</span><span class="sxs-lookup"><span data-stu-id="9668b-141">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="9668b-142">Un serveur proxy inverse peut résider sur un ordinateur dédié ou peut être déployé à côté d’un serveur HTTP.</span><span class="sxs-lookup"><span data-stu-id="9668b-142">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="9668b-143">Pour les besoins de ce guide, nous utilisons une seule instance de Nginx.</span><span class="sxs-lookup"><span data-stu-id="9668b-143">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="9668b-144">Elle s’exécute sur le même serveur, en plus du serveur HTTP.</span><span class="sxs-lookup"><span data-stu-id="9668b-144">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="9668b-145">Selon les exigences, un paramétrage différent peut être choisi.</span><span class="sxs-lookup"><span data-stu-id="9668b-145">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="9668b-146">Étant donné que les demandes sont transférées par le proxy inverse, utilisez l' [intergiciel d’en-têtes transmis](xref:host-and-deploy/proxy-load-balancer) à partir du [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) Package.</span><span class="sxs-lookup"><span data-stu-id="9668b-146">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) package.</span></span> <span data-ttu-id="9668b-147">Le middleware met à jour le `Request.Scheme`, à l’aide de l’en-tête `X-Forwarded-Proto`, afin que les URI de redirection et d’autres stratégies de sécurité fonctionnent correctement.</span><span class="sxs-lookup"><span data-stu-id="9668b-147">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="9668b-148">Appelez la <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> méthode en haut de `Startup.Configure` avant d’appeler un autre intergiciel.</span><span class="sxs-lookup"><span data-stu-id="9668b-148">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="9668b-149">Configurez le middleware pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` :</span><span class="sxs-lookup"><span data-stu-id="9668b-149">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="9668b-150">Si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifiée au middleware, les en-têtes par défaut à transférer sont `None`.</span><span class="sxs-lookup"><span data-stu-id="9668b-150">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="9668b-151">Les proxies s’exécutant sur des adresses de bouclage ( `127.0.0.0/8` , `[::1]` ), y compris l’adresse localhost standard ( `127.0.0.1` ), sont approuvés par défaut.</span><span class="sxs-lookup"><span data-stu-id="9668b-151">Proxies running on loopback addresses (`127.0.0.0/8`, `[::1]`), including the standard localhost address (`127.0.0.1`), are trusted by default.</span></span> <span data-ttu-id="9668b-152">Si d’autres proxys ou réseaux approuvés au sein de l’organisation gèrent les requêtes entre Internet et le serveur web, ajoutez-les à la liste des <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> ou des <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="9668b-152">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="9668b-153">L’exemple suivant ajoute un serveur proxy approuvé avec l’adresse IP 10.0.0.100 au middleware des en-têtes transférés `KnownProxies` dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9668b-153">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="9668b-154">Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="9668b-154">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="9668b-155">Installer Nginx</span><span class="sxs-lookup"><span data-stu-id="9668b-155">Install Nginx</span></span>

<span data-ttu-id="9668b-156">Utilisez `apt-get` pour installer Nginx.</span><span class="sxs-lookup"><span data-stu-id="9668b-156">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="9668b-157">Le programme d’installation crée un `systemd` script init qui exécute Nginx en tant que démon au démarrage du système.</span><span class="sxs-lookup"><span data-stu-id="9668b-157">The installer creates a `systemd` init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="9668b-158">Suivez les instructions d’installation pour Ubuntu sur le site [Nginx : Les packages officiels Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="9668b-158">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="9668b-159">Si des modules Nginx facultatifs sont requis, il peut s’avérer nécessaire de configurer Nginx à partir de la source.</span><span class="sxs-lookup"><span data-stu-id="9668b-159">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="9668b-160">Comme il s’agit de l’installation initiale de Nginx, vous devez le démarrer explicitement en exécutant :</span><span class="sxs-lookup"><span data-stu-id="9668b-160">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="9668b-161">Vérifiez qu’un navigateur affiche la page d’accueil par défaut de Nginx.</span><span class="sxs-lookup"><span data-stu-id="9668b-161">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="9668b-162">La page d’accueil est accessible à l’adresse `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="9668b-162">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="9668b-163">Configurer Nginx</span><span class="sxs-lookup"><span data-stu-id="9668b-163">Configure Nginx</span></span>

<span data-ttu-id="9668b-164">Pour configurer Nginx en tant que proxy inverse pour transférer les requêtes HTTP à votre application ASP.NET Core, modifiez `/etc/nginx/sites-available/default` .</span><span class="sxs-lookup"><span data-stu-id="9668b-164">To configure Nginx as a reverse proxy to forward HTTP requests to your ASP.NET Core app, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="9668b-165">Ouvrez-le dans un éditeur de texte et remplacez le contenu par l’extrait de code suivant :</span><span class="sxs-lookup"><span data-stu-id="9668b-165">Open it in a text editor, and replace the contents with the following snippet:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="9668b-166">Si l’application est une SignalR Blazor Server application ou, consultez <xref:signalr/scale#linux-with-nginx> et, respectivement, <xref:blazor/host-and-deploy/server#linux-with-nginx> pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="9668b-166">If the app is a SignalR or Blazor Server app, see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="9668b-167">Si aucun `server_name` ne correspond, Nginx utilise le serveur par défaut.</span><span class="sxs-lookup"><span data-stu-id="9668b-167">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="9668b-168">Si aucun serveur par défaut n’est défini, le premier serveur dans le fichier de configuration est le serveur par défaut.</span><span class="sxs-lookup"><span data-stu-id="9668b-168">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="9668b-169">Il est recommandé d’ajouter un serveur par défaut spécifique qui retourne le code d’État 444 dans votre fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="9668b-169">As a best practice, add a specific default server that returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="9668b-170">Voici un exemple de configuration de serveur par défaut :</span><span class="sxs-lookup"><span data-stu-id="9668b-170">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9668b-171">Avec les fichier de configuration et le serveur par défaut précédents, Nginx accepte le trafic public sur le port 80 avec un en-tête d’hôte `example.com` ou `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="9668b-171">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="9668b-172">Les requêtes qui ne correspondent pas à ces hôtes ne sont pas transférées à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9668b-172">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="9668b-173">Nginx transfère les requêtes correspondantes à Kestrel à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9668b-173">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="9668b-174">Pour plus d’informations, consultez [How Nginx traite une demande](https://nginx.org/docs/http/request_processing.html).</span><span class="sxs-lookup"><span data-stu-id="9668b-174">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="9668b-175">Pour changer le port/l’adresse IP Kestrel, consultez [Kestrel : configuration du point de terminaison](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="9668b-175">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9668b-176">Avec les fichier de configuration et le serveur par défaut précédents, Nginx accepte le trafic public sur le port 80 avec un en-tête d’hôte `example.com` ou `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="9668b-176">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="9668b-177">Les requêtes qui ne correspondent pas à ces hôtes ne sont pas transférées à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9668b-177">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="9668b-178">Nginx transfère les requêtes correspondantes à Kestrel à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9668b-178">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="9668b-179">Pour plus d’informations, consultez [How Nginx traite une demande](https://nginx.org/docs/http/request_processing.html).</span><span class="sxs-lookup"><span data-stu-id="9668b-179">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="9668b-180">Pour changer le port/l’adresse IP Kestrel, consultez [Kestrel : configuration du point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="9668b-180">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="9668b-181">La spécification d’une [directive server_name](https://nginx.org/docs/http/server_names.html) incorrecte expose votre application à des failles de sécurité.</span><span class="sxs-lookup"><span data-stu-id="9668b-181">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="9668b-182">Une liaison générique de sous-domaine (par exemple, `*.example.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable).</span><span class="sxs-lookup"><span data-stu-id="9668b-182">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="9668b-183">Pour plus d’informations, consultez la [section rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="9668b-183">For more information, see [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

<span data-ttu-id="9668b-184">Une fois la configuration de Nginx établie, exécutez `sudo nginx -t` pour vérifier la syntaxe des fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="9668b-184">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="9668b-185">Si le test de fichier de configuration réussit, forcez Nginx à appliquer les modifications en exécutant `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="9668b-185">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="9668b-186">Pour exécuter directement l’application sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="9668b-186">To directly run the app on the server:</span></span>

1. <span data-ttu-id="9668b-187">Accédez au répertoire de l’application.</span><span class="sxs-lookup"><span data-stu-id="9668b-187">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="9668b-188">Exécutez l’application : `dotnet <app_assembly.dll>`, où `app_assembly.dll` est le nom de fichier d’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="9668b-188">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="9668b-189">Si l’application s’exécute sur le serveur mais ne répond pas sur Internet, vérifiez le pare-feu du serveur et vérifiez que le port 80 est ouvert.</span><span class="sxs-lookup"><span data-stu-id="9668b-189">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm port 80 is open.</span></span> <span data-ttu-id="9668b-190">Si vous utilisez une machine virtuelle Azure Ubuntu, ajoutez une règle de groupe de sécurité réseau (NSG) qui autorise le trafic entrant sur le port 80.</span><span class="sxs-lookup"><span data-stu-id="9668b-190">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="9668b-191">Il est inutile d’activer une règle de trafic sortant sur le port 80, car le trafic sortant est accordé automatiquement quand la règle de trafic entrant est activée.</span><span class="sxs-lookup"><span data-stu-id="9668b-191">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="9668b-192">Lorsque vous avez terminé de tester l’application, arrêtez l’application en <kbd>appuyant sur CTRL</kbd>  +  <kbd>C</kbd> à l’invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="9668b-192">When done testing the app, shut down the app with <kbd>Ctrl</kbd> + <kbd>C</kbd> at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="9668b-193">Surveiller l’application</span><span class="sxs-lookup"><span data-stu-id="9668b-193">Monitor the app</span></span>

<span data-ttu-id="9668b-194">Le serveur est configuré pour transférer les demandes effectuées à `http://<serveraddress>:80` sur l’application ASP.net Core s’exécutant sur Kestrel à l’adresse `http://127.0.0.1:5000` .</span><span class="sxs-lookup"><span data-stu-id="9668b-194">The server is set up to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="9668b-195">Toutefois, Nginx n’est pas configuré pour gérer le processus Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9668b-195">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="9668b-196">`systemd` peut être utilisé pour créer un fichier de service pour démarrer et surveiller l’application Web sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="9668b-196">`systemd` can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="9668b-197">`systemd` est un système init qui fournit de nombreuses fonctionnalités puissantes pour le démarrage, l’arrêt et la gestion des processus.</span><span class="sxs-lookup"><span data-stu-id="9668b-197">`systemd` is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="9668b-198">Créer le fichier de service</span><span class="sxs-lookup"><span data-stu-id="9668b-198">Create the service file</span></span>

<span data-ttu-id="9668b-199">Créez le fichier de définition de service :</span><span class="sxs-lookup"><span data-stu-id="9668b-199">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="9668b-200">L’exemple suivant est un fichier de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="9668b-200">The following example is a service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="9668b-201">Dans l’exemple précédent, l’utilisateur qui gère le service est spécifié par l' `User` option.</span><span class="sxs-lookup"><span data-stu-id="9668b-201">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="9668b-202">L’utilisateur ( `www-data` ) doit exister et avoir la propriété correcte des fichiers de l’application.</span><span class="sxs-lookup"><span data-stu-id="9668b-202">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="9668b-203">Utilisez `TimeoutStopSec` pour configurer la durée d’attente de l’arrêt de l’application après la réception du signal d’interruption initial.</span><span class="sxs-lookup"><span data-stu-id="9668b-203">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="9668b-204">Si l’application ne s’arrête pas pendant cette période, le signal SIGKILL est émis pour mettre fin à l’application.</span><span class="sxs-lookup"><span data-stu-id="9668b-204">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="9668b-205">Indiquez la valeur en secondes sans unité (par exemple, `150`), une valeur d’intervalle de temps (par exemple, `2min 30s`) ou `infinity` pour désactiver le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9668b-205">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="9668b-206">`TimeoutStopSec` la valeur par défaut est la valeur de `DefaultTimeoutStopSec` dans le fichier de configuration du gestionnaire ( `systemd-system.conf` , `system.conf.d` , `systemd-user.conf` , `user.conf.d` ).</span><span class="sxs-lookup"><span data-stu-id="9668b-206">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span></span> <span data-ttu-id="9668b-207">Le délai d’expiration par défaut pour la plupart des distributions est de 90 secondes.</span><span class="sxs-lookup"><span data-stu-id="9668b-207">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="9668b-208">Linux possède un système de fichiers respectant la casse.</span><span class="sxs-lookup"><span data-stu-id="9668b-208">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="9668b-209">Si `ASPNETCORE_ENVIRONMENT` la valeur est `Production` , la recherche du fichier de configuration `appsettings.Production.json` n’a pas lieu `appsettings.production.json` .</span><span class="sxs-lookup"><span data-stu-id="9668b-209">Setting `ASPNETCORE_ENVIRONMENT` to `Production` results in searching for the configuration file `appsettings.Production.json`, not `appsettings.production.json`.</span></span>

<span data-ttu-id="9668b-210">Certaines valeurs (par exemple, les chaînes de connexion SQL) doivent être placées dans une séquence d’échappement afin que les fournisseurs de configuration puissent lire les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="9668b-210">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="9668b-211">Utilisez la commande suivante pour générer une valeur correctement placée dans une séquence d’échappement en vue de son utilisation dans le fichier de configuration :</span><span class="sxs-lookup"><span data-stu-id="9668b-211">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9668b-212">Les séparateurs `:` (signe deux-points) ne sont pas pris en charge dans les noms de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="9668b-212">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="9668b-213">Utilisez un double trait de soulignement (`__`) à la place d’un signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="9668b-213">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="9668b-214">Le [fournisseur de configuration de variables d’environnement](xref:fundamentals/configuration/index#environment-variables) convertit les doubles traits de soulignement en signes deux-points quand les variables d’environnement sont lues dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="9668b-214">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="9668b-215">Dans l’exemple suivant, la clé de chaîne de connexion `ConnectionStrings:DefaultConnection` est définie dans le fichier de définition de service en tant que `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="9668b-215">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9668b-216">Les séparateurs `:` (signe deux-points) ne sont pas pris en charge dans les noms de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="9668b-216">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="9668b-217">Utilisez un double trait de soulignement (`__`) à la place d’un signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="9668b-217">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="9668b-218">Le [fournisseur de configuration de variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) convertit les doubles traits de soulignement en signes deux-points quand les variables d’environnement sont lues dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="9668b-218">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="9668b-219">Dans l’exemple suivant, la clé de chaîne de connexion `ConnectionStrings:DefaultConnection` est définie dans le fichier de définition de service en tant que `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="9668b-219">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="9668b-220">Enregistrez le fichier et activez le service.</span><span class="sxs-lookup"><span data-stu-id="9668b-220">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="9668b-221">Démarrez le service et vérifiez qu’il s’exécute.</span><span class="sxs-lookup"><span data-stu-id="9668b-221">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="9668b-222">Avec le proxy inverse configuré et Kestrel géré via `systemd` , l’application Web est entièrement configurée et accessible à partir d’un navigateur sur l’ordinateur local à l’adresse `http://localhost` .</span><span class="sxs-lookup"><span data-stu-id="9668b-222">With the reverse proxy configured and Kestrel managed through `systemd`, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="9668b-223">Elle est également accessible à partir d’un ordinateur distant, sauf en cas de blocage par un pare-feu.</span><span class="sxs-lookup"><span data-stu-id="9668b-223">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="9668b-224">Si l’on inspecte les en-têtes de réponse, on constate que l’en-tête `Server` indique que l’application ASP.NET Core est traitée par Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9668b-224">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="9668b-225">Afficher les journaux d’activité</span><span class="sxs-lookup"><span data-stu-id="9668b-225">View logs</span></span>

<span data-ttu-id="9668b-226">Puisque l’application web utilisant Kestrel est gérée à l’aide de `systemd`, tous les processus et les événements sont enregistrés dans un journal centralisé.</span><span class="sxs-lookup"><span data-stu-id="9668b-226">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="9668b-227">Toutefois, ce journal inclut toutes les entrées pour tous les services et les processus gérés par `systemd`.</span><span class="sxs-lookup"><span data-stu-id="9668b-227">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="9668b-228">Pour afficher les éléments propres à `kestrel-helloapp.service`, utilisez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9668b-228">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="9668b-229">Pour un filtrage approfondi, les options d’heure telles que `--since today` , `--until 1 hour ago` ou une combinaison de ces options peuvent réduire le nombre d’entrées retournées.</span><span class="sxs-lookup"><span data-stu-id="9668b-229">For further filtering, time options such as `--since today`, `--until 1 hour ago`, or a combination of these can reduce the number of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="9668b-230">Protection de données</span><span class="sxs-lookup"><span data-stu-id="9668b-230">Data protection</span></span>

<span data-ttu-id="9668b-231">La [pile de protection des données ASP.net Core](xref:security/data-protection/introduction) est utilisée par plusieurs ASP.net Core [intergiciels](xref:fundamentals/middleware/index), y compris l’intergiciel (middleware) d’authentification (par exemple, cookie intergiciel) et les protections CSRF (cross-site request falsification).</span><span class="sxs-lookup"><span data-stu-id="9668b-231">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="9668b-232">Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes.</span><span class="sxs-lookup"><span data-stu-id="9668b-232">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="9668b-233">Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="9668b-233">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="9668b-234">Si le Key Ring est stocké en mémoire, au redémarrage de l’application :</span><span class="sxs-lookup"><span data-stu-id="9668b-234">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="9668b-235">Tous les cookie jetons d’authentification de base sont invalidés.</span><span class="sxs-lookup"><span data-stu-id="9668b-235">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="9668b-236">Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande.</span><span class="sxs-lookup"><span data-stu-id="9668b-236">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="9668b-237">toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées.</span><span class="sxs-lookup"><span data-stu-id="9668b-237">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="9668b-238">Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) et des ASP.NET Core les de la [MVC cookie ](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="9668b-238">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="9668b-239">Pour configurer la protection des données de façon à conserver et chiffrer le porte-clés (Key Ring), consultez :</span><span class="sxs-lookup"><span data-stu-id="9668b-239">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="9668b-240">Longs champs d'en-tête de demande</span><span class="sxs-lookup"><span data-stu-id="9668b-240">Long request header fields</span></span>

<span data-ttu-id="9668b-241">Les paramètres par défaut du serveur proxy limitent généralement les champs d’en-tête de demande à 4 K ou 8 K en fonction de la plateforme.</span><span class="sxs-lookup"><span data-stu-id="9668b-241">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="9668b-242">Une application peut nécessiter des champs plus longs que la valeur par défaut (par exemple, les applications qui utilisent [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="9668b-242">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="9668b-243">Si des champs plus longs sont requis, les paramètres par défaut du serveur proxy doivent être ajustés.</span><span class="sxs-lookup"><span data-stu-id="9668b-243">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="9668b-244">Les valeurs à appliquer dépendent du scénario.</span><span class="sxs-lookup"><span data-stu-id="9668b-244">The values to apply depend on the scenario.</span></span> <span data-ttu-id="9668b-245">Pour plus d'informations, voir la documentation du serveur.</span><span class="sxs-lookup"><span data-stu-id="9668b-245">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="9668b-246">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="9668b-246">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="9668b-247">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="9668b-247">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="9668b-248">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="9668b-248">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="9668b-249">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="9668b-249">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="9668b-250">N’augmentez pas les valeurs par défaut des mémoires tampons de proxy à moins que ce ne soit nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9668b-250">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="9668b-251">Leur augmentation augmente le risque de dépassement de mémoire tampon (dépassement de capacité) et d’attaques par déni de service (DoS) par des utilisateurs malveillants.</span><span class="sxs-lookup"><span data-stu-id="9668b-251">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="9668b-252">Sécuriser l’application</span><span class="sxs-lookup"><span data-stu-id="9668b-252">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="9668b-253">Activer AppArmor</span><span class="sxs-lookup"><span data-stu-id="9668b-253">Enable AppArmor</span></span>

<span data-ttu-id="9668b-254">Linux Security Modules (LSM) est un framework qui fait partie du noyau Linux depuis Linux 2.6.</span><span class="sxs-lookup"><span data-stu-id="9668b-254">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="9668b-255">LSM prend en charge différentes implémentations de modules de sécurité.</span><span class="sxs-lookup"><span data-stu-id="9668b-255">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="9668b-256">[AppArmor](https://wiki.ubuntu.com/AppArmor) est un LSM qui implémente un système Access Control obligatoire, ce qui permet de limiter le programme à un ensemble limité de ressources.</span><span class="sxs-lookup"><span data-stu-id="9668b-256">[AppArmor](https://wiki.ubuntu.com/AppArmor) is an LSM that implements a Mandatory Access Control system, which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="9668b-257">Vérifiez qu’AppArmor est activé et configuré correctement.</span><span class="sxs-lookup"><span data-stu-id="9668b-257">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="9668b-258">Configurer le pare-feu</span><span class="sxs-lookup"><span data-stu-id="9668b-258">Configure the firewall</span></span>

<span data-ttu-id="9668b-259">Fermez tous les ports externes qui ne sont pas en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="9668b-259">Close off all external ports that aren't in use.</span></span> <span data-ttu-id="9668b-260">Le pare-feu UFW fournit une interface de commande pour `iptables` la configuration du pare-feu.</span><span class="sxs-lookup"><span data-stu-id="9668b-260">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="9668b-261">Un pare-feu mal configuré bloque l’accès à l’ensemble du système.</span><span class="sxs-lookup"><span data-stu-id="9668b-261">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="9668b-262">Faute d’avoir spécifié le port SSH approprié, vous ne pourrez pas accéder au système si vous utilisez SSH pour vous y connecter.</span><span class="sxs-lookup"><span data-stu-id="9668b-262">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="9668b-263">Le numéro de port par défaut est 22.</span><span class="sxs-lookup"><span data-stu-id="9668b-263">The default port is 22.</span></span> <span data-ttu-id="9668b-264">Pour plus d’informations, consultez la [présentation d’ufw](https://help.ubuntu.com/community/UFW) et le [manuel](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="9668b-264">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="9668b-265">Installez `ufw` et configurez-le de façon à autoriser le trafic sur les ports nécessaires.</span><span class="sxs-lookup"><span data-stu-id="9668b-265">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="9668b-266">Sécuriser Nginx</span><span class="sxs-lookup"><span data-stu-id="9668b-266">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="9668b-267">Changer le nom de la réponse Nginx</span><span class="sxs-lookup"><span data-stu-id="9668b-267">Change the Nginx response name</span></span>

<span data-ttu-id="9668b-268">Modifiez `src/http/ngx_http_header_filter_module.c`:</span><span class="sxs-lookup"><span data-stu-id="9668b-268">Edit `src/http/ngx_http_header_filter_module.c`:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="9668b-269">Configurer les options</span><span class="sxs-lookup"><span data-stu-id="9668b-269">Configure options</span></span>

<span data-ttu-id="9668b-270">Configurez le serveur avec les modules nécessaires supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="9668b-270">Configure the server with additional required modules.</span></span> <span data-ttu-id="9668b-271">Pour renforcer l’application, vous pouvez utiliser un pare-feu d’application web tel que [ModSecurity](https://www.modsecurity.org/).</span><span class="sxs-lookup"><span data-stu-id="9668b-271">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="9668b-272">Configuration HTTPS</span><span class="sxs-lookup"><span data-stu-id="9668b-272">HTTPS configuration</span></span>

<span data-ttu-id="9668b-273">**Configurer l’application pour les connexions locales sécurisées (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="9668b-273">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="9668b-274">La commande [dotnet Run](/dotnet/core/tools/dotnet-run) utilise les *Propriétés/launchSettings.js* de l’application sur le fichier, ce qui configure l’application pour qu’elle écoute les URL fournies par la `applicationUrl` propriété.</span><span class="sxs-lookup"><span data-stu-id="9668b-274">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property.</span></span> <span data-ttu-id="9668b-275">Par exemple : `https://localhost:5001;http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9668b-275">For example, `https://localhost:5001;http://localhost:5000`.</span></span>

<span data-ttu-id="9668b-276">Configurez l’application pour qu’elle utilise un certificat en développement pour la `dotnet run` commande ou l’environnement de développement (<kbd>F5</kbd> ou <kbd>CTRL</kbd> + <kbd>F5</kbd> dans Visual Studio code) à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="9668b-276">Configure the app to use a certificate in development for the `dotnet run` command or development environment (<kbd>F5</kbd> or <kbd>Ctrl</kbd>+<kbd>F5</kbd> in Visual Studio Code) using one of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="9668b-277">[Remplacer le certificat par défaut dans la configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*recommandé*)</span><span class="sxs-lookup"><span data-stu-id="9668b-277">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="9668b-278">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="9668b-278">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="9668b-279">[Remplacer le certificat par défaut dans la configuration](xref:fundamentals/servers/kestrel#configuration) (*recommandé*)</span><span class="sxs-lookup"><span data-stu-id="9668b-279">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="9668b-280">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="9668b-280">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

<span data-ttu-id="9668b-281">**Configurer le proxy inverse pour les connexions clientes sécurisées (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="9668b-281">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="9668b-282">Configurez le serveur pour écouter le trafic HTTPs sur le port 443 en spécifiant un certificat valide émis par une autorité de certification approuvée.</span><span class="sxs-lookup"><span data-stu-id="9668b-282">Configure the server to listen to HTTPS traffic on port 443 by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="9668b-283">Renforcez la sécurité en appliquant certaines des pratiques mentionnées dans le fichier */etc/nginx/nginx.conf* suivant.</span><span class="sxs-lookup"><span data-stu-id="9668b-283">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="9668b-284">Vous pouvez par exemple choisir un chiffrement plus fort et rediriger tout le trafic sur HTTP vers HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9668b-284">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

  > [!NOTE]
  > <span data-ttu-id="9668b-285">Pour les environnements de développement, nous vous recommandons d’utiliser des redirections temporaires (302) au lieu de redirections permanentes (301).</span><span class="sxs-lookup"><span data-stu-id="9668b-285">For development environments, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="9668b-286">La mise en cache des liens peut provoquer un comportement instable dans les environnements de développement.</span><span class="sxs-lookup"><span data-stu-id="9668b-286">Link caching can cause unstable behavior in development environments.</span></span>

* <span data-ttu-id="9668b-287">L’ajout d’un en-tête `HTTP Strict-Transport-Security` (HSTS) garantit que toutes les demandes ultérieures du client s’effectuent par le biais du protocole HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9668b-287">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

  <span data-ttu-id="9668b-288">Pour obtenir des instructions importantes sur HSTS, consultez <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts> .</span><span class="sxs-lookup"><span data-stu-id="9668b-288">For important guidance on HSTS, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

* <span data-ttu-id="9668b-289">Si le protocole HTTPs est désactivé à l’avenir, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="9668b-289">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="9668b-290">N’ajoutez pas l’en-tête HSTS.</span><span class="sxs-lookup"><span data-stu-id="9668b-290">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="9668b-291">Choisissez une valeur abrégée `max-age` .</span><span class="sxs-lookup"><span data-stu-id="9668b-291">Choose a short `max-age` value.</span></span>

<span data-ttu-id="9668b-292">Ajoutez le fichier de configuration */etc/nginx/proxy.conf* :</span><span class="sxs-lookup"><span data-stu-id="9668b-292">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="9668b-293">**Remplacez** le contenu du fichier de configuration */etc/nginx/nginx.conf* par le fichier suivant.</span><span class="sxs-lookup"><span data-stu-id="9668b-293">**Replace** the contents of the */etc/nginx/nginx.conf* configuration file with the following file.</span></span> <span data-ttu-id="9668b-294">Dans l’exemple, les sections `http` et `server` figurent dans un même fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="9668b-294">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="9668b-295">Blazor WebAssembly les applications requièrent une plus grande `burst` valeur de paramètre pour prendre en charge le plus grand nombre de requêtes effectuées par une application.</span><span class="sxs-lookup"><span data-stu-id="9668b-295">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="9668b-296">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="9668b-296">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="9668b-297">Sécuriser Nginx contre le détournement de clic</span><span class="sxs-lookup"><span data-stu-id="9668b-297">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="9668b-298">Le [détournement de clic](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), également appelé *UI redress attack*, est une attaque malveillante qui amène le visiteur d’un site web à cliquer sur un lien ou un bouton sur une page différente de celle qu’il est en train de visiter.</span><span class="sxs-lookup"><span data-stu-id="9668b-298">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="9668b-299">Utilisez `X-FRAME-OPTIONS` pour sécuriser le site.</span><span class="sxs-lookup"><span data-stu-id="9668b-299">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="9668b-300">Pour atténuer les attaques par détournement de clic :</span><span class="sxs-lookup"><span data-stu-id="9668b-300">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="9668b-301">Modifiez le fichier *nginx.conf* :</span><span class="sxs-lookup"><span data-stu-id="9668b-301">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="9668b-302">Ajoutez la ligne : `add_header X-Frame-Options "SAMEORIGIN";`</span><span class="sxs-lookup"><span data-stu-id="9668b-302">Add the line: `add_header X-Frame-Options "SAMEORIGIN";`</span></span>

1. <span data-ttu-id="9668b-303">Enregistrez le fichier .</span><span class="sxs-lookup"><span data-stu-id="9668b-303">Save the file.</span></span>
1. <span data-ttu-id="9668b-304">Redémarrez Nginx.</span><span class="sxs-lookup"><span data-stu-id="9668b-304">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="9668b-305">Détection de type MIME</span><span class="sxs-lookup"><span data-stu-id="9668b-305">MIME-type sniffing</span></span>

<span data-ttu-id="9668b-306">Cet en-tête empêche la plupart des navigateurs de détourner le type MIME d’une réponse et de remplacer le type de contenu déclaré, car l’en-tête indique au navigateur qu’il ne doit pas substituer le type de contenu de la réponse.</span><span class="sxs-lookup"><span data-stu-id="9668b-306">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="9668b-307">Avec l' `nosniff` option, si le serveur indique que le contenu est `text/html` , le navigateur le restitue sous la forme `text/html` .</span><span class="sxs-lookup"><span data-stu-id="9668b-307">With the `nosniff` option, if the server says the content is `text/html`, the browser renders it as `text/html`.</span></span>

1. <span data-ttu-id="9668b-308">Modifiez le fichier *nginx.conf* :</span><span class="sxs-lookup"><span data-stu-id="9668b-308">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="9668b-309">Ajoutez la ligne : `add_header X-Content-Type-Options "nosniff";`</span><span class="sxs-lookup"><span data-stu-id="9668b-309">Add the line: `add_header X-Content-Type-Options "nosniff";`</span></span>

1. <span data-ttu-id="9668b-310">Enregistrez le fichier .</span><span class="sxs-lookup"><span data-stu-id="9668b-310">Save the file.</span></span>
1. <span data-ttu-id="9668b-311">Redémarrez Nginx.</span><span class="sxs-lookup"><span data-stu-id="9668b-311">Restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="9668b-312">Suggestions supplémentaires pour Nginx</span><span class="sxs-lookup"><span data-stu-id="9668b-312">Additional Nginx suggestions</span></span>

<span data-ttu-id="9668b-313">Après la mise à niveau de l’infrastructure partagée sur le serveur, redémarrez le ASP.NET Core les applications hébergées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="9668b-313">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9668b-314">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9668b-314">Additional resources</span></span>

* [<span data-ttu-id="9668b-315">Prérequis pour .NET Core sur Linux</span><span class="sxs-lookup"><span data-stu-id="9668b-315">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <span data-ttu-id="9668b-316">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx : versions binaires : packages Debian/Ubuntu officiels).</span><span class="sxs-lookup"><span data-stu-id="9668b-316">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)</span></span>
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="9668b-317">[NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded)</span><span class="sxs-lookup"><span data-stu-id="9668b-317">[NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)</span></span>
