---
title: Héberger et déployer ASP.NET Core
author: rick-anderson
description: Découvrez comment configurer des environnements d’hébergement et déployer des applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/index
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252966"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="1c2c0-103">Héberger et déployer ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c2c0-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1c2c0-104">En général, pour déployer une application ASP.NET Core sur un environnement d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="1c2c0-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="1c2c0-105">Déployer l’application publiée dans un dossier sur le serveur d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="1c2c0-106">Configurer un gestionnaire de processus qui démarre l’application à l’arrivée des requêtes et redémarre l’application en cas de blocage ou quand le serveur redémarre.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="1c2c0-107">Pour la configuration d’un proxy inverse, configurez un proxy inverse pour transférer les requêtes à l’application.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="1c2c0-108">Publier dans un dossier</span><span class="sxs-lookup"><span data-stu-id="1c2c0-108">Publish to a folder</span></span>

<span data-ttu-id="1c2c0-109">La commande [dotnet publish](/dotnet/core/tools/dotnet-publish) compile le code d’application et copie les fichiers nécessaires pour exécuter l’application dans un dossier *publish*.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="1c2c0-110">Dans le cadre d’un déploiement à partir de Visual Studio, l’étape `dotnet publish` est effectuée automatiquement avant que les fichiers ne soient copiés vers la destination du déploiement.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="1c2c0-111">Publier les fichiers de paramètres</span><span class="sxs-lookup"><span data-stu-id="1c2c0-111">Publish settings files</span></span>

<span data-ttu-id="1c2c0-112">`*.json` les fichiers sont publiés par défaut.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-112">`*.json` files are published by default.</span></span> <span data-ttu-id="1c2c0-113">Pour publier d’autres fichiers de paramètres, spécifiez-les dans un [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) élément du fichier projet.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="1c2c0-114">L’exemple suivant publie des fichiers XML :</span><span class="sxs-lookup"><span data-stu-id="1c2c0-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="1c2c0-115">Contenu du dossier</span><span class="sxs-lookup"><span data-stu-id="1c2c0-115">Folder contents</span></span>

<span data-ttu-id="1c2c0-116">Le dossier *publish* contient un ou plusieurs fichiers d’assembly d’application, ses dépendances et éventuellement le runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="1c2c0-117">Une application .NET Core peut être publiée en tant que *déploiement autonome* ou *déploiement dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="1c2c0-118">Si l’application est autonome, les fichiers d’assembly qui contiennent le runtime .NET sont inclus dans le dossier *publish*.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="1c2c0-119">Si l’application dépend du framework, les fichiers du runtime .NET ne sont pas inclus, car l’application a une référence à une version de .NET installée sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="1c2c0-120">Le modèle de déploiement par défaut est « dépendante du framework ».</span><span class="sxs-lookup"><span data-stu-id="1c2c0-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="1c2c0-121">Pour plus d’informations, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="1c2c0-122">En plus des fichiers *.exe* et *.dll*, le dossier *publish* d’une application ASP.NET Core contient généralement des fichiers de configuration, des ressources statiques et des vues MVC.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="1c2c0-123">Pour plus d'informations, consultez <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="1c2c0-124">Configurer un gestionnaire de processus</span><span class="sxs-lookup"><span data-stu-id="1c2c0-124">Set up a process manager</span></span>

