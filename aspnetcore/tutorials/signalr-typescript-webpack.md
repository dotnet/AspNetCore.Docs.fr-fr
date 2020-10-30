---
title: 'Utiliser ASP.NET Core :::no-loc(SignalR)::: avec la machine à écrire et WebPack'
author: ssougnez
description: 'Dans ce didacticiel, vous allez configurer WebPack pour regrouper et créer une :::no-loc(SignalR)::: application web ASP.net Core dont le client est écrit en écriture manuscrite.'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 949276bf4aae33c3af3fd1b8219a83868095f378
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056840"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="ac30e-103">Utiliser ASP.NET Core :::no-loc(SignalR)::: avec la machine à écrire et WebPack</span><span class="sxs-lookup"><span data-stu-id="ac30e-103">Use ASP.NET Core :::no-loc(SignalR)::: with TypeScript and Webpack</span></span>

<span data-ttu-id="ac30e-104">Par [Sébastien Sougnez](https://twitter.com/ssougnez) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="ac30e-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="ac30e-105">[Webpack](https://webpack.js.org/) permet aux développeurs de regrouper et générer les ressources côté client d’une application web.</span><span class="sxs-lookup"><span data-stu-id="ac30e-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="ac30e-106">Ce didacticiel montre comment utiliser WebPack dans une :::no-loc(SignalR)::: application web ASP.net Core dont le client est [TypeScript](https://www.typescriptlang.org/)écrit en écriture manuscrite.</span><span class="sxs-lookup"><span data-stu-id="ac30e-106">This tutorial demonstrates using Webpack in an ASP.NET Core :::no-loc(SignalR)::: web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="ac30e-107">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="ac30e-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ac30e-108">Génération automatique d’une application de démarrage ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ac30e-108">Scaffold a starter ASP.NET Core :::no-loc(SignalR)::: app</span></span>
> * <span data-ttu-id="ac30e-109">Configurer le :::no-loc(SignalR)::: client de machine à écrire</span><span class="sxs-lookup"><span data-stu-id="ac30e-109">Configure the :::no-loc(SignalR)::: TypeScript client</span></span>
> * <span data-ttu-id="ac30e-110">Configurer un pipeline de build à l’aide de Webpack</span><span class="sxs-lookup"><span data-stu-id="ac30e-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="ac30e-111">Configurer le :::no-loc(SignalR)::: serveur</span><span class="sxs-lookup"><span data-stu-id="ac30e-111">Configure the :::no-loc(SignalR)::: server</span></span>
> * <span data-ttu-id="ac30e-112">Activer la communication entre le client et le serveur</span><span class="sxs-lookup"><span data-stu-id="ac30e-112">Enable communication between client and server</span></span>

<span data-ttu-id="ac30e-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac30e-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="ac30e-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="ac30e-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac30e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac30e-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac30e-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**</span><span class="sxs-lookup"><span data-stu-id="ac30e-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="ac30e-117">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="ac30e-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="ac30e-118">[Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="ac30e-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac30e-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac30e-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="ac30e-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac30e-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="ac30e-121">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="ac30e-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="ac30e-122">C# pour Visual Studio Code version 1.17.1 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="ac30e-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="ac30e-123">[Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="ac30e-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="ac30e-124">Créer l’application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac30e-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac30e-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac30e-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac30e-126">Configurez Visual Studio pour rechercher npm dans la variable d'environnement *PATH* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="ac30e-127">Par défaut, Visual Studio utilise la version de npm qu’il trouve dans son répertoire d’installation.</span><span class="sxs-lookup"><span data-stu-id="ac30e-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="ac30e-128">Suivez ces instructions dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="ac30e-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="ac30e-129">Lancez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ac30e-129">Launch Visual Studio.</span></span> <span data-ttu-id="ac30e-130">Dans la fenêtre Démarrer, sélectionnez **continuer sans code** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-130">At the start window, select **Continue without code** .</span></span>
1. <span data-ttu-id="ac30e-131">Accédez à **Outils** > **options** > **projets et solutions** > **Web Package Management** > **Outils Web externes** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools** .</span></span>
1. <span data-ttu-id="ac30e-132">Sélectionnez l’entrée *$(PATH)* dans la liste.</span><span class="sxs-lookup"><span data-stu-id="ac30e-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="ac30e-133">Cliquez sur la flèche vers le haut pour déplacer l’entrée à la deuxième position dans la liste, puis sélectionnez **OK** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-133">Click the up arrow to move the entry to the second position in the list, and select **OK** .</span></span>

    ![Configuration de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="ac30e-135">La configuration de Visual Studio est terminée.</span><span class="sxs-lookup"><span data-stu-id="ac30e-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="ac30e-136">Utilisez l’option de menu **fichier**  >  **nouveau**  >  **projet** et choisissez le modèle **application Web ASP.net Core** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="ac30e-137">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-137">Select **Next** .</span></span>
1. <span data-ttu-id="ac30e-138">Nommez le projet *:::no-loc(SignalR)::: WebPack* , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-138">Name the project *:::no-loc(SignalR):::WebPack* , and select **Create** .</span></span>
1. <span data-ttu-id="ac30e-139">Sélectionnez *.net Core* dans la liste déroulante Framework cible, puis sélectionnez *ASP.net Core 3,1* dans la liste déroulante du sélecteur de Framework.</span><span class="sxs-lookup"><span data-stu-id="ac30e-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="ac30e-140">Sélectionnez le modèle **vide** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-140">Select the **Empty** template, and select **Create** .</span></span>

<span data-ttu-id="ac30e-141">Ajoutez le `Microsoft.TypeScript.MSBuild` package au projet :</span><span class="sxs-lookup"><span data-stu-id="ac30e-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="ac30e-142">Dans **Explorateur de solutions** (volet droit), cliquez avec le bouton droit sur le nœud du projet et sélectionnez **gérer les packages NuGet** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages** .</span></span> <span data-ttu-id="ac30e-143">Sous l’onglet **Parcourir** , recherchez `Microsoft.TypeScript.MSBuild` , puis cliquez sur **installer** à droite pour installer le package.</span><span class="sxs-lookup"><span data-stu-id="ac30e-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="ac30e-144">Visual Studio ajoute le package NuGet sous le nœud **dépendances** dans **Explorateur de solutions** , en activant la compilation d’une machine à écrire dans le projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer** , enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac30e-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac30e-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac30e-146">Exécutez la commande suivante dans le **Terminal intégré** :</span><span class="sxs-lookup"><span data-stu-id="ac30e-146">Run the following command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet new web -o :::no-loc(SignalR):::WebPack
code -r :::no-loc(SignalR):::WebPack
```

* <span data-ttu-id="ac30e-147">La `dotnet new` commande crée une application web ASP.net Core vide dans un répertoire *:::no-loc(SignalR)::: WebPack* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *:::no-loc(SignalR):::WebPack* directory.</span></span>
* <span data-ttu-id="ac30e-148">La `code` commande ouvre le dossier *:::no-loc(SignalR)::: WebPack* dans l’instance actuelle de Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="ac30e-148">The `code` command opens the *:::no-loc(SignalR):::WebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="ac30e-149">Exécutez la commande CLI .NET Core suivante dans le **Terminal intégré** :</span><span class="sxs-lookup"><span data-stu-id="ac30e-149">Run the following .NET Core CLI command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="ac30e-150">La commande précédente ajoute le package [Microsoft. dactylographié. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) , en activant la compilation d’une machine à écrire dans le projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="ac30e-151">Configurer Webpack et TypeScript</span><span class="sxs-lookup"><span data-stu-id="ac30e-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="ac30e-152">Les étapes suivantes configurent la conversion de TypeScript en JavaScript et le regroupement des ressources côté client.</span><span class="sxs-lookup"><span data-stu-id="ac30e-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="ac30e-153">Exécutez la commande suivante dans la racine du projet pour créer un *package.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="ac30e-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="ac30e-154">Ajoutez la propriété en surbrillance au *package.jssur* le fichier et enregistrez les modifications du fichier :</span><span class="sxs-lookup"><span data-stu-id="ac30e-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="ac30e-155">La définition de la propriété `private` sur `true` empêche les avertissements d’installation de package à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="ac30e-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="ac30e-156">Installez les packages npm nécessaires.</span><span class="sxs-lookup"><span data-stu-id="ac30e-156">Install the required npm packages.</span></span> <span data-ttu-id="ac30e-157">À partir de la racine du projet, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="ac30e-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="ac30e-158">Détails de commande à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="ac30e-158">Some command details to note:</span></span>

    * <span data-ttu-id="ac30e-159">Un numéro de version suit le signe `@` pour chaque nom de package.</span><span class="sxs-lookup"><span data-stu-id="ac30e-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="ac30e-160">npm installe ces versions de package spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ac30e-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="ac30e-161">L’option `-E` désactive le comportement par défaut de npm consistant à écrire des opérateurs de plage de [gestion sémantique des versions](https://semver.org/) dans *package.json* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json* .</span></span> <span data-ttu-id="ac30e-162">Par exemple, `"webpack": "4.41.5"` peut être utilisé à la place de `"webpack": "^4.41.5"`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="ac30e-163">Cette option empêche les mises à niveau involontaires vers des versions de package plus récentes.</span><span class="sxs-lookup"><span data-stu-id="ac30e-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="ac30e-164">Pour plus d’informations [, consultez NPM-install](https://docs.npmjs.com/cli/install) docs.</span><span class="sxs-lookup"><span data-stu-id="ac30e-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="ac30e-165">Remplacez la `scripts` propriété de l' *package.jssur* le fichier par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="ac30e-166">Explication des scripts :</span><span class="sxs-lookup"><span data-stu-id="ac30e-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="ac30e-167">`build`: Regroupe les ressources côté client en mode développement et surveille les modifications apportées aux fichiers.</span><span class="sxs-lookup"><span data-stu-id="ac30e-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="ac30e-168">L’observateur de fichiers force la regénération du regroupement chaque fois qu’un fichier projet change.</span><span class="sxs-lookup"><span data-stu-id="ac30e-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="ac30e-169">L’option `mode` désactive les optimisations de production, comme la minimisation de l’arborescence (tree shaking).</span><span class="sxs-lookup"><span data-stu-id="ac30e-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="ac30e-170">Utilisez uniquement `build` dans le développement.</span><span class="sxs-lookup"><span data-stu-id="ac30e-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="ac30e-171">`release`: Regroupe les ressources côté client en mode production.</span><span class="sxs-lookup"><span data-stu-id="ac30e-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="ac30e-172">`publish` : Exécute le script `release` pour regrouper les ressources côté client en mode de production.</span><span class="sxs-lookup"><span data-stu-id="ac30e-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="ac30e-173">La commande appelle la commande [publish](/dotnet/core/tools/dotnet-publish) de CLI .NET Core pour publier l’application.</span><span class="sxs-lookup"><span data-stu-id="ac30e-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="ac30e-174">Créez un fichier nommé *webpack.config.js* , à la racine du projet, à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-174">Create a file named *webpack.config.js* , in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="ac30e-175">Le fichier précédent configure la compilation Webpack.</span><span class="sxs-lookup"><span data-stu-id="ac30e-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="ac30e-176">Détails de configuration à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="ac30e-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="ac30e-177">La propriété `output` remplace la valeur par défaut de *dist* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-177">The `output` property overrides the default value of *dist* .</span></span> <span data-ttu-id="ac30e-178">Le regroupement est émis dans le répertoire *wwwroot* à la place.</span><span class="sxs-lookup"><span data-stu-id="ac30e-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="ac30e-179">Le `resolve.extensions` tableau comprend *. js* pour importer le :::no-loc(SignalR)::: client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ac30e-179">The `resolve.extensions` array includes *.js* to import the :::no-loc(SignalR)::: client JavaScript.</span></span>

1. <span data-ttu-id="ac30e-180">Créez un répertoire *src* dans la racine du projet pour stocker les ressources côté client du projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="ac30e-181">Créez *src/index.html* avec le balisage suivant.</span><span class="sxs-lookup"><span data-stu-id="ac30e-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="ac30e-182">Le code HTML précédent définit le balisage réutilisable de la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="ac30e-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="ac30e-183">Créez un répertoire *src/css* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="ac30e-184">Son objectif est de stocker les fichiers *.css* du projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="ac30e-185">Créez *src/CSS/main. CSS* avec la CSS suivante :</span><span class="sxs-lookup"><span data-stu-id="ac30e-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="ac30e-186">Le fichier *main.css* précédent définit le style de l’application.</span><span class="sxs-lookup"><span data-stu-id="ac30e-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="ac30e-187">Créez *src/tsconfig.jssur* avec le code JSON suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="ac30e-188">Le code précédent configure le compilateur TypeScript pour produire du code JavaScript compatible [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="ac30e-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="ac30e-189">Créez *src/index. TS* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="ac30e-190">Le code TypeScript précédent récupère les références aux éléments DOM et joint deux gestionnaires d’événements :</span><span class="sxs-lookup"><span data-stu-id="ac30e-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="ac30e-191">`keyup`: Cet événement se déclenche lorsque l’utilisateur tape dans la `tbMessage` zone de texte.</span><span class="sxs-lookup"><span data-stu-id="ac30e-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="ac30e-192">La fonction `send` est appelée quand l’utilisateur appuie sur la touche **Entrée** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="ac30e-193">`click` : Cet événement se déclenche quand l’utilisateur clique sur le bouton **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="ac30e-194">La fonction `send` est appelée.</span><span class="sxs-lookup"><span data-stu-id="ac30e-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="ac30e-195">Configurer l’application</span><span class="sxs-lookup"><span data-stu-id="ac30e-195">Configure the app</span></span>

1. <span data-ttu-id="ac30e-196">Dans `Startup.Configure` , ajoutez des appels à [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="ac30e-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="ac30e-197">Le code précédent permet au serveur de localiser et de servir le fichier *index.html* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="ac30e-198">Le fichier est servi, que l’utilisateur entre son URL complète ou l’URL racine de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="ac30e-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="ac30e-199">À la fin de `Startup.Configure` , mappez un itinéraire */Hub* au `ChatHub` Hub.</span><span class="sxs-lookup"><span data-stu-id="ac30e-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="ac30e-200">Remplacez le code qui affiche *Hello World !*</span><span class="sxs-lookup"><span data-stu-id="ac30e-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="ac30e-201">par la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="ac30e-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_Use:::no-loc(SignalR):::&highlight=3)]

1. <span data-ttu-id="ac30e-202">Dans `Startup.ConfigureServices` , appelez [Add :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_:::no-loc(SignalR):::DependencyInjectionExtensions_Add:::no-loc(SignalR):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="ac30e-202">In `Startup.ConfigureServices`, call [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_:::no-loc(SignalR):::DependencyInjectionExtensions_Add:::no-loc(SignalR):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_Add:::no-loc(SignalR):::)]

1. <span data-ttu-id="ac30e-203">Créez un répertoire nommé *hubs* dans la racine du projet *:::no-loc(SignalR)::: WebPack/* pour stocker le :::no-loc(SignalR)::: Hub.</span><span class="sxs-lookup"><span data-stu-id="ac30e-203">Create a new directory named *Hubs* in the project root *:::no-loc(SignalR):::WebPack/* to store the :::no-loc(SignalR)::: hub.</span></span>

1. <span data-ttu-id="ac30e-204">Créez un hub *Hubs/ChatHub.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="ac30e-205">Ajoutez l' `using` instruction suivante en haut du fichier *Startup.cs* pour résoudre la `ChatHub` référence :</span><span class="sxs-lookup"><span data-stu-id="ac30e-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="ac30e-206">Activer la communication entre le client et le serveur</span><span class="sxs-lookup"><span data-stu-id="ac30e-206">Enable client and server communication</span></span>

<span data-ttu-id="ac30e-207">L’application affiche actuellement un formulaire de base pour envoyer des messages, mais n’est pas encore fonctionnel.</span><span class="sxs-lookup"><span data-stu-id="ac30e-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="ac30e-208">Le serveur écoute une route spécifique, mais ne fait rien avec les messages envoyés.</span><span class="sxs-lookup"><span data-stu-id="ac30e-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="ac30e-209">Exécutez la commande suivante à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="ac30e-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="ac30e-210">La commande précédente installe :</span><span class="sxs-lookup"><span data-stu-id="ac30e-210">The preceding command installs:</span></span>

     * <span data-ttu-id="ac30e-211">Le [ :::no-loc(SignalR)::: client de machine à écrire](https://www.npmjs.com/package/@microsoft/signalr), qui permet au client d’envoyer des messages au serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-211">The [:::no-loc(SignalR)::: TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="ac30e-212">Définitions de type de machine à écrire pour Node.js, qui permet la vérification au moment de la compilation des types de Node.js.</span><span class="sxs-lookup"><span data-stu-id="ac30e-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="ac30e-213">Ajoutez le code en surbrillance au fichier *src/index.ts* :</span><span class="sxs-lookup"><span data-stu-id="ac30e-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="ac30e-214">Le code précédent prend en charge la réception de messages du serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="ac30e-215">La classe `HubConnectionBuilder` crée un générateur pour configurer la connexion de serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="ac30e-216">La fonction `withUrl` configure l’URL du hub.</span><span class="sxs-lookup"><span data-stu-id="ac30e-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="ac30e-217">:::no-loc(SignalR)::: permet l’échange de messages entre un client et un serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-217">:::no-loc(SignalR)::: enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="ac30e-218">Chaque message a un nom spécifique.</span><span class="sxs-lookup"><span data-stu-id="ac30e-218">Each message has a specific name.</span></span> <span data-ttu-id="ac30e-219">Par exemple, les messages portant le nom `messageReceived` peuvent exécuter la logique responsable de l’affichage du nouveau message dans la zone de messages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="ac30e-220">L’écoute d’un message spécifique peut être effectuée au moyen de la fonction `on`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="ac30e-221">N’importe quel nombre de noms de messages peut être écouté.</span><span class="sxs-lookup"><span data-stu-id="ac30e-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="ac30e-222">Vous pouvez aussi passer des paramètres au message, comme le nom de l’auteur et le contenu du message reçu.</span><span class="sxs-lookup"><span data-stu-id="ac30e-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="ac30e-223">Dès que le client reçoit un message, un élément `div` est créé avec le nom de l’auteur et le contenu du message dans son attribut `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="ac30e-224">Il est ajouté à l’élément `div` principal qui affiche les messages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="ac30e-225">Maintenant que le client peut recevoir un message, configurez-le pour en envoyer.</span><span class="sxs-lookup"><span data-stu-id="ac30e-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="ac30e-226">Ajoutez le code en surbrillance au fichier *src/index.ts* :</span><span class="sxs-lookup"><span data-stu-id="ac30e-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="ac30e-227">L’envoi d’un message au moyen de la connexion WebSocket nécessite l’appel de la méthode `send`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="ac30e-228">Le premier paramètre de la méthode est le nom du message.</span><span class="sxs-lookup"><span data-stu-id="ac30e-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="ac30e-229">Les données du message se trouvent dans les autres paramètres.</span><span class="sxs-lookup"><span data-stu-id="ac30e-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="ac30e-230">Dans cet exemple, un message identifié comme `newMessage` est envoyé au serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="ac30e-231">Le message se compose du nom d’utilisateur et de l’entrée de l’utilisateur dans une zone de texte.</span><span class="sxs-lookup"><span data-stu-id="ac30e-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="ac30e-232">Si l’envoi fonctionne, la valeur de la zone de texte est effacée.</span><span class="sxs-lookup"><span data-stu-id="ac30e-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="ac30e-233">Ajoutez la méthode `NewMessage` à la classe `ChatHub` :</span><span class="sxs-lookup"><span data-stu-id="ac30e-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="ac30e-234">Le code précédent diffuse les messages reçus à tous les utilisateurs connectés, une fois que le serveur les reçoit.</span><span class="sxs-lookup"><span data-stu-id="ac30e-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="ac30e-235">Vous n’avez pas besoin d’appeler une méthode générique `on` pour recevoir tous les messages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="ac30e-236">Il vous suffit d’avoir une méthode du même nom que le message.</span><span class="sxs-lookup"><span data-stu-id="ac30e-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="ac30e-237">Dans cet exemple, le client TypeScript envoie un message identifié comme `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="ac30e-238">La méthode C# `NewMessage` attend les données envoyées par le client.</span><span class="sxs-lookup"><span data-stu-id="ac30e-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="ac30e-239">Un appel est effectué sur [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) sur les [clients. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="ac30e-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="ac30e-240">Les messages reçus sont envoyés à tous les clients connectés au hub.</span><span class="sxs-lookup"><span data-stu-id="ac30e-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="ac30e-241">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="ac30e-241">Test the app</span></span>

<span data-ttu-id="ac30e-242">Vérifiez que l’application fonctionne avec les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="ac30e-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac30e-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac30e-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ac30e-244">Exécuter Webpack en mode de *mise en production* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="ac30e-245">À l’aide de la fenêtre de **console du gestionnaire de package** , exécutez la commande suivante dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="ac30e-246">Si vous ne vous trouvez pas à la racine du projet, tapez `cd :::no-loc(SignalR):::WebPack` avant d’entrer la commande.</span><span class="sxs-lookup"><span data-stu-id="ac30e-246">If you are not in the project root, enter `cd :::no-loc(SignalR):::WebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="ac30e-247">Sélectionnez **Déboguer**  >  **exécuter sans débogage** pour lancer l’application dans un navigateur sans attacher le débogueur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="ac30e-248">Le fichier *wwwroot/index.html* est délivré sous `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="ac30e-249">Si vous recevez des erreurs de compilation, essayez de fermer et de rouvrir la solution.</span><span class="sxs-lookup"><span data-stu-id="ac30e-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="ac30e-250">Ouvrez une autre instance de navigateur (n’importe quel navigateur).</span><span class="sxs-lookup"><span data-stu-id="ac30e-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="ac30e-251">Copiez l’URL de la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="ac30e-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ac30e-252">Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message** , puis cliquez sur le bouton **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="ac30e-253">Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac30e-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac30e-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ac30e-255">Exécutez Webpack en mode de *mise en production* en exécutant la commande suivante à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="ac30e-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="ac30e-256">Générez et exécutez l’application en exécutant la commande suivante à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="ac30e-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="ac30e-257">Le serveur web démarre l’application et la rend disponible sur localhost.</span><span class="sxs-lookup"><span data-stu-id="ac30e-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="ac30e-258">Ouvrez un navigateur et accédez à `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="ac30e-259">Le fichier *wwwroot/index.html* est présent.</span><span class="sxs-lookup"><span data-stu-id="ac30e-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="ac30e-260">Copiez l’URL à partir de la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="ac30e-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="ac30e-261">Ouvrez une autre instance de navigateur (n’importe quel navigateur).</span><span class="sxs-lookup"><span data-stu-id="ac30e-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="ac30e-262">Copiez l’URL de la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="ac30e-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ac30e-263">Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message** , puis cliquez sur le bouton **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="ac30e-264">Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![message affiché dans les deux fenêtres de navigateur](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="ac30e-266">Prérequis</span><span class="sxs-lookup"><span data-stu-id="ac30e-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac30e-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac30e-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ac30e-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**</span><span class="sxs-lookup"><span data-stu-id="ac30e-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="ac30e-269">Kit SDK .NET Core 2,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="ac30e-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="ac30e-270">[Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="ac30e-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac30e-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac30e-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="ac30e-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac30e-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="ac30e-273">Kit SDK .NET Core 2,2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="ac30e-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="ac30e-274">C# pour Visual Studio Code version 1.17.1 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="ac30e-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="ac30e-275">[Node.js](https://nodejs.org/) avec [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="ac30e-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="ac30e-276">Créer l’application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac30e-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac30e-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac30e-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ac30e-278">Configurez Visual Studio pour rechercher npm dans la variable d'environnement *PATH* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="ac30e-279">Par défaut, Visual Studio utilise la version de npm qu’il trouve dans son répertoire d’installation.</span><span class="sxs-lookup"><span data-stu-id="ac30e-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="ac30e-280">Suivez ces instructions dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="ac30e-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="ac30e-281">Accédez à **Outils** > **options** > **projets et solutions** > **Web Package Management** > **Outils Web externes** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools** .</span></span>
1. <span data-ttu-id="ac30e-282">Sélectionnez l’entrée *$(PATH)* dans la liste.</span><span class="sxs-lookup"><span data-stu-id="ac30e-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="ac30e-283">Cliquez sur la flèche vers le haut pour déplacer l’entrée à la deuxième position dans la liste.</span><span class="sxs-lookup"><span data-stu-id="ac30e-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Configuration de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="ac30e-285">La configuration de Visual Studio est terminée.</span><span class="sxs-lookup"><span data-stu-id="ac30e-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="ac30e-286">Nous allons maintenant créer le projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-286">It's time to create the project.</span></span>

1. <span data-ttu-id="ac30e-287">Utilisez l’option de menu **Fichier** > **Nouveau** > **Projet** et choisissez le modèle **Application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="ac30e-288">Nommez le projet *:::no-loc(SignalR)::: WebPack* , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-288">Name the project *:::no-loc(SignalR):::WebPack* , and select **Create** .</span></span>
1. <span data-ttu-id="ac30e-289">Sélectionnez *.NET Core* dans la liste déroulante du framework cible, puis *ASP.NET Core 2.2* dans la liste déroulante du sélecteur de framework.</span><span class="sxs-lookup"><span data-stu-id="ac30e-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="ac30e-290">Sélectionnez le modèle **vide** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-290">Select the **Empty** template, and select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac30e-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac30e-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ac30e-292">Exécutez la commande suivante dans le **Terminal intégré** :</span><span class="sxs-lookup"><span data-stu-id="ac30e-292">Run the following command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet new web -o :::no-loc(SignalR):::WebPack
```

<span data-ttu-id="ac30e-293">Une application Web ASP.NET Core vide, ciblant .NET Core, est créée dans un répertoire *:::no-loc(SignalR)::: WebPack* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *:::no-loc(SignalR):::WebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="ac30e-294">Configurer Webpack et TypeScript</span><span class="sxs-lookup"><span data-stu-id="ac30e-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="ac30e-295">Les étapes suivantes configurent la conversion de TypeScript en JavaScript et le regroupement des ressources côté client.</span><span class="sxs-lookup"><span data-stu-id="ac30e-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="ac30e-296">Exécutez la commande suivante dans la racine du projet pour créer un *package.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="ac30e-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="ac30e-297">Ajoutez la propriété en surbrillance au fichier *package.json* :</span><span class="sxs-lookup"><span data-stu-id="ac30e-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="ac30e-298">La définition de la propriété `private` sur `true` empêche les avertissements d’installation de package à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="ac30e-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="ac30e-299">Installez les packages npm nécessaires.</span><span class="sxs-lookup"><span data-stu-id="ac30e-299">Install the required npm packages.</span></span> <span data-ttu-id="ac30e-300">À partir de la racine du projet, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="ac30e-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="ac30e-301">Détails de commande à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="ac30e-301">Some command details to note:</span></span>

    * <span data-ttu-id="ac30e-302">Un numéro de version suit le signe `@` pour chaque nom de package.</span><span class="sxs-lookup"><span data-stu-id="ac30e-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="ac30e-303">npm installe ces versions de package spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ac30e-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="ac30e-304">L’option `-E` désactive le comportement par défaut de npm consistant à écrire des opérateurs de plage de [gestion sémantique des versions](https://semver.org/) dans *package.json* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json* .</span></span> <span data-ttu-id="ac30e-305">Par exemple, `"webpack": "4.29.3"` peut être utilisé à la place de `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="ac30e-306">Cette option empêche les mises à niveau involontaires vers des versions de package plus récentes.</span><span class="sxs-lookup"><span data-stu-id="ac30e-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="ac30e-307">Pour plus d’informations [, consultez NPM-install](https://docs.npmjs.com/cli/install) docs.</span><span class="sxs-lookup"><span data-stu-id="ac30e-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="ac30e-308">Remplacez la `scripts` propriété de l' *package.jssur* le fichier par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="ac30e-309">Explication des scripts :</span><span class="sxs-lookup"><span data-stu-id="ac30e-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="ac30e-310">`build`: Regroupe les ressources côté client en mode développement et surveille les modifications apportées aux fichiers.</span><span class="sxs-lookup"><span data-stu-id="ac30e-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="ac30e-311">L’observateur de fichiers force la regénération du regroupement chaque fois qu’un fichier projet change.</span><span class="sxs-lookup"><span data-stu-id="ac30e-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="ac30e-312">L’option `mode` désactive les optimisations de production, comme la minimisation de l’arborescence (tree shaking).</span><span class="sxs-lookup"><span data-stu-id="ac30e-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="ac30e-313">Utilisez uniquement `build` dans le développement.</span><span class="sxs-lookup"><span data-stu-id="ac30e-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="ac30e-314">`release`: Regroupe les ressources côté client en mode production.</span><span class="sxs-lookup"><span data-stu-id="ac30e-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="ac30e-315">`publish` : Exécute le script `release` pour regrouper les ressources côté client en mode de production.</span><span class="sxs-lookup"><span data-stu-id="ac30e-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="ac30e-316">La commande appelle la commande [publish](/dotnet/core/tools/dotnet-publish) de CLI .NET Core pour publier l’application.</span><span class="sxs-lookup"><span data-stu-id="ac30e-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="ac30e-317">Créez un fichier nommé *webpack.config.js* à la racine du projet, avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="ac30e-318">Le fichier précédent configure la compilation Webpack.</span><span class="sxs-lookup"><span data-stu-id="ac30e-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="ac30e-319">Détails de configuration à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="ac30e-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="ac30e-320">La propriété `output` remplace la valeur par défaut de *dist* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-320">The `output` property overrides the default value of *dist* .</span></span> <span data-ttu-id="ac30e-321">Le regroupement est émis dans le répertoire *wwwroot* à la place.</span><span class="sxs-lookup"><span data-stu-id="ac30e-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="ac30e-322">Le `resolve.extensions` tableau comprend *. js* pour importer le :::no-loc(SignalR)::: client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ac30e-322">The `resolve.extensions` array includes *.js* to import the :::no-loc(SignalR)::: client JavaScript.</span></span>

1. <span data-ttu-id="ac30e-323">Créez un répertoire *src* dans la racine du projet pour stocker les ressources côté client du projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="ac30e-324">Créez *src/index.html* avec le balisage suivant.</span><span class="sxs-lookup"><span data-stu-id="ac30e-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="ac30e-325">Le code HTML précédent définit le balisage réutilisable de la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="ac30e-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="ac30e-326">Créez un répertoire *src/css* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="ac30e-327">Son objectif est de stocker les fichiers *.css* du projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="ac30e-328">Créez *src/CSS/main. CSS* avec le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="ac30e-329">Le fichier *main.css* précédent définit le style de l’application.</span><span class="sxs-lookup"><span data-stu-id="ac30e-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="ac30e-330">Créez *src/tsconfig.jssur* avec le code JSON suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="ac30e-331">Le code précédent configure le compilateur TypeScript pour produire du code JavaScript compatible [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="ac30e-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="ac30e-332">Créez *src/index. TS* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="ac30e-333">Le code TypeScript précédent récupère les références aux éléments DOM et joint deux gestionnaires d’événements :</span><span class="sxs-lookup"><span data-stu-id="ac30e-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="ac30e-334">`keyup`: Cet événement se déclenche lorsque l’utilisateur tape dans la `tbMessage` zone de texte.</span><span class="sxs-lookup"><span data-stu-id="ac30e-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="ac30e-335">La fonction `send` est appelée quand l’utilisateur appuie sur la touche **Entrée** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="ac30e-336">`click` : Cet événement se déclenche quand l’utilisateur clique sur le bouton **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="ac30e-337">La fonction `send` est appelée.</span><span class="sxs-lookup"><span data-stu-id="ac30e-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="ac30e-338">Configurer l’application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac30e-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="ac30e-339">Le code fourni dans la méthode `Startup.Configure` affiche *Hello World!* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-339">The code provided in the `Startup.Configure` method displays *Hello World!* .</span></span> <span data-ttu-id="ac30e-340">Remplacez l’appel de méthode `app.Run` par des appels à [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="ac30e-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="ac30e-341">Le code précédent permet au serveur de localiser et traiter le fichier *index.html* , que l’utilisateur entre son URL complète ou l’URL racine de l’application web.</span><span class="sxs-lookup"><span data-stu-id="ac30e-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="ac30e-342">Appelez [Add :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_:::no-loc(SignalR):::DependencyInjectionExtensions_Add:::no-loc(SignalR):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ac30e-342">Call [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_:::no-loc(SignalR):::DependencyInjectionExtensions_Add:::no-loc(SignalR):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ac30e-343">Il ajoute les :::no-loc(SignalR)::: services au projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-343">It adds the :::no-loc(SignalR)::: services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_Add:::no-loc(SignalR):::)]

1. <span data-ttu-id="ac30e-344">Mappez une route */hub* au hub `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="ac30e-345">Ajoutez les lignes suivantes à la fin de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="ac30e-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_Use:::no-loc(SignalR):::)]

1. <span data-ttu-id="ac30e-346">Créez un répertoire *Hubs* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-346">Create a new directory, called *Hubs* , in the project root.</span></span> <span data-ttu-id="ac30e-347">Son objectif est de stocker le :::no-loc(SignalR)::: Hub, créé à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="ac30e-347">Its purpose is to store the :::no-loc(SignalR)::: hub, which is created in the next step.</span></span>

1. <span data-ttu-id="ac30e-348">Créez un hub *Hubs/ChatHub.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac30e-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="ac30e-349">Ajoutez le code suivant en haut du fichier *Startup.cs* pour résoudre la référence de `ChatHub` :</span><span class="sxs-lookup"><span data-stu-id="ac30e-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="ac30e-350">Activer la communication entre le client et le serveur</span><span class="sxs-lookup"><span data-stu-id="ac30e-350">Enable client and server communication</span></span>

<span data-ttu-id="ac30e-351">Actuellement, l’application affiche un formulaire simple pour envoyer des messages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="ac30e-352">Rien ne se produit quand vous essayez de le faire.</span><span class="sxs-lookup"><span data-stu-id="ac30e-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="ac30e-353">Le serveur écoute une route spécifique, mais ne fait rien avec les messages envoyés.</span><span class="sxs-lookup"><span data-stu-id="ac30e-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="ac30e-354">Exécutez la commande suivante à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="ac30e-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="ac30e-355">La commande précédente installe le [ :::no-loc(SignalR)::: client de machine à écrire](https://www.npmjs.com/package/@microsoft/signalr), ce qui permet au client d’envoyer des messages au serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-355">The preceding command installs the [:::no-loc(SignalR)::: TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="ac30e-356">Ajoutez le code en surbrillance au fichier *src/index.ts* :</span><span class="sxs-lookup"><span data-stu-id="ac30e-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="ac30e-357">Le code précédent prend en charge la réception de messages du serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="ac30e-358">La classe `HubConnectionBuilder` crée un générateur pour configurer la connexion de serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="ac30e-359">La fonction `withUrl` configure l’URL du hub.</span><span class="sxs-lookup"><span data-stu-id="ac30e-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="ac30e-360">:::no-loc(SignalR)::: permet l’échange de messages entre un client et un serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-360">:::no-loc(SignalR)::: enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="ac30e-361">Chaque message a un nom spécifique.</span><span class="sxs-lookup"><span data-stu-id="ac30e-361">Each message has a specific name.</span></span> <span data-ttu-id="ac30e-362">Par exemple, les messages portant le nom `messageReceived` peuvent exécuter la logique responsable de l’affichage du nouveau message dans la zone de messages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="ac30e-363">L’écoute d’un message spécifique peut être effectuée au moyen de la fonction `on`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="ac30e-364">Vous pouvez écouter n’importe quel nombre de noms de message.</span><span class="sxs-lookup"><span data-stu-id="ac30e-364">You can listen to any number of message names.</span></span> <span data-ttu-id="ac30e-365">Vous pouvez aussi passer des paramètres au message, comme le nom de l’auteur et le contenu du message reçu.</span><span class="sxs-lookup"><span data-stu-id="ac30e-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="ac30e-366">Dès que le client reçoit un message, un élément `div` est créé avec le nom de l’auteur et le contenu du message dans son attribut `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="ac30e-367">Le nouveau message est ajouté à l’élément principal qui `div` affiche les messages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="ac30e-368">Maintenant que le client peut recevoir un message, configurez-le pour en envoyer.</span><span class="sxs-lookup"><span data-stu-id="ac30e-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="ac30e-369">Ajoutez le code en surbrillance au fichier *src/index.ts* :</span><span class="sxs-lookup"><span data-stu-id="ac30e-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="ac30e-370">L’envoi d’un message au moyen de la connexion WebSocket nécessite l’appel de la méthode `send`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="ac30e-371">Le premier paramètre de la méthode est le nom du message.</span><span class="sxs-lookup"><span data-stu-id="ac30e-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="ac30e-372">Les données du message se trouvent dans les autres paramètres.</span><span class="sxs-lookup"><span data-stu-id="ac30e-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="ac30e-373">Dans cet exemple, un message identifié comme `newMessage` est envoyé au serveur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="ac30e-374">Le message se compose du nom d’utilisateur et de l’entrée de l’utilisateur dans une zone de texte.</span><span class="sxs-lookup"><span data-stu-id="ac30e-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="ac30e-375">Si l’envoi fonctionne, la valeur de la zone de texte est effacée.</span><span class="sxs-lookup"><span data-stu-id="ac30e-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="ac30e-376">Ajoutez la méthode `NewMessage` à la classe `ChatHub` :</span><span class="sxs-lookup"><span data-stu-id="ac30e-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="ac30e-377">Le code précédent diffuse les messages reçus à tous les utilisateurs connectés, une fois que le serveur les reçoit.</span><span class="sxs-lookup"><span data-stu-id="ac30e-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="ac30e-378">Vous n’avez pas besoin d’appeler une méthode générique `on` pour recevoir tous les messages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="ac30e-379">Il vous suffit d’avoir une méthode du même nom que le message.</span><span class="sxs-lookup"><span data-stu-id="ac30e-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="ac30e-380">Dans cet exemple, le client TypeScript envoie un message identifié comme `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="ac30e-381">La méthode C# `NewMessage` attend les données envoyées par le client.</span><span class="sxs-lookup"><span data-stu-id="ac30e-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="ac30e-382">Un appel est effectué sur [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) sur les [clients. All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="ac30e-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="ac30e-383">Les messages reçus sont envoyés à tous les clients connectés au hub.</span><span class="sxs-lookup"><span data-stu-id="ac30e-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="ac30e-384">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="ac30e-384">Test the app</span></span>

<span data-ttu-id="ac30e-385">Vérifiez que l’application fonctionne avec les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="ac30e-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ac30e-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac30e-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ac30e-387">Exécuter Webpack en mode de *mise en production* .</span><span class="sxs-lookup"><span data-stu-id="ac30e-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="ac30e-388">À l’aide de la fenêtre de **console du gestionnaire de package** , exécutez la commande suivante dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ac30e-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="ac30e-389">Si vous ne vous trouvez pas à la racine du projet, tapez `cd :::no-loc(SignalR):::WebPack` avant d’entrer la commande.</span><span class="sxs-lookup"><span data-stu-id="ac30e-389">If you are not in the project root, enter `cd :::no-loc(SignalR):::WebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="ac30e-390">Sélectionnez **Déboguer**  >  **exécuter sans débogage** pour lancer l’application dans un navigateur sans attacher le débogueur.</span><span class="sxs-lookup"><span data-stu-id="ac30e-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="ac30e-391">Le fichier *wwwroot/index.html* est délivré sous `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="ac30e-392">Ouvrez une autre instance de navigateur (n’importe quel navigateur).</span><span class="sxs-lookup"><span data-stu-id="ac30e-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="ac30e-393">Copiez l’URL de la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="ac30e-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ac30e-394">Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message** , puis cliquez sur le bouton **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="ac30e-395">Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ac30e-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ac30e-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ac30e-397">Exécutez Webpack en mode de *mise en production* en exécutant la commande suivante à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="ac30e-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="ac30e-398">Générez et exécutez l’application en exécutant la commande suivante à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="ac30e-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="ac30e-399">Le serveur web démarre l’application et la rend disponible sur localhost.</span><span class="sxs-lookup"><span data-stu-id="ac30e-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="ac30e-400">Ouvrez un navigateur et accédez à `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="ac30e-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="ac30e-401">Le fichier *wwwroot/index.html* est présent.</span><span class="sxs-lookup"><span data-stu-id="ac30e-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="ac30e-402">Copiez l’URL à partir de la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="ac30e-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="ac30e-403">Ouvrez une autre instance de navigateur (n’importe quel navigateur).</span><span class="sxs-lookup"><span data-stu-id="ac30e-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="ac30e-404">Copiez l’URL de la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="ac30e-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ac30e-405">Choisissez un navigateur, tapez quelque chose dans la zone de texte **Message** , puis cliquez sur le bouton **Envoyer** .</span><span class="sxs-lookup"><span data-stu-id="ac30e-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="ac30e-406">Le nom unique de l’utilisateur et le message sont affichés instantanément dans les deux pages.</span><span class="sxs-lookup"><span data-stu-id="ac30e-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![message affiché dans les deux fenêtres de navigateur](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ac30e-408">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ac30e-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
