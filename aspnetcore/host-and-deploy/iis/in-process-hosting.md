---
title: Hébergement in-process avec IIS et ASP.NET Core
author: rick-anderson
description: En savoir plus sur l’hébergement in-process avec IIS et le module ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058478"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="f2095-103">Hébergement in-process avec IIS et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f2095-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="f2095-104">L’hébergement in-process exécute une application ASP.NET Core dans le même processus que son processus de travail IIS.</span><span class="sxs-lookup"><span data-stu-id="f2095-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="f2095-105">L’hébergement in-process offre de meilleures performances que l’hébergement out-of-process parce que les requêtes n’ont pas de proxy sur l’adaptateur de bouclage, interface réseau qui retourne du trafic réseau sortant à la même machine.</span><span class="sxs-lookup"><span data-stu-id="f2095-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="f2095-106">Le schéma suivant montre la relation entre IIS, le module ASP.NET Core et une application hébergée dans un processus :</span><span class="sxs-lookup"><span data-stu-id="f2095-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Module ASP.NET Core dans le scénario d’hébergement in-process](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="f2095-108">Activer l’hébergement in-process</span><span class="sxs-lookup"><span data-stu-id="f2095-108">Enable in-process hosting</span></span>

<span data-ttu-id="f2095-109">Étant donné que ASP.NET Core 3,0, l’hébergement in-process a été activé par défaut pour toutes les applications déployées sur IIS.</span><span class="sxs-lookup"><span data-stu-id="f2095-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="f2095-110">Pour configurer explicitement une application pour l’hébergement in-process, affectez à la `<AspNetCoreHostingModel>` propriété la valeur `InProcess` dans le fichier projet ( `.csproj` ) :</span><span class="sxs-lookup"><span data-stu-id="f2095-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="f2095-111">Architecture générale</span><span class="sxs-lookup"><span data-stu-id="f2095-111">General architecture</span></span>

<span data-ttu-id="f2095-112">Le déroulement général d’une requête est le suivant :</span><span class="sxs-lookup"><span data-stu-id="f2095-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="f2095-113">Une requête arrive du web au pilote HTTP.sys en mode noyau.</span><span class="sxs-lookup"><span data-stu-id="f2095-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="f2095-114">Le pilote achemine la requête native vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f2095-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="f2095-115">Le module ASP.NET Core reçoit la demande native et la transmet au serveur HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="f2095-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="f2095-116">Le serveur HTTP IIS est une implémentation du serveur in-process pour IIS qui convertit la requête native en requête managée.</span><span class="sxs-lookup"><span data-stu-id="f2095-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="f2095-117">Une fois que le serveur HTTP IIS a traité la requête :</span><span class="sxs-lookup"><span data-stu-id="f2095-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="f2095-118">La demande est envoyée au pipeline de l’intergiciel (middleware) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f2095-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="f2095-119">Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application.</span><span class="sxs-lookup"><span data-stu-id="f2095-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="f2095-120">La réponse de l’application est retransmise à IIS via le serveur HTTP IIS.</span><span class="sxs-lookup"><span data-stu-id="f2095-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="f2095-121">IIS envoie la réponse au client qui a initié la demande.</span><span class="sxs-lookup"><span data-stu-id="f2095-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="f2095-122">`CreateDefaultBuilder` Ajoute une <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance en appelant la <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> méthode pour démarrer [CoreCLR](/dotnet/standard/glossary#coreclr) et héberger l’application à l’intérieur du processus de travail IIS ( `w3wp.exe` ou `iisexpress.exe` ).</span><span class="sxs-lookup"><span data-stu-id="f2095-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="f2095-123">Les tests de performance indiquent que l’hébergement d’une application.NET Core in-process fournit un débit de requêtes beaucoup plus élevé que l'hébergement de l’application out-of-process et des requêtes proxy vers [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f2095-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f2095-124">Les applications publiées comme un fichier exécutable unique ne peuvent pas être chargées par le modèle d’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="f2095-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="f2095-125">Configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="f2095-125">Application configuration</span></span>

<span data-ttu-id="f2095-126">Pour configurer les options IIS, incluez une configuration de service pour <xref:Microsoft.AspNetCore.Builder.IISServerOptions> dans <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="f2095-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="f2095-127">L’exemple suivant désactive AutomaticAuthentication :</span><span class="sxs-lookup"><span data-stu-id="f2095-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="f2095-128">Option</span><span class="sxs-lookup"><span data-stu-id="f2095-128">Option</span></span> | <span data-ttu-id="f2095-129">Default</span><span class="sxs-lookup"><span data-stu-id="f2095-129">Default</span></span> | <span data-ttu-id="f2095-130">Paramètre</span><span class="sxs-lookup"><span data-stu-id="f2095-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="f2095-131">Si `true`, IIS Server définit le `HttpContext.User` authentifié par [Authentification Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="f2095-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="f2095-132">Si `false`, le serveur fournit uniquement une identité pour `HttpContext.User` et répond aux questions explicitement posées par `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="f2095-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="f2095-133">L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne.</span><span class="sxs-lookup"><span data-stu-id="f2095-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="f2095-134">Pour plus d’informations, consultez [authentification Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="f2095-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="f2095-135">Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion.</span><span class="sxs-lookup"><span data-stu-id="f2095-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="f2095-136">Indique si des e/s synchrones sont autorisées pour `HttpContext.Request` et `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="f2095-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="f2095-137">Récupère ou définit la taille maximale du corps de la demande de `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="f2095-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="f2095-138">Il est à noter que IIS a comme limite `maxAllowedContentLength`, qui doit être traité avant `MaxRequestBodySize` défini dans `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="f2095-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="f2095-139">Les modifications de `MaxRequestBodySize` n’ont pas d’incidence sur `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="f2095-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="f2095-140">Pour augmenter `maxAllowedContentLength` , ajoutez une entrée dans le `web.config` pour définir `maxAllowedContentLength` sur une valeur supérieure.</span><span class="sxs-lookup"><span data-stu-id="f2095-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="f2095-141">Pour plus d’informations, voir [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="f2095-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="f2095-142">Différences entre l’hébergement in-process et out-of-process</span><span class="sxs-lookup"><span data-stu-id="f2095-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="f2095-143">Les caractéristiques suivantes s’appliquent lors de l’hébergement in-process :</span><span class="sxs-lookup"><span data-stu-id="f2095-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f2095-144">Le serveur HTTP IIS (`IISHttpServer`) est utilisé à la place du serveur [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f2095-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f2095-145">Pour in-process, les [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) appels <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> à :</span><span class="sxs-lookup"><span data-stu-id="f2095-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="f2095-146">Enregistrer `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="f2095-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f2095-147">Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f2095-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f2095-148">Configurer l’hôte pour capturer des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="f2095-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f2095-149">L' [ `requestTimeout` attribut](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) ne s’applique pas à l’hébergement in-process.</span><span class="sxs-lookup"><span data-stu-id="f2095-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f2095-150">Le partage d’un pool d’applications entre les applications n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f2095-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f2095-151">Utilisez un pool d’applications par application.</span><span class="sxs-lookup"><span data-stu-id="f2095-151">Use one app pool per app.</span></span>

* <span data-ttu-id="f2095-152">L’architecture (nombre de bits) de l’application et le runtime installé (x64 ou x86) doivent correspondre à l’architecture du pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="f2095-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="f2095-153">Par exemple, les applications publiées pour 32 bits (x86) doivent avoir 32 bits activés pour leurs pools d’applications IIS.</span><span class="sxs-lookup"><span data-stu-id="f2095-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="f2095-154">Pour plus d’informations, consultez la section [créer le site IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="f2095-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="f2095-155">Les déconnexions du client sont détectées.</span><span class="sxs-lookup"><span data-stu-id="f2095-155">Client disconnects are detected.</span></span> <span data-ttu-id="f2095-156">Le [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) jeton d’annulation est annulé lorsque le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="f2095-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f2095-157">Dans le cas d’un hébergement in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> n’est pas appelé en interne pour initialiser un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f2095-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f2095-158">Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="f2095-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f2095-159">Si vous transformez les revendications avec une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, appelez <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> pour ajouter des services d’authentification :</span><span class="sxs-lookup"><span data-stu-id="f2095-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

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
  
* <span data-ttu-id="f2095-160">[Les déploiements de package Web (à fichier unique)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f2095-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