<span data-ttu-id="1c2c0-125">Une application ASP.NET Core est une application console qui doit être démarrée quand un serveur démarre, et redémarrée si elle se bloque.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="1c2c0-126">Pour automatiser le démarrage et le redémarrage, un gestionnaire de processus est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="1c2c0-127">Les gestionnaires de processus les plus courants pour ASP.NET Core sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="1c2c0-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="1c2c0-128">Linux</span><span class="sxs-lookup"><span data-stu-id="1c2c0-128">Linux</span></span>
  * [<span data-ttu-id="1c2c0-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="1c2c0-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="1c2c0-130">Apache</span><span class="sxs-lookup"><span data-stu-id="1c2c0-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="1c2c0-131">Windows</span><span class="sxs-lookup"><span data-stu-id="1c2c0-131">Windows</span></span>
  * [<span data-ttu-id="1c2c0-132">IIS</span><span class="sxs-lookup"><span data-stu-id="1c2c0-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="1c2c0-133">Service Windows</span><span class="sxs-lookup"><span data-stu-id="1c2c0-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="1c2c0-134">Configurer un proxy inverse</span><span class="sxs-lookup"><span data-stu-id="1c2c0-134">Set up a reverse proxy</span></span>

<span data-ttu-id="1c2c0-135">Si l’application utilise le serveur [Kestrel](xref:fundamentals/servers/kestrel), vous pouvez utiliser [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) ou [IIS](xref:host-and-deploy/iis/index) comme serveur proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="1c2c0-136">Un serveur proxy inverse reçoit les requêtes HTTP en provenance d’Internet et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="1c2c0-137">Les deux configurations, &mdash;avec ou sans serveur proxy inverse&mdash;, sont des configurations d’hébergement prises en charge.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="1c2c0-138">Pour plus d’informations, consultez [Quand utiliser Kestrel avec un proxy inverse ?](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
<span data-ttu-id="1c2c0-139">Les deux configurations, &mdash;avec ou sans serveur proxy inverse&mdash;, sont des configurations d’hébergement prises en charge.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="1c2c0-140">Pour plus d’informations, consultez [Quand utiliser Kestrel avec un proxy inverse ?](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-140">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1c2c0-141">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="1c2c0-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1c2c0-142">Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-142">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="1c2c0-143">Sans configuration supplémentaire, une application peut ne pas avoir accès au schéma (HTTP/HTTPS) et à l’adresse IP distante d’où provient une requête.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-143">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="1c2c0-144">Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-144">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="1c2c0-145">Utiliser Visual Studio et MSBuild pour automatiser les déploiements</span><span class="sxs-lookup"><span data-stu-id="1c2c0-145">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="1c2c0-146">Le déploiement nécessite souvent des tâches supplémentaires en plus de la copie de la sortie de [dotnet publish](/dotnet/core/tools/dotnet-publish) vers un serveur.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-146">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="1c2c0-147">Par exemple, des fichiers supplémentaires peuvent être requis ou exclus du dossier *publish*.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-147">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="1c2c0-148">Visual Studio utilise [MSBuild](/visualstudio/msbuild/msbuild) pour le déploiement Web et MSBuild peut être personnalisé pour effectuer de nombreuses autres tâches pendant le déploiement.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-148">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="1c2c0-149">Pour plus d’informations, consultez <xref:host-and-deploy/visual-studio-publish-profiles> et l’ouvrage [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Utilisation de MSBuild et Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-149">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="1c2c0-150">Les applications peuvent être déployées directement à partir de Visual Studio sur Azure App Service à l’aide de la [fonctionnalité de publication web](xref:tutorials/publish-to-azure-webapp-using-vs) ou de la [prise en charge intégrée de Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-150">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="1c2c0-151">Azure DevOps Services prend en charge le [déploiement continu sur Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-151">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="1c2c0-152">Pour plus d’informations, consultez [DevOps avec ASP.NET Core et Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-152">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="1c2c0-153">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="1c2c0-153">Publish to Azure</span></span>

<span data-ttu-id="1c2c0-154">Consultez <xref:tutorials/publish-to-azure-webapp-using-vs> pour obtenir des instructions sur la façon de publier une application sur Azure à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-154">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="1c2c0-155">Un exemple supplémentaire est fourni par [Créer une application web ASP.NET Core dans Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-155">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="1c2c0-156">Publier avec MSDeploy sur Windows</span><span class="sxs-lookup"><span data-stu-id="1c2c0-156">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="1c2c0-157">Consultez <xref:host-and-deploy/visual-studio-publish-profiles> pour obtenir des instructions sur la publication d’une application avec un profil de publication Visual Studio, notamment à partir d’une invite de commandes Windows à l’aide de la commande [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-157">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="1c2c0-158">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="1c2c0-158">Internet Information Services (IIS)</span></span>

<span data-ttu-id="1c2c0-159">Pour les déploiements vers Internet Information Services (IIS) avec la configuration fournie par le fichier *web.config* , consultez les articles sous <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="1c2c0-159">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="1c2c0-160">Héberger dans une batterie de serveurs web</span><span class="sxs-lookup"><span data-stu-id="1c2c0-160">Host in a web farm</span></span>

<span data-ttu-id="1c2c0-161">Pour plus d’informations sur la configuration pour héberger des applications ASP.NET Core dans un environnement de batterie de serveurs web (par exemple, le déploiement de plusieurs instances de votre application pour la scalabilité), consultez <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-161">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="1c2c0-162">Héberger sur l’ancrage</span><span class="sxs-lookup"><span data-stu-id="1c2c0-162">Host on Docker</span></span>

<span data-ttu-id="1c2c0-163">Pour plus d'informations, consultez <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-163">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="1c2c0-164">Effectuer des contrôles d’intégrité</span><span class="sxs-lookup"><span data-stu-id="1c2c0-164">Perform health checks</span></span>

<span data-ttu-id="1c2c0-165">Utilisez Health Check Middleware pour effectuer des contrôles d’intégrité sur une application et ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-165">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="1c2c0-166">Pour plus d'informations, consultez <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-166">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c2c0-167">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1c2c0-167">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="1c2c0-168">Hébergement ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1c2c0-168">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1c2c0-169">En général, pour déployer une application ASP.NET Core sur un environnement d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="1c2c0-169">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="1c2c0-170">Déployer l’application publiée dans un dossier sur le serveur d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-170">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="1c2c0-171">Configurer un gestionnaire de processus qui démarre l’application à l’arrivée des requêtes et redémarre l’application en cas de blocage ou quand le serveur redémarre.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-171">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="1c2c0-172">Pour la configuration d’un proxy inverse, configurez un proxy inverse pour transférer les requêtes à l’application.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-172">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="1c2c0-173">Publier dans un dossier</span><span class="sxs-lookup"><span data-stu-id="1c2c0-173">Publish to a folder</span></span>

<span data-ttu-id="1c2c0-174">La commande [dotnet publish](/dotnet/core/tools/dotnet-publish) compile le code d’application et copie les fichiers nécessaires pour exécuter l’application dans un dossier *publish*.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-174">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="1c2c0-175">Dans le cadre d’un déploiement à partir de Visual Studio, l’étape `dotnet publish` est effectuée automatiquement avant que les fichiers ne soient copiés vers la destination du déploiement.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-175">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="1c2c0-176">Contenu du dossier</span><span class="sxs-lookup"><span data-stu-id="1c2c0-176">Folder contents</span></span>

<span data-ttu-id="1c2c0-177">Le dossier *publish* contient un ou plusieurs fichiers d’assembly d’application, ses dépendances et éventuellement le runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-177">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="1c2c0-178">Une application .NET Core peut être publiée en tant que *déploiement autonome* ou *déploiement dépendant du framework*.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-178">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="1c2c0-179">Si l’application est autonome, les fichiers d’assembly qui contiennent le runtime .NET sont inclus dans le dossier *publish*.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-179">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="1c2c0-180">Si l’application dépend du framework, les fichiers du runtime .NET ne sont pas inclus, car l’application a une référence à une version de .NET installée sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-180">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="1c2c0-181">Le modèle de déploiement par défaut est « dépendante du framework ».</span><span class="sxs-lookup"><span data-stu-id="1c2c0-181">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="1c2c0-182">Pour plus d’informations, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-182">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="1c2c0-183">En plus des fichiers *.exe* et *.dll*, le dossier *publish* d’une application ASP.NET Core contient généralement des fichiers de configuration, des ressources statiques et des vues MVC.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-183">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="1c2c0-184">Pour plus d'informations, consultez <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-184">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="1c2c0-185">Configurer un gestionnaire de processus</span><span class="sxs-lookup"><span data-stu-id="1c2c0-185">Set up a process manager</span></span>

<span data-ttu-id="1c2c0-186">Une application ASP.NET Core est une application console qui doit être démarrée quand un serveur démarre, et redémarrée si elle se bloque.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-186">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="1c2c0-187">Pour automatiser le démarrage et le redémarrage, un gestionnaire de processus est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-187">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="1c2c0-188">Les gestionnaires de processus les plus courants pour ASP.NET Core sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="1c2c0-188">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="1c2c0-189">Linux</span><span class="sxs-lookup"><span data-stu-id="1c2c0-189">Linux</span></span>
  * [<span data-ttu-id="1c2c0-190">Nginx</span><span class="sxs-lookup"><span data-stu-id="1c2c0-190">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="1c2c0-191">Apache</span><span class="sxs-lookup"><span data-stu-id="1c2c0-191">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="1c2c0-192">Windows</span><span class="sxs-lookup"><span data-stu-id="1c2c0-192">Windows</span></span>
  * [<span data-ttu-id="1c2c0-193">IIS</span><span class="sxs-lookup"><span data-stu-id="1c2c0-193">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="1c2c0-194">Service Windows</span><span class="sxs-lookup"><span data-stu-id="1c2c0-194">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="1c2c0-195">Configurer un proxy inverse</span><span class="sxs-lookup"><span data-stu-id="1c2c0-195">Set up a reverse proxy</span></span>

<span data-ttu-id="1c2c0-196">Si l’application utilise le serveur [Kestrel](xref:fundamentals/servers/kestrel), vous pouvez utiliser [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) ou [IIS](xref:host-and-deploy/iis/index) comme serveur proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-196">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="1c2c0-197">Un serveur proxy inverse reçoit les requêtes HTTP en provenance d’Internet et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="1c2c0-198">Les deux configurations, &mdash;avec ou sans serveur proxy inverse&mdash;, sont des configurations d’hébergement prises en charge.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-198">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="1c2c0-199">Pour plus d’informations, consultez [Quand utiliser Kestrel avec un proxy inverse ?](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-199">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1c2c0-200">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="1c2c0-200">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1c2c0-201">Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-201">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="1c2c0-202">Sans configuration supplémentaire, une application peut ne pas avoir accès au schéma (HTTP/HTTPS) et à l’adresse IP distante d’où provient une requête.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-202">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="1c2c0-203">Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-203">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="1c2c0-204">Utiliser Visual Studio et MSBuild pour automatiser les déploiements</span><span class="sxs-lookup"><span data-stu-id="1c2c0-204">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="1c2c0-205">Le déploiement nécessite souvent des tâches supplémentaires en plus de la copie de la sortie de [dotnet publish](/dotnet/core/tools/dotnet-publish) vers un serveur.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-205">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="1c2c0-206">Par exemple, des fichiers supplémentaires peuvent être requis ou exclus du dossier *publish*.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-206">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="1c2c0-207">Visual Studio utilise MSBuild pour le déploiement web, et MSBuild peut être personnalisé pour effectuer de nombreuses autres tâches pendant le déploiement.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-207">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="1c2c0-208">Pour plus d’informations, consultez <xref:host-and-deploy/visual-studio-publish-profiles> et l’ouvrage [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Utilisation de MSBuild et Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-208">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="1c2c0-209">Les applications peuvent être déployées directement à partir de Visual Studio sur Azure App Service à l’aide de la [fonctionnalité de publication web](xref:tutorials/publish-to-azure-webapp-using-vs) ou de la [prise en charge intégrée de Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-209">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="1c2c0-210">Azure DevOps Services prend en charge le [déploiement continu sur Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-210">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="1c2c0-211">Pour plus d’informations, consultez [DevOps avec ASP.NET Core et Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-211">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="1c2c0-212">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="1c2c0-212">Publish to Azure</span></span>

<span data-ttu-id="1c2c0-213">Consultez <xref:tutorials/publish-to-azure-webapp-using-vs> pour obtenir des instructions sur la façon de publier une application sur Azure à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-213">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="1c2c0-214">Un exemple supplémentaire est fourni par [Créer une application web ASP.NET Core dans Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-214">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="1c2c0-215">Publier avec MSDeploy sur Windows</span><span class="sxs-lookup"><span data-stu-id="1c2c0-215">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="1c2c0-216">Consultez <xref:host-and-deploy/visual-studio-publish-profiles> pour obtenir des instructions sur la publication d’une application avec un profil de publication Visual Studio, notamment à partir d’une invite de commandes Windows à l’aide de la commande [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="1c2c0-216">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="1c2c0-217">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="1c2c0-217">Internet Information Services (IIS)</span></span>

<span data-ttu-id="1c2c0-218">Pour les déploiements vers Internet Information Services (IIS) avec la configuration fournie par le fichier *web.config* , consultez les articles sous <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="1c2c0-218">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="1c2c0-219">Héberger dans une batterie de serveurs web</span><span class="sxs-lookup"><span data-stu-id="1c2c0-219">Host in a web farm</span></span>

<span data-ttu-id="1c2c0-220">Pour plus d’informations sur la configuration pour héberger des applications ASP.NET Core dans un environnement de batterie de serveurs web (par exemple, le déploiement de plusieurs instances de votre application pour la scalabilité), consultez <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-220">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="1c2c0-221">Héberger sur l’ancrage</span><span class="sxs-lookup"><span data-stu-id="1c2c0-221">Host on Docker</span></span>

<span data-ttu-id="1c2c0-222">Pour plus d'informations, consultez <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="1c2c0-222">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c2c0-223">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1c2c0-223">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="1c2c0-224">Hébergement ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1c2c0-224">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
