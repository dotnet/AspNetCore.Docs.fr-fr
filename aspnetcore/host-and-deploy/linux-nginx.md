---
title: Héberger ASP.NET Core sur Linux avec Nginx
author: rick-anderson
description: Découvrez comment configurer Nginx comme proxy inverse sur Ubuntu 16.04 pour transférer le trafic HTTP vers une application web ASP.NET Core s’exécutant sur Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: c4e0d70b41221f272bb4b1fe82cfa531ec6fcf15
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431062"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="24d34-103">Héberger ASP.NET Core sur Linux avec Nginx</span><span class="sxs-lookup"><span data-stu-id="24d34-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="24d34-104">De [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="24d34-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="24d34-105">Ce guide explique comment configurer un environnement ASP.NET Core prêt pour la production sur un serveur Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="24d34-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="24d34-106">Ces instructions fonctionnent normalement avec les versions d’Ubuntu plus récentes, mais elles n’ont pas fait l’objet de tests avec ces versions.</span><span class="sxs-lookup"><span data-stu-id="24d34-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="24d34-107">Pour plus d’informations sur les autres distributions Linux prises en charge par ASP.NET Core, consultez [Prérequis pour .NET Core sur Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="24d34-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="24d34-108">Pour Ubuntu 14,04, `supervisord` est recommandé en tant que solution pour surveiller le processus Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d34-108">For Ubuntu 14.04, `supervisord` is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="24d34-109">`systemd` n’est pas disponible sur Ubuntu 14,04.</span><span class="sxs-lookup"><span data-stu-id="24d34-109">`systemd` isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="24d34-110">Pour obtenir des instructions pour Ubuntu 14.04, consultez la [version précédente de cette rubrique](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="24d34-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="24d34-111">Ce guide montre comment effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="24d34-111">This guide:</span></span>

* <span data-ttu-id="24d34-112">Placer une application ASP.NET Core existante derrière un serveur proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="24d34-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="24d34-113">Configurer le serveur proxy inverse pour transférer les requêtes au serveur web Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d34-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="24d34-114">S’assurer que l’application web s’exécute au démarrage en tant que démon.</span><span class="sxs-lookup"><span data-stu-id="24d34-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="24d34-115">Configurer un outil de gestion des processus pour aider à redémarrer l’application web.</span><span class="sxs-lookup"><span data-stu-id="24d34-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="24d34-116">Prérequis</span><span class="sxs-lookup"><span data-stu-id="24d34-116">Prerequisites</span></span>

1. <span data-ttu-id="24d34-117">Accédez à un serveur Ubuntu 16.04 avec un compte d’utilisateur standard disposant de privilèges sudo.</span><span class="sxs-lookup"><span data-stu-id="24d34-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="24d34-118">Installez le runtime .NET Core sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="24d34-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="24d34-119">Visitez la [page Télécharger .net Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="24d34-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="24d34-120">Sélectionnez la dernière version non préliminaire de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="24d34-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="24d34-121">Téléchargez le dernier Runtime non Preview dans le tableau sous **exécuter des applications-Runtime**.</span><span class="sxs-lookup"><span data-stu-id="24d34-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="24d34-122">Sélectionnez le lien **des instructions du gestionnaire de package** Linux et suivez les instructions Ubuntu pour votre version d’Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="24d34-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="24d34-123">Une application ASP.NET Core existante.</span><span class="sxs-lookup"><span data-stu-id="24d34-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="24d34-124">À tout moment après la mise à niveau de l’infrastructure partagée, redémarrez le ASP.NET Core les applications hébergées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="24d34-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="24d34-125">Publier et copier sur l’application</span><span class="sxs-lookup"><span data-stu-id="24d34-125">Publish and copy over the app</span></span>

<span data-ttu-id="24d34-126">Configurez l’application pour un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="24d34-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="24d34-127">Si l’application est exécutée localement et n’est pas configurée pour établir des connexions sécurisées (HTTPS), adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="24d34-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="24d34-128">Configurez l’application pour gérer les connexions locales sécurisées.</span><span class="sxs-lookup"><span data-stu-id="24d34-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="24d34-129">Pour plus d’informations, consultez la section [Configuration HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="24d34-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="24d34-130">Supprimez `https://localhost:5001` (le cas échéant) de la `applicationUrl` propriété dans le `Properties/launchSettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="24d34-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the `Properties/launchSettings.json` file.</span></span>

<span data-ttu-id="24d34-131">Exécutez [dotnet Publish](/dotnet/core/tools/dotnet-publish) à partir de l’environnement de développement pour empaqueter une application dans un répertoire (par exemple, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish` , où l’espace réservé `{TARGET FRAMEWORK MONIKER}` est le moniker du Framework cible/TFM) qui peut s’exécuter sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="24d34-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, where the placeholder `{TARGET FRAMEWORK MONIKER}` is the Target Framework Moniker/TFM) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="24d34-132">L’application peut également être publiée en tant que [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) si vous préférez ne pas gérer le runtime .NET Core sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="24d34-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="24d34-133">Copiez l’application ASP.NET Core sur le serveur à l’aide d’un outil qui s’intègre dans le flux de travail de l’organisation (par exemple, `SCP` `SFTP` ).</span><span class="sxs-lookup"><span data-stu-id="24d34-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, `SCP`, `SFTP`).</span></span> <span data-ttu-id="24d34-134">Il est courant de rechercher des applications Web dans le `var` répertoire (par exemple, `var/www/helloapp` ).</span><span class="sxs-lookup"><span data-stu-id="24d34-134">It's common to locate web apps under the `var` directory (for example, `var/www/helloapp`).</span></span>

> [!NOTE]
> <span data-ttu-id="24d34-135">Dans un scénario de déploiement en production, un workflow d’intégration continue effectue le travail de publication de l’application et de copie des composants sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="24d34-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="24d34-136">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="24d34-136">Test the app:</span></span>

1. <span data-ttu-id="24d34-137">À partir de la ligne de commande, exécutez l’application : `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="24d34-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="24d34-138">Dans un navigateur, accédez à `http://<serveraddress>:<port>` pour vérifier que l’application fonctionne sur Linux localement.</span><span class="sxs-lookup"><span data-stu-id="24d34-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="24d34-139">Configurer un serveur proxy inverse</span><span class="sxs-lookup"><span data-stu-id="24d34-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="24d34-140">Un proxy inverse est une configuration courante pour traiter les applications web dynamiques.</span><span class="sxs-lookup"><span data-stu-id="24d34-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="24d34-141">Un proxy inverse met fin à la requête HTTP et la transfère à l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24d34-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="24d34-142">Utiliser un serveur proxy inverse</span><span class="sxs-lookup"><span data-stu-id="24d34-142">Use a reverse proxy server</span></span>

<span data-ttu-id="24d34-143">Kestrel est idéal pour servir du contenu dynamique à partir d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24d34-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="24d34-144">Toutefois, les fonctionnalités de service web ne sont pas aussi complètes que les serveurs tels qu’IIS, Apache ou Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d34-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="24d34-145">Un serveur proxy inverse peut décharger du travail tel que le traitement du contenu statique, la mise en cache des requêtes, la compression des requêtes et l’arrêt HTTPS à partir du serveur HTTP.</span><span class="sxs-lookup"><span data-stu-id="24d34-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="24d34-146">Un serveur proxy inverse peut résider sur un ordinateur dédié ou peut être déployé à côté d’un serveur HTTP.</span><span class="sxs-lookup"><span data-stu-id="24d34-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="24d34-147">Pour les besoins de ce guide, nous utilisons une seule instance de Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d34-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="24d34-148">Elle s’exécute sur le même serveur, en plus du serveur HTTP.</span><span class="sxs-lookup"><span data-stu-id="24d34-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="24d34-149">Selon les exigences, un paramétrage différent peut être choisi.</span><span class="sxs-lookup"><span data-stu-id="24d34-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="24d34-150">Étant donné que les demandes sont transférées par le proxy inverse, utilisez l' [intergiciel d’en-têtes transmis](xref:host-and-deploy/proxy-load-balancer) à partir du [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) Package.</span><span class="sxs-lookup"><span data-stu-id="24d34-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) package.</span></span> <span data-ttu-id="24d34-151">Le middleware met à jour le `Request.Scheme`, à l’aide de l’en-tête `X-Forwarded-Proto`, afin que les URI de redirection et d’autres stratégies de sécurité fonctionnent correctement.</span><span class="sxs-lookup"><span data-stu-id="24d34-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="24d34-152">Appelez la <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> méthode en haut de `Startup.Configure` avant d’appeler un autre intergiciel.</span><span class="sxs-lookup"><span data-stu-id="24d34-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="24d34-153">Configurez le middleware pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` :</span><span class="sxs-lookup"><span data-stu-id="24d34-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="24d34-154">Si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifiée au middleware, les en-têtes par défaut à transférer sont `None`.</span><span class="sxs-lookup"><span data-stu-id="24d34-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="24d34-155">Les proxies s’exécutant sur des adresses de bouclage ( `127.0.0.0/8` , `[::1]` ), y compris l’adresse localhost standard ( `127.0.0.1` ), sont approuvés par défaut.</span><span class="sxs-lookup"><span data-stu-id="24d34-155">Proxies running on loopback addresses (`127.0.0.0/8`, `[::1]`), including the standard localhost address (`127.0.0.1`), are trusted by default.</span></span> <span data-ttu-id="24d34-156">Si d’autres proxys ou réseaux approuvés au sein de l’organisation gèrent les requêtes entre Internet et le serveur web, ajoutez-les à la liste des <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> ou des <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="24d34-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="24d34-157">L’exemple suivant ajoute un serveur proxy approuvé avec l’adresse IP 10.0.0.100 au middleware des en-têtes transférés `KnownProxies` dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="24d34-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="24d34-158">Pour plus d'informations, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="24d34-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="24d34-159">Installer Nginx</span><span class="sxs-lookup"><span data-stu-id="24d34-159">Install Nginx</span></span>

<span data-ttu-id="24d34-160">Utilisez `apt-get` pour installer Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d34-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="24d34-161">Le programme d’installation crée un `systemd` script init qui exécute Nginx en tant que démon au démarrage du système.</span><span class="sxs-lookup"><span data-stu-id="24d34-161">The installer creates a `systemd` init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="24d34-162">Suivez les instructions d’installation pour Ubuntu sur le site [Nginx : Les packages officiels Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="24d34-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="24d34-163">Si des modules Nginx facultatifs sont requis, il peut s’avérer nécessaire de configurer Nginx à partir de la source.</span><span class="sxs-lookup"><span data-stu-id="24d34-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="24d34-164">Comme il s’agit de l’installation initiale de Nginx, vous devez le démarrer explicitement en exécutant :</span><span class="sxs-lookup"><span data-stu-id="24d34-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="24d34-165">Vérifiez qu’un navigateur affiche la page d’accueil par défaut de Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d34-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="24d34-166">La page d’accueil est accessible à l’adresse `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="24d34-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="24d34-167">Configurer Nginx</span><span class="sxs-lookup"><span data-stu-id="24d34-167">Configure Nginx</span></span>

<span data-ttu-id="24d34-168">Pour configurer Nginx en tant que proxy inverse pour transférer les requêtes HTTP à votre application ASP.NET Core, modifiez `/etc/nginx/sites-available/default` .</span><span class="sxs-lookup"><span data-stu-id="24d34-168">To configure Nginx as a reverse proxy to forward HTTP requests to your ASP.NET Core app, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="24d34-169">Ouvrez-le dans un éditeur de texte et remplacez le contenu par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="24d34-169">Open it in a text editor, and replace the contents with the following:</span></span>

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

<span data-ttu-id="24d34-170">Si l’application est une SignalR Blazor Server application ou <xref:signalr/scale#linux-with-nginx> , consultez et, <xref:blazor/host-and-deploy/server#linux-with-nginx> respectivement, pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="24d34-170">If the app is a SignalR or Blazor Server app see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="24d34-171">Si aucun `server_name` ne correspond, Nginx utilise le serveur par défaut.</span><span class="sxs-lookup"><span data-stu-id="24d34-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="24d34-172">Si aucun serveur par défaut n’est défini, le premier serveur dans le fichier de configuration est le serveur par défaut.</span><span class="sxs-lookup"><span data-stu-id="24d34-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="24d34-173">En guise de bonne pratique, ajoutez un serveur par défaut spécifique qui retourne un code d’état 444 dans votre fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="24d34-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="24d34-174">Voici un exemple de configuration de serveur par défaut :</span><span class="sxs-lookup"><span data-stu-id="24d34-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="24d34-175">Avec les fichier de configuration et le serveur par défaut précédents, Nginx accepte le trafic public sur le port 80 avec un en-tête d’hôte `example.com` ou `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="24d34-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="24d34-176">Les requêtes qui ne correspondent pas à ces hôtes ne sont pas transférées à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d34-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="24d34-177">Nginx transfère les requêtes correspondantes à Kestrel à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d34-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="24d34-178">Consultez [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) (Comment nginx traite une requête) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="24d34-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="24d34-179">Pour changer le port/l’adresse IP Kestrel, consultez [Kestrel : configuration du point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="24d34-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="24d34-180">La spécification d’une [directive server_name](https://nginx.org/docs/http/server_names.html) incorrecte expose votre application à des failles de sécurité.</span><span class="sxs-lookup"><span data-stu-id="24d34-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="24d34-181">Une liaison générique de sous-domaine (par exemple, `*.example.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable).</span><span class="sxs-lookup"><span data-stu-id="24d34-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="24d34-182">Consultez la [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="24d34-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="24d34-183">Une fois la configuration de Nginx établie, exécutez `sudo nginx -t` pour vérifier la syntaxe des fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="24d34-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="24d34-184">Si le test de fichier de configuration réussit, forcez Nginx à appliquer les modifications en exécutant `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="24d34-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="24d34-185">Pour exécuter directement l’application sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="24d34-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="24d34-186">Accédez au répertoire de l’application.</span><span class="sxs-lookup"><span data-stu-id="24d34-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="24d34-187">Exécutez l’application : `dotnet <app_assembly.dll>`, où `app_assembly.dll` est le nom de fichier d’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="24d34-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="24d34-188">Si l’application s’exécute sur le serveur, mais ne répond pas sur Internet, vérifiez que le port 80 est ouvert sur le pare-feu du serveur.</span><span class="sxs-lookup"><span data-stu-id="24d34-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="24d34-189">Si vous utilisez une machine virtuelle Azure Ubuntu, ajoutez une règle de groupe de sécurité réseau (NSG) qui autorise le trafic entrant sur le port 80.</span><span class="sxs-lookup"><span data-stu-id="24d34-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="24d34-190">Il est inutile d’activer une règle de trafic sortant sur le port 80, car le trafic sortant est accordé automatiquement quand la règle de trafic entrant est activée.</span><span class="sxs-lookup"><span data-stu-id="24d34-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="24d34-191">Quand vous avez terminé de tester l’application, arrêtez-la avec `Ctrl+C` depuis l’invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="24d34-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="24d34-192">Surveiller l’application</span><span class="sxs-lookup"><span data-stu-id="24d34-192">Monitor the app</span></span>

<span data-ttu-id="24d34-193">Le serveur est configuré pour transférer les requêtes faites à `http://<serveraddress>:80` à l’application ASP.NET Core s’exécutant sur Kestrel à l’adresse `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d34-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="24d34-194">Toutefois, Nginx n’est pas configuré pour gérer le processus Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d34-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="24d34-195">`systemd` peut être utilisé pour créer un fichier de service pour démarrer et surveiller l’application Web sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="24d34-195">`systemd` can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="24d34-196">`systemd` est un système init qui fournit de nombreuses fonctionnalités puissantes pour le démarrage, l’arrêt et la gestion des processus.</span><span class="sxs-lookup"><span data-stu-id="24d34-196">`systemd` is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="24d34-197">Créer le fichier de service</span><span class="sxs-lookup"><span data-stu-id="24d34-197">Create the service file</span></span>

<span data-ttu-id="24d34-198">Créez le fichier de définition de service :</span><span class="sxs-lookup"><span data-stu-id="24d34-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="24d34-199">Voici un exemple de fichier de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="24d34-199">The following is an example service file for the app:</span></span>

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

<span data-ttu-id="24d34-200">Dans l’exemple précédent, l’utilisateur qui gère le service est spécifié par l' `User` option.</span><span class="sxs-lookup"><span data-stu-id="24d34-200">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="24d34-201">L’utilisateur ( `www-data` ) doit exister et avoir la propriété correcte des fichiers de l’application.</span><span class="sxs-lookup"><span data-stu-id="24d34-201">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="24d34-202">Utilisez `TimeoutStopSec` pour configurer la durée d’attente de l’arrêt de l’application après la réception du signal d’interruption initial.</span><span class="sxs-lookup"><span data-stu-id="24d34-202">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="24d34-203">Si l’application ne s’arrête pas pendant cette période, le signal SIGKILL est émis pour mettre fin à l’application.</span><span class="sxs-lookup"><span data-stu-id="24d34-203">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="24d34-204">Indiquez la valeur en secondes sans unité (par exemple, `150`), une valeur d’intervalle de temps (par exemple, `2min 30s`) ou `infinity` pour désactiver le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="24d34-204">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="24d34-205">`TimeoutStopSec` la valeur par défaut est la valeur de `DefaultTimeoutStopSec` dans le fichier de configuration du gestionnaire ( `systemd-system.conf` , `system.conf.d` , `systemd-user.conf` , `user.conf.d` ).</span><span class="sxs-lookup"><span data-stu-id="24d34-205">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span></span> <span data-ttu-id="24d34-206">Le délai d’expiration par défaut pour la plupart des distributions est de 90 secondes.</span><span class="sxs-lookup"><span data-stu-id="24d34-206">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="24d34-207">Linux possède un système de fichiers respectant la casse.</span><span class="sxs-lookup"><span data-stu-id="24d34-207">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="24d34-208">Si `ASPNETCORE_ENVIRONMENT` la valeur est `Production` , la recherche du fichier de configuration `appsettings.Production.json` n’a pas lieu `appsettings.production.json` .</span><span class="sxs-lookup"><span data-stu-id="24d34-208">Setting `ASPNETCORE_ENVIRONMENT` to `Production` results in searching for the configuration file `appsettings.Production.json`, not `appsettings.production.json`.</span></span>

<span data-ttu-id="24d34-209">Certaines valeurs (par exemple, les chaînes de connexion SQL) doivent être placées dans une séquence d’échappement afin que les fournisseurs de configuration puissent lire les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="24d34-209">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="24d34-210">Utilisez la commande suivante pour générer une valeur correctement placée dans une séquence d’échappement en vue de son utilisation dans le fichier de configuration :</span><span class="sxs-lookup"><span data-stu-id="24d34-210">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24d34-211">Les séparateurs `:` (signe deux-points) ne sont pas pris en charge dans les noms de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="24d34-211">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="24d34-212">Utilisez un double trait de soulignement (`__`) à la place d’un signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="24d34-212">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="24d34-213">Le [fournisseur de configuration de variables d’environnement](xref:fundamentals/configuration/index#environment-variables) convertit les doubles traits de soulignement en signes deux-points quand les variables d’environnement sont lues dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="24d34-213">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="24d34-214">Dans l’exemple suivant, la clé de chaîne de connexion `ConnectionStrings:DefaultConnection` est définie dans le fichier de définition de service en tant que `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="24d34-214">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="24d34-215">Les séparateurs `:` (signe deux-points) ne sont pas pris en charge dans les noms de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="24d34-215">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="24d34-216">Utilisez un double trait de soulignement (`__`) à la place d’un signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="24d34-216">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="24d34-217">Le [fournisseur de configuration de variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) convertit les doubles traits de soulignement en signes deux-points quand les variables d’environnement sont lues dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="24d34-217">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="24d34-218">Dans l’exemple suivant, la clé de chaîne de connexion `ConnectionStrings:DefaultConnection` est définie dans le fichier de définition de service en tant que `ConnectionStrings__DefaultConnection` :</span><span class="sxs-lookup"><span data-stu-id="24d34-218">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="24d34-219">Enregistrez le fichier et activez le service.</span><span class="sxs-lookup"><span data-stu-id="24d34-219">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="24d34-220">Démarrez le service et vérifiez qu’il s’exécute.</span><span class="sxs-lookup"><span data-stu-id="24d34-220">Start the service and verify that it's running.</span></span>

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

<span data-ttu-id="24d34-221">Avec le proxy inverse configuré et Kestrel géré via `systemd` , l’application Web est entièrement configurée et accessible à partir d’un navigateur sur l’ordinateur local à l’adresse `http://localhost` .</span><span class="sxs-lookup"><span data-stu-id="24d34-221">With the reverse proxy configured and Kestrel managed through `systemd`, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="24d34-222">Elle est également accessible à partir d’un ordinateur distant, sauf en cas de blocage par un pare-feu.</span><span class="sxs-lookup"><span data-stu-id="24d34-222">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="24d34-223">Si l’on inspecte les en-têtes de réponse, on constate que l’en-tête `Server` indique que l’application ASP.NET Core est traitée par Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d34-223">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="24d34-224">Afficher les journaux d’activité</span><span class="sxs-lookup"><span data-stu-id="24d34-224">View logs</span></span>

<span data-ttu-id="24d34-225">Puisque l’application web utilisant Kestrel est gérée à l’aide de `systemd`, tous les processus et les événements sont enregistrés dans un journal centralisé.</span><span class="sxs-lookup"><span data-stu-id="24d34-225">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="24d34-226">Toutefois, ce journal inclut toutes les entrées pour tous les services et les processus gérés par `systemd`.</span><span class="sxs-lookup"><span data-stu-id="24d34-226">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="24d34-227">Pour afficher les éléments propres à `kestrel-helloapp.service`, utilisez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="24d34-227">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="24d34-228">Si vous voulez appliquer un filtrage supplémentaire, des options chronologiques, comme `--since today`, `--until 1 hour ago` ou une combinaison de ces options, peuvent réduire la quantité d’entrées retournées.</span><span class="sxs-lookup"><span data-stu-id="24d34-228">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="24d34-229">Protection de données</span><span class="sxs-lookup"><span data-stu-id="24d34-229">Data protection</span></span>

<span data-ttu-id="24d34-230">La [pile de protection des données ASP.net Core](xref:security/data-protection/introduction) est utilisée par plusieurs ASP.net Core [intergiciels](xref:fundamentals/middleware/index), y compris l’intergiciel (middleware) d’authentification (par exemple, cookie intergiciel) et les protections CSRF (cross-site request falsification).</span><span class="sxs-lookup"><span data-stu-id="24d34-230">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="24d34-231">Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes.</span><span class="sxs-lookup"><span data-stu-id="24d34-231">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="24d34-232">Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="24d34-232">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="24d34-233">Si le Key Ring est stocké en mémoire, au redémarrage de l’application :</span><span class="sxs-lookup"><span data-stu-id="24d34-233">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="24d34-234">Tous les cookie jetons d’authentification de base sont invalidés.</span><span class="sxs-lookup"><span data-stu-id="24d34-234">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="24d34-235">Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande.</span><span class="sxs-lookup"><span data-stu-id="24d34-235">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="24d34-236">toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées.</span><span class="sxs-lookup"><span data-stu-id="24d34-236">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="24d34-237">Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) et des ASP.NET Core les de la [MVC cookie ](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="24d34-237">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="24d34-238">Pour configurer la protection des données de façon à conserver et chiffrer le porte-clés (Key Ring), consultez :</span><span class="sxs-lookup"><span data-stu-id="24d34-238">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="24d34-239">Longs champs d'en-tête de demande</span><span class="sxs-lookup"><span data-stu-id="24d34-239">Long request header fields</span></span>

<span data-ttu-id="24d34-240">Les paramètres par défaut du serveur proxy limitent généralement les champs d’en-tête de demande à 4 K ou 8 K en fonction de la plateforme.</span><span class="sxs-lookup"><span data-stu-id="24d34-240">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="24d34-241">Une application peut nécessiter des champs plus longs que la valeur par défaut (par exemple, les applications qui utilisent [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="24d34-241">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="24d34-242">Si des champs plus longs sont requis, les paramètres par défaut du serveur proxy doivent être ajustés.</span><span class="sxs-lookup"><span data-stu-id="24d34-242">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="24d34-243">Les valeurs à appliquer dépendent du scénario.</span><span class="sxs-lookup"><span data-stu-id="24d34-243">The values to apply depend on the scenario.</span></span> <span data-ttu-id="24d34-244">Pour plus d'informations, voir la documentation du serveur.</span><span class="sxs-lookup"><span data-stu-id="24d34-244">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="24d34-245">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="24d34-245">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="24d34-246">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="24d34-246">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="24d34-247">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="24d34-247">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="24d34-248">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="24d34-248">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="24d34-249">N’augmentez pas les valeurs par défaut des mémoires tampons de proxy à moins que ce ne soit nécessaire.</span><span class="sxs-lookup"><span data-stu-id="24d34-249">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="24d34-250">Leur augmentation augmente le risque de dépassement de mémoire tampon (dépassement de capacité) et d’attaques par déni de service (DoS) par des utilisateurs malveillants.</span><span class="sxs-lookup"><span data-stu-id="24d34-250">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="24d34-251">Sécuriser l’application</span><span class="sxs-lookup"><span data-stu-id="24d34-251">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="24d34-252">Activer AppArmor</span><span class="sxs-lookup"><span data-stu-id="24d34-252">Enable AppArmor</span></span>

<span data-ttu-id="24d34-253">Linux Security Modules (LSM) est un framework qui fait partie du noyau Linux depuis Linux 2.6.</span><span class="sxs-lookup"><span data-stu-id="24d34-253">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="24d34-254">LSM prend en charge différentes implémentations de modules de sécurité.</span><span class="sxs-lookup"><span data-stu-id="24d34-254">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="24d34-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) est un LSM qui implémente un système de contrôle d’accès obligatoire permettant de confiner le programme à un ensemble limité de ressources.</span><span class="sxs-lookup"><span data-stu-id="24d34-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="24d34-256">Vérifiez qu’AppArmor est activé et configuré correctement.</span><span class="sxs-lookup"><span data-stu-id="24d34-256">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="24d34-257">Configurer le pare-feu</span><span class="sxs-lookup"><span data-stu-id="24d34-257">Configure the firewall</span></span>

<span data-ttu-id="24d34-258">Fermez tous les ports externes qui ne sont pas en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="24d34-258">Close off all external ports that are not in use.</span></span> <span data-ttu-id="24d34-259">Le pare-feu UFW fournit une interface de commande pour `iptables` la configuration du pare-feu.</span><span class="sxs-lookup"><span data-stu-id="24d34-259">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="24d34-260">Un pare-feu mal configuré bloque l’accès à l’ensemble du système.</span><span class="sxs-lookup"><span data-stu-id="24d34-260">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="24d34-261">Faute d’avoir spécifié le port SSH approprié, vous ne pourrez pas accéder au système si vous utilisez SSH pour vous y connecter.</span><span class="sxs-lookup"><span data-stu-id="24d34-261">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="24d34-262">Le numéro de port par défaut est 22.</span><span class="sxs-lookup"><span data-stu-id="24d34-262">The default port is 22.</span></span> <span data-ttu-id="24d34-263">Pour plus d’informations, consultez la [présentation d’ufw](https://help.ubuntu.com/community/UFW) et le [manuel](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="24d34-263">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="24d34-264">Installez `ufw` et configurez-le de façon à autoriser le trafic sur les ports nécessaires.</span><span class="sxs-lookup"><span data-stu-id="24d34-264">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="24d34-265">Sécuriser Nginx</span><span class="sxs-lookup"><span data-stu-id="24d34-265">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="24d34-266">Changer le nom de la réponse Nginx</span><span class="sxs-lookup"><span data-stu-id="24d34-266">Change the Nginx response name</span></span>

<span data-ttu-id="24d34-267">Modifiez `src/http/ngx_http_header_filter_module.c`:</span><span class="sxs-lookup"><span data-stu-id="24d34-267">Edit `src/http/ngx_http_header_filter_module.c`:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="24d34-268">Configurer les options</span><span class="sxs-lookup"><span data-stu-id="24d34-268">Configure options</span></span>

<span data-ttu-id="24d34-269">Configurez le serveur avec les modules nécessaires supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="24d34-269">Configure the server with additional required modules.</span></span> <span data-ttu-id="24d34-270">Pour renforcer l’application, vous pouvez utiliser un pare-feu d’application web tel que [ModSecurity](https://www.modsecurity.org/).</span><span class="sxs-lookup"><span data-stu-id="24d34-270">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="24d34-271">Configuration HTTPS</span><span class="sxs-lookup"><span data-stu-id="24d34-271">HTTPS configuration</span></span>

<span data-ttu-id="24d34-272">**Configurer l’application pour les connexions locales sécurisées (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="24d34-272">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="24d34-273">La commande [dotnet Run](/dotnet/core/tools/dotnet-run) utilise le fichier de l’application `Properties/launchSettings.json` , qui configure l’application pour écouter les URL fournies par la `applicationUrl` propriété (par exemple, `https://localhost:5001;http://localhost:5000` ).</span><span class="sxs-lookup"><span data-stu-id="24d34-273">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's `Properties/launchSettings.json` file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="24d34-274">Configurez l’application pour qu’elle utilise un certificat en développement pour la `dotnet run` commande ou l’environnement de développement (<kbd>F5</kbd> ou <kbd>CTRL</kbd> + <kbd>F5</kbd> dans Visual Studio code) à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="24d34-274">Configure the app to use a certificate in development for the `dotnet run` command or development environment (<kbd>F5</kbd> or <kbd>Ctrl</kbd>+<kbd>F5</kbd> in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="24d34-275">[Remplacer le certificat par défaut dans la configuration](xref:fundamentals/servers/kestrel#configuration) ( *recommandé* )</span><span class="sxs-lookup"><span data-stu-id="24d34-275">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) ( *Recommended* )</span></span>
* [<span data-ttu-id="24d34-276">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="24d34-276">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="24d34-277">**Configurer le proxy inverse pour les connexions clientes sécurisées (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="24d34-277">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="24d34-278">Configurez le serveur pour qu’il écoute le trafic HTTPS sur le port `443` en spécifiant un certificat valide émis par une autorité de certificat approuvée.</span><span class="sxs-lookup"><span data-stu-id="24d34-278">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="24d34-279">Renforcez la sécurité en utilisant certaines des pratiques décrites dans le `/etc/nginx/nginx.conf` fichier suivant.</span><span class="sxs-lookup"><span data-stu-id="24d34-279">Harden the security by employing some of the practices depicted in the following `/etc/nginx/nginx.conf` file.</span></span> <span data-ttu-id="24d34-280">Vous pouvez par exemple choisir un chiffrement plus fort et rediriger tout le trafic sur HTTP vers HTTPS.</span><span class="sxs-lookup"><span data-stu-id="24d34-280">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

  > [!NOTE]
  > <span data-ttu-id="24d34-281">Pour les environnements de développement, nous vous recommandons d’utiliser des redirections temporaires (302) au lieu de redirections permanentes (301).</span><span class="sxs-lookup"><span data-stu-id="24d34-281">For development environments, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="24d34-282">La mise en cache des liens peut provoquer un comportement instable dans les environnements de développement.</span><span class="sxs-lookup"><span data-stu-id="24d34-282">Link caching can cause unstable behavior in development environments.</span></span>

* <span data-ttu-id="24d34-283">L’ajout d’un en-tête `HTTP Strict-Transport-Security` (HSTS) garantit que toutes les demandes ultérieures du client s’effectuent par le biais du protocole HTTPS.</span><span class="sxs-lookup"><span data-stu-id="24d34-283">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

  <span data-ttu-id="24d34-284">Pour obtenir des instructions importantes sur HSTS, consultez <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts> .</span><span class="sxs-lookup"><span data-stu-id="24d34-284">For important guidance on HSTS, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

* <span data-ttu-id="24d34-285">Si le protocole HTTPs est désactivé à l’avenir, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="24d34-285">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="24d34-286">N’ajoutez pas l’en-tête HSTS.</span><span class="sxs-lookup"><span data-stu-id="24d34-286">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="24d34-287">Choisissez une valeur abrégée `max-age` .</span><span class="sxs-lookup"><span data-stu-id="24d34-287">Choose a short `max-age` value.</span></span>

<span data-ttu-id="24d34-288">Ajoutez le `/etc/nginx/proxy.conf` fichier de configuration :</span><span class="sxs-lookup"><span data-stu-id="24d34-288">Add the `/etc/nginx/proxy.conf` configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="24d34-289">**Remplacez** le contenu du `/etc/nginx/nginx.conf` fichier de configuration par le fichier suivant.</span><span class="sxs-lookup"><span data-stu-id="24d34-289">**Replace** the contents of the `/etc/nginx/nginx.conf` configuration file with the following file.</span></span> <span data-ttu-id="24d34-290">Dans l’exemple, les sections `http` et `server` figurent dans un même fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="24d34-290">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="24d34-291">Blazor WebAssembly les applications requièrent une plus grande `burst` valeur de paramètre pour prendre en charge le plus grand nombre de requêtes effectuées par une application.</span><span class="sxs-lookup"><span data-stu-id="24d34-291">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="24d34-292">Pour plus d'informations, consultez <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="24d34-292">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="24d34-293">Sécuriser Nginx contre le détournement de clic</span><span class="sxs-lookup"><span data-stu-id="24d34-293">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="24d34-294">Le [détournement de clic](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), également appelé *UI redress attack* , est une attaque malveillante qui amène le visiteur d’un site web à cliquer sur un lien ou un bouton sur une page différente de celle qu’il est en train de visiter.</span><span class="sxs-lookup"><span data-stu-id="24d34-294">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack* , is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="24d34-295">Utilisez `X-FRAME-OPTIONS` pour sécuriser le site.</span><span class="sxs-lookup"><span data-stu-id="24d34-295">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="24d34-296">Pour atténuer les attaques par détournement de clic :</span><span class="sxs-lookup"><span data-stu-id="24d34-296">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="24d34-297">Modifiez le `nginx.conf` fichier :</span><span class="sxs-lookup"><span data-stu-id="24d34-297">Edit the `nginx.conf` file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="24d34-298">Ajoutez la ligne : `add_header X-Frame-Options "SAMEORIGIN";`</span><span class="sxs-lookup"><span data-stu-id="24d34-298">Add the line: `add_header X-Frame-Options "SAMEORIGIN";`</span></span>

1. <span data-ttu-id="24d34-299">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="24d34-299">Save the file.</span></span>
1. <span data-ttu-id="24d34-300">Redémarrez Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d34-300">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="24d34-301">Détection de type MIME</span><span class="sxs-lookup"><span data-stu-id="24d34-301">MIME-type sniffing</span></span>

<span data-ttu-id="24d34-302">Cet en-tête empêche la plupart des navigateurs de détourner le type MIME d’une réponse et de remplacer le type de contenu déclaré, car l’en-tête indique au navigateur qu’il ne doit pas substituer le type de contenu de la réponse.</span><span class="sxs-lookup"><span data-stu-id="24d34-302">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="24d34-303">Avec l' `nosniff` option, si le serveur indique que le contenu est `text/html` , le navigateur le restitue sous la forme `text/html` .</span><span class="sxs-lookup"><span data-stu-id="24d34-303">With the `nosniff` option, if the server says the content is `text/html`, the browser renders it as `text/html`.</span></span>

1. <span data-ttu-id="24d34-304">Modifiez le `nginx.conf` fichier :</span><span class="sxs-lookup"><span data-stu-id="24d34-304">Edit the `nginx.conf` file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="24d34-305">Ajoutez la ligne : `add_header X-Content-Type-Options "nosniff";`</span><span class="sxs-lookup"><span data-stu-id="24d34-305">Add the line: `add_header X-Content-Type-Options "nosniff";`</span></span>

1. <span data-ttu-id="24d34-306">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="24d34-306">Save the file.</span></span>
1. <span data-ttu-id="24d34-307">Redémarrez Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d34-307">Restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="24d34-308">Suggestions supplémentaires pour Nginx</span><span class="sxs-lookup"><span data-stu-id="24d34-308">Additional Nginx suggestions</span></span>

<span data-ttu-id="24d34-309">Après la mise à niveau de l’infrastructure partagée sur le serveur, redémarrez le ASP.NET Core les applications hébergées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="24d34-309">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24d34-310">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="24d34-310">Additional resources</span></span>

* [<span data-ttu-id="24d34-311">Prérequis pour .NET Core sur Linux</span><span class="sxs-lookup"><span data-stu-id="24d34-311">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <span data-ttu-id="24d34-312">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx : versions binaires : packages Debian/Ubuntu officiels).</span><span class="sxs-lookup"><span data-stu-id="24d34-312">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)</span></span>
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="24d34-313">[NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded)</span><span class="sxs-lookup"><span data-stu-id="24d34-313">[NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)</span></span>
