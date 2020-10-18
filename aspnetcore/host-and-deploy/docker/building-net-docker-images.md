---
title: Images Docker pour ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser les images Docker .NET Core publiées à partir du Registre Docker. Extrayez des images et créez les vôtres.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
no-loc:
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
ms.openlocfilehash: 6f94323873937d1428d6cfa1b0ef15e44611fd47
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92153639"
---
# <a name="docker-images-for-aspnet-core"></a>Images Docker pour ASP.NET Core

Ce tutoriel explique comment exécuter une application ASP.NET Core dans des conteneurs Docker.

Dans ce tutoriel, vous allez :
> [!div class="checklist"]
> * Découvrir les images Docker Microsoft .NET Core
> * Télécharger un exemple d’application ASP.NET Core
> * Exécuter l’exemple d’application localement
> * Exécuter l’exemple d’application dans des conteneurs Linux
> * Exécuter l’exemple d’application dans des conteneurs Windows
> * Effectuer manuellement le build et le déploiement

## <a name="aspnet-core-docker-images"></a>Images Docker ASP.NET Core

Dans ce tutoriel, vous allez télécharger un exemple d’application ASP.NET Core pour l’exécuter dans des conteneurs Docker. L’exemple s’applique aux conteneurs Linux et Windows.

L’exemple de fichier Dockerfile utilise la [fonctionnalité de build en plusieurs étapes de Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) pour séparer le build et l’exécution dans différents conteneurs. Les conteneurs de build et d’exécution sont créés à partir d’images fournies par Microsoft dans Docker Hub :

* `dotnet/core/sdk`

  L’exemple utilise cette image pour créer l’application. Elle contient le Kit SDK .NET Core, qui inclut les outils en ligne de commande. Elle est optimisée pour le développement, le débogage et le test unitaire en local. En raison des outils installés pour le développement et la compilation, elle est relativement volumineuse. 

* `dotnet/core/aspnet`

   L’exemple utilise cette image pour exécuter l’application. Elle contient le runtime ASP.NET Core et les bibliothèques. Elle est optimisée pour l’exécution d’applications en production. Conçue pour la vitesse de déploiement et de démarrage de l’application, elle est relativement petite afin d’optimiser les performances réseau du Registre Docker vers l’hôte Docker. Seuls les binaires et le contenu nécessaires pour exécuter une application sont copiés dans le conteneur. Le contenu est prêt à s’exécuter, ce qui réduit le délai entre `Docker run` et le démarrage de l’application. La compilation de code dynamique n’est pas nécessaire dans le modèle Docker.

## <a name="prerequisites"></a>Prérequis
::: moniker range="< aspnetcore-3.0"

