---
title: Images Docker pour ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser les images de l’ASP.NET Core d’ancrage publiées à partir du registre de l’ancrage. Tirez et créez vos propres images.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 32e721035df8bd9e746ad4db6bb2753c358f3dac
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413494"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="aa57f-104">Images Docker pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa57f-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="aa57f-105">Ce tutoriel explique comment exécuter une application ASP.NET Core dans des conteneurs Docker.</span><span class="sxs-lookup"><span data-stu-id="aa57f-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="aa57f-106">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="aa57f-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="aa57f-107">En savoir plus sur les images de l’arrimeur ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa57f-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="aa57f-108">Télécharger un exemple d’application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa57f-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="aa57f-109">Exécuter l’exemple d’application localement</span><span class="sxs-lookup"><span data-stu-id="aa57f-109">Run the sample app locally</span></span>
> * <span data-ttu-id="aa57f-110">Exécuter l’exemple d’application dans des conteneurs Linux</span><span class="sxs-lookup"><span data-stu-id="aa57f-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="aa57f-111">Exécuter l’exemple d’application dans des conteneurs Windows</span><span class="sxs-lookup"><span data-stu-id="aa57f-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="aa57f-112">Effectuer manuellement le build et le déploiement</span><span class="sxs-lookup"><span data-stu-id="aa57f-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="aa57f-113">Images Docker ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa57f-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="aa57f-114">Dans ce tutoriel, vous allez télécharger un exemple d’application ASP.NET Core pour l’exécuter dans des conteneurs Docker.</span><span class="sxs-lookup"><span data-stu-id="aa57f-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="aa57f-115">L’exemple s’applique aux conteneurs Linux et Windows.</span><span class="sxs-lookup"><span data-stu-id="aa57f-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="aa57f-116">L’exemple de fichier Dockerfile utilise la [fonctionnalité de build en plusieurs étapes de Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) pour séparer le build et l’exécution dans différents conteneurs.</span><span class="sxs-lookup"><span data-stu-id="aa57f-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="aa57f-117">Les conteneurs de build et d’exécution sont créés à partir d’images fournies par Microsoft dans Docker Hub :</span><span class="sxs-lookup"><span data-stu-id="aa57f-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="aa57f-118">L’exemple utilise cette image pour créer l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="aa57f-119">L’image contient le kit de développement logiciel (SDK) .NET, qui inclut les outils en ligne de commande (CLI).</span><span class="sxs-lookup"><span data-stu-id="aa57f-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="aa57f-120">Elle est optimisée pour le développement, le débogage et le test unitaire en local.</span><span class="sxs-lookup"><span data-stu-id="aa57f-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="aa57f-121">Les outils installés pour le développement et la compilation rendent l’image relativement volumineuse.</span><span class="sxs-lookup"><span data-stu-id="aa57f-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="aa57f-122">L’exemple utilise cette image pour créer l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="aa57f-123">Elle contient le Kit SDK .NET Core, qui inclut les outils en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="aa57f-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="aa57f-124">Elle est optimisée pour le développement, le débogage et le test unitaire en local.</span><span class="sxs-lookup"><span data-stu-id="aa57f-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="aa57f-125">Les outils installés pour le développement et la compilation rendent l’image relativement volumineuse.</span><span class="sxs-lookup"><span data-stu-id="aa57f-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

   <span data-ttu-id="aa57f-126">L’exemple utilise cette image pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="aa57f-127">Elle contient le runtime ASP.NET Core et les bibliothèques. Elle est optimisée pour l’exécution d’applications en production.</span><span class="sxs-lookup"><span data-stu-id="aa57f-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="aa57f-128">Conçue pour la vitesse de déploiement et de démarrage de l’application, elle est relativement petite afin d’optimiser les performances réseau du Registre Docker vers l’hôte Docker.</span><span class="sxs-lookup"><span data-stu-id="aa57f-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="aa57f-129">Seuls les binaires et le contenu nécessaires pour exécuter une application sont copiés dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="aa57f-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="aa57f-130">Le contenu est prêt à s’exécuter, ce qui réduit le délai entre `docker run` et le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="aa57f-131">La compilation de code dynamique n’est pas nécessaire dans le modèle Docker.</span><span class="sxs-lookup"><span data-stu-id="aa57f-131">Dynamic code compilation isn't needed in the Docker model.</span></span>
   
