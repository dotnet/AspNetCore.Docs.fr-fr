---
title: Utiliser le modèle de projet React avec ASP.NET Core
author: SteveSandersonMS
description: Découvrez comment démarrer avec le modèle de projet d’application monopage ASP.NET Core pour React et create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
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
uid: spa/react
ms.openlocfilehash: 6c3539d96329489189f49a3af0c718791824be6b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054448"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Utiliser le modèle de projet React avec ASP.NET Core

Le modèle de projet React mis à jour fournit un point de départ pratique pour les applications ASP.NET Core utilisant les conventions React et [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) pour implémenter une interface utilisateur (IU) côté client enrichie.

Le modèle revient à créer un projet ASP.NET Core faisant office de backend d’API et un projet React CRA standard en guise d’interface utilisateur, qu’il héberge tous deux dans un projet d’application unique pouvant être généré et publié en tant qu’unité unique.

Le modèle de projet REACT n’est pas destiné au rendu côté serveur (SSR). Pour SSR avec REACT et Node.js, envisagez [Next.js](https://github.com/zeit/next.js/) ou [Razzle](https://github.com/jaredpalmer/razzle).

## <a name="create-a-new-app"></a>Créer une application

Si ASP.NET Core 2.1 est installé, il est inutile d’installer le modèle de projet React.

Créez un projet à partir d’une invite de commandes à l’aide de la commande `dotnet new react` dans un répertoire vide. Par exemple, les commandes suivantes créent l’application dans un répertoire *my-new-app* et basculent vers ce répertoire :

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Exécutez l’application à partir de Visual Studio ou de CLI .NET Core :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ouvrez le fichier *.csproj* généré, puis exécutez l’application normalement à partir de là.

Le processus de génération restaure les dépendances npm à la première exécution, ce qui peut prendre plusieurs minutes. Les générations suivantes sont beaucoup plus rapides.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Vérifiez que vous avez une variable d’environnement `ASPNETCORE_Environment` ayant pour valeur `Development`. Sur Windows (dans les invites non-PowerShell), exécutez `SET ASPNETCORE_Environment=Development`. Sur Linux ou macOS, exécutez `export ASPNETCORE_Environment=Development`.

Exécutez [dotnet build](/dotnet/core/tools/dotnet-build) pour vérifier que votre application se génère correctement. À la première exécution, le processus de génération restaure les dépendances npm, ce qui peut prendre plusieurs minutes. Les générations suivantes sont beaucoup plus rapides.

Exécutez [dotnet run](/dotnet/core/tools/dotnet-run) pour démarrer l’application.

---

Le modèle de projet crée une application ASP.NET Core et une application React. L’application ASP.NET Core est destinée à être utilisée pour tous les aspects liés au serveur, tels que l’accès aux données et l’autorisation. L’application React, qui réside dans le sous-répertoire *ClientApp* , est destinée à être utilisée pour tout ce qui touche l’interface utilisateur.

## <a name="add-pages-images-styles-modules-etc"></a>Ajouter des pages, des images, des styles, des modules, etc.

Le répertoire *ClientApp* est une application React CRA standard. Pour plus d’informations, consultez la [documentation CRA](https://create-react-app.dev/docs/getting-started/) officielle.

Il existe de légères différences entre l’application React créée par ce modèle et celle créée par CRA ; toutefois, les fonctionnalités de l’application sont identiques. L’application créée par le modèle contient une mise en page basée sur [Bootstrap](https://getbootstrap.com/) et un exemple de routage de base.

## <a name="install-npm-packages"></a>Installer les packages npm

Pour installer des packages npm tiers, utilisez une invite de commandes dans le sous-répertoire *ClientApp* . Exemple :

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publier et déployer

Pendant le développement, l’application s’exécute en mode optimisé pour des raisons pratiques. Par exemple, les bundles JavaScript incluent des mappages de sources (ce qui vous permet de voir votre code source d’origine pendant le débogage). L’application se recompile et se recharge automatiquement en cas de modification des fichiers JavaScript, HTML et CSS sur le disque.

Dans un environnement de production, fournissez une version de votre application qui est optimisée pour les performances. Ce comportement est configuré pour se produire automatiquement. Quand vous publiez, la configuration de build émet une build transpilée réduite de votre code côté client. Contrairement à la build de développement, la build de production n’a pas besoin que Node.js soit installé sur le serveur.

Vous pouvez utiliser des [méthodes d’hébergement et de déploiement ASP.NET Core](xref:host-and-deploy/index) standard.

## <a name="run-the-cra-server-independently"></a>Exécuter le serveur CRA indépendamment

Le projet est configuré pour démarrer sa propre instance du serveur de développement CRA en arrière-plan quand l’application ASP.NET Core démarre en mode de développement. Ainsi, vous n’êtes pas obligé d’exécuter un serveur distinct manuellement.

Cette configuration par défaut présente un inconvénient. Chaque fois que vous modifiez votre code C# et que votre application ASP.NET Core doit redémarrer, le serveur CRA redémarre. Quelques secondes sont nécessaires avant de démarrer la sauvegarde. Si vous apportez des modifications fréquentes au code C# et que vous ne souhaitez pas attendre que le serveur CRA redémarre, exécutez ce dernier en externe, indépendamment du processus ASP.NET Core. Pour ce faire :

1. Ajoutez un fichier *. env* au sous-répertoire *ClientApp* avec le paramètre suivant :

    ```
    BROWSER=none
    ```

    Cela empêchera votre navigateur Web de s’ouvrir lors du démarrage du serveur arc en externe.

2. Dans une invite de commandes, basculez vers le sous-répertoire *ClientApp* et lancez le serveur de développement CRA :

    ```console
    cd ClientApp
    npm start
    ```

3. Modifiez votre application ASP.NET Core afin qu’elle utilise l’instance de serveur CRA externe au lieu de lancer une instance propre. Dans votre classe *Startup* , remplacez l’appel `spa.UseReactDevelopmentServer` par ce qui suit :

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Quand vous démarrez votre application ASP.NET Core, celle-ci ne lance pas un serveur CRA. L’instance que vous avez démarrée manuellement est utilisée à la place. Cela lui permet de démarrer et de redémarrer plus rapidement. Elle n’attend plus que votre application React soit systématiquement regénérée.

> [!IMPORTANT]
> « Rendu côté serveur » n’est pas une fonctionnalité prise en charge de ce modèle. L’objectif de ce modèle est de respecter la parité avec « Create-REACT-App ». Ainsi, les scénarios et les fonctionnalités qui ne sont pas inclus dans un projet « Create-REACT-App » (par exemple, SSR) ne sont pas pris en charge et sont laissés en tant qu’exercice pour l’utilisateur.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authentication/identity/spa>
