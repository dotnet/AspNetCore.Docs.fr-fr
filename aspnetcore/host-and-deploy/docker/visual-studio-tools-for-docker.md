---
title: Outils de conteneur Visual Studio avec ASP.NET Core
author: spboyer
description: Découvrez comment utiliser les outils Visual Studio et Docker pour Windows pour mettre une application ASP.NET Core dans un conteneur.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: c11261fef84234a9caaf872ae9871932d02617fa
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585992"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Outils de conteneur Visual Studio avec ASP.NET Core

Visual Studio 2017 et versions ultérieures prennent en charge la génération, le débogage et l’exécution d’applications ASP.NET Core conteneurisées ciblant .NET Core. Les conteneurs Windows et Linux sont pris en charge.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

* [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail **Développement multiplateforme .NET Core**

## <a name="installation-and-setup"></a>Installation et configuration

Pour l’installation Docker, commencez par passer en revue les informations contenues dans [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). Ensuite, installez [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/).

Il est nécessaire de configurer les **[lecteurs partagés](https://docs.docker.com/docker-for-windows/#shared-drives)** dans Docker pour Windows pour prendre en charge le mappage de volume et le débogage. Cliquez avec le bouton droit sur l’icône Docker de la zone de notification, sélectionnez **Paramètres**, puis **Lecteurs partagés**. Sélectionnez le lecteur où Docker stocke les fichiers. Cliquez sur **Appliquer**.

![Boîte de dialogue où est sélectionné le partage de lecteur C local pour les conteneurs](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 versions 15.6 et ultérieures sollicitent l’utilisateur quand les **lecteurs partagés** ne sont pas configurés.

## <a name="add-a-project-to-a-docker-container"></a>Ajouter un projet à un conteneur Docker

Pour mettre en conteneur un projet ASP.NET Core, le projet doit cibler .NET Core. Les conteneurs Linux et Windows sont pris en charge.

Quand vous ajoutez la prise en charge de Docker à un projet, choisissez un conteneur Windows ou Linux. L’hôte Docker doit exécuter le même type de conteneur. Pour changer le type de conteneur dans l’instance de Docker en cours d’exécution, cliquez avec le bouton droit sur l’icône Docker de la zone de notification, puis choisissez **Basculer vers les conteneurs Windows...** ou **Basculer vers les conteneurs Linux...**.

### <a name="new-app"></a>Nouvelle application

Quand vous créez une application avec les modèles de projet **Application web ASP.NET Core**, cochez la case **Activer la prise en charge de Docker** :

![Case Activer la prise en charge de Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Si la version cible du .NET Framework est .NET Core, la liste déroulante **OS** (SE) permet la sélection d’un type de conteneur.

### <a name="existing-app"></a>Application existante

Pour les projets ASP.NET Core ciblant .NET Core, il existe deux options pour ajouter la prise en charge de Docker via les outils. Ouvrez le projet dans Visual Studio et choisissez l’une des options suivantes :

* Sélectionnez **Prise en charge de Docker** dans le menu **Projet**.
* Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** la  >  **prise en charge** de l’ancrage.

Les outils de conteneur Visual Studio ne prennent pas en charge l’ajout de Docker à un projet ASP.NET Core existant ciblant le .NET Framework.

## <a name="dockerfile-overview"></a>Vue d’ensemble du fichier Dockerfile

Un fichier *Dockerfile*, la recette de la création d’une image Docker finale, est ajouté à la racine du projet. Reportez-vous à la [référence fichier dockerfile](https://docs.docker.com/engine/reference/builder/) pour connaître les commandes qu’il contient. Ce fichier *Dockerfile* spécifique utilise une [build en plusieurs étapes](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) avec quatre différentes étapes de build nommées :

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Le fichier *Dockerfile* précédent est basé sur l’image [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/). Cette image de base comprend le runtime ASP.NET Core et des packages NuGet. Les packages sont compilés juste-à-temps (JIT) pour améliorer les performances de démarrage.

Quand la case **Configurer pour HTTPS** de la boîte de dialogue du nouveau projet est cochée, le fichier *Dockerfile* expose deux ports. Un port est utilisé pour le trafic HTTP tandis que l’autre est utilisé pour HTTPS. Si la case n’est pas cochée, un seul port (80) est exposé pour le trafic HTTP.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Le fichier *Dockerfile* précédent est basé sur l’image [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/). Cette image de base comprend les packages NuGet ASP.NET Core, qui ont été compilés juste-à-temps (JIT) pour améliorer les performances de démarrage.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Ajouter la prise en charge des orchestrateurs de conteneurs à une application

Visual Studio 2017 version 15.7 ou antérieure prend en charge [Docker Compose](https://docs.docker.com/compose/overview/) en tant que solution d’orchestration de conteneurs unique. Les artefacts docker compose sont ajoutés via **Ajouter** la  >  **prise en charge** de l’ancrage.

Visual Studio 2017 version 15.8 ou ultérieure ajoute une solution d’orchestration seulement si cela lui est demandé. Cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions**, puis sélectionnez **Ajouter** > **Prise en charge de l’orchestrateur de conteneurs**. Les choix suivants sont disponibles : 

* [Docker Compose](#docker-compose)
* [Service Fabric](#service-fabric)
* [Kubernetes/Helm ](https://helm.sh/)

### <a name="docker-compose"></a>Docker Compose

Les outils de conteneur Visual Studio ajoutent un projet *docker-compose* à la solution avec les fichiers suivants :

* *dockr-compose. dcproj*: fichier représentant le projet. Comprend un élément `<DockerTargetOS>` spécifiant le système d’exploitation à utiliser.
* *. dockerignore*: répertorie les modèles de fichiers et de répertoires à exclure lors de la génération d’un contexte de génération.
* *docker-compose. yml*: fichier [docker compose](https://docs.docker.com/compose/overview/) de base utilisé pour définir la collection d’images générées et exécutées avec `docker-compose build` et `docker-compose run` , respectivement.
* *docker-compose. override. yml*: fichier facultatif, lu par docker compose, avec substitutions de configuration pour les services. Visual Studio exécute `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` pour fusionner ces fichiers.

Le fichier *docker-compose.yml* référence le nom de l’image créée pendant l’exécution du projet :

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

Dans l’exemple précédent, `image: hellodockertools` génère l’image `hellodockertools:dev` quand l’application est exécutée en mode **débogage**. L’image `hellodockertools:latest` est générée quand l’application est exécutée en mode **mise en production**.

En guise de préfixe, ajoutez au nom de l’image le nom d’utilisateur [Docker Hub](https://hub.docker.com/) (par exemple, `dockerhubusername/hellodockertools`) si l’image est destinée à être envoyée (push) au Registre. Vous pouvez aussi changer le nom de l’image pour inclure l’URL de Registre privé (par exemple, `privateregistry.domain.com/hellodockertools`) en fonction de la configuration.

Si vous souhaitez un autre comportement basé sur la configuration de build (par exemple, Debug ou Release), ajoutez des fichiers *docker-compose* propres à la configuration. Les fichiers doivent être nommés en fonction de la configuration de build (par exemple, *docker-compose.vs.debug.yml* et *docker-compose.vs.release.yml*), et placés dans le même emplacement que le fichier *docker-compose-override.yml*. 

À l’aide des fichiers de substitution spécifiques à la configuration, vous pouvez spécifier différents paramètres de configuration (par exemple, des variables d’environnement ou des points d’entrée) pour les configurations de build Debug et Release.

Par Docker Compose pour afficher une option à exécuter dans Visual Studio, le projet d’ancrage doit être le projet de démarrage.

### <a name="service-fabric"></a>Service Fabric

En plus des [prérequis](#prerequisites) de base, la solution d’orchestration [Service Fabric](/azure/service-fabric/) impose les prérequis suivants :

* [Kit SDK Microsoft Azure Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 ou ultérieure
* Charge de travail **Développement Azure** de Visual Studio

Service Fabric ne prend pas en charge les conteneurs Linux s’exécutant dans le cluster de développement local sur Windows. Si le projet utilise déjà un conteneur Linux, Visual Studio vous invite à basculer vers des conteneurs Windows.

Les outils de conteneur Visual Studio effectuent les tâches suivantes :

* Ajoute un projet *&lt; project_name &gt; application* **service Fabric application** à la solution.
* Ajoute un fichier *Dockerfile* et un fichier *.dockerignore* au projet ASP.NET Core. S’il existe déjà un fichier *Dockerfile* dans le projet ASP.NET Core, il est renommé *Dockerfile.original*. Un nouveau fichier *Dockerfile*, semblable au suivant, est créé :

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* Ajoute un élément `<IsServiceFabricServiceProject>` au fichier *.csproj* du projet ASP.NET Core :

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* Ajoute un dossier *PackageRoot* au projet ASP.NET Core. Le dossier comprend le manifeste de service et les paramètres du nouveau service.

Pour plus d’informations, consultez [Déployer une application .NET dans un conteneur Windows vers Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Débogage

Sélectionnez **Docker** dans la liste déroulante de débogage dans la barre d’outils et démarrez le débogage de l’application. La vue **Docker** de la fenêtre **Sortie** affiche les actions suivantes qui se déroulent :

::: moniker range=">= aspnetcore-2.1"

* La balise *2.1-aspnetcore-runtime* de l’image de runtime *microsoft/dotnet* est acquise (si elle ne se trouve pas déjà dans le cache). L’image installe les runtimes ASP.NET Core et .NET Core et les bibliothèques associées. Elle est optimisée pour l’exécution des applications ASP.NET Core en production.
* La variable d’environnement `ASPNETCORE_ENVIRONMENT` a la valeur `Development` dans le conteneur.
* Deux ports attribués dynamiquement sont exposés : un pour HTTP et l’autre pour HTTPS. Le port attribué à localhost peut être interrogé avec la commande `docker ps`.
* L’application est copiée dans le conteneur.
* Le navigateur par défaut est lancé avec le débogueur attaché au conteneur, en utilisant le port attribué dynamiquement.

L’image Docker obtenue de l’application est marquée avec la balise *dev*. L’image est basée sur la balise *2.1-aspnetcore-runtime* de l’image de base *microsoft/dotnet*. Exécutez la commande `docker images` dans la fenêtre **Console du Gestionnaire de package**. Les images sur la machine s’affichent :

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* L’image d’exécution *microsoft/aspnetcore* est acquise (si elle n’est pas déjà dans le cache).
* La variable d’environnement `ASPNETCORE_ENVIRONMENT` a la valeur `Development` dans le conteneur.
* Le port 80 est exposé et mappé à un port attribué dynamiquement pour localhost. Le port est déterminé par l’hôte Docker et peut être interrogé avec la commande `docker ps`.
* L’application est copiée dans le conteneur.
* Le navigateur par défaut est lancé avec le débogueur attaché au conteneur, en utilisant le port attribué dynamiquement.

L’image Docker obtenue de l’application est marquée avec la balise *dev*. L’image est basée sur l’image de base *microsoft/aspnetcore*. Exécutez la commande `docker images` dans la fenêtre **Console du Gestionnaire de package**. Les images sur la machine s’affichent :

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> L’image de *développement* ne contient pas le contenu de l’application, car les configurations de **débogage** utilisent le montage de volume pour fournir l’expérience itérative. Pour placer une image, utilisez la configuration **release**.

Exécutez la commande `docker ps` dans la console du Gestionnaire de package. Notez que l’application s’exécute à l’aide du conteneur :

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Modifier & Continuer

Les modifications apportées aux fichiers et aux vues statiques Razor sont automatiquement mises à jour sans qu’une étape de compilation soit nécessaire. Apportez la modification, enregistrez et actualisez le navigateur pour afficher la mise à jour.

Les modifications de fichiers de code nécessitent une compilation et un redémarrage de Kestrel au sein du conteneur. Après avoir effectué la modification, utilisez `CTRL+F5` pour exécuter le processus et démarrer l’application au sein du conteneur. Le conteneur Docker n’est pas regénéré ni arrêté. Exécutez la commande `docker ps` dans la console du Gestionnaire de package. Notez que le conteneur d’origine est toujours en cours d’exécution comme 10 minutes auparavant :

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Publier des images Docker

Une fois terminé le cycle de développement et de débogage de l’application, les outils de conteneur Visual Studio aident à créer l’image de production de l’application. Changez la liste déroulante de configuration en **Release** et générez l’application. Les outils obtiennent l’image de compilation/publication auprès de Docker Hub (si elle ne se trouve pas déjà dans le cache). Une image est produite avec la balise *latest*, qui peut être envoyée (push) au Registre privé ou à Docker Hub.

Exécutez la commande `docker images` dans la console du Gestionnaire de package pour afficher la liste des images. Une sortie similaire à la suivante s’affiche à l’écran :

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

Les images `microsoft/aspnetcore-build` et `microsoft/aspnetcore` répertoriées dans la sortie précédente sont remplacées par des images `microsoft/dotnet` à compter de .NET Core 2.1. Pour plus d’informations, consultez [l’annonce de migration des dépôts Docker](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> La `docker images` commande retourne des images intermédiaires avec des noms et des balises de référentiel identifiés comme *\<none>* (non répertoriés ci-dessus). Ces images sans nom sont produites par la [build en plusieurs étapes](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*. Elles améliorent l’efficacité de la création de l’image finale ; seules les couches nécessaires sont regénérées en cas de modifications. Quand les images intermédiaires ne sont plus nécessaires, supprimez-les à l’aide de la commande [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

Vous pourriez vous attendre à ce que l’image de production ou de publication ait une taille inférieure à l’image de *développement*. En raison de l’utilisation du mappage de volume, le débogueur et l’application étaient exécutés à partir de l’ordinateur local et non dans le conteneur. L’image *latest* a empaqueté le code de l’application nécessaire pour l’exécuter sur un ordinateur hôte. Le delta correspond donc à la taille du code de l’application.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Développement de conteneurs avec Visual Studio](/visualstudio/containers)
* [Azure Service Fabric : Préparer votre environnement de développement](/azure/service-fabric/service-fabric-get-started)
* [Déployer une application .NET dans un conteneur Windows vers Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Détecter un problème de développement Visual Studio avec Docker](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Référentiel GitHub des outils de conteneur Visual Studio](https://github.com/Microsoft/DockerTools)
* [GC avec l’ancrage et les petits conteneurs](xref:performance/memory#sc)