::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

   <span data-ttu-id="aa57f-132">L’exemple utilise cette image pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-132">The sample uses this image for running the app.</span></span> <span data-ttu-id="aa57f-133">Elle contient le runtime ASP.NET Core et les bibliothèques. Elle est optimisée pour l’exécution d’applications en production.</span><span class="sxs-lookup"><span data-stu-id="aa57f-133">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="aa57f-134">Conçue pour la vitesse de déploiement et de démarrage de l’application, elle est relativement petite afin d’optimiser les performances réseau du Registre Docker vers l’hôte Docker.</span><span class="sxs-lookup"><span data-stu-id="aa57f-134">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="aa57f-135">Seuls les binaires et le contenu nécessaires pour exécuter une application sont copiés dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="aa57f-135">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="aa57f-136">Le contenu est prêt à s’exécuter, ce qui réduit le délai entre `docker run` et le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-136">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="aa57f-137">La compilation de code dynamique n’est pas nécessaire dans le modèle Docker.</span><span class="sxs-lookup"><span data-stu-id="aa57f-137">Dynamic code compilation isn't needed in the Docker model.</span></span>
   
::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="aa57f-138">Prérequis</span><span class="sxs-lookup"><span data-stu-id="aa57f-138">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="aa57f-139">Kit de développement logiciel (SDK) .NET 5.0</span><span class="sxs-lookup"><span data-stu-id="aa57f-139">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="aa57f-140">Kit SDK .NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="aa57f-140">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="aa57f-141">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="aa57f-141">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="aa57f-142">Client Docker 18.03 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="aa57f-142">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="aa57f-143">Distributions Linux</span><span class="sxs-lookup"><span data-stu-id="aa57f-143">Linux distributions</span></span>
    * [<span data-ttu-id="aa57f-144">CentOS</span><span class="sxs-lookup"><span data-stu-id="aa57f-144">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="aa57f-145">Debian</span><span class="sxs-lookup"><span data-stu-id="aa57f-145">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="aa57f-146">Fedora</span><span class="sxs-lookup"><span data-stu-id="aa57f-146">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="aa57f-147">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="aa57f-147">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="aa57f-148">macOS</span><span class="sxs-lookup"><span data-stu-id="aa57f-148">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="aa57f-149">Windows</span><span class="sxs-lookup"><span data-stu-id="aa57f-149">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="aa57f-150">Git</span><span class="sxs-lookup"><span data-stu-id="aa57f-150">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="aa57f-151">Télécharger l’exemple d’application</span><span class="sxs-lookup"><span data-stu-id="aa57f-151">Download the sample app</span></span>

* <span data-ttu-id="aa57f-152">Téléchargez l’exemple en clonant le [référentiel de l’amarrage .net](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="aa57f-152">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="aa57f-153">Exécutez l’application localement.</span><span class="sxs-lookup"><span data-stu-id="aa57f-153">Run the app locally</span></span>

* <span data-ttu-id="aa57f-154">Accédez au dossier de projet à l’adresse *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="aa57f-154">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="aa57f-155">Exécutez la commande suivante pour générer et exécuter l’application localement :</span><span class="sxs-lookup"><span data-stu-id="aa57f-155">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="aa57f-156">Accédez à `http://localhost:5000` dans un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-156">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="aa57f-157">Appuyez sur Ctrl+C dans l’invite de commande pour arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-157">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="aa57f-158">Exécuter dans un conteneur Linux</span><span class="sxs-lookup"><span data-stu-id="aa57f-158">Run in a Linux container</span></span>

* <span data-ttu-id="aa57f-159">Dans le client docker, [basculez vers les conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="aa57f-159">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="aa57f-160">Accédez au dossier Dockerfile à l’adresse *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="aa57f-160">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="aa57f-161">Exécutez les commandes suivantes pour générer et exécuter l’exemple dans Docker :</span><span class="sxs-lookup"><span data-stu-id="aa57f-161">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="aa57f-162">Voici le rôle des arguments de la commande `build` :</span><span class="sxs-lookup"><span data-stu-id="aa57f-162">The `build` command arguments:</span></span>
  * <span data-ttu-id="aa57f-163">nommer l’image aspnetapp ;</span><span class="sxs-lookup"><span data-stu-id="aa57f-163">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="aa57f-164">rechercher le fichier Dockerfile dans le dossier actif (le point final).</span><span class="sxs-lookup"><span data-stu-id="aa57f-164">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="aa57f-165">Voici le rôle des arguments de la commande run :</span><span class="sxs-lookup"><span data-stu-id="aa57f-165">The run command arguments:</span></span>
  * <span data-ttu-id="aa57f-166">allouer un pseudoterminal TTY et le laisser ouvert même s’il n’est pas attaché</span><span class="sxs-lookup"><span data-stu-id="aa57f-166">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="aa57f-167">(même effet que `--interactive --tty`) ;</span><span class="sxs-lookup"><span data-stu-id="aa57f-167">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="aa57f-168">supprimer automatiquement le conteneur lorsqu’il se ferme ;</span><span class="sxs-lookup"><span data-stu-id="aa57f-168">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="aa57f-169">mapper le port 5000 de l’ordinateur local avec le port 80 du conteneur ;</span><span class="sxs-lookup"><span data-stu-id="aa57f-169">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="aa57f-170">nommer le conteneur aspnetcore_sample ;</span><span class="sxs-lookup"><span data-stu-id="aa57f-170">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="aa57f-171">spécifier l’image aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="aa57f-171">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="aa57f-172">Accédez à `http://localhost:5000` dans un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-172">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="aa57f-173">Exécuter dans un conteneur Windows</span><span class="sxs-lookup"><span data-stu-id="aa57f-173">Run in a Windows container</span></span>

* <span data-ttu-id="aa57f-174">Dans le client docker, [basculez vers les conteneurs Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="aa57f-174">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="aa57f-175">Accédez au dossier de fichiers Dockerfile à l’adresse `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="aa57f-175">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="aa57f-176">Exécutez les commandes suivantes pour générer et exécuter l’exemple dans Docker :</span><span class="sxs-lookup"><span data-stu-id="aa57f-176">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="aa57f-177">Pour les conteneurs Windows, l’adresse IP du conteneur est nécessaire (accéder à `http://localhost:5000` ne fonctionnera pas) :</span><span class="sxs-lookup"><span data-stu-id="aa57f-177">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="aa57f-178">Ouvrez une autre invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="aa57f-178">Open up another command prompt.</span></span>
  * <span data-ttu-id="aa57f-179">Exécutez `docker ps` pour voir les conteneurs en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="aa57f-179">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="aa57f-180">Vérifiez que le conteneur « aspnetcore_sample » en fait partie.</span><span class="sxs-lookup"><span data-stu-id="aa57f-180">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="aa57f-181">Exécutez `docker exec aspnetcore_sample ipconfig` pour afficher l’adresse IP du conteneur.</span><span class="sxs-lookup"><span data-stu-id="aa57f-181">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="aa57f-182">La sortie de la commande se présente ainsi :</span><span class="sxs-lookup"><span data-stu-id="aa57f-182">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="aa57f-183">Copiez l’adresse IPv4 du conteneur (par exemple, 172.29.245.43) et collez-la dans la barre d’adresse du navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-183">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="aa57f-184">Effectuer manuellement le build et le déploiement</span><span class="sxs-lookup"><span data-stu-id="aa57f-184">Build and deploy manually</span></span>

<span data-ttu-id="aa57f-185">Dans certains scénarios, vous souhaiterez peut-être déployer une application dans un conteneur en copiant les ressources qui sont nécessaires au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="aa57f-185">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="aa57f-186">Cette section montre comment effectuer un déploiement manuel.</span><span class="sxs-lookup"><span data-stu-id="aa57f-186">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="aa57f-187">Accédez au dossier de projet à l’adresse *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="aa57f-187">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="aa57f-188">Exécutez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="aa57f-188">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="aa57f-189">Voici le rôle des arguments de la commande :</span><span class="sxs-lookup"><span data-stu-id="aa57f-189">The command arguments:</span></span>
  * <span data-ttu-id="aa57f-190">Générez l’application en mode mise en sortie (la valeur par défaut est le mode débogage).</span><span class="sxs-lookup"><span data-stu-id="aa57f-190">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="aa57f-191">Créez les éléments multimédias dans le dossier *publié* .</span><span class="sxs-lookup"><span data-stu-id="aa57f-191">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="aa57f-192">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="aa57f-192">Run the app.</span></span>

  * <span data-ttu-id="aa57f-193">Windows :</span><span class="sxs-lookup"><span data-stu-id="aa57f-193">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="aa57f-194">Linux :</span><span class="sxs-lookup"><span data-stu-id="aa57f-194">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="aa57f-195">Accédez à `http://localhost:5000` pour afficher la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="aa57f-195">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="aa57f-196">Pour utiliser l’application publiée manuellement dans un conteneur d’ancrage, créez un nouveau *fichier dockerfile* et utilisez la `docker build .` commande pour générer une image.</span><span class="sxs-lookup"><span data-stu-id="aa57f-196">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build an image.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="aa57f-197">Pour afficher la nouvelle image, utilisez la `docker images` commande.</span><span class="sxs-lookup"><span data-stu-id="aa57f-197">To see the new image use the `docker images` command.</span></span>

### <a name="the-dockerfile"></a><span data-ttu-id="aa57f-198">Le fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="aa57f-198">The Dockerfile</span></span>

<span data-ttu-id="aa57f-199">Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="aa57f-199">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="aa57f-200">Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.</span><span class="sxs-lookup"><span data-stu-id="aa57f-200">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="aa57f-201">Dans les *fichier dockerfile* précédents, les `*.csproj` fichiers sont copiés et restaurés en tant que *couches* distinctes.</span><span class="sxs-lookup"><span data-stu-id="aa57f-201">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="aa57f-202">Lorsque la `docker build` commande génère une image, elle utilise un cache intégré.</span><span class="sxs-lookup"><span data-stu-id="aa57f-202">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="aa57f-203">Si les `*.csproj` fichiers n’ont pas été modifiés depuis la dernière exécution de la `docker build` commande, la `dotnet restore` commande n’a pas besoin de s’exécuter à nouveau.</span><span class="sxs-lookup"><span data-stu-id="aa57f-203">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="aa57f-204">Au lieu de cela, le cache intégré pour la `dotnet restore` couche correspondante est réutilisé.</span><span class="sxs-lookup"><span data-stu-id="aa57f-204">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="aa57f-205">Pour plus d’informations, consultez [meilleures pratiques pour l’écriture de fichiers dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="aa57f-205">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="aa57f-206">Le fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="aa57f-206">The Dockerfile</span></span>

<span data-ttu-id="aa57f-207">Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="aa57f-207">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="aa57f-208">Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.</span><span class="sxs-lookup"><span data-stu-id="aa57f-208">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="aa57f-209">Comme indiqué dans le fichier dockerfile précédent, les `*.csproj` fichiers sont copiés et restaurés en tant que *couches* distinctes.</span><span class="sxs-lookup"><span data-stu-id="aa57f-209">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="aa57f-210">Lorsque la `docker build` commande génère une image, elle utilise un cache intégré.</span><span class="sxs-lookup"><span data-stu-id="aa57f-210">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="aa57f-211">Si les `*.csproj` fichiers n’ont pas été modifiés depuis la dernière exécution de la `docker build` commande, la `dotnet restore` commande n’a pas besoin de s’exécuter à nouveau.</span><span class="sxs-lookup"><span data-stu-id="aa57f-211">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="aa57f-212">Au lieu de cela, le cache intégré pour la `dotnet restore` couche correspondante est réutilisé.</span><span class="sxs-lookup"><span data-stu-id="aa57f-212">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="aa57f-213">Pour plus d’informations, consultez [meilleures pratiques pour l’écriture de fichiers dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="aa57f-213">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="aa57f-214">Le fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="aa57f-214">The Dockerfile</span></span>

<span data-ttu-id="aa57f-215">Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="aa57f-215">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="aa57f-216">Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.</span><span class="sxs-lookup"><span data-stu-id="aa57f-216">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="aa57f-217">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="aa57f-217">Additional resources</span></span>

* [<span data-ttu-id="aa57f-218">Commande docker build</span><span class="sxs-lookup"><span data-stu-id="aa57f-218">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="aa57f-219">Commande d’exécution de l’ancreur</span><span class="sxs-lookup"><span data-stu-id="aa57f-219">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="aa57f-220">[Exemple Docker ASP.NET Core](https://github.com/dotnet/dotnet-docker) (utilisé dans ce tutoriel)</span><span class="sxs-lookup"><span data-stu-id="aa57f-220">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="aa57f-221">Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge</span><span class="sxs-lookup"><span data-stu-id="aa57f-221">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="aa57f-222">Utilisation des outils Docker dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa57f-222">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="aa57f-223">Débogage avec Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa57f-223">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="aa57f-224">GC avec l’ancrage et les petits conteneurs</span><span class="sxs-lookup"><span data-stu-id="aa57f-224">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="aa57f-225">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="aa57f-225">Next steps</span></span>

<span data-ttu-id="aa57f-226">Le référentiel Git qui contient l’exemple d’application comporte également une documentation.</span><span class="sxs-lookup"><span data-stu-id="aa57f-226">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="aa57f-227">Pour une vue d’ensemble des ressources disponibles dans le référentiel, voir le [fichier Lisez-moi](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="aa57f-227">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="aa57f-228">En particulier, découvrez comment implémenter le protocole HTTPS :</span><span class="sxs-lookup"><span data-stu-id="aa57f-228">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="aa57f-229">Développer des applications ASP.NET Core avec Docker sur le protocole HTTPS</span><span class="sxs-lookup"><span data-stu-id="aa57f-229">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)
