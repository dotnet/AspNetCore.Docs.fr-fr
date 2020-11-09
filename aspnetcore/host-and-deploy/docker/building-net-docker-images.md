---
title: Images Docker pour ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser les images Docker .NET Core publiées à partir du Registre Docker. Extrayez des images et créez les vôtres.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 81daa1d4a996519f44e513b4f61c27cdf2b6ef5e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059804"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="49be6-104">Images Docker pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49be6-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="49be6-105">Ce tutoriel explique comment exécuter une application ASP.NET Core dans des conteneurs Docker.</span><span class="sxs-lookup"><span data-stu-id="49be6-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="49be6-106">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="49be6-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="49be6-107">Découvrir les images Docker Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="49be6-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="49be6-108">Télécharger un exemple d’application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49be6-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="49be6-109">Exécuter l’exemple d’application localement</span><span class="sxs-lookup"><span data-stu-id="49be6-109">Run the sample app locally</span></span>
> * <span data-ttu-id="49be6-110">Exécuter l’exemple d’application dans des conteneurs Linux</span><span class="sxs-lookup"><span data-stu-id="49be6-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="49be6-111">Exécuter l’exemple d’application dans des conteneurs Windows</span><span class="sxs-lookup"><span data-stu-id="49be6-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="49be6-112">Effectuer manuellement le build et le déploiement</span><span class="sxs-lookup"><span data-stu-id="49be6-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="49be6-113">Images Docker ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49be6-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="49be6-114">Dans ce tutoriel, vous allez télécharger un exemple d’application ASP.NET Core pour l’exécuter dans des conteneurs Docker.</span><span class="sxs-lookup"><span data-stu-id="49be6-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="49be6-115">L’exemple s’applique aux conteneurs Linux et Windows.</span><span class="sxs-lookup"><span data-stu-id="49be6-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="49be6-116">L’exemple de fichier Dockerfile utilise la [fonctionnalité de build en plusieurs étapes de Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) pour séparer le build et l’exécution dans différents conteneurs.</span><span class="sxs-lookup"><span data-stu-id="49be6-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="49be6-117">Les conteneurs de build et d’exécution sont créés à partir d’images fournies par Microsoft dans Docker Hub :</span><span class="sxs-lookup"><span data-stu-id="49be6-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="49be6-118">L’exemple utilise cette image pour créer l’application.</span><span class="sxs-lookup"><span data-stu-id="49be6-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="49be6-119">Elle contient le Kit SDK .NET Core, qui inclut les outils en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="49be6-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="49be6-120">Elle est optimisée pour le développement, le débogage et le test unitaire en local.</span><span class="sxs-lookup"><span data-stu-id="49be6-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="49be6-121">En raison des outils installés pour le développement et la compilation, elle est relativement volumineuse.</span><span class="sxs-lookup"><span data-stu-id="49be6-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="49be6-122">L’exemple utilise cette image pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="49be6-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="49be6-123">Elle contient le runtime ASP.NET Core et les bibliothèques. Elle est optimisée pour l’exécution d’applications en production.</span><span class="sxs-lookup"><span data-stu-id="49be6-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="49be6-124">Conçue pour la vitesse de déploiement et de démarrage de l’application, elle est relativement petite afin d’optimiser les performances réseau du Registre Docker vers l’hôte Docker.</span><span class="sxs-lookup"><span data-stu-id="49be6-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="49be6-125">Seuls les binaires et le contenu nécessaires pour exécuter une application sont copiés dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="49be6-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="49be6-126">Le contenu est prêt à s’exécuter, ce qui réduit le délai entre `Docker run` et le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="49be6-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="49be6-127">La compilation de code dynamique n’est pas nécessaire dans le modèle Docker.</span><span class="sxs-lookup"><span data-stu-id="49be6-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49be6-128">Prérequis</span><span class="sxs-lookup"><span data-stu-id="49be6-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="49be6-129">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="49be6-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="49be6-130">SDK .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="49be6-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="49be6-131">Client Docker 18.03 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="49be6-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="49be6-132">Distributions Linux</span><span class="sxs-lookup"><span data-stu-id="49be6-132">Linux distributions</span></span>
    * [<span data-ttu-id="49be6-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="49be6-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="49be6-134">Debian</span><span class="sxs-lookup"><span data-stu-id="49be6-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="49be6-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="49be6-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="49be6-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="49be6-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="49be6-137">macOS</span><span class="sxs-lookup"><span data-stu-id="49be6-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="49be6-138">Windows</span><span class="sxs-lookup"><span data-stu-id="49be6-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="49be6-139">Git</span><span class="sxs-lookup"><span data-stu-id="49be6-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="49be6-140">Télécharger l’exemple d’application</span><span class="sxs-lookup"><span data-stu-id="49be6-140">Download the sample app</span></span>

* <span data-ttu-id="49be6-141">Téléchargez l’exemple en clonant le [référentiel Docker .NET Core](https://github.com/dotnet/dotnet-docker) :</span><span class="sxs-lookup"><span data-stu-id="49be6-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="49be6-142">Exécutez l’application localement.</span><span class="sxs-lookup"><span data-stu-id="49be6-142">Run the app locally</span></span>

* <span data-ttu-id="49be6-143">Accédez au dossier de projet à l’adresse *dotnet-docker/samples/aspnetapp/aspnetapp* .</span><span class="sxs-lookup"><span data-stu-id="49be6-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp* .</span></span>

* <span data-ttu-id="49be6-144">Exécutez la commande suivante pour générer et exécuter l’application localement :</span><span class="sxs-lookup"><span data-stu-id="49be6-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="49be6-145">Accédez à `http://localhost:5000` dans un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="49be6-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="49be6-146">Appuyez sur Ctrl+C dans l’invite de commande pour arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="49be6-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="49be6-147">Exécuter dans un conteneur Linux</span><span class="sxs-lookup"><span data-stu-id="49be6-147">Run in a Linux container</span></span>

* <span data-ttu-id="49be6-148">Dans le client docker, [basculez vers les conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="49be6-148">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="49be6-149">Accédez au dossier Dockerfile à l’adresse *dotnet-docker/samples/aspnetapp* .</span><span class="sxs-lookup"><span data-stu-id="49be6-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp* .</span></span>

* <span data-ttu-id="49be6-150">Exécutez les commandes suivantes pour générer et exécuter l’exemple dans Docker :</span><span class="sxs-lookup"><span data-stu-id="49be6-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="49be6-151">Voici le rôle des arguments de la commande `build` :</span><span class="sxs-lookup"><span data-stu-id="49be6-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="49be6-152">nommer l’image aspnetapp ;</span><span class="sxs-lookup"><span data-stu-id="49be6-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="49be6-153">rechercher le fichier Dockerfile dans le dossier actif (le point final).</span><span class="sxs-lookup"><span data-stu-id="49be6-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="49be6-154">Voici le rôle des arguments de la commande run :</span><span class="sxs-lookup"><span data-stu-id="49be6-154">The run command arguments:</span></span>
  * <span data-ttu-id="49be6-155">allouer un pseudoterminal TTY et le laisser ouvert même s’il n’est pas attaché</span><span class="sxs-lookup"><span data-stu-id="49be6-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="49be6-156">(même effet que `--interactive --tty`) ;</span><span class="sxs-lookup"><span data-stu-id="49be6-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="49be6-157">supprimer automatiquement le conteneur lorsqu’il se ferme ;</span><span class="sxs-lookup"><span data-stu-id="49be6-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="49be6-158">mapper le port 5000 de l’ordinateur local avec le port 80 du conteneur ;</span><span class="sxs-lookup"><span data-stu-id="49be6-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="49be6-159">nommer le conteneur aspnetcore_sample ;</span><span class="sxs-lookup"><span data-stu-id="49be6-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="49be6-160">spécifier l’image aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="49be6-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="49be6-161">Accédez à `http://localhost:5000` dans un navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="49be6-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="49be6-162">Exécuter dans un conteneur Windows</span><span class="sxs-lookup"><span data-stu-id="49be6-162">Run in a Windows container</span></span>

* <span data-ttu-id="49be6-163">Dans le client docker, [basculez vers les conteneurs Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="49be6-163">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="49be6-164">Accédez au dossier de fichiers Dockerfile à l’adresse `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="49be6-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="49be6-165">Exécutez les commandes suivantes pour générer et exécuter l’exemple dans Docker :</span><span class="sxs-lookup"><span data-stu-id="49be6-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="49be6-166">Pour les conteneurs Windows, l’adresse IP du conteneur est nécessaire (accéder à `http://localhost:5000` ne fonctionnera pas) :</span><span class="sxs-lookup"><span data-stu-id="49be6-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="49be6-167">Ouvrez une autre invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="49be6-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="49be6-168">Exécutez `docker ps` pour voir les conteneurs en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="49be6-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="49be6-169">Vérifiez que le conteneur « aspnetcore_sample » en fait partie.</span><span class="sxs-lookup"><span data-stu-id="49be6-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="49be6-170">Exécutez `docker exec aspnetcore_sample ipconfig` pour afficher l’adresse IP du conteneur.</span><span class="sxs-lookup"><span data-stu-id="49be6-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="49be6-171">La sortie de la commande se présente ainsi :</span><span class="sxs-lookup"><span data-stu-id="49be6-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="49be6-172">Copiez l’adresse IPv4 du conteneur (par exemple, 172.29.245.43) et collez-la dans la barre d’adresse du navigateur pour tester l’application.</span><span class="sxs-lookup"><span data-stu-id="49be6-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="49be6-173">Effectuer manuellement le build et le déploiement</span><span class="sxs-lookup"><span data-stu-id="49be6-173">Build and deploy manually</span></span>

<span data-ttu-id="49be6-174">Dans certains scénarios, il peut être intéressant de déployer une application sur un conteneur en y copiant les fichiers d’application nécessaires à l’exécution.</span><span class="sxs-lookup"><span data-stu-id="49be6-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="49be6-175">Cette section montre comment effectuer un déploiement manuel.</span><span class="sxs-lookup"><span data-stu-id="49be6-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="49be6-176">Accédez au dossier de projet à l’adresse *dotnet-docker/samples/aspnetapp/aspnetapp* .</span><span class="sxs-lookup"><span data-stu-id="49be6-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp* .</span></span>

* <span data-ttu-id="49be6-177">Exécutez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="49be6-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="49be6-178">Voici le rôle des arguments de la commande :</span><span class="sxs-lookup"><span data-stu-id="49be6-178">The command arguments:</span></span>
  * <span data-ttu-id="49be6-179">Générez l’application en mode version finale (la valeur par défaut est le mode débogage).</span><span class="sxs-lookup"><span data-stu-id="49be6-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="49be6-180">Créer les fichiers dans le dossier *published* .</span><span class="sxs-lookup"><span data-stu-id="49be6-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="49be6-181">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="49be6-181">Run the application.</span></span>

  * <span data-ttu-id="49be6-182">Windows :</span><span class="sxs-lookup"><span data-stu-id="49be6-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="49be6-183">Linux :</span><span class="sxs-lookup"><span data-stu-id="49be6-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="49be6-184">Accédez à `http://localhost:5000` pour afficher la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="49be6-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="49be6-185">Pour utiliser l’application publiée manuellement dans un conteneur Docker, créez un nouveau fichier Dockerfile et utilisez la commande `docker build .` pour générer le conteneur.</span><span class="sxs-lookup"><span data-stu-id="49be6-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="49be6-186">Le fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="49be6-186">The Dockerfile</span></span>

<span data-ttu-id="49be6-187">Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="49be6-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="49be6-188">Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.</span><span class="sxs-lookup"><span data-stu-id="49be6-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="49be6-189">Le fichier Dockerfile</span><span class="sxs-lookup"><span data-stu-id="49be6-189">The Dockerfile</span></span>

<span data-ttu-id="49be6-190">Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="49be6-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="49be6-191">Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.</span><span class="sxs-lookup"><span data-stu-id="49be6-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="49be6-192">Comme indiqué dans le fichier dockerfile précédent, les `*.csproj` fichiers sont copiés et restaurés en tant que *couches* distinctes.</span><span class="sxs-lookup"><span data-stu-id="49be6-192">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers* .</span></span> <span data-ttu-id="49be6-193">Lorsque la `docker build` commande génère une image, elle utilise un cache intégré.</span><span class="sxs-lookup"><span data-stu-id="49be6-193">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="49be6-194">Si les `*.csproj` fichiers n’ont pas été modifiés depuis la dernière exécution de la `docker build` commande, la `dotnet restore` commande n’a pas besoin de s’exécuter à nouveau.</span><span class="sxs-lookup"><span data-stu-id="49be6-194">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="49be6-195">Au lieu de cela, le cache intégré pour la `dotnet restore` couche correspondante est réutilisé.</span><span class="sxs-lookup"><span data-stu-id="49be6-195">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="49be6-196">Pour plus d’informations, consultez [meilleures pratiques pour l’écriture de fichiers dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="49be6-196">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="49be6-197">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="49be6-197">Additional resources</span></span>

* [<span data-ttu-id="49be6-198">Commande docker build</span><span class="sxs-lookup"><span data-stu-id="49be6-198">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="49be6-199">Commande docker run</span><span class="sxs-lookup"><span data-stu-id="49be6-199">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="49be6-200">[Exemple Docker ASP.NET Core](https://github.com/dotnet/dotnet-docker) (utilisé dans ce tutoriel)</span><span class="sxs-lookup"><span data-stu-id="49be6-200">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="49be6-201">Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge</span><span class="sxs-lookup"><span data-stu-id="49be6-201">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="49be6-202">Utilisation des outils Docker dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49be6-202">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="49be6-203">Débogage avec Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49be6-203">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="49be6-204">GC avec l’ancrage et les petits conteneurs</span><span class="sxs-lookup"><span data-stu-id="49be6-204">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="49be6-205">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="49be6-205">Next steps</span></span>

<span data-ttu-id="49be6-206">Le référentiel Git qui contient l’exemple d’application comporte également une documentation.</span><span class="sxs-lookup"><span data-stu-id="49be6-206">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="49be6-207">Pour une vue d’ensemble des ressources disponibles dans le référentiel, voir le [fichier Lisez-moi](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="49be6-207">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="49be6-208">En particulier, découvrez comment implémenter le protocole HTTPS :</span><span class="sxs-lookup"><span data-stu-id="49be6-208">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="49be6-209">Développer des applications ASP.NET Core avec Docker sur le protocole HTTPS</span><span class="sxs-lookup"><span data-stu-id="49be6-209">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
