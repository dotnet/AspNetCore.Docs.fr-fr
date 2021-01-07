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
ms.openlocfilehash: 2cd21722082af88e536bc1001b606ee96e7cf59b
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972052"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="3fbf6-104">Images Docker pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fbf6-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="3fbf6-105">Ce tutoriel explique comment exécuter une application ASP.NET Core dans des conteneurs Docker.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="3fbf6-106">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="3fbf6-107">En savoir plus sur les images de l’arrimeur ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fbf6-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="3fbf6-108">Télécharger un exemple d’application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fbf6-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="3fbf6-109">Exécuter l’exemple d’application localement</span><span class="sxs-lookup"><span data-stu-id="3fbf6-109">Run the sample app locally</span></span>
> * <span data-ttu-id="3fbf6-110">Exécuter l’exemple d’application dans des conteneurs Linux</span><span class="sxs-lookup"><span data-stu-id="3fbf6-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="3fbf6-111">Exécuter l’exemple d’application dans des conteneurs Windows</span><span class="sxs-lookup"><span data-stu-id="3fbf6-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="3fbf6-112">Effectuer manuellement le build et le déploiement</span><span class="sxs-lookup"><span data-stu-id="3fbf6-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="3fbf6-113">Images Docker ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fbf6-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="3fbf6-114">Dans ce tutoriel, vous allez télécharger un exemple d’application ASP.NET Core pour l’exécuter dans des conteneurs Docker.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="3fbf6-115">L’exemple s’applique aux conteneurs Linux et Windows.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="3fbf6-116">L’exemple de fichier Dockerfile utilise la [fonctionnalité de build en plusieurs étapes de Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) pour séparer le build et l’exécution dans différents conteneurs.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="3fbf6-117">Les conteneurs de build et d’exécution sont créés à partir d’images fournies par Microsoft dans Docker Hub :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="3fbf6-118">L’exemple utilise cette image pour créer l’application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="3fbf6-119">L’image contient le kit de développement logiciel (SDK) .NET, qui inclut les outils en ligne de commande (CLI).</span><span class="sxs-lookup"><span data-stu-id="3fbf6-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="3fbf6-120">Elle est optimisée pour le développement, le débogage et le test unitaire en local.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="3fbf6-121">Les outils installés pour le développement et la compilation rendent l’image relativement volumineuse.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="3fbf6-122">L’exemple utilise cette image pour créer l’application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="3fbf6-123">Elle contient le Kit SDK .NET Core, qui inclut les outils en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="3fbf6-124">Elle est optimisée pour le développement, le débogage et le test unitaire en local.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="3fbf6-125">Les outils installés pour le développement et la compilation rendent l’image relativement volumineuse.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="3fbf6-126">L’exemple utilise cette image pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="3fbf6-127">Elle contient le runtime ASP.NET Core et les bibliothèques. Elle est optimisée pour l’exécution d’applications en production.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="3fbf6-128">Conçue pour la vitesse de déploiement et de démarrage de l’application, elle est relativement petite afin d’optimiser les performances réseau du Registre Docker vers l’hôte Docker.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="3fbf6-129">Seuls les binaires et le contenu nécessaires pour exécuter une application sont copiés dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="3fbf6-130">Le contenu est prêt à s’exécuter, ce qui réduit le délai entre `docker run` et le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="3fbf6-131">La compilation de code dynamique n’est pas nécessaire dans le modèle Docker.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3fbf6-132">Prérequis</span><span class="sxs-lookup"><span data-stu-id="3fbf6-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="3fbf6-133">Kit de développement logiciel (SDK) .NET 5.0</span><span class="sxs-lookup"><span data-stu-id="3fbf6-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="3fbf6-134">Kit SDK .NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="3fbf6-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="3fbf6-135">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="3fbf6-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="3fbf6-136">Client Docker 18.03 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="3fbf6-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="3fbf6-137">Distributions Linux</span><span class="sxs-lookup"><span data-stu-id="3fbf6-137">Linux distributions</span></span>
    * [<span data-ttu-id="3fbf6-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="3fbf6-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="3fbf6-139">Debian</span><span class="sxs-lookup"><span data-stu-id="3fbf6-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="3fbf6-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="3fbf6-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="3fbf6-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="3fbf6-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="3fbf6-142">macOS</span><span class="sxs-lookup"><span data-stu-id="3fbf6-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="3fbf6-143">Windows</span><span class="sxs-lookup"><span data-stu-id="3fbf6-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="3fbf6-144">Git</span><span class="sxs-lookup"><span data-stu-id="3fbf6-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="3fbf6-145">Télécharger l’exemple d’application</span><span class="sxs-lookup"><span data-stu-id="3fbf6-145">Download the sample app</span></span>

* <span data-ttu-id="3fbf6-146">Téléchargez l’exemple en clonant le [référentiel de l’amarrage .net](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="3fbf6-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="3fbf6-147">Exécutez l’application localement.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-147">Run the app locally</span></span>

* <span data-ttu-id="3fbf6-148">Accédez au dossier de projet à l’adresse *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="3fbf6-149">Exécutez la commande suivante pour générer et exécuter l’application localement :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="3fbf6-150">Accédez à `http://localhost:5000` dans un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="3fbf6-151">Appuyez sur Ctrl+C dans l’invite de commande pour arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="3fbf6-152">Exécuter dans un conteneur Linux</span><span class="sxs-lookup"><span data-stu-id="3fbf6-152">Run in a Linux container</span></span>

* <span data-ttu-id="3fbf6-153">Dans le client docker, [basculez vers les conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="3fbf6-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="3fbf6-154">Accédez au dossier Dockerfile à l’adresse *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="3fbf6-155">Exécutez les commandes suivantes pour générer et exécuter l’exemple dans Docker :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="3fbf6-156">Voici le rôle des arguments de la commande `build` :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="3fbf6-157">nommer l’image aspnetapp ;</span><span class="sxs-lookup"><span data-stu-id="3fbf6-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="3fbf6-158">rechercher le fichier Dockerfile dans le dossier actif (le point final).</span><span class="sxs-lookup"><span data-stu-id="3fbf6-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="3fbf6-159">Voici le rôle des arguments de la commande run :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-159">The run command arguments:</span></span>
  * <span data-ttu-id="3fbf6-160">allouer un pseudoterminal TTY et le laisser ouvert même s’il n’est pas attaché</span><span class="sxs-lookup"><span data-stu-id="3fbf6-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="3fbf6-161">(même effet que `--interactive --tty`) ;</span><span class="sxs-lookup"><span data-stu-id="3fbf6-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="3fbf6-162">supprimer automatiquement le conteneur lorsqu’il se ferme ;</span><span class="sxs-lookup"><span data-stu-id="3fbf6-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="3fbf6-163">mapper le port 5000 de l’ordinateur local avec le port 80 du conteneur ;</span><span class="sxs-lookup"><span data-stu-id="3fbf6-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="3fbf6-164">nommer le conteneur aspnetcore_sample ;</span><span class="sxs-lookup"><span data-stu-id="3fbf6-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="3fbf6-165">spécifier l’image aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="3fbf6-166">Accédez à `http://localhost:5000` dans un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="3fbf6-167">Exécuter dans un conteneur Windows</span><span class="sxs-lookup"><span data-stu-id="3fbf6-167">Run in a Windows container</span></span>

* <span data-ttu-id="3fbf6-168">Dans le client docker, [basculez vers les conteneurs Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="3fbf6-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="3fbf6-169">Accédez au dossier de fichiers Dockerfile à l’adresse `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="3fbf6-170">Exécutez les commandes suivantes pour générer et exécuter l’exemple dans Docker :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="3fbf6-171">Pour les conteneurs Windows, l’adresse IP du conteneur est nécessaire (accéder à `http://localhost:5000` ne fonctionnera pas) :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="3fbf6-172">Ouvrez une autre invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="3fbf6-173">Exécutez `docker ps` pour voir les conteneurs en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="3fbf6-174">Vérifiez que le conteneur « aspnetcore_sample » en fait partie.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="3fbf6-175">Exécutez `docker exec aspnetcore_sample ipconfig` pour afficher l’adresse IP du conteneur.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="3fbf6-176">La sortie de la commande se présente ainsi :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="3fbf6-177">Copiez l’adresse IPv4 du conteneur (par exemple, 172.29.245.43) et collez-la dans la barre d’adresse du navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="3fbf6-178">Effectuer manuellement le build et le déploiement</span><span class="sxs-lookup"><span data-stu-id="3fbf6-178">Build and deploy manually</span></span>

<span data-ttu-id="3fbf6-179">Dans certains scénarios, vous souhaiterez peut-être déployer une application dans un conteneur en copiant les ressources qui sont nécessaires au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="3fbf6-180">Cette section montre comment effectuer un déploiement manuel.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="3fbf6-181">Accédez au dossier de projet à l’adresse *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="3fbf6-182">Exécutez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="3fbf6-183">Voici le rôle des arguments de la commande :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-183">The command arguments:</span></span>
  * <span data-ttu-id="3fbf6-184">Générez l’application en mode mise en sortie (la valeur par défaut est le mode débogage).</span><span class="sxs-lookup"><span data-stu-id="3fbf6-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="3fbf6-185">Créez les éléments multimédias dans le dossier *publié* .</span><span class="sxs-lookup"><span data-stu-id="3fbf6-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="3fbf6-186">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-186">Run the app.</span></span>

  * <span data-ttu-id="3fbf6-187">Windows :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="3fbf6-188">Linux :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="3fbf6-189">Accédez à `http://localhost:5000` pour afficher la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="3fbf6-190">Pour utiliser l’application publiée manuellement dans un conteneur d’ancrage, créez un nouveau *fichier dockerfile* et utilisez la `docker build .` commande pour générer le conteneur.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="3fbf6-191">Le fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="3fbf6-191">The Dockerfile</span></span>

<span data-ttu-id="3fbf6-192">Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="3fbf6-193">Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="3fbf6-194">Dans les *fichier dockerfile* précédents, les `*.csproj` fichiers sont copiés et restaurés en tant que *couches* distinctes.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="3fbf6-195">Lorsque la `docker build` commande génère une image, elle utilise un cache intégré.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="3fbf6-196">Si les `*.csproj` fichiers n’ont pas été modifiés depuis la dernière exécution de la `docker build` commande, la `dotnet restore` commande n’a pas besoin de s’exécuter à nouveau.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="3fbf6-197">Au lieu de cela, le cache intégré pour la `dotnet restore` couche correspondante est réutilisé.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="3fbf6-198">Pour plus d’informations, consultez [meilleures pratiques pour l’écriture de fichiers dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="3fbf6-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="3fbf6-199">Le fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="3fbf6-199">The Dockerfile</span></span>

<span data-ttu-id="3fbf6-200">Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="3fbf6-201">Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="3fbf6-202">Comme indiqué dans le fichier dockerfile précédent, les `*.csproj` fichiers sont copiés et restaurés en tant que *couches* distinctes.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="3fbf6-203">Lorsque la `docker build` commande génère une image, elle utilise un cache intégré.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="3fbf6-204">Si les `*.csproj` fichiers n’ont pas été modifiés depuis la dernière exécution de la `docker build` commande, la `dotnet restore` commande n’a pas besoin de s’exécuter à nouveau.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="3fbf6-205">Au lieu de cela, le cache intégré pour la `dotnet restore` couche correspondante est réutilisé.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="3fbf6-206">Pour plus d’informations, consultez [meilleures pratiques pour l’écriture de fichiers dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="3fbf6-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="3fbf6-207">Le fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="3fbf6-207">The Dockerfile</span></span>

<span data-ttu-id="3fbf6-208">Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="3fbf6-209">Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

## <a name="additional-resources"></a><span data-ttu-id="3fbf6-210">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3fbf6-210">Additional resources</span></span>

* [<span data-ttu-id="3fbf6-211">Commande docker build</span><span class="sxs-lookup"><span data-stu-id="3fbf6-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="3fbf6-212">Commande d’exécution de l’ancreur</span><span class="sxs-lookup"><span data-stu-id="3fbf6-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="3fbf6-213">[Exemple Docker ASP.NET Core](https://github.com/dotnet/dotnet-docker) (utilisé dans ce tutoriel)</span><span class="sxs-lookup"><span data-stu-id="3fbf6-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="3fbf6-214">Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge</span><span class="sxs-lookup"><span data-stu-id="3fbf6-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="3fbf6-215">Utilisation des outils Docker dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fbf6-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="3fbf6-216">Débogage avec Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fbf6-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="3fbf6-217">GC avec l’ancrage et les petits conteneurs</span><span class="sxs-lookup"><span data-stu-id="3fbf6-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="3fbf6-218">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="3fbf6-218">Next steps</span></span>

<span data-ttu-id="3fbf6-219">Le référentiel Git qui contient l’exemple d’application comporte également une documentation.</span><span class="sxs-lookup"><span data-stu-id="3fbf6-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="3fbf6-220">Pour une vue d’ensemble des ressources disponibles dans le référentiel, voir le [fichier Lisez-moi](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="3fbf6-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="3fbf6-221">En particulier, découvrez comment implémenter le protocole HTTPS :</span><span class="sxs-lookup"><span data-stu-id="3fbf6-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3fbf6-222">Développer des applications ASP.NET Core avec Docker sur le protocole HTTPS</span><span class="sxs-lookup"><span data-stu-id="3fbf6-222">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
