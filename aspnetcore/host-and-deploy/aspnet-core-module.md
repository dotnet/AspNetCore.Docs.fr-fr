---
title: Module ASP.NET Core
author: rick-anderson
description: En savoir plus sur le module ASP.NET Core pour l’hébergement d’applications ASP.NET Core avec IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: a644214c208ece38bc118c31cf3c9265706706ea
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061494"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="fab92-103">Module ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fab92-103">ASP.NET Core Module</span></span>

<span data-ttu-id="fab92-104">Par [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti)et [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="fab92-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fab92-105">Le module ASP.NET Core est un module IIS natif qui se connecte au pipeline IIS, ce qui permet aux applications ASP.NET Core de fonctionner avec IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="fab92-106">Exécutez ASP.NET Core des applications avec IIS en procédant de l’une des deux :</span><span class="sxs-lookup"><span data-stu-id="fab92-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="fab92-107">L’hébergement d’une application ASP.NET Core à l’intérieur du processus de travail IIS ( `w3wp.exe` ), appelé [modèle d’hébergement in-process](xref:host-and-deploy/iis/in-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="fab92-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="fab92-108">Transfert des requêtes Web à un serveur principal ASP.NET Core application exécutant le serveur Kestrel, appelé [modèle d’hébergement hors processus](xref:host-and-deploy/iis/out-of-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="fab92-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="fab92-109">Il existe des compromis entre chacun des modèles d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="fab92-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="fab92-110">Par défaut, le modèle d’hébergement in-process est utilisé en raison de meilleures performances et Diagnostics.</span><span class="sxs-lookup"><span data-stu-id="fab92-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="fab92-111">Installer le module ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fab92-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="fab92-112">Téléchargez le programme d’installation à l’aide du lien suivant :</span><span class="sxs-lookup"><span data-stu-id="fab92-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="fab92-113">Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)</span><span class="sxs-lookup"><span data-stu-id="fab92-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="fab92-114">Pour plus d’informations sur l’installation du module ASP.NET Core ou sur l’installation de différentes versions du module, consultez [installer le bundle d’hébergement .net Core](xref:host-and-deploy/iis/hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="fab92-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="fab92-115">Pour une expérience de tutoriel sur la publication d'une app ASP.NET Core sur un serveur IIS, voir <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="fab92-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="fab92-116">Le module ASP.NET Core est un module IIS natif qui se connecte dans le pipeline IIS pour effectuer l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="fab92-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="fab92-117">Héberger une application ASP.NET Core à l’intérieur du processus de travail IIS (`w3wp.exe`), appelé [modèle d’hébergement in-process](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="fab92-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="fab92-118">Transférer les requêtes web à une application ASP.NET Core du back-end exécutant le [serveur Kestrel](xref:fundamentals/servers/kestrel), appelé [modèle d’hébergement out-of-process](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="fab92-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="fab92-119">Versions de Windows prises en charge :</span><span class="sxs-lookup"><span data-stu-id="fab92-119">Supported Windows versions:</span></span>

* <span data-ttu-id="fab92-120">Windows 7 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fab92-120">Windows 7 or later</span></span>
* <span data-ttu-id="fab92-121">Windows Server 2012 R2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fab92-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="fab92-122">Lors de l’hébergement in-process, le module utilise une implémentation du serveur in-process pour IIS, nommée serveur HTTP IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="fab92-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="fab92-123">Lors de l’hébergement out-of-process, le module fonctionne uniquement avec Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fab92-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="fab92-124">Le module ne fonctionne pas avec [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="fab92-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="fab92-125">Modèles d'hébergement</span><span class="sxs-lookup"><span data-stu-id="fab92-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="fab92-126">Modèle d’hébergement in-process</span><span class="sxs-lookup"><span data-stu-id="fab92-126">In-process hosting model</span></span>

<span data-ttu-id="fab92-127">ASP.NET Core les applications par défaut au modèle d’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="fab92-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="fab92-128">Les caractéristiques suivantes s’appliquent lors de l’hébergement in-process :</span><span class="sxs-lookup"><span data-stu-id="fab92-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="fab92-129">Le serveur HTTP IIS (`IISHttpServer`) est utilisé à la place du serveur [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="fab92-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="fab92-130">Pour in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> pour :</span><span class="sxs-lookup"><span data-stu-id="fab92-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="fab92-131">Enregistrer `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="fab92-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="fab92-132">Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fab92-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="fab92-133">Configurer l’hôte pour capturer des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="fab92-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="fab92-134">L’[attribut requestTimeout](#attributes-of-the-aspnetcore-element) ne s’applique pas à l’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="fab92-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="fab92-135">Le partage d’un pool d’applications entre les applications n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fab92-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="fab92-136">Utilisez un pool d’applications par application.</span><span class="sxs-lookup"><span data-stu-id="fab92-136">Use one app pool per app.</span></span>

* <span data-ttu-id="fab92-137">Lorsque vous utilisez [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou que vous placez manuellement un [ `app_offline.htm` fichier dans le déploiement](xref:host-and-deploy/iis/index#locked-deployment-files), l’application risque de ne pas s’arrêter immédiatement s’il existe une connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="fab92-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="fab92-138">Par exemple, une connexion WebSocket peut retarder l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="fab92-139">L’architecture (nombre de bits) de l’application et le runtime installé (x64 ou x86) doivent correspondre à l’architecture du pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="fab92-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="fab92-140">Les déconnexions du client sont détectées.</span><span class="sxs-lookup"><span data-stu-id="fab92-140">Client disconnects are detected.</span></span> <span data-ttu-id="fab92-141">Le [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) jeton d’annulation est annulé lorsque le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="fab92-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="fab92-142">Dans ASP.NET Core 2.2.1 ou version antérieure, <xref:System.IO.Directory.GetCurrentDirectory*> retourne le répertoire Worker du processus démarré par IIS plutôt que le répertoire de l’application (par exemple, `C:\Windows\System32\inetsrv` pour `w3wp.exe` ).</span><span class="sxs-lookup"><span data-stu-id="fab92-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="fab92-143">Pour obtenir un exemple de code qui définit le répertoire actif de l’application, consultez la [ `CurrentDirectoryHelpers` classe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="fab92-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="fab92-144">Appelez la méthode `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="fab92-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="fab92-145">Les appels suivants à <xref:System.IO.Directory.GetCurrentDirectory*> fournissent le répertoire de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="fab92-146">Dans le cas d’un hébergement in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> n’est pas appelé en interne pour initialiser un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fab92-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="fab92-147">Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="fab92-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="fab92-148">Si vous transformez les revendications avec une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, appelez <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pour ajouter des services d’authentification :</span><span class="sxs-lookup"><span data-stu-id="fab92-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="fab92-149">[Les déploiements de package Web (à fichier unique)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fab92-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="fab92-150">Modèle d’hébergement out-of-process</span><span class="sxs-lookup"><span data-stu-id="fab92-150">Out-of-process hosting model</span></span>

<span data-ttu-id="fab92-151">Pour configurer une application pour l’hébergement hors processus, affectez à la propriété la valeur `<AspNetCoreHostingModel>` `OutOfProcess` dans le fichier projet ( `.csproj` ) :</span><span class="sxs-lookup"><span data-stu-id="fab92-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="fab92-152">L’hébergement in-process est défini avec `InProcess` , qui est la valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="fab92-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="fab92-153">La valeur de ne respecte pas la `<AspNetCoreHostingModel>` casse, donc `inprocess` et `outofprocess` sont des valeurs valides.</span><span class="sxs-lookup"><span data-stu-id="fab92-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="fab92-154">Le serveur [Kestrel](xref:fundamentals/servers/kestrel) est utilisé à la place du serveur HTTP IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="fab92-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="fab92-155">Pour out-of-process, les [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) appels <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> à :</span><span class="sxs-lookup"><span data-stu-id="fab92-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="fab92-156">Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fab92-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="fab92-157">Configurer l’hôte pour capturer des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="fab92-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="fab92-158">Modifications du modèle d’hébergement</span><span class="sxs-lookup"><span data-stu-id="fab92-158">Hosting model changes</span></span>

<span data-ttu-id="fab92-159">Si le `hostingModel` paramètre est modifié dans le `web.config` fichier (expliqué dans la section [configuration `web.config` avec](#configuration-with-webconfig) ), le module recycle le processus de travail pour IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="fab92-160">Pour IIS Express, le module ne recycle pas le processus de travail, mais déclenche plutôt un arrêt approprié du processus IIS Express en cours.</span><span class="sxs-lookup"><span data-stu-id="fab92-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="fab92-161">La requête suivante à l’application génère un nouveau processus IIS Express.</span><span class="sxs-lookup"><span data-stu-id="fab92-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="fab92-162">Nom du processus</span><span class="sxs-lookup"><span data-stu-id="fab92-162">Process name</span></span>

<span data-ttu-id="fab92-163">`Process.GetCurrentProcess().ProcessName` signale `w3wp`/`iisexpress` (in-process) ou `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="fab92-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="fab92-164">De nombreux modules natifs, comme l’authentification Windows, restent actifs.</span><span class="sxs-lookup"><span data-stu-id="fab92-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="fab92-165">Pour obtenir plus d’informations sur les modules IIS actifs avec le module ASP.NET Core, consultez <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="fab92-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="fab92-166">Le module ASP.NET Core peut également  :</span><span class="sxs-lookup"><span data-stu-id="fab92-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="fab92-167">Définir des variables d’environnement pour le processus de travail.</span><span class="sxs-lookup"><span data-stu-id="fab92-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="fab92-168">Journaliser la sortie de stdout dans un stockage de fichiers pour résoudre les problèmes de démarrage.</span><span class="sxs-lookup"><span data-stu-id="fab92-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="fab92-169">Transférer des jetons d’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="fab92-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="fab92-170">Comment installer et utiliser le module ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fab92-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="fab92-171">Pour obtenir des instructions sur l’installation du module ASP.NET Core, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="fab92-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="fab92-172">Configuration avec web.config</span><span class="sxs-lookup"><span data-stu-id="fab92-172">Configuration with web.config</span></span>

<span data-ttu-id="fab92-173">Le module ASP.NET Core est configuré avec la section `aspNetCore` du nœud `system.webServer` dans le fichier *web.config* du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="fab92-174">Le `web.config` fichier suivant est publié pour un [déploiement dépendant du Framework](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) et configure le module ASP.net Core pour gérer les demandes de site :</span><span class="sxs-lookup"><span data-stu-id="fab92-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="fab92-175">Le fichier *web.config* suivant est publié pour un [déploiement autonome](/dotnet/articles/core/deploying/#self-contained-deployments-scd) :</span><span class="sxs-lookup"><span data-stu-id="fab92-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="fab92-176">La <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriété a la valeur `false` pour indiquer que les paramètres spécifiés dans l' [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) élément ne sont pas hérités par les applications qui résident dans un sous-répertoire de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="fab92-177">Lorsqu’une application est déployée sur [Azure App Service](https://azure.microsoft.com/services/app-service/), le chemin d’accès `stdoutLogFile` est défini sur `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="fab92-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="fab92-178">Le chemin d’accès enregistre les journaux stdout dans le `LogFiles` dossier, qui est un emplacement créé automatiquement par le service.</span><span class="sxs-lookup"><span data-stu-id="fab92-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="fab92-179">Pour plus d’informations sur la configuration d’une sous-application IIS, consultez <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="fab92-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="fab92-180">Attributs de l’élément aspNetCore</span><span class="sxs-lookup"><span data-stu-id="fab92-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="fab92-181">Attribut</span><span class="sxs-lookup"><span data-stu-id="fab92-181">Attribute</span></span> | <span data-ttu-id="fab92-182">Description</span><span class="sxs-lookup"><span data-stu-id="fab92-182">Description</span></span> | <span data-ttu-id="fab92-183">Default</span><span class="sxs-lookup"><span data-stu-id="fab92-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="fab92-184">Attribut de chaîne facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-184">Optional string attribute.</span></span></p><p><span data-ttu-id="fab92-185">Arguments pour l’exécutable spécifié dans **processPath** .</span><span class="sxs-lookup"><span data-stu-id="fab92-185">Arguments to the executable specified in **processPath** .</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="fab92-186">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-187">Si la valeur est true, la page **502.5 - Échec du processus** est supprimée, et la page de code d’état 502 configurée dans le fichier *web.config* est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="fab92-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="fab92-188">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-189">Si la valeur est true, le jeton est transmis au processus enfant qui écoute `%ASPNETCORE_PORT%` en tant qu’en-tête `'MS-ASPNETCORE-WINAUTHTOKEN'` par demande.</span><span class="sxs-lookup"><span data-stu-id="fab92-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="fab92-190">Il incombe à ce processus d’appeler CloseHandle sur ce jeton par demande.</span><span class="sxs-lookup"><span data-stu-id="fab92-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="fab92-191">Attribut de chaîne facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-191">Optional string attribute.</span></span></p><p><span data-ttu-id="fab92-192">Spécifie le modèle d’hébergement comme in-process ( `InProcess` / `inprocess` ) ou out-of-process ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="fab92-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="fab92-193">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-194">Spécifie le nombre d’instances du processus indiqué dans le paramètre **processPath** qui peuvent être lancées par application.</span><span class="sxs-lookup"><span data-stu-id="fab92-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="fab92-195">&dagger;Pour l’hébergement in-process, la valeur est limitée à `1`.</span><span class="sxs-lookup"><span data-stu-id="fab92-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="fab92-196">Il est déconseillé de définir `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="fab92-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="fab92-197">Cet attribut sera supprimé dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="fab92-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="fab92-198">Valeur par défaut : `1`</span><span class="sxs-lookup"><span data-stu-id="fab92-198">Default: `1`</span></span><br><span data-ttu-id="fab92-199">Min : `1`</span><span class="sxs-lookup"><span data-stu-id="fab92-199">Min: `1`</span></span><br><span data-ttu-id="fab92-200">Max : `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="fab92-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="fab92-201">Attribut de chaîne requis.</span><span class="sxs-lookup"><span data-stu-id="fab92-201">Required string attribute.</span></span></p><p><span data-ttu-id="fab92-202">Chemin d’accès au fichier exécutable lançant un processus d’écoute des requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fab92-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="fab92-203">Les chemins d’accès relatifs sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fab92-203">Relative paths are supported.</span></span> <span data-ttu-id="fab92-204">Si le chemin d’accès commence par `.`, il est considéré comme étant relatif par rapport à la racine du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="fab92-205">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-206">Indique le nombre de fois où le processus spécifié dans **processPath** est autorisé à se bloquer par minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="fab92-207">Si cette limite est dépassée, le module arrête le lancement du processus pour le reste de la minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="fab92-208">Non pris en charge avec hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="fab92-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="fab92-209">Valeur par défaut : `10`</span><span class="sxs-lookup"><span data-stu-id="fab92-209">Default: `10`</span></span><br><span data-ttu-id="fab92-210">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-210">Min: `0`</span></span><br><span data-ttu-id="fab92-211">Max : `100`</span><span class="sxs-lookup"><span data-stu-id="fab92-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="fab92-212">Attribut timespan facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="fab92-213">Spécifie la durée pendant laquelle le module ASP.NET Core attend une réponse de la part du processus à l’écoute sur %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="fab92-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="fab92-214">Dans les versions du module ASP.NET Core fournies avec ASP.NET Core 2.1 ou version ultérieure, la valeur `requestTimeout` est spécifiée en heures, minutes et secondes.</span><span class="sxs-lookup"><span data-stu-id="fab92-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="fab92-215">Ne s’applique pas à l’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="fab92-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="fab92-216">Pour l’hébergement in-process, le module attend que l’application traite la requête.</span><span class="sxs-lookup"><span data-stu-id="fab92-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="fab92-217">Les valeurs valides pour les segments de minutes et secondes de la chaîne sont dans la plage de 0 à 59.</span><span class="sxs-lookup"><span data-stu-id="fab92-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="fab92-218">L’utilisation de **60** dans la valeur de minutes ou de secondes affiche *500 - Erreur interne du serveur* .</span><span class="sxs-lookup"><span data-stu-id="fab92-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error* .</span></span></p> | <span data-ttu-id="fab92-219">Valeur par défaut : `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-219">Default: `00:02:00`</span></span><br><span data-ttu-id="fab92-220">Min : `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-220">Min: `00:00:00`</span></span><br><span data-ttu-id="fab92-221">Max : `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="fab92-222">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-223">Durée en secondes pendant laquelle le module attend que l’exécutable s’arrête normalement lorsque le fichier *app_offline.htm* est détecté.</span><span class="sxs-lookup"><span data-stu-id="fab92-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="fab92-224">Valeur par défaut : `10`</span><span class="sxs-lookup"><span data-stu-id="fab92-224">Default: `10`</span></span><br><span data-ttu-id="fab92-225">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-225">Min: `0`</span></span><br><span data-ttu-id="fab92-226">Max : `600`</span><span class="sxs-lookup"><span data-stu-id="fab92-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="fab92-227">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-228">Durée en secondes pendant laquelle le module attend que le fichier exécutable démarre un processus à l’écoute sur le port.</span><span class="sxs-lookup"><span data-stu-id="fab92-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="fab92-229">Si cette limite de temps est dépassée, le module met fin au processus.</span><span class="sxs-lookup"><span data-stu-id="fab92-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="fab92-230">Lors de l’hébergement *in-process* : le processus n’est **pas** redémarré et n’utilise **pas** le paramètre **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="fab92-230">When hosting *in-process* : The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="fab92-231">Lors de l’hébergement *out-of-process* : le module tente de relancer le processus lorsqu’il reçoit une nouvelle demande et continue de tenter de redémarrer le processus lors des demandes entrantes suivantes, sauf si l’application ne parvient pas à démarrer **rapidFailsPerMinute** nombre de fois au cours de la dernière minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-231">When hosting *out-of-process* : The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="fab92-232">La valeur 0 (zéro) n’est **pas** considérée comme un délai infini.</span><span class="sxs-lookup"><span data-stu-id="fab92-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="fab92-233">Valeur par défaut : `120`</span><span class="sxs-lookup"><span data-stu-id="fab92-233">Default: `120`</span></span><br><span data-ttu-id="fab92-234">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-234">Min: `0`</span></span><br><span data-ttu-id="fab92-235">Max : `3600`</span><span class="sxs-lookup"><span data-stu-id="fab92-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="fab92-236">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-237">Si la valeur est true, les attributs **stdout** et **stderr** du processus spécifié dans **processPath** sont redirigés vers le fichier spécifié dans **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="fab92-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile** .</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="fab92-238">Attribut de chaîne facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-238">Optional string attribute.</span></span></p><p><span data-ttu-id="fab92-239">Spécifie le chemin d’accès relatif ou absolu pour lequel les attributs **stdout** et **stderr** du processus spécifié dans **processPath** sont consignés.</span><span class="sxs-lookup"><span data-stu-id="fab92-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="fab92-240">Les chemins d’accès relatifs sont relatifs par rapport à la racine du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="fab92-241">Tout chemin d’accès commençant par `.` est relatif par rapport à la racine du site et tous les autres chemins d’accès sont traités comme des chemins d’accès absolus.</span><span class="sxs-lookup"><span data-stu-id="fab92-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="fab92-242">Tous les dossiers fournis dans le chemin sont créés par le module au moment de la création du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="fab92-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="fab92-243">À l’aide de délimiteurs de trait de soulignement, un horodateur, un ID de processus et une extension de fichier ( `.log` ) sont ajoutés au dernier segment du chemin d’accès **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="fab92-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="fab92-244">Si `.\logs\stdout` est fourni en tant que valeur, un exemple de journal stdout est enregistré en tant que `stdout_20180205194132_1934.log` dans le `logs` dossier lorsqu’il est enregistré sur 2/5/2018 à 19:41:32 avec l’ID de processus 1934.</span><span class="sxs-lookup"><span data-stu-id="fab92-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="fab92-245">Définir des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="fab92-245">Set environment variables</span></span>

<span data-ttu-id="fab92-246">Des variables d’environnement peuvent être spécifiées pour le processus dans l’attribut `processPath`.</span><span class="sxs-lookup"><span data-stu-id="fab92-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="fab92-247">Spécifiez une variable d’environnement à l’aide de l’élément enfant `<environmentVariable>` d’un élément de collection `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="fab92-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="fab92-248">Les variables d’environnement définies dans cette section prévalent sur les variables d’environnement système.</span><span class="sxs-lookup"><span data-stu-id="fab92-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="fab92-249">L’exemple suivant définit deux variables d’environnement dans `web.config` .</span><span class="sxs-lookup"><span data-stu-id="fab92-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="fab92-250">`ASPNETCORE_ENVIRONMENT` définit l’environnement de l’application sur `Development`.</span><span class="sxs-lookup"><span data-stu-id="fab92-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="fab92-251">Un développeur peut définir temporairement cette valeur dans le `web.config` fichier afin de forcer le chargement de la [page d’exception du développeur](xref:fundamentals/error-handling) lors du débogage d’une exception d’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="fab92-252">`CONFIG_DIR` est un exemple de variable d’environnement définie par l’utilisateur, dans laquelle le développeur a écrit du code qui lit la valeur de démarrage afin de former un chemin d’accès pour le chargement du fichier de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="fab92-253">Une alternative à la définition de l’environnement directement dans `web.config` consiste à inclure la `<EnvironmentName>` propriété dans le [profil de publication ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="fab92-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="fab92-254">Cette approche définit l’environnement dans le `web.config` moment où le projet est publié :</span><span class="sxs-lookup"><span data-stu-id="fab92-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="fab92-255">Affectez uniquement `Development` à la variable d’environnement `ASPNETCORE_ENVIRONMENT` sur les serveurs de test et de préproduction non accessibles aux réseaux non approuvés, par exemple Internet.</span><span class="sxs-lookup"><span data-stu-id="fab92-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="fab92-256">Si un fichier portant le nom `app_offline.htm` est détecté dans le répertoire racine d’une application, le Module ASP.net Core tente d’arrêter correctement l’application et d’arrêter le traitement des demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="fab92-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="fab92-257">Si l’application est toujours active après le nombre de secondes défini dans `shutdownTimeLimit`, le module ASP.NET Core met fin au processus en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="fab92-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="fab92-258">Pendant que le `app_offline.htm` fichier est présent, le Module ASP.net Core répond aux demandes en renvoyant le contenu du `app_offline.htm` fichier.</span><span class="sxs-lookup"><span data-stu-id="fab92-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="fab92-259">Lorsque le `app_offline.htm` fichier est supprimé, la requête suivante démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="fab92-260">Lorsque vous utilisez le modèle d’hébergement out-of-process, l’application peut ne pas s’arrêter immédiatement s’il existe une connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="fab92-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="fab92-261">Par exemple, une connexion WebSocket peut retarder l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="fab92-262">Page d’erreur de démarrage</span><span class="sxs-lookup"><span data-stu-id="fab92-262">Start-up error page</span></span>

<span data-ttu-id="fab92-263">Les hébergements in-process et out-of-process produisent des pages d’erreurs personnalisées quand ils ne parviennent pas à démarrer l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="fab92-264">Si le module ASP.NET Core ne trouve pas le gestionnaire de requêtes in-process ou out-of-process, une page de code d’état *500.0 - Échec de chargement du gestionnaire in-process/out-of-process* s’affiche.</span><span class="sxs-lookup"><span data-stu-id="fab92-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="fab92-265">Pour l’hébergement in-process, si le module ASP.NET Core ne peut pas démarrer l’application, une page de code d’état *500.30 - Échec de démarrage* s’affiche.</span><span class="sxs-lookup"><span data-stu-id="fab92-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="fab92-266">Pour l’hébergement out-of-process, si le module ASP.NET Core ne peut pas lancer le processus backend ou que ce dernier démarre mais ne parvient pas à écouter sur le port configuré, une page de code d’état *502.5 - Échec du processus* s’affiche.</span><span class="sxs-lookup"><span data-stu-id="fab92-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="fab92-267">Pour supprimer cette page et revenir à la page IIS de codes d’état 5xx par défaut, utilisez l’attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="fab92-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="fab92-268">Pour plus d’informations sur la configuration des messages d’erreur personnalisés, consultez [Erreurs HTTP `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="fab92-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="fab92-269">Création et redirection de journal</span><span class="sxs-lookup"><span data-stu-id="fab92-269">Log creation and redirection</span></span>

<span data-ttu-id="fab92-270">Le module ASP.NET Core redirige la sortie de console stdout et stderr vers le disque si les attributs `stdoutLogEnabled` et `stdoutLogFile` de l’élément `aspNetCore` sont définis.</span><span class="sxs-lookup"><span data-stu-id="fab92-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="fab92-271">Tous les dossiers du chemin `stdoutLogFile` sont créés par le module au moment de la création du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="fab92-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="fab92-272">Le pool d’applications doit avoir un accès en écriture à l’emplacement auquel les journaux sont écrits (utilisez `IIS AppPool\<app_pool_name>` pour fournir les autorisations d’écriture).</span><span class="sxs-lookup"><span data-stu-id="fab92-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="fab92-273">Aucune rotation n’est appliquée aux journaux, sauf en cas de recyclage/redémarrage du processus.</span><span class="sxs-lookup"><span data-stu-id="fab92-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="fab92-274">Il incombe à l’hébergeur de limiter l’espace disque utilisé par les journaux.</span><span class="sxs-lookup"><span data-stu-id="fab92-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="fab92-275">L’utilisation du journal stdout est recommandée uniquement pour résoudre les problèmes de démarrage d’application lors de l’hébergement sur IIS ou lors de l’utilisation [de la prise en charge au moment du développement pour IIS avec Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), et non pendant le débogage local et l’exécution de l’application avec IIS Express.</span><span class="sxs-lookup"><span data-stu-id="fab92-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="fab92-276">N’utilisez pas le journal stdout à des fins de journalisation d’application générale.</span><span class="sxs-lookup"><span data-stu-id="fab92-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="fab92-277">Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux.</span><span class="sxs-lookup"><span data-stu-id="fab92-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fab92-278">Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="fab92-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="fab92-279">Un horodatage et une extension de fichier sont ajoutés automatiquement quand le fichier journal est créé.</span><span class="sxs-lookup"><span data-stu-id="fab92-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="fab92-280">Le nom du fichier journal est composé en ajoutant l’horodateur, l’ID de processus et l’extension de fichier ( `.log` ) au dernier segment du `stdoutLogFile` chemin (généralement `stdout` ) délimité par des traits de soulignement.</span><span class="sxs-lookup"><span data-stu-id="fab92-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="fab92-281">Si le `stdoutLogFile` chemin d’accès se termine par `stdout` , un journal pour une application avec un PID de 1934 créé le 2/5/2018 à 19:42:32 a le nom de fichier `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="fab92-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="fab92-282">Si `stdoutLogEnabled` a la valeur false, les erreurs qui se produisent au moment du démarrage de l’application sont capturées et émises dans le journal des événements (30 Ko maximum).</span><span class="sxs-lookup"><span data-stu-id="fab92-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="fab92-283">Après le démarrage, tous les journaux supplémentaires sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="fab92-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="fab92-284">L’exemple `aspNetCore` d’élément suivant configure la journalisation stdout sur le chemin d’accès relatif `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="fab92-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="fab92-285">Vérifiez que l’identité de l’utilisateur AppPool à l’autorisation d’écrire dans le chemin d’accès fourni.</span><span class="sxs-lookup"><span data-stu-id="fab92-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="fab92-286">Lors de la publication d’une application pour le déploiement Azure App Service, le kit de développement logiciel (SDK) Web définit la `stdoutLogFile` valeur sur `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="fab92-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="fab92-287">La `%home` variable d’environnement est prédéfinie pour les applications hébergées par Azure App service.</span><span class="sxs-lookup"><span data-stu-id="fab92-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="fab92-288">Pour créer des règles de filtre de journalisation, consultez les sections [configuration](xref:fundamentals/logging/index#log-filtering) et [filtrage des journaux](xref:fundamentals/logging/index#log-filtering) de la documentation ASP.net Core Logging.</span><span class="sxs-lookup"><span data-stu-id="fab92-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="fab92-289">Pour plus d’informations sur les formats de chemin d’accès, consultez [formats de chemin d’accès de fichier sur les systèmes Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="fab92-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="fab92-290">Journaux de diagnostic améliorés</span><span class="sxs-lookup"><span data-stu-id="fab92-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="fab92-291">Le module ASP.NET Core est configurable pour proposer des journaux de diagnostic améliorés.</span><span class="sxs-lookup"><span data-stu-id="fab92-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="fab92-292">Ajoutez l' `<handlerSettings>` élément à l' `<aspNetCore>` élément dans `web.config` .</span><span class="sxs-lookup"><span data-stu-id="fab92-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="fab92-293">L’affectation de la valeur `TRACE` à `debugLevel` expose une fidélité plus élevée des informations de diagnostic :</span><span class="sxs-lookup"><span data-stu-id="fab92-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="fab92-294">Tous les dossiers dans le chemin d’accès ( `logs` dans l’exemple précédent) sont créés par le module lors de la création du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="fab92-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="fab92-295">Le pool d’applications doit avoir un accès en écriture à l’emplacement où les journaux sont écrits (utilisez `IIS AppPool\{APP POOL NAME}` , où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications, pour fournir l’autorisation d’écriture).</span><span class="sxs-lookup"><span data-stu-id="fab92-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="fab92-296">Les valeurs du niveau de débogage (`debugLevel`) peuvent inclure à la fois le niveau et l’emplacement.</span><span class="sxs-lookup"><span data-stu-id="fab92-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="fab92-297">Niveaux (dans l’ordre du moins au plus détaillé) :</span><span class="sxs-lookup"><span data-stu-id="fab92-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="fab92-298">ERROR</span><span class="sxs-lookup"><span data-stu-id="fab92-298">ERROR</span></span>
* <span data-ttu-id="fab92-299">WARNING</span><span class="sxs-lookup"><span data-stu-id="fab92-299">WARNING</span></span>
* <span data-ttu-id="fab92-300">INFO</span><span class="sxs-lookup"><span data-stu-id="fab92-300">INFO</span></span>
* <span data-ttu-id="fab92-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="fab92-301">TRACE</span></span>

<span data-ttu-id="fab92-302">Emplacements (plusieurs emplacements sont autorisés) :</span><span class="sxs-lookup"><span data-stu-id="fab92-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="fab92-303">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="fab92-303">CONSOLE</span></span>
* <span data-ttu-id="fab92-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="fab92-304">EVENTLOG</span></span>
* <span data-ttu-id="fab92-305">FILE</span><span class="sxs-lookup"><span data-stu-id="fab92-305">FILE</span></span>

<span data-ttu-id="fab92-306">Les paramètres de gestionnaire peuvent également être fournis par le biais de variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="fab92-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="fab92-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Chemin d’accès au fichier journal de débogage.</span><span class="sxs-lookup"><span data-stu-id="fab92-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="fab92-308">(Par défaut : `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="fab92-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="fab92-309">`ASPNETCORE_MODULE_DEBUG`: Paramètre de niveau de débogage.</span><span class="sxs-lookup"><span data-stu-id="fab92-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="fab92-310">Ne laissez **pas** la journalisation du débogage activée dans le déploiement plus longtemps que nécessaire pour résoudre un problème.</span><span class="sxs-lookup"><span data-stu-id="fab92-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="fab92-311">La taille du journal n’est pas limitée.</span><span class="sxs-lookup"><span data-stu-id="fab92-311">The size of the log isn't limited.</span></span> <span data-ttu-id="fab92-312">Si vous laissez la journalisation du débogage activée, vous risquez d’épuiser l’espace disque disponible et de bloquer le serveur ou le service d’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="fab92-313">Pour obtenir un exemple de l’élément dans le fichier, consultez [configuration avec web.config](#configuration-with-webconfig) `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="fab92-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="fab92-314">Modifier la taille de pile</span><span class="sxs-lookup"><span data-stu-id="fab92-314">Modify the stack size</span></span>

<span data-ttu-id="fab92-315">*S’applique uniquement lors de l’utilisation du modèle d’hébergement in-process.*</span><span class="sxs-lookup"><span data-stu-id="fab92-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="fab92-316">Configurez la taille de la pile managée en utilisant le `stackSize` paramètre en octets dans `web.config` .</span><span class="sxs-lookup"><span data-stu-id="fab92-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="fab92-317">La taille par défaut est de 1 048 576 octets (1 Mo).</span><span class="sxs-lookup"><span data-stu-id="fab92-317">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="fab92-318">La configuration du proxy utilise le protocole HTTP et un jeton d’appariement</span><span class="sxs-lookup"><span data-stu-id="fab92-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="fab92-319">*S’applique uniquement à l’hébergement out-of-process.*</span><span class="sxs-lookup"><span data-stu-id="fab92-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="fab92-320">Le proxy créé entre le module ASP.NET Core et Kestrel utilise le protocole HTTP.</span><span class="sxs-lookup"><span data-stu-id="fab92-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="fab92-321">Il n’existe aucun risque d’écoute clandestine du trafic entre le module et Kestrel à partir d’un emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fab92-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="fab92-322">Un jeton d’appariement est utilisé pour garantir que les demandes reçues par Kestrel ont été traitées par IIS et ne proviennent pas d’une autre source.</span><span class="sxs-lookup"><span data-stu-id="fab92-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="fab92-323">Le jeton d’appariement est créé et défini dans une variable d’environnement (`ASPNETCORE_TOKEN`) par le module.</span><span class="sxs-lookup"><span data-stu-id="fab92-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="fab92-324">Le jeton d’appariement est également défini dans un en-tête (`MS-ASPNETCORE-TOKEN`) sur toutes les demandes traitées.</span><span class="sxs-lookup"><span data-stu-id="fab92-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="fab92-325">L'intergiciel IIS vérifie chaque demande qu’il reçoit pour confirmer que la valeur d’en-tête du jeton d'appariement correspond à la valeur de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fab92-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="fab92-326">Si les valeurs de jeton ne correspondent pas, la demande est connectée et rejetée.</span><span class="sxs-lookup"><span data-stu-id="fab92-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="fab92-327">La variable d’environnement du jeton d'appariement et le trafic entre le module et Kestrel ne sont pas accessibles à partir d’un emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fab92-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="fab92-328">Sans connaître la valeur du jeton d'appariement, une personne malveillante ne peut pas soumettre des demandes qui contournent la vérification de l’intergiciel IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="fab92-329">Module ASP.NET Core avec une configuration partagée IIS</span><span class="sxs-lookup"><span data-stu-id="fab92-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="fab92-330">Le programme d’installation du module ASP.NET Core s’exécute avec les privilèges du compte **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="fab92-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="fab92-331">Étant donné que le compte système local ne dispose pas de l’autorisation de modification pour le chemin d’accès de partage utilisé par la configuration partagée IIS, le programme d’installation lève une erreur d’accès refusé lors de la tentative de configuration des paramètres du module dans le `applicationHost.config` fichier sur le partage.</span><span class="sxs-lookup"><span data-stu-id="fab92-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="fab92-332">Quand une configuration partagée IIS est utilisée sur la même machine que l’installation IIS, il convient d’exécuter le programme d’installation du bundle d’hébergement ASP.NET Core avec le paramètre `OPT_NO_SHARED_CONFIG_CHECK` défini sur `1` :</span><span class="sxs-lookup"><span data-stu-id="fab92-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="fab92-333">Quand le chemin de la configuration partagée ne se trouve pas sur la même machine que l’installation IIS, effectuez ces étapes :</span><span class="sxs-lookup"><span data-stu-id="fab92-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="fab92-334">Désactivez la configuration partagée IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="fab92-335">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="fab92-335">Run the installer.</span></span>
1. <span data-ttu-id="fab92-336">Exportez le fichier mis à jour `applicationHost.config` vers le partage.</span><span class="sxs-lookup"><span data-stu-id="fab92-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="fab92-337">Réactivez la configuration partagée IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="fab92-338">Version du module et journaux du programme d’installation du bundle d’hébergement</span><span class="sxs-lookup"><span data-stu-id="fab92-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="fab92-339">Pour déterminer la version du module ASP.NET Core installé :</span><span class="sxs-lookup"><span data-stu-id="fab92-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="fab92-340">Sur le système d’hébergement, accédez à `%windir%\System32\inetsrv` .</span><span class="sxs-lookup"><span data-stu-id="fab92-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="fab92-341">Localisez le `aspnetcore.dll` fichier.</span><span class="sxs-lookup"><span data-stu-id="fab92-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="fab92-342">Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="fab92-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="fab92-343">Sélectionnez l’onglet **Détails** . La version du **fichier** et la version du **produit** représentent la version installée du module.</span><span class="sxs-lookup"><span data-stu-id="fab92-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="fab92-344">Les journaux du programme d’installation du bundle d’hébergement pour le module sont disponibles à l’adresse `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="fab92-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="fab92-345">Le fichier est nommé `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span><span class="sxs-lookup"><span data-stu-id="fab92-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="fab92-346">Emplacements des fichiers du module, du schéma et de configuration</span><span class="sxs-lookup"><span data-stu-id="fab92-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="fab92-347">Module</span><span class="sxs-lookup"><span data-stu-id="fab92-347">Module</span></span>

<span data-ttu-id="fab92-348">**IIS (x86/amd64) :**</span><span class="sxs-lookup"><span data-stu-id="fab92-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="fab92-349">**IIS Express (x86/amd64) :**</span><span class="sxs-lookup"><span data-stu-id="fab92-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="fab92-350">schéma</span><span class="sxs-lookup"><span data-stu-id="fab92-350">Schema</span></span>

<span data-ttu-id="fab92-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="fab92-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="fab92-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="fab92-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="fab92-353">Configuration</span><span class="sxs-lookup"><span data-stu-id="fab92-353">Configuration</span></span>

<span data-ttu-id="fab92-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="fab92-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="fab92-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="fab92-355">**IIS Express**</span></span>

* <span data-ttu-id="fab92-356">Visual Studio : `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="fab92-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="fab92-357">*iisexpress.exe* COMMUN `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="fab92-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="fab92-358">Vous pouvez trouver les fichiers en recherchant `aspnetcore` dans le `applicationHost.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="fab92-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fab92-359">Le module ASP.NET Core est un module IIS natif qui se connecte dans le pipeline IIS pour effectuer l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="fab92-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="fab92-360">Héberger une application ASP.NET Core à l’intérieur du processus de travail IIS (`w3wp.exe`), appelé [modèle d’hébergement in-process](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="fab92-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="fab92-361">Transférer les requêtes web à une application ASP.NET Core du back-end exécutant le [serveur Kestrel](xref:fundamentals/servers/kestrel), appelé [modèle d’hébergement out-of-process](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="fab92-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="fab92-362">Versions de Windows prises en charge :</span><span class="sxs-lookup"><span data-stu-id="fab92-362">Supported Windows versions:</span></span>

* <span data-ttu-id="fab92-363">Windows 7 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fab92-363">Windows 7 or later</span></span>
* <span data-ttu-id="fab92-364">Windows Server 2008 R2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fab92-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="fab92-365">Lors de l’hébergement in-process, le module utilise une implémentation du serveur in-process pour IIS, nommée serveur HTTP IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="fab92-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="fab92-366">Lors de l’hébergement out-of-process, le module fonctionne uniquement avec Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fab92-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="fab92-367">Le module ne fonctionne pas avec [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="fab92-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="fab92-368">Modèles d'hébergement</span><span class="sxs-lookup"><span data-stu-id="fab92-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="fab92-369">Modèle d’hébergement in-process</span><span class="sxs-lookup"><span data-stu-id="fab92-369">In-process hosting model</span></span>

<span data-ttu-id="fab92-370">Pour configurer l’hébergement in-process dans une application, ajoutez la propriété `<AspNetCoreHostingModel>` au fichier projet de l’application en lui attribuant la valeur `InProcess` (l’hébergement out-of-process est défini sur `OutOfProcess`) :</span><span class="sxs-lookup"><span data-stu-id="fab92-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="fab92-371">Le modèle d’hébergement in-process n’est pas pris en charge pour les applications ASP.NET Core qui ciblent le .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="fab92-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="fab92-372">La valeur de ne respecte pas la `<AspNetCoreHostingModel>` casse, donc `inprocess` et `outofprocess` sont des valeurs valides.</span><span class="sxs-lookup"><span data-stu-id="fab92-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="fab92-373">Si la propriété `<AspNetCoreHostingModel>` n’est pas présente dans le fichier, la valeur par défaut est `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="fab92-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="fab92-374">Les caractéristiques suivantes s’appliquent lors de l’hébergement in-process :</span><span class="sxs-lookup"><span data-stu-id="fab92-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="fab92-375">Le serveur HTTP IIS (`IISHttpServer`) est utilisé à la place du serveur [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="fab92-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="fab92-376">Pour in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> pour :</span><span class="sxs-lookup"><span data-stu-id="fab92-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="fab92-377">Enregistrer `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="fab92-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="fab92-378">Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fab92-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="fab92-379">Configurer l’hôte pour capturer des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="fab92-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="fab92-380">L’[attribut requestTimeout](#attributes-of-the-aspnetcore-element) ne s’applique pas à l’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="fab92-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="fab92-381">Le partage d’un pool d’applications entre les applications n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fab92-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="fab92-382">Utilisez un pool d’applications par application.</span><span class="sxs-lookup"><span data-stu-id="fab92-382">Use one app pool per app.</span></span>

* <span data-ttu-id="fab92-383">Lorsque vous utilisez [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou que vous placez manuellement un [fichier app_offline.htm dans le déploiement](xref:host-and-deploy/iis/index#locked-deployment-files), l’application peut ne pas s’arrêter immédiatement si une connexion est ouverte.</span><span class="sxs-lookup"><span data-stu-id="fab92-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="fab92-384">Par exemple, une connexion websocket peut retarder l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="fab92-385">L’architecture (nombre de bits) de l’application et le runtime installé (x64 ou x86) doivent correspondre à l’architecture du pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="fab92-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="fab92-386">Les déconnexions du client sont détectées.</span><span class="sxs-lookup"><span data-stu-id="fab92-386">Client disconnects are detected.</span></span> <span data-ttu-id="fab92-387">Le jeton d’annulation [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) est annulé quand le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="fab92-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="fab92-388">Dans ASP.NET Core version 2.2.1 ou antérieure, <xref:System.IO.Directory.GetCurrentDirectory*> retourne le répertoire de travail du processus démarré par IIS, et non le répertoire de l’application (par exemple, *C:\Windows\System32\inetsrv* pour *w3wp.exe* ).</span><span class="sxs-lookup"><span data-stu-id="fab92-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe* ).</span></span>

  <span data-ttu-id="fab92-389">Pour connaître l’exemple de code qui définit le répertoire actuel de l’application, consultez [CurrentDirectoryHelpers, classe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="fab92-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="fab92-390">Appelez la méthode `SetCurrentDirectory` .</span><span class="sxs-lookup"><span data-stu-id="fab92-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="fab92-391">Les appels suivants à <xref:System.IO.Directory.GetCurrentDirectory*> fournissent le répertoire de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="fab92-392">Dans le cas d’un hébergement in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> n’est pas appelé en interne pour initialiser un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fab92-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="fab92-393">Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="fab92-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="fab92-394">Si vous transformez les revendications avec une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, appelez <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pour ajouter des services d’authentification :</span><span class="sxs-lookup"><span data-stu-id="fab92-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="fab92-395">Modèle d’hébergement out-of-process</span><span class="sxs-lookup"><span data-stu-id="fab92-395">Out-of-process hosting model</span></span>

<span data-ttu-id="fab92-396">Pour configurer une application pour un hébergement out-of-process, utilisez l’une des approches suivantes dans le fichier de projet :</span><span class="sxs-lookup"><span data-stu-id="fab92-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="fab92-397">Ne spécifiez pas la propriété `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="fab92-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="fab92-398">Si la propriété `<AspNetCoreHostingModel>` n’est pas présente dans le fichier, la valeur par défaut est `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="fab92-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="fab92-399">Définissez la valeur de la propriété `<AspNetCoreHostingModel>` sur `OutOfProcess` (l’hébergement in-process est défini avec `InProcess`) :</span><span class="sxs-lookup"><span data-stu-id="fab92-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="fab92-400">La valeur ne respecte pas la casse, donc `inprocess` et `outofprocess` sont des valeurs valides.</span><span class="sxs-lookup"><span data-stu-id="fab92-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="fab92-401">Le serveur [Kestrel](xref:fundamentals/servers/kestrel) est utilisé à la place du serveur HTTP IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="fab92-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="fab92-402">Pour out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> pour :</span><span class="sxs-lookup"><span data-stu-id="fab92-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="fab92-403">Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fab92-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="fab92-404">Configurer l’hôte pour capturer des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="fab92-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="fab92-405">Modifications du modèle d’hébergement</span><span class="sxs-lookup"><span data-stu-id="fab92-405">Hosting model changes</span></span>

<span data-ttu-id="fab92-406">Si le paramètre `hostingModel` est modifié dans le fichier *web.config* (comme expliqué dans la section [Configuration avec web.config](#configuration-with-webconfig)), le module recycle le processus de travail pour IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="fab92-407">Pour IIS Express, le module ne recycle pas le processus de travail, mais déclenche plutôt un arrêt approprié du processus IIS Express en cours.</span><span class="sxs-lookup"><span data-stu-id="fab92-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="fab92-408">La requête suivante à l’application génère un nouveau processus IIS Express.</span><span class="sxs-lookup"><span data-stu-id="fab92-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="fab92-409">Nom du processus</span><span class="sxs-lookup"><span data-stu-id="fab92-409">Process name</span></span>

<span data-ttu-id="fab92-410">`Process.GetCurrentProcess().ProcessName` signale `w3wp`/`iisexpress` (in-process) ou `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="fab92-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="fab92-411">De nombreux modules natifs, comme l’authentification Windows, restent actifs.</span><span class="sxs-lookup"><span data-stu-id="fab92-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="fab92-412">Pour obtenir plus d’informations sur les modules IIS actifs avec le module ASP.NET Core, consultez <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="fab92-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="fab92-413">Le module ASP.NET Core peut également  :</span><span class="sxs-lookup"><span data-stu-id="fab92-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="fab92-414">Définir des variables d’environnement pour le processus de travail.</span><span class="sxs-lookup"><span data-stu-id="fab92-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="fab92-415">Journaliser la sortie de stdout dans un stockage de fichiers pour résoudre les problèmes de démarrage.</span><span class="sxs-lookup"><span data-stu-id="fab92-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="fab92-416">Transférer des jetons d’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="fab92-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="fab92-417">Comment installer et utiliser le module ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fab92-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="fab92-418">Pour obtenir des instructions sur l’installation du module ASP.NET Core, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="fab92-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="fab92-419">Configuration avec web.config</span><span class="sxs-lookup"><span data-stu-id="fab92-419">Configuration with web.config</span></span>

<span data-ttu-id="fab92-420">Le module ASP.NET Core est configuré avec la section `aspNetCore` du nœud `system.webServer` dans le fichier *web.config* du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="fab92-421">Le fichier *web.config* suivant est publié pour un [déploiement dépendant du framework](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) et configure le module ASP.NET Core pour gérer les demandes du site :</span><span class="sxs-lookup"><span data-stu-id="fab92-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="fab92-422">Le fichier *web.config* suivant est publié pour un [déploiement autonome](/dotnet/articles/core/deploying/#self-contained-deployments-scd) :</span><span class="sxs-lookup"><span data-stu-id="fab92-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="fab92-423">La <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriété a la valeur `false` pour indiquer que les paramètres spécifiés dans l' [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) élément ne sont pas hérités par les applications qui résident dans un sous-répertoire de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="fab92-424">Lorsqu’une application est déployée sur [Azure App Service](https://azure.microsoft.com/services/app-service/), le chemin d’accès `stdoutLogFile` est défini sur `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="fab92-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="fab92-425">Le chemin d’accès enregistre les journaux stdout dans le dossier *LogFiles* , un emplacement automatiquement créé par le service.</span><span class="sxs-lookup"><span data-stu-id="fab92-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="fab92-426">Pour plus d’informations sur la configuration d’une sous-application IIS, consultez <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="fab92-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="fab92-427">Attributs de l’élément aspNetCore</span><span class="sxs-lookup"><span data-stu-id="fab92-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="fab92-428">Attribut</span><span class="sxs-lookup"><span data-stu-id="fab92-428">Attribute</span></span> | <span data-ttu-id="fab92-429">Description</span><span class="sxs-lookup"><span data-stu-id="fab92-429">Description</span></span> | <span data-ttu-id="fab92-430">Default</span><span class="sxs-lookup"><span data-stu-id="fab92-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="fab92-431">Attribut de chaîne facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-431">Optional string attribute.</span></span></p><p><span data-ttu-id="fab92-432">Arguments de l’exécutable spécifié dans `processPath` .</span><span class="sxs-lookup"><span data-stu-id="fab92-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="fab92-433">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-434">Si la valeur est true, la page **502.5 - Échec du processus** est supprimée, et la page de code d’état 502 configurée dans le fichier *web.config* est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="fab92-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="fab92-435">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-436">Si la valeur est true, le jeton est transmis au processus enfant qui écoute sur %ASPNETCORE_PORT% sous la forme d’un en-tête 'MS-ASPNETCORE-WINAUTHTOKEN' par demande.</span><span class="sxs-lookup"><span data-stu-id="fab92-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="fab92-437">Il incombe à ce processus d’appeler CloseHandle sur ce jeton par demande.</span><span class="sxs-lookup"><span data-stu-id="fab92-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="fab92-438">Attribut de chaîne facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-438">Optional string attribute.</span></span></p><p><span data-ttu-id="fab92-439">Spécifie le modèle d’hébergement comme in-process ( `InProcess` / `inprocess` ) ou out-of-process ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="fab92-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="fab92-440">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-441">Spécifie le nombre d’instances du processus spécifié dans le `processPath` paramètre qui peut être lancé par application.</span><span class="sxs-lookup"><span data-stu-id="fab92-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="fab92-442">&dagger;Pour l’hébergement in-process, la valeur est limitée à `1`.</span><span class="sxs-lookup"><span data-stu-id="fab92-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="fab92-443">Il est déconseillé de définir `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="fab92-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="fab92-444">Cet attribut sera supprimé dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="fab92-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="fab92-445">Valeur par défaut : `1`</span><span class="sxs-lookup"><span data-stu-id="fab92-445">Default: `1`</span></span><br><span data-ttu-id="fab92-446">Min : `1`</span><span class="sxs-lookup"><span data-stu-id="fab92-446">Min: `1`</span></span><br><span data-ttu-id="fab92-447">Max : `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="fab92-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="fab92-448">Attribut de chaîne requis.</span><span class="sxs-lookup"><span data-stu-id="fab92-448">Required string attribute.</span></span></p><p><span data-ttu-id="fab92-449">Chemin d’accès au fichier exécutable lançant un processus d’écoute des requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fab92-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="fab92-450">Les chemins d’accès relatifs sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fab92-450">Relative paths are supported.</span></span> <span data-ttu-id="fab92-451">Si le chemin d’accès commence par `.`, il est considéré comme étant relatif par rapport à la racine du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="fab92-452">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-453">Spécifie le nombre de fois où le processus spécifié dans `processPath` est autorisé à se bloquer par minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="fab92-454">Si cette limite est dépassée, le module arrête le lancement du processus pour le reste de la minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="fab92-455">Non pris en charge avec hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="fab92-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="fab92-456">Valeur par défaut : `10`</span><span class="sxs-lookup"><span data-stu-id="fab92-456">Default: `10`</span></span><br><span data-ttu-id="fab92-457">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-457">Min: `0`</span></span><br><span data-ttu-id="fab92-458">Max : `100`</span><span class="sxs-lookup"><span data-stu-id="fab92-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="fab92-459">Attribut timespan facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="fab92-460">Spécifie la durée pendant laquelle le module ASP.NET Core attend une réponse de la part du processus à l’écoute sur %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="fab92-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="fab92-461">Dans les versions du module ASP.NET Core fournies avec ASP.NET Core 2.1 ou version ultérieure, la valeur `requestTimeout` est spécifiée en heures, minutes et secondes.</span><span class="sxs-lookup"><span data-stu-id="fab92-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="fab92-462">Ne s’applique pas à l’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="fab92-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="fab92-463">Pour l’hébergement in-process, le module attend que l’application traite la requête.</span><span class="sxs-lookup"><span data-stu-id="fab92-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="fab92-464">Les valeurs valides pour les segments de minutes et secondes de la chaîne sont dans la plage de 0 à 59.</span><span class="sxs-lookup"><span data-stu-id="fab92-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="fab92-465">L’utilisation de **60** dans la valeur de minutes ou de secondes affiche *500 - Erreur interne du serveur* .</span><span class="sxs-lookup"><span data-stu-id="fab92-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error* .</span></span></p> | <span data-ttu-id="fab92-466">Valeur par défaut : `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-466">Default: `00:02:00`</span></span><br><span data-ttu-id="fab92-467">Min : `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-467">Min: `00:00:00`</span></span><br><span data-ttu-id="fab92-468">Max : `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="fab92-469">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-470">Durée en secondes pendant laquelle le module attend que l’exécutable s’arrête correctement lorsque le `app_offline.htm` fichier est détecté.</span><span class="sxs-lookup"><span data-stu-id="fab92-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="fab92-471">Valeur par défaut : `10`</span><span class="sxs-lookup"><span data-stu-id="fab92-471">Default: `10`</span></span><br><span data-ttu-id="fab92-472">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-472">Min: `0`</span></span><br><span data-ttu-id="fab92-473">Max : `600`</span><span class="sxs-lookup"><span data-stu-id="fab92-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="fab92-474">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-475">Durée en secondes pendant laquelle le module attend que le fichier exécutable démarre un processus à l’écoute sur le port.</span><span class="sxs-lookup"><span data-stu-id="fab92-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="fab92-476">Si cette limite de temps est dépassée, le module met fin au processus.</span><span class="sxs-lookup"><span data-stu-id="fab92-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="fab92-477">Lors de l’hébergement *in-process* : le processus n’est **pas** redémarré et n’utilise **pas** le `rapidFailsPerMinute` paramètre.</span><span class="sxs-lookup"><span data-stu-id="fab92-477">When hosting *in-process* : The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="fab92-478">En cas *d’hébergement out-of-process* : le module tente de relancer le processus lorsqu’il reçoit une nouvelle demande et continue de tenter de redémarrer le processus lors des demandes entrantes suivantes, à moins que l’application ne démarre pas le `rapidFailsPerMinute` nombre de fois au cours de la dernière minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-478">When hosting *out-of-process* : The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="fab92-479">La valeur 0 (zéro) n’est **pas** considérée comme un délai infini.</span><span class="sxs-lookup"><span data-stu-id="fab92-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="fab92-480">Valeur par défaut : `120`</span><span class="sxs-lookup"><span data-stu-id="fab92-480">Default: `120`</span></span><br><span data-ttu-id="fab92-481">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-481">Min: `0`</span></span><br><span data-ttu-id="fab92-482">Max : `3600`</span><span class="sxs-lookup"><span data-stu-id="fab92-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="fab92-483">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-484">Si la valeur est true, **stdout** et **stderr** pour le processus spécifié dans `processPath` sont redirigés vers le fichier spécifié dans **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="fab92-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile** .</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="fab92-485">Attribut de chaîne facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-485">Optional string attribute.</span></span></p><p><span data-ttu-id="fab92-486">Spécifie le chemin d’accès relatif ou absolu pour lequel `stdout` et `stderr` à partir du processus spécifié dans `processPath` sont journalisés.</span><span class="sxs-lookup"><span data-stu-id="fab92-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="fab92-487">Les chemins d’accès relatifs sont relatifs par rapport à la racine du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="fab92-488">Tout chemin d’accès commençant par `.` est relatif par rapport à la racine du site et tous les autres chemins d’accès sont traités comme des chemins d’accès absolus.</span><span class="sxs-lookup"><span data-stu-id="fab92-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="fab92-489">Tous les dossiers fournis dans le chemin sont créés par le module au moment de la création du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="fab92-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="fab92-490">À l’aide de délimiteurs de trait de soulignement, un horodateur, un ID de processus et une extension de fichier ( `.log` ) sont ajoutés au dernier segment du `stdoutLogFile` chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="fab92-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="fab92-491">Si `.\logs\stdout` est fourni en tant que valeur, un exemple de journal stdout est enregistré en tant que `stdout_20180205194132_1934.log` dans le `logs` dossier lorsqu’il est enregistré sur 2/5/2018 à 19:41:32 avec l’ID de processus 1934.</span><span class="sxs-lookup"><span data-stu-id="fab92-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="fab92-492">Définition des variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="fab92-492">Setting environment variables</span></span>

<span data-ttu-id="fab92-493">Des variables d’environnement peuvent être spécifiées pour le processus dans l’attribut `processPath`.</span><span class="sxs-lookup"><span data-stu-id="fab92-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="fab92-494">Spécifiez une variable d’environnement à l’aide de l’élément enfant `<environmentVariable>` d’un élément de collection `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="fab92-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="fab92-495">Les variables d’environnement définies dans cette section prévalent sur les variables d’environnement système.</span><span class="sxs-lookup"><span data-stu-id="fab92-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="fab92-496">L’exemple suivant définit deux variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="fab92-496">The following example sets two environment variables.</span></span> <span data-ttu-id="fab92-497">`ASPNETCORE_ENVIRONMENT` définit l’environnement de l’application sur `Development`.</span><span class="sxs-lookup"><span data-stu-id="fab92-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="fab92-498">Un développeur peut définir temporairement cette valeur dans le `web.config` fichier afin de forcer le chargement de la [page d’exception du développeur](xref:fundamentals/error-handling) lors du débogage d’une exception d’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="fab92-499">`CONFIG_DIR` est un exemple de variable d’environnement définie par l’utilisateur, dans laquelle le développeur a écrit du code qui lit la valeur de démarrage afin de former un chemin d’accès pour le chargement du fichier de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="fab92-500">Une alternative à la définition de l’environnement directement dans `web.config` consiste à inclure la `<EnvironmentName>` propriété dans le [profil de publication (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="fab92-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="fab92-501">Cette approche définit l’environnement dans le `web.config` moment où le projet est publié :</span><span class="sxs-lookup"><span data-stu-id="fab92-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="fab92-502">Affectez uniquement `Development` à la variable d’environnement `ASPNETCORE_ENVIRONMENT` sur les serveurs de test et de préproduction non accessibles aux réseaux non approuvés, par exemple Internet.</span><span class="sxs-lookup"><span data-stu-id="fab92-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="fab92-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="fab92-503">app_offline.htm</span></span>

<span data-ttu-id="fab92-504">Si un fichier portant le nom `app_offline.htm` est détecté dans le répertoire racine d’une application, le Module ASP.net Core tente d’arrêter correctement l’application et d’arrêter le traitement des demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="fab92-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="fab92-505">Si l’application est toujours active après le nombre de secondes défini dans `shutdownTimeLimit`, le module ASP.NET Core met fin au processus en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="fab92-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="fab92-506">Pendant que le `app_offline.htm` fichier est présent, le Module ASP.net Core répond aux demandes en renvoyant le contenu du `app_offline.htm` fichier.</span><span class="sxs-lookup"><span data-stu-id="fab92-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="fab92-507">Lorsque le `app_offline.htm` fichier est supprimé, la requête suivante démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="fab92-508">Lorsque vous utilisez le modèle d’hébergement out-of-process, l’application peut ne pas s’arrêter immédiatement s’il existe une connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="fab92-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="fab92-509">Par exemple, une connexion websocket peut retarder l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="fab92-510">Page d’erreur de démarrage</span><span class="sxs-lookup"><span data-stu-id="fab92-510">Start-up error page</span></span>

<span data-ttu-id="fab92-511">Les hébergements in-process et out-of-process produisent des pages d’erreurs personnalisées quand ils ne parviennent pas à démarrer l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="fab92-512">Si le module ASP.NET Core ne trouve pas le gestionnaire de requêtes in-process ou out-of-process, une page de code d’état *500.0 - Échec de chargement du gestionnaire in-process/out-of-process* s’affiche.</span><span class="sxs-lookup"><span data-stu-id="fab92-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="fab92-513">Pour l’hébergement in-process, si le module ASP.NET Core ne peut pas démarrer l’application, une page de code d’état *500.30 - Échec de démarrage* s’affiche.</span><span class="sxs-lookup"><span data-stu-id="fab92-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="fab92-514">Pour l’hébergement out-of-process, si le module ASP.NET Core ne peut pas lancer le processus backend ou que ce dernier démarre mais ne parvient pas à écouter sur le port configuré, une page de code d’état *502.5 - Échec du processus* s’affiche.</span><span class="sxs-lookup"><span data-stu-id="fab92-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="fab92-515">Pour supprimer cette page et revenir à la page IIS de codes d’état 5xx par défaut, utilisez l’attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="fab92-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="fab92-516">Pour plus d’informations sur la configuration des messages d’erreur personnalisés, consultez [Erreurs HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="fab92-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="fab92-517">Création et redirection de journal</span><span class="sxs-lookup"><span data-stu-id="fab92-517">Log creation and redirection</span></span>

<span data-ttu-id="fab92-518">Le module ASP.NET Core redirige la sortie de console stdout et stderr vers le disque si les attributs `stdoutLogEnabled` et `stdoutLogFile` de l’élément `aspNetCore` sont définis.</span><span class="sxs-lookup"><span data-stu-id="fab92-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="fab92-519">Tous les dossiers du chemin `stdoutLogFile` sont créés par le module au moment de la création du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="fab92-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="fab92-520">Le pool d’applications doit avoir un accès en écriture à l’emplacement où les journaux sont écrits (utilisez `IIS AppPool\{APP POOL NAME}` pour fournir une autorisation d’écriture, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications).</span><span class="sxs-lookup"><span data-stu-id="fab92-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="fab92-521">Aucune rotation n’est appliquée aux journaux, sauf en cas de recyclage/redémarrage du processus.</span><span class="sxs-lookup"><span data-stu-id="fab92-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="fab92-522">Il incombe à l’hébergeur de limiter l’espace disque utilisé par les journaux.</span><span class="sxs-lookup"><span data-stu-id="fab92-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="fab92-523">L’utilisation du journal stdout est recommandée uniquement pour résoudre les problèmes de démarrage d’application lors de l’hébergement sur IIS ou lors de l’utilisation [de la prise en charge au moment du développement pour IIS avec Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), et non pendant le débogage local et l’exécution de l’application avec IIS Express.</span><span class="sxs-lookup"><span data-stu-id="fab92-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="fab92-524">N’utilisez pas le journal stdout à des fins de journalisation d’application générale.</span><span class="sxs-lookup"><span data-stu-id="fab92-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="fab92-525">Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux.</span><span class="sxs-lookup"><span data-stu-id="fab92-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fab92-526">Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="fab92-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="fab92-527">Un horodatage et une extension de fichier sont ajoutés automatiquement quand le fichier journal est créé.</span><span class="sxs-lookup"><span data-stu-id="fab92-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="fab92-528">Le nom du fichier journal est composé en ajoutant l’horodateur, l’ID de processus et l’extension de fichier ( `.log` ) au dernier segment du `stdoutLogFile` chemin (généralement `stdout` ) délimité par des traits de soulignement.</span><span class="sxs-lookup"><span data-stu-id="fab92-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="fab92-529">Si le `stdoutLogFile` chemin d’accès se termine par `stdout` , un journal pour une application avec un PID de 1934 créé le 2/5/2018 à 19:42:32 a le nom de fichier `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="fab92-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="fab92-530">Si `stdoutLogEnabled` a la valeur false, les erreurs qui se produisent au moment du démarrage de l’application sont capturées et émises dans le journal des événements (30 Ko maximum).</span><span class="sxs-lookup"><span data-stu-id="fab92-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="fab92-531">Après le démarrage, tous les journaux supplémentaires sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="fab92-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="fab92-532">L’exemple `aspNetCore` d’élément suivant configure la journalisation stdout sur le chemin d’accès relatif `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="fab92-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="fab92-533">Vérifiez que l’identité de l’utilisateur du pool d’applications a l’autorisation d’écrire dans le chemin d’accès fourni.</span><span class="sxs-lookup"><span data-stu-id="fab92-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="fab92-534">Lors de la publication d’une application pour le déploiement Azure App Service, le kit de développement logiciel (SDK) Web définit la `stdoutLogFile` valeur sur `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="fab92-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="fab92-535">La `%home` variable d’environnement est prédéfinie pour les applications hébergées par Azure App service.</span><span class="sxs-lookup"><span data-stu-id="fab92-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="fab92-536">Pour plus d’informations sur les formats de chemin d’accès, consultez [formats de chemin d’accès de fichier sur les systèmes Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="fab92-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="fab92-537">Journaux de diagnostic améliorés</span><span class="sxs-lookup"><span data-stu-id="fab92-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="fab92-538">Le module ASP.NET Core est configurable pour proposer des journaux de diagnostic améliorés.</span><span class="sxs-lookup"><span data-stu-id="fab92-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="fab92-539">Ajoutez l' `<handlerSettings>` élément à l' `<aspNetCore>` élément dans `web.config` .</span><span class="sxs-lookup"><span data-stu-id="fab92-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="fab92-540">L’affectation de la valeur `TRACE` à `debugLevel` expose une fidélité plus élevée des informations de diagnostic :</span><span class="sxs-lookup"><span data-stu-id="fab92-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="fab92-541">Les dossiers dans le chemin d’accès fourni à la `<handlerSetting>` valeur ( `logs` dans l’exemple précédent) ne sont pas créés automatiquement par le module et doivent être préexistants dans le déploiement.</span><span class="sxs-lookup"><span data-stu-id="fab92-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="fab92-542">Le pool d’applications doit avoir un accès en écriture à l’emplacement où les journaux sont écrits (utilisez `IIS AppPool\{APP POOL NAME}` pour fournir une autorisation d’écriture, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications).</span><span class="sxs-lookup"><span data-stu-id="fab92-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="fab92-543">Les valeurs du niveau de débogage (`debugLevel`) peuvent inclure à la fois le niveau et l’emplacement.</span><span class="sxs-lookup"><span data-stu-id="fab92-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="fab92-544">Niveaux (dans l’ordre du moins au plus détaillé) :</span><span class="sxs-lookup"><span data-stu-id="fab92-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="fab92-545">ERROR</span><span class="sxs-lookup"><span data-stu-id="fab92-545">ERROR</span></span>
* <span data-ttu-id="fab92-546">WARNING</span><span class="sxs-lookup"><span data-stu-id="fab92-546">WARNING</span></span>
* <span data-ttu-id="fab92-547">INFO</span><span class="sxs-lookup"><span data-stu-id="fab92-547">INFO</span></span>
* <span data-ttu-id="fab92-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="fab92-548">TRACE</span></span>

<span data-ttu-id="fab92-549">Emplacements (plusieurs emplacements sont autorisés) :</span><span class="sxs-lookup"><span data-stu-id="fab92-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="fab92-550">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="fab92-550">CONSOLE</span></span>
* <span data-ttu-id="fab92-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="fab92-551">EVENTLOG</span></span>
* <span data-ttu-id="fab92-552">FILE</span><span class="sxs-lookup"><span data-stu-id="fab92-552">FILE</span></span>

<span data-ttu-id="fab92-553">Les paramètres de gestionnaire peuvent également être fournis par le biais de variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="fab92-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="fab92-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Chemin d’accès au fichier journal de débogage.</span><span class="sxs-lookup"><span data-stu-id="fab92-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="fab92-555">(Par défaut : `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="fab92-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="fab92-556">`ASPNETCORE_MODULE_DEBUG`: Paramètre de niveau de débogage.</span><span class="sxs-lookup"><span data-stu-id="fab92-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="fab92-557">Ne laissez **pas** la journalisation du débogage activée dans le déploiement plus longtemps que nécessaire pour résoudre un problème.</span><span class="sxs-lookup"><span data-stu-id="fab92-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="fab92-558">La taille du journal n’est pas limitée.</span><span class="sxs-lookup"><span data-stu-id="fab92-558">The size of the log isn't limited.</span></span> <span data-ttu-id="fab92-559">Si vous laissez la journalisation du débogage activée, vous risquez d’épuiser l’espace disque disponible et de bloquer le serveur ou le service d’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="fab92-560">Pour obtenir un exemple de l’élément dans le fichier, consultez [configuration avec web.config](#configuration-with-webconfig) `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="fab92-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="fab92-561">La configuration du proxy utilise le protocole HTTP et un jeton d’appariement</span><span class="sxs-lookup"><span data-stu-id="fab92-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="fab92-562">*S’applique uniquement à l’hébergement out-of-process.*</span><span class="sxs-lookup"><span data-stu-id="fab92-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="fab92-563">Le proxy créé entre le module ASP.NET Core et Kestrel utilise le protocole HTTP.</span><span class="sxs-lookup"><span data-stu-id="fab92-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="fab92-564">Il n’existe aucun risque d’écoute clandestine du trafic entre le module et Kestrel à partir d’un emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fab92-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="fab92-565">Un jeton d’appariement est utilisé pour garantir que les demandes reçues par Kestrel ont été traitées par IIS et ne proviennent pas d’une autre source.</span><span class="sxs-lookup"><span data-stu-id="fab92-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="fab92-566">Le jeton d’appariement est créé et défini dans une variable d’environnement (`ASPNETCORE_TOKEN`) par le module.</span><span class="sxs-lookup"><span data-stu-id="fab92-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="fab92-567">Le jeton d’appariement est également défini dans un en-tête (`MS-ASPNETCORE-TOKEN`) sur toutes les demandes traitées.</span><span class="sxs-lookup"><span data-stu-id="fab92-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="fab92-568">L'intergiciel IIS vérifie chaque demande qu’il reçoit pour confirmer que la valeur d’en-tête du jeton d'appariement correspond à la valeur de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fab92-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="fab92-569">Si les valeurs de jeton ne correspondent pas, la demande est connectée et rejetée.</span><span class="sxs-lookup"><span data-stu-id="fab92-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="fab92-570">La variable d’environnement du jeton d'appariement et le trafic entre le module et Kestrel ne sont pas accessibles à partir d’un emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fab92-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="fab92-571">Sans connaître la valeur du jeton d'appariement, une personne malveillante ne peut pas soumettre des demandes qui contournent la vérification de l’intergiciel IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="fab92-572">Module ASP.NET Core avec une configuration partagée IIS</span><span class="sxs-lookup"><span data-stu-id="fab92-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="fab92-573">Le programme d’installation du module ASP.NET Core s’exécute avec les privilèges du `TrustedInstaller` compte.</span><span class="sxs-lookup"><span data-stu-id="fab92-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="fab92-574">Étant donné que le compte système local ne dispose pas de l’autorisation de modification pour le chemin d’accès de partage utilisé par la configuration partagée IIS, le programme d’installation lève une erreur d’accès refusé lors de la tentative de configuration des paramètres du module dans le `applicationHost.config` fichier sur le partage.</span><span class="sxs-lookup"><span data-stu-id="fab92-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="fab92-575">Quand une configuration partagée IIS est utilisée sur la même machine que l’installation IIS, il convient d’exécuter le programme d’installation du bundle d’hébergement ASP.NET Core avec le paramètre `OPT_NO_SHARED_CONFIG_CHECK` défini sur `1` :</span><span class="sxs-lookup"><span data-stu-id="fab92-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="fab92-576">Quand le chemin de la configuration partagée ne se trouve pas sur la même machine que l’installation IIS, effectuez ces étapes :</span><span class="sxs-lookup"><span data-stu-id="fab92-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="fab92-577">Désactivez la configuration partagée IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="fab92-578">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="fab92-578">Run the installer.</span></span>
1. <span data-ttu-id="fab92-579">Exportez le fichier mis à jour `applicationHost.config` vers le partage.</span><span class="sxs-lookup"><span data-stu-id="fab92-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="fab92-580">Réactivez la configuration partagée IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="fab92-581">Version du module et journaux du programme d’installation du bundle d’hébergement</span><span class="sxs-lookup"><span data-stu-id="fab92-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="fab92-582">Pour déterminer la version du module ASP.NET Core installé :</span><span class="sxs-lookup"><span data-stu-id="fab92-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="fab92-583">Sur le système d’hébergement, accédez à `%windir%\System32\inetsrv` .</span><span class="sxs-lookup"><span data-stu-id="fab92-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="fab92-584">Localisez le `aspnetcore.dll` fichier.</span><span class="sxs-lookup"><span data-stu-id="fab92-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="fab92-585">Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="fab92-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="fab92-586">Sélectionnez l’onglet **Détails** . La version du **fichier** et la version du **produit** représentent la version installée du module.</span><span class="sxs-lookup"><span data-stu-id="fab92-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="fab92-587">Les journaux du programme d’installation du bundle d’hébergement pour le module sont disponibles à l’adresse `C:\\Users\\%UserName%\\AppData\\Local\\Temp` .</span><span class="sxs-lookup"><span data-stu-id="fab92-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="fab92-588">Le fichier est nommé `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log` , où l’espace réservé `{TIMESTAMP}` est l’horodateur.</span><span class="sxs-lookup"><span data-stu-id="fab92-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="fab92-589">Emplacements des fichiers du module, du schéma et de configuration</span><span class="sxs-lookup"><span data-stu-id="fab92-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="fab92-590">Module</span><span class="sxs-lookup"><span data-stu-id="fab92-590">Module</span></span>

<span data-ttu-id="fab92-591">**IIS (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="fab92-591">**IIS (x86/amd64)** :</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="fab92-592">**IIS Express (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="fab92-592">**IIS Express (x86/amd64)** :</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="fab92-593">schéma</span><span class="sxs-lookup"><span data-stu-id="fab92-593">Schema</span></span>

<span data-ttu-id="fab92-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="fab92-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="fab92-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="fab92-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="fab92-596">Configuration</span><span class="sxs-lookup"><span data-stu-id="fab92-596">Configuration</span></span>

<span data-ttu-id="fab92-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="fab92-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="fab92-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="fab92-598">**IIS Express**</span></span>

* <span data-ttu-id="fab92-599">Visual Studio : `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="fab92-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="fab92-600">*iisexpress.exe* COMMUN `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="fab92-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="fab92-601">Vous pouvez trouver les fichiers en recherchant `aspnetcore` dans le `applicationHost.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="fab92-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fab92-602">Le module ASP.NET Core est un module IIS natif qui se connecte dans le pipeline IIS pour transférer les requêtes web aux applications ASP.NET Core du back-end.</span><span class="sxs-lookup"><span data-stu-id="fab92-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="fab92-603">Versions de Windows prises en charge :</span><span class="sxs-lookup"><span data-stu-id="fab92-603">Supported Windows versions:</span></span>

* <span data-ttu-id="fab92-604">Windows 7 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fab92-604">Windows 7 or later</span></span>
* <span data-ttu-id="fab92-605">Windows Server 2008 R2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="fab92-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="fab92-606">Le module fonctionne seulement avec Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fab92-606">The module only works with Kestrel.</span></span> <span data-ttu-id="fab92-607">Le module n’est pas compatible avec [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="fab92-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="fab92-608">Étant donné que les applications ASP.NET Core s’exécutent dans un processus distinct du processus de travail IIS, le module traite également la gestion des processus.</span><span class="sxs-lookup"><span data-stu-id="fab92-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="fab92-609">Le module démarre le processus de l’application ASP.NET Core quand la première requête arrive et redémarre l’application si elle plante.</span><span class="sxs-lookup"><span data-stu-id="fab92-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="fab92-610">Il s’agit essentiellement du même comportement que celui des applications ASP.NET 4.x qui s’exécutent in-process dans IIS et sont gérées par le [service WAS (Windows Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="fab92-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="fab92-611">Le schéma suivant illustre la relation entre IIS, le module ASP.NET Core et une application :</span><span class="sxs-lookup"><span data-stu-id="fab92-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Module ASP.NET Core](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="fab92-613">Les requêtes arrivent du web au pilote HTTP.sys en mode noyau.</span><span class="sxs-lookup"><span data-stu-id="fab92-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="fab92-614">Le pilote route les requêtes vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fab92-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="fab92-615">Le module transfère les requêtes à Kestrel sur un port aléatoire pour l’application, qui n’est ni le port 80 ni le port 443.</span><span class="sxs-lookup"><span data-stu-id="fab92-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="fab92-616">Le module spécifie le port via une variable d’environnement au démarrage, et l' [intergiciel (middleware) d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configure le serveur pour l’écoute `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="fab92-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="fab92-617">Des vérifications supplémentaires sont effectuées, et les requêtes qui ne proviennent pas du module sont rejetées.</span><span class="sxs-lookup"><span data-stu-id="fab92-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="fab92-618">Le module ne prend pas en charge le transfert HTTPS : les requêtes sont donc transférées via HTTP, même si IIS les reçoit via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fab92-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="fab92-619">Dès que Kestrel sélectionne la requête dans le module, celle-ci est envoyée (push) dans le pipeline de middlewares d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fab92-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="fab92-620">Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="fab92-621">L’intergiciel (middleware) ajouté par l’intégration d’IIS met à jour le schéma, l’adresse IP distante et la base du chemin pour prendre en compte le transfert de la requête à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fab92-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="fab92-622">La réponse de l’application est ensuite repassée à IIS, qui la renvoie au client HTTP à l’origine de la requête.</span><span class="sxs-lookup"><span data-stu-id="fab92-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="fab92-623">De nombreux modules natifs, comme l’authentification Windows, restent actifs.</span><span class="sxs-lookup"><span data-stu-id="fab92-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="fab92-624">Pour obtenir plus d’informations sur les modules IIS actifs avec le module ASP.NET Core, consultez <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="fab92-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="fab92-625">Le module ASP.NET Core peut également  :</span><span class="sxs-lookup"><span data-stu-id="fab92-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="fab92-626">Définir des variables d’environnement pour le processus de travail.</span><span class="sxs-lookup"><span data-stu-id="fab92-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="fab92-627">Journaliser la sortie de stdout dans un stockage de fichiers pour résoudre les problèmes de démarrage.</span><span class="sxs-lookup"><span data-stu-id="fab92-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="fab92-628">Transférer des jetons d’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="fab92-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="fab92-629">Comment installer et utiliser le module ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fab92-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="fab92-630">Pour obtenir des instructions sur l’installation du module ASP.NET Core, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="fab92-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="fab92-631">Configuration avec web.config</span><span class="sxs-lookup"><span data-stu-id="fab92-631">Configuration with web.config</span></span>

<span data-ttu-id="fab92-632">Le module ASP.NET Core est configuré avec la section `aspNetCore` du nœud `system.webServer` dans le fichier *web.config* du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="fab92-633">Le fichier *web.config* suivant est publié pour un [déploiement dépendant du framework](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) et configure le module ASP.NET Core pour gérer les demandes du site :</span><span class="sxs-lookup"><span data-stu-id="fab92-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="fab92-634">Le fichier *web.config* suivant est publié pour un [déploiement autonome](/dotnet/articles/core/deploying/#self-contained-deployments-scd) :</span><span class="sxs-lookup"><span data-stu-id="fab92-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="fab92-635">Lorsqu’une application est déployée sur [Azure App Service](https://azure.microsoft.com/services/app-service/), le chemin d’accès `stdoutLogFile` est défini sur `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="fab92-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="fab92-636">Le chemin d’accès enregistre les journaux stdout dans le dossier *LogFiles* , un emplacement automatiquement créé par le service.</span><span class="sxs-lookup"><span data-stu-id="fab92-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="fab92-637">Pour plus d’informations sur la configuration d’une sous-application IIS, consultez <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="fab92-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="fab92-638">Attributs de l’élément aspNetCore</span><span class="sxs-lookup"><span data-stu-id="fab92-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="fab92-639">Attribut</span><span class="sxs-lookup"><span data-stu-id="fab92-639">Attribute</span></span> | <span data-ttu-id="fab92-640">Description</span><span class="sxs-lookup"><span data-stu-id="fab92-640">Description</span></span> | <span data-ttu-id="fab92-641">Default</span><span class="sxs-lookup"><span data-stu-id="fab92-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="fab92-642">Attribut de chaîne facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-642">Optional string attribute.</span></span></p><p><span data-ttu-id="fab92-643">Arguments pour l’exécutable spécifié dans **processPath** .</span><span class="sxs-lookup"><span data-stu-id="fab92-643">Arguments to the executable specified in **processPath** .</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="fab92-644">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-645">Si la valeur est true, la page **502.5 - Échec du processus** est supprimée, et la page de code d’état 502 configurée dans le fichier *web.config* est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="fab92-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="fab92-646">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-647">Si la valeur est true, le jeton est transmis au processus enfant qui écoute sur %ASPNETCORE_PORT% sous la forme d’un en-tête 'MS-ASPNETCORE-WINAUTHTOKEN' par demande.</span><span class="sxs-lookup"><span data-stu-id="fab92-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="fab92-648">Il incombe à ce processus d’appeler CloseHandle sur ce jeton par demande.</span><span class="sxs-lookup"><span data-stu-id="fab92-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="fab92-649">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-650">Spécifie le nombre d’instances du processus indiqué dans le paramètre **processPath** qui peuvent être lancées par application.</span><span class="sxs-lookup"><span data-stu-id="fab92-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="fab92-651">Il est déconseillé de définir `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="fab92-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="fab92-652">Cet attribut sera supprimé dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="fab92-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="fab92-653">Valeur par défaut : `1`</span><span class="sxs-lookup"><span data-stu-id="fab92-653">Default: `1`</span></span><br><span data-ttu-id="fab92-654">Min : `1`</span><span class="sxs-lookup"><span data-stu-id="fab92-654">Min: `1`</span></span><br><span data-ttu-id="fab92-655">Max : `100`</span><span class="sxs-lookup"><span data-stu-id="fab92-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="fab92-656">Attribut de chaîne requis.</span><span class="sxs-lookup"><span data-stu-id="fab92-656">Required string attribute.</span></span></p><p><span data-ttu-id="fab92-657">Chemin d’accès au fichier exécutable lançant un processus d’écoute des requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fab92-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="fab92-658">Les chemins d’accès relatifs sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fab92-658">Relative paths are supported.</span></span> <span data-ttu-id="fab92-659">Si le chemin d’accès commence par `.`, il est considéré comme étant relatif par rapport à la racine du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="fab92-660">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-661">Indique le nombre de fois où le processus spécifié dans **processPath** est autorisé à se bloquer par minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="fab92-662">Si cette limite est dépassée, le module arrête le lancement du processus pour le reste de la minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="fab92-663">Valeur par défaut : `10`</span><span class="sxs-lookup"><span data-stu-id="fab92-663">Default: `10`</span></span><br><span data-ttu-id="fab92-664">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-664">Min: `0`</span></span><br><span data-ttu-id="fab92-665">Max : `100`</span><span class="sxs-lookup"><span data-stu-id="fab92-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="fab92-666">Attribut timespan facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="fab92-667">Spécifie la durée pendant laquelle le module ASP.NET Core attend une réponse de la part du processus à l’écoute sur %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="fab92-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="fab92-668">Dans les versions du module ASP.NET Core fournies avec ASP.NET Core 2.1 ou version ultérieure, la valeur `requestTimeout` est spécifiée en heures, minutes et secondes.</span><span class="sxs-lookup"><span data-stu-id="fab92-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="fab92-669">Valeur par défaut : `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-669">Default: `00:02:00`</span></span><br><span data-ttu-id="fab92-670">Min : `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-670">Min: `00:00:00`</span></span><br><span data-ttu-id="fab92-671">Max : `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="fab92-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="fab92-672">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-673">Durée en secondes pendant laquelle le module attend que l’exécutable s’arrête normalement lorsque le fichier *app_offline.htm* est détecté.</span><span class="sxs-lookup"><span data-stu-id="fab92-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="fab92-674">Valeur par défaut : `10`</span><span class="sxs-lookup"><span data-stu-id="fab92-674">Default: `10`</span></span><br><span data-ttu-id="fab92-675">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-675">Min: `0`</span></span><br><span data-ttu-id="fab92-676">Max : `600`</span><span class="sxs-lookup"><span data-stu-id="fab92-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="fab92-677">Attribut entier facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="fab92-678">Durée en secondes pendant laquelle le module attend que le fichier exécutable démarre un processus à l’écoute sur le port.</span><span class="sxs-lookup"><span data-stu-id="fab92-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="fab92-679">Si cette limite de temps est dépassée, le module met fin au processus.</span><span class="sxs-lookup"><span data-stu-id="fab92-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="fab92-680">Le module tente de relancer le processus lorsqu’il reçoit une nouvelle requête, puis continue d’essayer de redémarrer le processus pour les requêtes entrantes suivantes, sauf si l’application ne démarre pas **rapidFailsPerMinute** un certain nombre de fois au cours de la dernière minute.</span><span class="sxs-lookup"><span data-stu-id="fab92-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="fab92-681">La valeur 0 (zéro) n’est **pas** considérée comme un délai infini.</span><span class="sxs-lookup"><span data-stu-id="fab92-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="fab92-682">Valeur par défaut : `120`</span><span class="sxs-lookup"><span data-stu-id="fab92-682">Default: `120`</span></span><br><span data-ttu-id="fab92-683">Min : `0`</span><span class="sxs-lookup"><span data-stu-id="fab92-683">Min: `0`</span></span><br><span data-ttu-id="fab92-684">Max : `3600`</span><span class="sxs-lookup"><span data-stu-id="fab92-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="fab92-685">Attribut booléen facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="fab92-686">Si la valeur est true, les attributs **stdout** et **stderr** du processus spécifié dans **processPath** sont redirigés vers le fichier spécifié dans **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="fab92-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile** .</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="fab92-687">Attribut de chaîne facultatif.</span><span class="sxs-lookup"><span data-stu-id="fab92-687">Optional string attribute.</span></span></p><p><span data-ttu-id="fab92-688">Spécifie le chemin d’accès relatif ou absolu pour lequel les attributs **stdout** et **stderr** du processus spécifié dans **processPath** sont consignés.</span><span class="sxs-lookup"><span data-stu-id="fab92-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="fab92-689">Les chemins d’accès relatifs sont relatifs par rapport à la racine du site.</span><span class="sxs-lookup"><span data-stu-id="fab92-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="fab92-690">Tout chemin d’accès commençant par `.` est relatif par rapport à la racine du site et tous les autres chemins d’accès sont traités comme des chemins d’accès absolus.</span><span class="sxs-lookup"><span data-stu-id="fab92-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="fab92-691">Tous les dossiers spécifiés dans le chemin d’accès doivent exister pour permettre au module de créer le fichier journal.</span><span class="sxs-lookup"><span data-stu-id="fab92-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="fab92-692">Si vous utilisez des délimiteurs de trait de soulignement, un horodatage, un ID de processus et une extension de fichier ( *.log* ) sont ajoutés au dernier segment du chemin d'accès **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="fab92-692">Using underscore delimiters, a timestamp, process ID, and file extension ( *.log* ) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="fab92-693">Si `.\logs\stdout` est fourni en tant que valeur, un exemple de journal stdout est enregistré en tant que *stdout_20180205194132_1934.log* dans le dossier *journaux* avec un enregistrement effectué le 05/02/2018 à 19:41:32 et un ID de processus de 1934.</span><span class="sxs-lookup"><span data-stu-id="fab92-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="fab92-694">Définition des variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="fab92-694">Setting environment variables</span></span>

<span data-ttu-id="fab92-695">Des variables d’environnement peuvent être spécifiées pour le processus dans l’attribut `processPath`.</span><span class="sxs-lookup"><span data-stu-id="fab92-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="fab92-696">Spécifiez une variable d’environnement à l’aide de l’élément enfant `<environmentVariable>` d’un élément de collection `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="fab92-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="fab92-697">Les variables d’environnement définies dans cette section sont en conflit avec les variables d’environnement système qui ont le même nom.</span><span class="sxs-lookup"><span data-stu-id="fab92-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="fab92-698">Si une variable d’environnement est définie à la fois dans le fichier *web.config* et au niveau du système dans Windows, la valeur provenant du fichier *web.config* est ajoutée à la valeur de la variable d’environnement système (par exemple, `ASPNETCORE_ENVIRONMENT: Development;Development`), ce qui empêche le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="fab92-699">L’exemple suivant définit deux variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="fab92-699">The following example sets two environment variables.</span></span> <span data-ttu-id="fab92-700">`ASPNETCORE_ENVIRONMENT` définit l’environnement de l’application sur `Development`.</span><span class="sxs-lookup"><span data-stu-id="fab92-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="fab92-701">Un développeur peut définir temporairement cette valeur dans le fichier *web.config* afin de forcer le chargement de la [Page d’exception de développeur](xref:fundamentals/error-handling) lors du débogage d’une exception d’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="fab92-702">`CONFIG_DIR` est un exemple de variable d’environnement définie par l’utilisateur, dans laquelle le développeur a écrit du code qui lit la valeur de démarrage afin de former un chemin d’accès pour le chargement du fichier de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="fab92-703">Affectez uniquement `Development` à la variable d’environnement `ASPNETCORE_ENVIRONMENT` sur les serveurs de test et de préproduction non accessibles aux réseaux non approuvés, par exemple Internet.</span><span class="sxs-lookup"><span data-stu-id="fab92-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="fab92-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="fab92-704">app_offline.htm</span></span>

<span data-ttu-id="fab92-705">Si un fichier portant le nom *app_offline.htm* est détecté dans le répertoire racine d’une application, le module ASP.NET Core tente d’arrêter normalement l’application, puis interrompt le traitement des requêtes entrantes.</span><span class="sxs-lookup"><span data-stu-id="fab92-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="fab92-706">Si l’application est toujours active après le nombre de secondes défini dans `shutdownTimeLimit`, le module ASP.NET Core met fin au processus en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="fab92-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="fab92-707">Tant que le fichier *app_offline.htm* est présent, le module ASP.NET Core répond aux requêtes en renvoyant le contenu du fichier *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="fab92-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="fab92-708">Lorsque le fichier *app_offline.htm* est supprimé, la requête suivante démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="fab92-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="fab92-709">Page d’erreur de démarrage</span><span class="sxs-lookup"><span data-stu-id="fab92-709">Start-up error page</span></span>

<span data-ttu-id="fab92-710">Si le module ASP.NET Core ne peut pas lancer le processus backend ou que ce dernier démarre mais ne parvient pas à écouter sur le port configuré, une page de code d’état *502.5 - Échec du processus* s’affiche.</span><span class="sxs-lookup"><span data-stu-id="fab92-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="fab92-711">Pour supprimer cette page et revenir à la page IIS de code d’état 502 par défaut, utilisez l’attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="fab92-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="fab92-712">Pour plus d’informations sur la configuration des messages d’erreur personnalisés, consultez [Erreurs HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="fab92-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="fab92-713">Création et redirection de journal</span><span class="sxs-lookup"><span data-stu-id="fab92-713">Log creation and redirection</span></span>

<span data-ttu-id="fab92-714">Le module ASP.NET Core redirige la sortie de console stdout et stderr vers le disque si les attributs `stdoutLogEnabled` et `stdoutLogFile` de l’élément `aspNetCore` sont définis.</span><span class="sxs-lookup"><span data-stu-id="fab92-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="fab92-715">Tous les dossiers du chemin `stdoutLogFile` sont créés par le module au moment de la création du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="fab92-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="fab92-716">Le pool d’applications doit avoir un accès en écriture à l’emplacement auquel les journaux sont écrits (utilisez `IIS AppPool\<app_pool_name>` pour fournir les autorisations d’écriture).</span><span class="sxs-lookup"><span data-stu-id="fab92-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="fab92-717">Aucune rotation n’est appliquée aux journaux, sauf en cas de recyclage/redémarrage du processus.</span><span class="sxs-lookup"><span data-stu-id="fab92-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="fab92-718">Il incombe à l’hébergeur de limiter l’espace disque utilisé par les journaux.</span><span class="sxs-lookup"><span data-stu-id="fab92-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="fab92-719">L’utilisation du journal stdout est recommandée uniquement pour résoudre les problèmes de démarrage d’application lors de l’hébergement sur IIS ou lors de l’utilisation [de la prise en charge au moment du développement pour IIS avec Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), et non pendant le débogage local et l’exécution de l’application avec IIS Express.</span><span class="sxs-lookup"><span data-stu-id="fab92-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="fab92-720">N’utilisez pas le journal stdout à des fins de journalisation d’application générale.</span><span class="sxs-lookup"><span data-stu-id="fab92-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="fab92-721">Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux.</span><span class="sxs-lookup"><span data-stu-id="fab92-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="fab92-722">Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="fab92-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="fab92-723">Un horodatage et une extension de fichier sont ajoutés automatiquement quand le fichier journal est créé.</span><span class="sxs-lookup"><span data-stu-id="fab92-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="fab92-724">Le nom du fichier journal est créé en ajoutant l’horodatage, un ID de processus et une extension de fichier ( *.log* ) au dernier segment du chemin d'accès `stdoutLogFile` (généralement *stdout* ), séparés par des traits de soulignement.</span><span class="sxs-lookup"><span data-stu-id="fab92-724">The log file name is composed by appending the timestamp, process ID, and file extension ( *.log* ) to the last segment of the `stdoutLogFile` path (typically *stdout* ) delimited by underscores.</span></span> <span data-ttu-id="fab92-725">Si le chemin d'accès `stdoutLogFile` se termine par *stdout* , un journal pour une application avec un PID de 1934 créé le 5/2/2018 à 19:42:32 affiche le nom de fichier *stdout_20180205194132_1934.log* .</span><span class="sxs-lookup"><span data-stu-id="fab92-725">If the `stdoutLogFile` path ends with *stdout* , a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log* .</span></span>

<span data-ttu-id="fab92-726">L’exemple `aspNetCore` d’élément suivant configure la journalisation stdout sur le chemin d’accès relatif `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="fab92-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="fab92-727">Vérifiez que l’identité de l’utilisateur AppPool à l’autorisation d’écrire dans le chemin d’accès fourni.</span><span class="sxs-lookup"><span data-stu-id="fab92-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="fab92-728">Lors de la publication d’une application pour le déploiement Azure App Service, le kit de développement logiciel (SDK) Web définit la `stdoutLogFile` valeur sur `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="fab92-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="fab92-729">La `%home` variable d’environnement est prédéfinie pour les applications hébergées par Azure App service.</span><span class="sxs-lookup"><span data-stu-id="fab92-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="fab92-730">Pour créer des règles de filtre de journalisation, consultez les sections [configuration](xref:fundamentals/logging/index#log-filtering) et [filtrage des journaux](xref:fundamentals/logging/index#log-filtering) de la documentation ASP.net Core Logging.</span><span class="sxs-lookup"><span data-stu-id="fab92-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="fab92-731">Pour plus d’informations sur les formats de chemin d’accès, consultez [formats de chemin d’accès de fichier sur les systèmes Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="fab92-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="fab92-732">La configuration du proxy utilise le protocole HTTP et un jeton d’appariement</span><span class="sxs-lookup"><span data-stu-id="fab92-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="fab92-733">Le proxy créé entre le module ASP.NET Core et Kestrel utilise le protocole HTTP.</span><span class="sxs-lookup"><span data-stu-id="fab92-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="fab92-734">Il n’existe aucun risque d’écoute clandestine du trafic entre le module et Kestrel à partir d’un emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fab92-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="fab92-735">Un jeton d’appariement est utilisé pour garantir que les demandes reçues par Kestrel ont été traitées par IIS et ne proviennent pas d’une autre source.</span><span class="sxs-lookup"><span data-stu-id="fab92-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="fab92-736">Le jeton d’appariement est créé et défini dans une variable d’environnement (`ASPNETCORE_TOKEN`) par le module.</span><span class="sxs-lookup"><span data-stu-id="fab92-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="fab92-737">Le jeton d’appariement est également défini dans un en-tête (`MS-ASPNETCORE-TOKEN`) sur toutes les demandes traitées.</span><span class="sxs-lookup"><span data-stu-id="fab92-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="fab92-738">L'intergiciel IIS vérifie chaque demande qu’il reçoit pour confirmer que la valeur d’en-tête du jeton d'appariement correspond à la valeur de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fab92-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="fab92-739">Si les valeurs de jeton ne correspondent pas, la demande est connectée et rejetée.</span><span class="sxs-lookup"><span data-stu-id="fab92-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="fab92-740">La variable d’environnement du jeton d'appariement et le trafic entre le module et Kestrel ne sont pas accessibles à partir d’un emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fab92-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="fab92-741">Sans connaître la valeur du jeton d'appariement, une personne malveillante ne peut pas soumettre des demandes qui contournent la vérification de l’intergiciel IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="fab92-742">Module ASP.NET Core avec une configuration partagée IIS</span><span class="sxs-lookup"><span data-stu-id="fab92-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="fab92-743">Le programme d’installation du module ASP.NET Core s’exécute avec les privilèges du compte **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="fab92-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="fab92-744">Comme le compte système local n’a pas l’autorisation de modifier le chemin de partage utilisé par la configuration partagée IIS, le programme d’installation génère une erreur d’accès refusé pendant la tentative de configuration des paramètres du module dans le fichier *applicationHost.config* du partage.</span><span class="sxs-lookup"><span data-stu-id="fab92-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="fab92-745">Lorsque vous utilisez une configuration partagée IIS, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="fab92-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="fab92-746">Désactivez la configuration partagée IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="fab92-747">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="fab92-747">Run the installer.</span></span>
1. <span data-ttu-id="fab92-748">Exportez le fichier *applicationHost.config* mis à jour vers le partage.</span><span class="sxs-lookup"><span data-stu-id="fab92-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="fab92-749">Réactivez la configuration partagée IIS.</span><span class="sxs-lookup"><span data-stu-id="fab92-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="fab92-750">Version du module et journaux du programme d’installation du bundle d’hébergement</span><span class="sxs-lookup"><span data-stu-id="fab92-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="fab92-751">Pour déterminer la version du module ASP.NET Core installé :</span><span class="sxs-lookup"><span data-stu-id="fab92-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="fab92-752">Sur le système hôte, accédez à *%windir%\System32\inetsrv* .</span><span class="sxs-lookup"><span data-stu-id="fab92-752">On the hosting system, navigate to *%windir%\System32\inetsrv* .</span></span>
1. <span data-ttu-id="fab92-753">Recherchez le fichier *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="fab92-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="fab92-754">Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="fab92-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="fab92-755">Sélectionnez l’onglet **Détails** . La version du **fichier** et la version du **produit** représentent la version installée du module.</span><span class="sxs-lookup"><span data-stu-id="fab92-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="fab92-756">Les journaux du programme d’installation du bundle d’hébergement pour le module se trouvent sur *C : \\ Users \\ % UserName% \\ AppData \\ local \\ temp* . Le fichier est nommé *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log* .</span><span class="sxs-lookup"><span data-stu-id="fab92-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp* . The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log* .</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="fab92-757">Emplacements des fichiers du module, du schéma et de configuration</span><span class="sxs-lookup"><span data-stu-id="fab92-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="fab92-758">Module</span><span class="sxs-lookup"><span data-stu-id="fab92-758">Module</span></span>

<span data-ttu-id="fab92-759">**IIS (x86/amd64) :**</span><span class="sxs-lookup"><span data-stu-id="fab92-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="fab92-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="fab92-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="fab92-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="fab92-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="fab92-762">**IIS Express (x86/amd64) :**</span><span class="sxs-lookup"><span data-stu-id="fab92-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="fab92-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="fab92-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="fab92-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="fab92-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="fab92-765">schéma</span><span class="sxs-lookup"><span data-stu-id="fab92-765">Schema</span></span>

<span data-ttu-id="fab92-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="fab92-766">**IIS**</span></span>

* <span data-ttu-id="fab92-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="fab92-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="fab92-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="fab92-768">**IIS Express**</span></span>

* <span data-ttu-id="fab92-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="fab92-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="fab92-770">Configuration</span><span class="sxs-lookup"><span data-stu-id="fab92-770">Configuration</span></span>

<span data-ttu-id="fab92-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="fab92-771">**IIS**</span></span>

* <span data-ttu-id="fab92-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="fab92-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="fab92-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="fab92-773">**IIS Express**</span></span>

* <span data-ttu-id="fab92-774">Visual Studio : {RACINE DE L’APPLICATION}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="fab92-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="fab92-775">*iisexpress.exe* CLI : %PROFILUTILISATEUR%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="fab92-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="fab92-776">Vous trouverez les fichiers en recherchant *aspnetcore* dans le fichier *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="fab92-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fab92-777">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fab92-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="fab92-778">[Source de référence du Module ASP.net Core (branche principale)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): utilisez la liste déroulante **branche** pour sélectionner une version spécifique (par exemple, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="fab92-778">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