* [Kit SDK .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [SDK .NET Core 3.0](https://dotnet.microsoft.com/download)

::: moniker-end

* Client Docker 18.03 ou version ultérieure

  * Distributions Linux
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [MacOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Télécharger l’exemple d’application

* Téléchargez l’exemple en clonant le [référentiel Docker .NET Core](https://github.com/dotnet/dotnet-docker) : 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Exécutez l’application localement.

* Accédez au dossier de projet à l’adresse *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Exécutez la commande suivante pour générer et exécuter l’application localement :

  ```dotnetcli
  dotnet run
  ```

* Accédez à `http://localhost:5000` dans un navigateur pour tester l’application.

* Appuyez sur Ctrl+C dans l’invite de commande pour arrêter l’application.

## <a name="run-in-a-linux-container"></a>Exécuter dans un conteneur Linux

* Dans le client docker, [basculez vers les conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* Accédez au dossier Dockerfile à l’adresse *dotnet-docker/samples/aspnetapp*.

* Exécutez les commandes suivantes pour générer et exécuter l’exemple dans Docker :

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  Voici le rôle des arguments de la commande `build` :
  * nommer l’image aspnetapp ;
  * rechercher le fichier Dockerfile dans le dossier actif (le point final).

  Voici le rôle des arguments de la commande run :
  * allouer un pseudoterminal TTY et le laisser ouvert même s’il n’est pas attaché (même effet que `--interactive --tty`) ;
  * supprimer automatiquement le conteneur lorsqu’il se ferme ;
  * mapper le port 5000 de l’ordinateur local avec le port 80 du conteneur ;
  * nommer le conteneur aspnetcore_sample ;
  * spécifier l’image aspnetapp.

* Accédez à `http://localhost:5000` dans un navigateur pour tester l’application.

## <a name="run-in-a-windows-container"></a>Exécuter dans un conteneur Windows

* Dans le client docker, [basculez vers les conteneurs Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

Accédez au dossier de fichiers Dockerfile à l’adresse `dotnet-docker/samples/aspnetapp`.

* Exécutez les commandes suivantes pour générer et exécuter l’exemple dans Docker :

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Pour les conteneurs Windows, l’adresse IP du conteneur est nécessaire (accéder à `http://localhost:5000` ne fonctionnera pas) :
  * Ouvrez une autre invite de commandes.
  * Exécutez `docker ps` pour voir les conteneurs en cours d’exécution. Vérifiez que le conteneur « aspnetcore_sample » en fait partie.
  * Exécutez `docker exec aspnetcore_sample ipconfig` pour afficher l’adresse IP du conteneur. La sortie de la commande se présente ainsi :

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Copiez l’adresse IPv4 du conteneur (par exemple, 172.29.245.43) et collez-la dans la barre d’adresse du navigateur pour tester l’application.

## <a name="build-and-deploy-manually"></a>Effectuer manuellement le build et le déploiement

Dans certains scénarios, il peut être intéressant de déployer une application sur un conteneur en y copiant les fichiers d’application nécessaires à l’exécution. Cette section montre comment effectuer un déploiement manuel.

* Accédez au dossier de projet à l’adresse *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Exécutez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Voici le rôle des arguments de la commande :
  * Générez l’application en mode version finale (la valeur par défaut est le mode débogage).
  * Créer les fichiers dans le dossier *published*.

* Exécutez l'application.

  * Windows :

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux :

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Accédez à `http://localhost:5000` pour afficher la page d’accueil.

Pour utiliser l’application publiée manuellement dans un conteneur Docker, créez un nouveau fichier Dockerfile et utilisez la commande `docker build .` pour générer le conteneur.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Le fichier Dockerfile

Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.  Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.  

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

### <a name="the-dockerfile"></a>Le fichier Dockerfile

Voici le *fichier dockerfile* utilisé par la `docker build` commande que vous avez exécutée précédemment.  Il utilise `dotnet publish` comme nous l’avons fait dans cette section pour le build et le déploiement.  

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

Comme indiqué dans le fichier dockerfile précédent, les `*.csproj` fichiers sont copiés et restaurés en tant que *couches*distinctes. Lorsque la `docker build` commande génère une image, elle utilise un cache intégré. Si les `*.csproj` fichiers n’ont pas été modifiés depuis la dernière exécution de la `docker build` commande, la `dotnet restore` commande n’a pas besoin de s’exécuter à nouveau. Au lieu de cela, le cache intégré pour la `dotnet restore` couche correspondante est réutilisé. Pour plus d’informations, consultez [meilleures pratiques pour l’écriture de fichiers dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [Commande docker build](https://docs.docker.com/engine/reference/commandline/build)
* [Commande docker run](https://docs.docker.com/engine/reference/commandline/run)
* [Exemple Docker ASP.NET Core](https://github.com/dotnet/dotnet-docker) (utilisé dans ce tutoriel)
* [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](../proxy-load-balancer.md)
* [Utilisation des outils Docker dans Visual Studio](./visual-studio-tools-for-docker.md)
* [Débogage avec Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC avec l’ancrage et les petits conteneurs](xref:performance/memory#sc)

## <a name="next-steps"></a>Étapes suivantes

Le référentiel Git qui contient l’exemple d’application comporte également une documentation. Pour une vue d’ensemble des ressources disponibles dans le référentiel, voir le [fichier Lisez-moi](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md). En particulier, découvrez comment implémenter le protocole HTTPS :

> [!div class="nextstepaction"]
> [Développer des applications ASP.NET Core avec Docker sur le protocole HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
