---
title: Utiliser ASP.NET Core SignalR avec la machine à écrire et WebPack
author: ssougnez
description: Dans ce didacticiel, vous allez configurer WebPack pour regrouper et créer une SignalR application web ASP.net Core dont le client est écrit en écriture manuscrite.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
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
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 949276bf4aae33c3af3fd1b8219a83868095f378
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056840"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a>Utiliser ASP.NET Core SignalR avec la machine à écrire et WebPack

Par [Sébastien Sougnez](https://twitter.com/ssougnez) et [Scott Addie](https://twitter.com/Scott_Addie)

[Webpack](https://webpack.js.org/) permet aux développeurs de regrouper et générer les ressources côté client d’une application web. Ce didacticiel montre comment utiliser WebPack dans une SignalR application web ASP.net Core dont le client est [TypeScript](https://www.typescriptlang.org/)écrit en écriture manuscrite.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Génération automatique d’une application de démarrage ASP.NET Core SignalR
> * Configurer le SignalR client de machine à écrire
> * Configurer un pipeline de build à l’aide de Webpack
> * Configurer le SignalR serveur
> * Activer la communication entre le client et le serveur

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**
* [SDK .NET Core 3.0 ou ultérieur](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [SDK .NET Core 3.0 ou ultérieur](https://dotnet.microsoft.com/download/dotnet-core)
* [C# pour Visual Studio Code version 1.17.1 ou ultérieure](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Créer l’application web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Configurez Visual Studio pour rechercher npm dans la variable d'environnement *PATH* . Par défaut, Visual Studio utilise la version de npm qu’il trouve dans son répertoire d’installation. Suivez ces instructions dans Visual Studio :

1. Lancez Visual Studio. Dans la fenêtre Démarrer, sélectionnez **continuer sans code** .
1. Accédez à **Outils** > **options** > **projets et solutions** > **Web Package Management** > **Outils Web externes** .
1. Sélectionnez l’entrée *$(PATH)* dans la liste. Cliquez sur la flèche vers le haut pour déplacer l’entrée à la deuxième position dans la liste, puis sélectionnez **OK** .

    ![Configuration de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

La configuration de Visual Studio est terminée.

1. Utilisez l’option de menu **fichier**  >  **nouveau**  >  **projet** et choisissez le modèle **application Web ASP.net Core** . Sélectionnez **Suivant** .
1. Nommez le projet *SignalR WebPack* , puis sélectionnez **créer** .
1. Sélectionnez *.net Core* dans la liste déroulante Framework cible, puis sélectionnez *ASP.net Core 3,1* dans la liste déroulante du sélecteur de Framework. Sélectionnez le modèle **vide** , puis sélectionnez **créer** .

Ajoutez le `Microsoft.TypeScript.MSBuild` package au projet :

1. Dans **Explorateur de solutions** (volet droit), cliquez avec le bouton droit sur le nœud du projet et sélectionnez **gérer les packages NuGet** . Sous l’onglet **Parcourir** , recherchez `Microsoft.TypeScript.MSBuild` , puis cliquez sur **installer** à droite pour installer le package.

Visual Studio ajoute le package NuGet sous le nœud **dépendances** dans **Explorateur de solutions** , en activant la compilation d’une machine à écrire dans le projet.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante dans le **Terminal intégré** :

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* La `dotnet new` commande crée une application web ASP.net Core vide dans un répertoire *SignalR WebPack* .
* La `code` commande ouvre le dossier *SignalR WebPack* dans l’instance actuelle de Visual Studio code.

Exécutez la commande CLI .NET Core suivante dans le **Terminal intégré** :

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

La commande précédente ajoute le package [Microsoft. dactylographié. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) , en activant la compilation d’une machine à écrire dans le projet.

---

## <a name="configure-webpack-and-typescript"></a>Configurer Webpack et TypeScript

Les étapes suivantes configurent la conversion de TypeScript en JavaScript et le regroupement des ressources côté client.

1. Exécutez la commande suivante dans la racine du projet pour créer un *package.jssur* le fichier :

    ```console
    npm init -y
    ```

1. Ajoutez la propriété en surbrillance au *package.jssur* le fichier et enregistrez les modifications du fichier :

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    La définition de la propriété `private` sur `true` empêche les avertissements d’installation de package à l’étape suivante.

1. Installez les packages npm nécessaires. À partir de la racine du projet, exécutez la commande suivante :

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Détails de commande à prendre en compte :

    * Un numéro de version suit le signe `@` pour chaque nom de package. npm installe ces versions de package spécifiques.
    * L’option `-E` désactive le comportement par défaut de npm consistant à écrire des opérateurs de plage de [gestion sémantique des versions](https://semver.org/) dans *package.json* . Par exemple, `"webpack": "4.41.5"` peut être utilisé à la place de `"webpack": "^4.41.5"`. Cette option empêche les mises à niveau involontaires vers des versions de package plus récentes.

    Pour plus d’informations [, consultez NPM-install](https://docs.npmjs.com/cli/install) docs.

1. Remplacez la `scripts` propriété de l' *package.jssur* le fichier par le code suivant :

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Explication des scripts :

    * `build`: Regroupe les ressources côté client en mode développement et surveille les modifications apportées aux fichiers. L’observateur de fichiers force la regénération du regroupement chaque fois qu’un fichier projet change. L’option `mode` désactive les optimisations de production, comme la minimisation de l’arborescence (tree shaking). Utilisez uniquement `build` dans le développement.
    * `release`: Regroupe les ressources côté client en mode production.
    * `publish` : Exécute le script `release` pour regrouper les ressources côté client en mode de production. La commande appelle la commande [publish](/dotnet/core/tools/dotnet-publish) de CLI .NET Core pour publier l’application.

1. Créez un fichier nommé *webpack.config.js* , à la racine du projet, à l’aide du code suivant :

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Le fichier précédent configure la compilation Webpack. Détails de configuration à prendre en compte :

    * La propriété `output` remplace la valeur par défaut de *dist* . Le regroupement est émis dans le répertoire *wwwroot* à la place.
    * Le `resolve.extensions` tableau comprend *. js* pour importer le SignalR client JavaScript.

1. Créez un répertoire *src* dans la racine du projet pour stocker les ressources côté client du projet.

1. Créez *src/index.html* avec le balisage suivant.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Le code HTML précédent définit le balisage réutilisable de la page d’accueil.

1. Créez un répertoire *src/css* . Son objectif est de stocker les fichiers *.css* du projet.

1. Créez *src/CSS/main. CSS* avec la CSS suivante :

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Le fichier *main.css* précédent définit le style de l’application.

1. Créez *src/tsconfig.jssur* avec le code JSON suivant :

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Le code précédent configure le compilateur TypeScript pour produire du code JavaScript compatible [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Créez *src/index. TS* avec le code suivant :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Le code TypeScript précédent récupère les références aux éléments DOM et joint deux gestionnaires d’événements :

    * `keyup`: Cet événement se déclenche lorsque l’utilisateur tape dans la `tbMessage` zone de texte. La fonction `send` est appelée quand l’utilisateur appuie sur la touche **Entrée** .
    * `click` : Cet événement se déclenche quand l’utilisateur clique sur le bouton **Envoyer** . La fonction `send` est appelée.

## <a name="configure-the-app"></a>Configurer l’application

1. Dans `Startup.Configure` , ajoutez des appels à [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Le code précédent permet au serveur de localiser et de servir le fichier *index.html* .  Le fichier est servi, que l’utilisateur entre son URL complète ou l’URL racine de l’application Web.

1. À la fin de `Startup.Configure` , mappez un itinéraire */Hub* au `ChatHub` Hub. Remplacez le code qui affiche *Hello World !* par la ligne suivante : 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. Dans `Startup.ConfigureServices` , appelez [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Créez un répertoire nommé *hubs* dans la racine du projet *SignalR WebPack/* pour stocker le SignalR Hub.

1. Créez un hub *Hubs/ChatHub.cs* avec le code suivant :

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Ajoutez l' `using` instruction suivante en haut du fichier *Startup.cs* pour résoudre la `ChatHub` référence :

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Activer la communication entre le client et le serveur

L’application affiche actuellement un formulaire de base pour envoyer des messages, mais n’est pas encore fonctionnel. Le serveur écoute une route spécifique, mais ne fait rien avec les messages envoyés.

1. Exécutez la commande suivante à la racine du projet :

    ```console
    npm i @microsoft/signalr @types/node
    ```

    La commande précédente installe :

     * Le [ SignalR client de machine à écrire](https://www.npmjs.com/package/@microsoft/signalr), qui permet au client d’envoyer des messages au serveur.
     * Définitions de type de machine à écrire pour Node.js, qui permet la vérification au moment de la compilation des types de Node.js.

1. Ajoutez le code en surbrillance au fichier *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Le code précédent prend en charge la réception de messages du serveur. La classe `HubConnectionBuilder` crée un générateur pour configurer la connexion de serveur. La fonction `withUrl` configure l’URL du hub.

    SignalR permet l’échange de messages entre un client et un serveur. Chaque message a un nom spécifique. Par exemple, les messages portant le nom `messageReceived` peuvent exécuter la logique responsable de l’affichage du nouveau message dans la zone de messages. L’écoute d’un message spécifique peut être effectuée au moyen de la fonction `on`. N’importe quel nombre de noms de messages peut être écouté. Vous pouvez aussi passer des paramètres au message, comme le nom de l’auteur et le contenu du message reçu. Dès que le client reçoit un message, un élément `div` est créé avec le nom de l’auteur et le contenu du message dans son attribut `innerHTML`. Il est ajouté à l’élément `div` principal qui affiche les messages.

1. Maintenant que le client peut recevoir un message, configurez-le pour en envoyer. Ajoutez le code en surbrillance au fichier *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    L’envoi d’un message au moyen de la connexion WebSocket nécessite l’appel de la méthode `send`. Le premier paramètre de la méthode est le nom du message. Les données du message se trouvent dans les autres paramètres. Dans cet exemple, un message identifié comme `newMessage` est envoyé au serveur. Le message se compose du nom d’utilisateur et de l’entrée de l’utilisateur dans une zone de texte. Si l’envoi fonctionne, la valeur de la zone de texte est effacée.

1. Ajoutez la méthode `NewMessage` à la classe `ChatHub` :

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Le code précédent diffuse les messages reçus à tous les utilisateurs connectés, une fois que le serveur les reçoit. Vous n’avez pas besoin d’appeler une méthode générique `on` pour recevoir tous les messages. Il vous suffit d’avoir une méthode du même nom que le message.

    Dans cet exemple, le client TypeScript envoie un message identifié comme `newMessage`. La méthode C# `NewMessage` attend les données envoyées par le client. Un appel est effectué sur [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) sur les [clients. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Les messages reçus sont envoyés à tous les clients connectés au hub.

## <a name="test-the-app"></a>Tester l’application

Vérifiez que l’application fonctionne avec les étapes suivantes.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Exécuter Webpack en mode de *mise en production* . À l’aide de la fenêtre de **console du gestionnaire de package** , exécutez la commande suivante dans la racine du projet. Si vous ne vous trouvez pas à la racine du projet, tapez `cd SignalRWebPack` avant d’entrer la commande.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sélectionnez **Déboguer**  >  **exécuter sans débogage** pour lancer l’application dans un navigateur sans attacher le débogueur. Le fichier *wwwroot/index.html* est délivré sous `http://localhost:<port_number>`.

   Si vous recevez des erreurs de compilation, essayez de fermer et de rouvrir la solution. 

1. Ouvrez une autre instance de navigateur (n’importe quel navigateur). Copiez l’URL de la barre d’adresse.

1. Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message** , puis cliquez sur le bouton **Envoyer** . Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Exécutez Webpack en mode de *mise en production* en exécutant la commande suivante à la racine du projet :

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Générez et exécutez l’application en exécutant la commande suivante à la racine du projet :

    ```dotnetcli
    dotnet run
    ```

    Le serveur web démarre l’application et la rend disponible sur localhost.

1. Ouvrez un navigateur et accédez à `http://localhost:<port_number>`. Le fichier *wwwroot/index.html* est présent. Copiez l’URL à partir de la barre d’adresse.

1. Ouvrez une autre instance de navigateur (n’importe quel navigateur). Copiez l’URL de la barre d’adresse.

1. Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message** , puis cliquez sur le bouton **Envoyer** . Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.

---

![message affiché dans les deux fenêtres de navigateur](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**
* [Kit SDK .NET Core 2,2 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Kit SDK .NET Core 2,2 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-core)
* [C# pour Visual Studio Code version 1.17.1 ou ultérieure](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Créer l’application web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Configurez Visual Studio pour rechercher npm dans la variable d'environnement *PATH* . Par défaut, Visual Studio utilise la version de npm qu’il trouve dans son répertoire d’installation. Suivez ces instructions dans Visual Studio :

1. Accédez à **Outils** > **options** > **projets et solutions** > **Web Package Management** > **Outils Web externes** .
1. Sélectionnez l’entrée *$(PATH)* dans la liste. Cliquez sur la flèche vers le haut pour déplacer l’entrée à la deuxième position dans la liste.

    ![Configuration de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

La configuration de Visual Studio est terminée. Nous allons maintenant créer le projet.

1. Utilisez l’option de menu **Fichier** > **Nouveau** > **Projet** et choisissez le modèle **Application web ASP.NET Core** .
1. Nommez le projet *SignalR WebPack* , puis sélectionnez **créer** .
1. Sélectionnez *.NET Core* dans la liste déroulante du framework cible, puis *ASP.NET Core 2.2* dans la liste déroulante du sélecteur de framework. Sélectionnez le modèle **vide** , puis sélectionnez **créer** .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante dans le **Terminal intégré** :

```dotnetcli
dotnet new web -o SignalRWebPack
```

Une application Web ASP.NET Core vide, ciblant .NET Core, est créée dans un répertoire *SignalR WebPack* .

---

## <a name="configure-webpack-and-typescript"></a>Configurer Webpack et TypeScript

Les étapes suivantes configurent la conversion de TypeScript en JavaScript et le regroupement des ressources côté client.

1. Exécutez la commande suivante dans la racine du projet pour créer un *package.jssur* le fichier :

    ```console
    npm init -y
    ```

1. Ajoutez la propriété en surbrillance au fichier *package.json* :

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    La définition de la propriété `private` sur `true` empêche les avertissements d’installation de package à l’étape suivante.

1. Installez les packages npm nécessaires. À partir de la racine du projet, exécutez la commande suivante :

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Détails de commande à prendre en compte :

    * Un numéro de version suit le signe `@` pour chaque nom de package. npm installe ces versions de package spécifiques.
    * L’option `-E` désactive le comportement par défaut de npm consistant à écrire des opérateurs de plage de [gestion sémantique des versions](https://semver.org/) dans *package.json* . Par exemple, `"webpack": "4.29.3"` peut être utilisé à la place de `"webpack": "^4.29.3"`. Cette option empêche les mises à niveau involontaires vers des versions de package plus récentes.

    Pour plus d’informations [, consultez NPM-install](https://docs.npmjs.com/cli/install) docs.

1. Remplacez la `scripts` propriété de l' *package.jssur* le fichier par le code suivant :

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Explication des scripts :

    * `build`: Regroupe les ressources côté client en mode développement et surveille les modifications apportées aux fichiers. L’observateur de fichiers force la regénération du regroupement chaque fois qu’un fichier projet change. L’option `mode` désactive les optimisations de production, comme la minimisation de l’arborescence (tree shaking). Utilisez uniquement `build` dans le développement.
    * `release`: Regroupe les ressources côté client en mode production.
    * `publish` : Exécute le script `release` pour regrouper les ressources côté client en mode de production. La commande appelle la commande [publish](/dotnet/core/tools/dotnet-publish) de CLI .NET Core pour publier l’application.

1. Créez un fichier nommé *webpack.config.js* à la racine du projet, avec le code suivant :

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Le fichier précédent configure la compilation Webpack. Détails de configuration à prendre en compte :

    * La propriété `output` remplace la valeur par défaut de *dist* . Le regroupement est émis dans le répertoire *wwwroot* à la place.
    * Le `resolve.extensions` tableau comprend *. js* pour importer le SignalR client JavaScript.

1. Créez un répertoire *src* dans la racine du projet pour stocker les ressources côté client du projet.

1. Créez *src/index.html* avec le balisage suivant.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Le code HTML précédent définit le balisage réutilisable de la page d’accueil.

1. Créez un répertoire *src/css* . Son objectif est de stocker les fichiers *.css* du projet.

1. Créez *src/CSS/main. CSS* avec le balisage suivant :

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Le fichier *main.css* précédent définit le style de l’application.

1. Créez *src/tsconfig.jssur* avec le code JSON suivant :

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Le code précédent configure le compilateur TypeScript pour produire du code JavaScript compatible [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Créez *src/index. TS* avec le code suivant :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Le code TypeScript précédent récupère les références aux éléments DOM et joint deux gestionnaires d’événements :

    * `keyup`: Cet événement se déclenche lorsque l’utilisateur tape dans la `tbMessage` zone de texte. La fonction `send` est appelée quand l’utilisateur appuie sur la touche **Entrée** .
    * `click` : Cet événement se déclenche quand l’utilisateur clique sur le bouton **Envoyer** . La fonction `send` est appelée.

## <a name="configure-the-aspnet-core-app"></a>Configurer l’application ASP.NET Core

1. Le code fourni dans la méthode `Startup.Configure` affiche *Hello World!* . Remplacez l’appel de méthode `app.Run` par des appels à [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Le code précédent permet au serveur de localiser et traiter le fichier *index.html* , que l’utilisateur entre son URL complète ou l’URL racine de l’application web.

1. Appelez [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices` . Il ajoute les SignalR services au projet.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Mappez une route */hub* au hub `ChatHub`. Ajoutez les lignes suivantes à la fin de `Startup.Configure` :

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Créez un répertoire *Hubs* à la racine du projet. Son objectif est de stocker le SignalR Hub, créé à l’étape suivante.

1. Créez un hub *Hubs/ChatHub.cs* avec le code suivant :

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Ajoutez le code suivant en haut du fichier *Startup.cs* pour résoudre la référence de `ChatHub` :

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Activer la communication entre le client et le serveur

Actuellement, l’application affiche un formulaire simple pour envoyer des messages. Rien ne se produit quand vous essayez de le faire. Le serveur écoute une route spécifique, mais ne fait rien avec les messages envoyés.

1. Exécutez la commande suivante à la racine du projet :

    ```console
    npm install @aspnet/signalr
    ```

    La commande précédente installe le [ SignalR client de machine à écrire](https://www.npmjs.com/package/@microsoft/signalr), ce qui permet au client d’envoyer des messages au serveur.

1. Ajoutez le code en surbrillance au fichier *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Le code précédent prend en charge la réception de messages du serveur. La classe `HubConnectionBuilder` crée un générateur pour configurer la connexion de serveur. La fonction `withUrl` configure l’URL du hub.

    SignalR permet l’échange de messages entre un client et un serveur. Chaque message a un nom spécifique. Par exemple, les messages portant le nom `messageReceived` peuvent exécuter la logique responsable de l’affichage du nouveau message dans la zone de messages. L’écoute d’un message spécifique peut être effectuée au moyen de la fonction `on`. Vous pouvez écouter n’importe quel nombre de noms de message. Vous pouvez aussi passer des paramètres au message, comme le nom de l’auteur et le contenu du message reçu. Dès que le client reçoit un message, un élément `div` est créé avec le nom de l’auteur et le contenu du message dans son attribut `innerHTML`. Le nouveau message est ajouté à l’élément principal qui `div` affiche les messages.

1. Maintenant que le client peut recevoir un message, configurez-le pour en envoyer. Ajoutez le code en surbrillance au fichier *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    L’envoi d’un message au moyen de la connexion WebSocket nécessite l’appel de la méthode `send`. Le premier paramètre de la méthode est le nom du message. Les données du message se trouvent dans les autres paramètres. Dans cet exemple, un message identifié comme `newMessage` est envoyé au serveur. Le message se compose du nom d’utilisateur et de l’entrée de l’utilisateur dans une zone de texte. Si l’envoi fonctionne, la valeur de la zone de texte est effacée.

1. Ajoutez la méthode `NewMessage` à la classe `ChatHub` :

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Le code précédent diffuse les messages reçus à tous les utilisateurs connectés, une fois que le serveur les reçoit. Vous n’avez pas besoin d’appeler une méthode générique `on` pour recevoir tous les messages. Il vous suffit d’avoir une méthode du même nom que le message.

    Dans cet exemple, le client TypeScript envoie un message identifié comme `newMessage`. La méthode C# `NewMessage` attend les données envoyées par le client. Un appel est effectué sur [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) sur les [clients. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Les messages reçus sont envoyés à tous les clients connectés au hub.

## <a name="test-the-app"></a>Tester l’application

Vérifiez que l’application fonctionne avec les étapes suivantes.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Exécuter Webpack en mode de *mise en production* . À l’aide de la fenêtre de **console du gestionnaire de package** , exécutez la commande suivante dans la racine du projet. Si vous ne vous trouvez pas à la racine du projet, tapez `cd SignalRWebPack` avant d’entrer la commande.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sélectionnez **Déboguer**  >  **exécuter sans débogage** pour lancer l’application dans un navigateur sans attacher le débogueur. Le fichier *wwwroot/index.html* est délivré sous `http://localhost:<port_number>`.

1. Ouvrez une autre instance de navigateur (n’importe quel navigateur). Copiez l’URL de la barre d’adresse.

1. Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message** , puis cliquez sur le bouton **Envoyer** . Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Exécutez Webpack en mode de *mise en production* en exécutant la commande suivante à la racine du projet :

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Générez et exécutez l’application en exécutant la commande suivante à la racine du projet :

    ```dotnetcli
    dotnet run
    ```

    Le serveur web démarre l’application et la rend disponible sur localhost.

1. Ouvrez un navigateur et accédez à `http://localhost:<port_number>`. Le fichier *wwwroot/index.html* est présent. Copiez l’URL à partir de la barre d’adresse.

1. Ouvrez une autre instance de navigateur (n’importe quel navigateur). Copiez l’URL de la barre d’adresse.

1. Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message** , puis cliquez sur le bouton **Envoyer** . Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.

---

![message affiché dans les deux fenêtres de navigateur](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
