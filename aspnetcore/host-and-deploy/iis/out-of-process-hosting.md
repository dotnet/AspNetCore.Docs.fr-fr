---
title: Hébergement out-of-process avec IIS et ASP.NET Core
author: rick-anderson
description: En savoir plus sur l’hébergement hors processus avec IIS et le module ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060415"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="504d6-103">Hébergement out-of-process avec IIS et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="504d6-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="504d6-104">Étant donné que ASP.NET Core applications s’exécutent dans un processus distinct du processus de travail IIS, le module ASP.NET Core gère la gestion des processus.</span><span class="sxs-lookup"><span data-stu-id="504d6-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="504d6-105">Le module démarre le processus pour l’application ASP.NET Core quand la première requête arrive, et il redémarre l’application si elle s’arrête ou se bloque.</span><span class="sxs-lookup"><span data-stu-id="504d6-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="504d6-106">Il s’agit essentiellement du même comportement que celui des applications s’exécutant in-process, et qui sont gérées par le [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="504d6-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="504d6-107">Le schéma suivant illustre la relation entre IIS, le module ASP.NET Core et une application hébergée hors processus :</span><span class="sxs-lookup"><span data-stu-id="504d6-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Module ASP.NET Core dans le scénario d’hébergement out-of-process](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="504d6-109">Les requêtes arrivent du web au pilote HTTP.sys en mode noyau.</span><span class="sxs-lookup"><span data-stu-id="504d6-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="504d6-110">Le pilote achemine les requêtes vers IIS sur le port configuré du site Web.</span><span class="sxs-lookup"><span data-stu-id="504d6-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="504d6-111">Le port configuré est généralement 80 (HTTP) ou 443 (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="504d6-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="504d6-112">Le module transfère les demandes à Kestrel sur un port aléatoire pour l’application.</span><span class="sxs-lookup"><span data-stu-id="504d6-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="504d6-113">Le port aléatoire n’est pas 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="504d6-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="504d6-114">Le module ASP.NET Core spécifie le port via une variable d’environnement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="504d6-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="504d6-115">L' <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configure le serveur pour l’écoute `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="504d6-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="504d6-116">Des vérifications supplémentaires sont effectuées, et les requêtes qui ne proviennent pas du module sont rejetées.</span><span class="sxs-lookup"><span data-stu-id="504d6-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="504d6-117">Le module ne prend pas en charge le transfert HTTPs.</span><span class="sxs-lookup"><span data-stu-id="504d6-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="504d6-118">Les demandes sont transférées via HTTP, même si elles sont reçues par IIS sur HTTPs.</span><span class="sxs-lookup"><span data-stu-id="504d6-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="504d6-119">Une fois que Kestrel a extrait la demande du module, la demande est transmise dans le pipeline de l’intergiciel (middleware) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="504d6-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="504d6-120">Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application.</span><span class="sxs-lookup"><span data-stu-id="504d6-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="504d6-121">L’intergiciel (middleware) ajouté par l’intégration d’IIS met à jour le schéma, l’adresse IP distante et la base du chemin pour prendre en compte le transfert de la requête à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="504d6-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="504d6-122">La réponse de l’application est retransmise à IIS, qui la retransmet au client HTTP qui a initié la demande.</span><span class="sxs-lookup"><span data-stu-id="504d6-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="504d6-123">Pour obtenir de l’aide sur la configuration du module ASP.NET Core, consultez <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="504d6-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="504d6-124">Pour plus d’informations sur l’hébergement, consultez [Héberger dans ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="504d6-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="504d6-125">Configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="504d6-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="504d6-126">Activer les composants IISIntegration</span><span class="sxs-lookup"><span data-stu-id="504d6-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="504d6-127">Lors de la génération d’un hôte dans `CreateHostBuilder` ( `Program.cs` ), appelez <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> pour activer l’intégration d’IIS :</span><span class="sxs-lookup"><span data-stu-id="504d6-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="504d6-128">Pour plus d'informations sur le `CreateDefaultBuilder`, consultez <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="504d6-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="504d6-129">**Modèle d’hébergement out-of-process**</span><span class="sxs-lookup"><span data-stu-id="504d6-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="504d6-130">Pour configurer les options IIS, incluez une configuration de service pour <xref:Microsoft.AspNetCore.Builder.IISOptions> dans <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="504d6-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="504d6-131">L’exemple suivant empêche l’application d’être renseignée `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="504d6-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="504d6-132">Option</span><span class="sxs-lookup"><span data-stu-id="504d6-132">Option</span></span>                         | <span data-ttu-id="504d6-133">Default</span><span class="sxs-lookup"><span data-stu-id="504d6-133">Default</span></span> | <span data-ttu-id="504d6-134">Paramètre</span><span class="sxs-lookup"><span data-stu-id="504d6-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="504d6-135">Si la valeur est `true`, le [middleware d’intégration IIS](#enable-the-iisintegration-components) définit l’élément `HttpContext.User` authentifié par [Windows Authentication](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="504d6-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="504d6-136">Si `false`, l’intergiciel (middleware) fournit uniquement une identité pour `HttpContext.User` et répond aux questions explicitement posées par `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="504d6-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="504d6-137">L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne.</span><span class="sxs-lookup"><span data-stu-id="504d6-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="504d6-138">Pour plus d'informations, consultez la rubrique [Authentification Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="504d6-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="504d6-139">Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion.</span><span class="sxs-lookup"><span data-stu-id="504d6-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="504d6-140">Si la valeur est `true` et que l’en-tête de requête `MS-ASPNETCORE-CLIENTCERT` est présent, `HttpContext.Connection.ClientCertificate` est renseigné.</span><span class="sxs-lookup"><span data-stu-id="504d6-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="504d6-141">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="504d6-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="504d6-142">L' [intergiciel (middleware) d’intégration IIS](#enable-the-iisintegration-components) et le module ASP.net Core sont configurés pour transférer les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="504d6-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="504d6-143">Schéma (HTTP/HTTPs).</span><span class="sxs-lookup"><span data-stu-id="504d6-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="504d6-144">Adresse IP distante à l’origine de la demande.</span><span class="sxs-lookup"><span data-stu-id="504d6-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="504d6-145">L' [intergiciel (middleware) d’intégration IIS](#enable-the-iisintegration-components) configure l’intergiciel (middleware) des en-têtes transférés.</span><span class="sxs-lookup"><span data-stu-id="504d6-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="504d6-146">Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="504d6-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="504d6-147">Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="504d6-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="504d6-148">Modèle d’hébergement out-of-process</span><span class="sxs-lookup"><span data-stu-id="504d6-148">Out-of-process hosting model</span></span>

<span data-ttu-id="504d6-149">Pour configurer une application pour l’hébergement hors processus, affectez à la propriété la valeur `<AspNetCoreHostingModel>` `OutOfProcess` dans le fichier projet ( `.csproj` ) :</span><span class="sxs-lookup"><span data-stu-id="504d6-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="504d6-150">L’hébergement in-process est défini avec `InProcess` , qui est la valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="504d6-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="504d6-151">La valeur de ne respecte pas la `<AspNetCoreHostingModel>` casse, donc `inprocess` et `outofprocess` sont des valeurs valides.</span><span class="sxs-lookup"><span data-stu-id="504d6-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="504d6-152">Le serveur [Kestrel](xref:fundamentals/servers/kestrel) est utilisé à la place du serveur HTTP IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="504d6-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="504d6-153">Pour out-of-process, les [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) appels <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> à :</span><span class="sxs-lookup"><span data-stu-id="504d6-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="504d6-154">Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="504d6-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="504d6-155">Configurer l’hôte pour capturer des erreurs de démarrage.</span><span class="sxs-lookup"><span data-stu-id="504d6-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="504d6-156">Nom du processus</span><span class="sxs-lookup"><span data-stu-id="504d6-156">Process name</span></span>

<span data-ttu-id="504d6-157">`Process.GetCurrentProcess().ProcessName` signale `w3wp`/`iisexpress` (in-process) ou `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="504d6-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="504d6-158">De nombreux modules natifs, comme l’authentification Windows, restent actifs.</span><span class="sxs-lookup"><span data-stu-id="504d6-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="504d6-159">Pour obtenir plus d’informations sur les modules IIS actifs avec le module ASP.NET Core, consultez <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="504d6-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="504d6-160">Le module ASP.NET Core peut également  :</span><span class="sxs-lookup"><span data-stu-id="504d6-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="504d6-161">Définir des variables d’environnement pour le processus de travail.</span><span class="sxs-lookup"><span data-stu-id="504d6-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="504d6-162">Journaliser la sortie de stdout dans un stockage de fichiers pour résoudre les problèmes de démarrage.</span><span class="sxs-lookup"><span data-stu-id="504d6-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="504d6-163">Transférer des jetons d’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="504d6-163">Forward Windows authentication tokens.</span></span>
