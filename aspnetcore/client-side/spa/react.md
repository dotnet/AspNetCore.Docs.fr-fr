---
title: Utiliser le modèle de projet React avec ASP.NET Core
author: SteveSandersonMS
description: Découvrez comment démarrer avec le modèle de projet d’application monopage ASP.NET Core pour React et create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
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
uid: spa/react
ms.openlocfilehash: 6c3539d96329489189f49a3af0c718791824be6b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054448"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="4fc77-103">Utiliser le modèle de projet React avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4fc77-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="4fc77-104">Le modèle de projet React mis à jour fournit un point de départ pratique pour les applications ASP.NET Core utilisant les conventions React et [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) pour implémenter une interface utilisateur (IU) côté client enrichie.</span><span class="sxs-lookup"><span data-stu-id="4fc77-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="4fc77-105">Le modèle revient à créer un projet ASP.NET Core faisant office de backend d’API et un projet React CRA standard en guise d’interface utilisateur, qu’il héberge tous deux dans un projet d’application unique pouvant être généré et publié en tant qu’unité unique.</span><span class="sxs-lookup"><span data-stu-id="4fc77-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="4fc77-106">Le modèle de projet REACT n’est pas destiné au rendu côté serveur (SSR).</span><span class="sxs-lookup"><span data-stu-id="4fc77-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="4fc77-107">Pour SSR avec REACT et Node.js, envisagez [Next.js](https://github.com/zeit/next.js/) ou [Razzle](https://github.com/jaredpalmer/razzle).</span><span class="sxs-lookup"><span data-stu-id="4fc77-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="4fc77-108">Créer une application</span><span class="sxs-lookup"><span data-stu-id="4fc77-108">Create a new app</span></span>

<span data-ttu-id="4fc77-109">Si ASP.NET Core 2.1 est installé, il est inutile d’installer le modèle de projet React.</span><span class="sxs-lookup"><span data-stu-id="4fc77-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="4fc77-110">Créez un projet à partir d’une invite de commandes à l’aide de la commande `dotnet new react` dans un répertoire vide.</span><span class="sxs-lookup"><span data-stu-id="4fc77-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="4fc77-111">Par exemple, les commandes suivantes créent l’application dans un répertoire *my-new-app* et basculent vers ce répertoire :</span><span class="sxs-lookup"><span data-stu-id="4fc77-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="4fc77-112">Exécutez l’application à partir de Visual Studio ou de CLI .NET Core :</span><span class="sxs-lookup"><span data-stu-id="4fc77-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fc77-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fc77-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4fc77-114">Ouvrez le fichier *.csproj* généré, puis exécutez l’application normalement à partir de là.</span><span class="sxs-lookup"><span data-stu-id="4fc77-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="4fc77-115">Le processus de génération restaure les dépendances npm à la première exécution, ce qui peut prendre plusieurs minutes.</span><span class="sxs-lookup"><span data-stu-id="4fc77-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="4fc77-116">Les générations suivantes sont beaucoup plus rapides.</span><span class="sxs-lookup"><span data-stu-id="4fc77-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fc77-117">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="4fc77-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4fc77-118">Vérifiez que vous avez une variable d’environnement `ASPNETCORE_Environment` ayant pour valeur `Development`.</span><span class="sxs-lookup"><span data-stu-id="4fc77-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="4fc77-119">Sur Windows (dans les invites non-PowerShell), exécutez `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="4fc77-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="4fc77-120">Sur Linux ou macOS, exécutez `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="4fc77-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="4fc77-121">Exécutez [dotnet build](/dotnet/core/tools/dotnet-build) pour vérifier que votre application se génère correctement.</span><span class="sxs-lookup"><span data-stu-id="4fc77-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="4fc77-122">À la première exécution, le processus de génération restaure les dépendances npm, ce qui peut prendre plusieurs minutes.</span><span class="sxs-lookup"><span data-stu-id="4fc77-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="4fc77-123">Les générations suivantes sont beaucoup plus rapides.</span><span class="sxs-lookup"><span data-stu-id="4fc77-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="4fc77-124">Exécutez [dotnet run](/dotnet/core/tools/dotnet-run) pour démarrer l’application.</span><span class="sxs-lookup"><span data-stu-id="4fc77-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="4fc77-125">Le modèle de projet crée une application ASP.NET Core et une application React.</span><span class="sxs-lookup"><span data-stu-id="4fc77-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="4fc77-126">L’application ASP.NET Core est destinée à être utilisée pour tous les aspects liés au serveur, tels que l’accès aux données et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4fc77-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="4fc77-127">L’application React, qui réside dans le sous-répertoire *ClientApp* , est destinée à être utilisée pour tout ce qui touche l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4fc77-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="4fc77-128">Ajouter des pages, des images, des styles, des modules, etc.</span><span class="sxs-lookup"><span data-stu-id="4fc77-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="4fc77-129">Le répertoire *ClientApp* est une application React CRA standard.</span><span class="sxs-lookup"><span data-stu-id="4fc77-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="4fc77-130">Pour plus d’informations, consultez la [documentation CRA](https://create-react-app.dev/docs/getting-started/) officielle.</span><span class="sxs-lookup"><span data-stu-id="4fc77-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="4fc77-131">Il existe de légères différences entre l’application React créée par ce modèle et celle créée par CRA ; toutefois, les fonctionnalités de l’application sont identiques.</span><span class="sxs-lookup"><span data-stu-id="4fc77-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="4fc77-132">L’application créée par le modèle contient une mise en page basée sur [Bootstrap](https://getbootstrap.com/) et un exemple de routage de base.</span><span class="sxs-lookup"><span data-stu-id="4fc77-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="4fc77-133">Installer les packages npm</span><span class="sxs-lookup"><span data-stu-id="4fc77-133">Install npm packages</span></span>

<span data-ttu-id="4fc77-134">Pour installer des packages npm tiers, utilisez une invite de commandes dans le sous-répertoire *ClientApp* .</span><span class="sxs-lookup"><span data-stu-id="4fc77-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="4fc77-135">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4fc77-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="4fc77-136">Publier et déployer</span><span class="sxs-lookup"><span data-stu-id="4fc77-136">Publish and deploy</span></span>

<span data-ttu-id="4fc77-137">Pendant le développement, l’application s’exécute en mode optimisé pour des raisons pratiques.</span><span class="sxs-lookup"><span data-stu-id="4fc77-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="4fc77-138">Par exemple, les bundles JavaScript incluent des mappages de sources (ce qui vous permet de voir votre code source d’origine pendant le débogage).</span><span class="sxs-lookup"><span data-stu-id="4fc77-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="4fc77-139">L’application se recompile et se recharge automatiquement en cas de modification des fichiers JavaScript, HTML et CSS sur le disque.</span><span class="sxs-lookup"><span data-stu-id="4fc77-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="4fc77-140">Dans un environnement de production, fournissez une version de votre application qui est optimisée pour les performances.</span><span class="sxs-lookup"><span data-stu-id="4fc77-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="4fc77-141">Ce comportement est configuré pour se produire automatiquement.</span><span class="sxs-lookup"><span data-stu-id="4fc77-141">This is configured to happen automatically.</span></span> <span data-ttu-id="4fc77-142">Quand vous publiez, la configuration de build émet une build transpilée réduite de votre code côté client.</span><span class="sxs-lookup"><span data-stu-id="4fc77-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="4fc77-143">Contrairement à la build de développement, la build de production n’a pas besoin que Node.js soit installé sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="4fc77-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="4fc77-144">Vous pouvez utiliser des [méthodes d’hébergement et de déploiement ASP.NET Core](xref:host-and-deploy/index) standard.</span><span class="sxs-lookup"><span data-stu-id="4fc77-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="4fc77-145">Exécuter le serveur CRA indépendamment</span><span class="sxs-lookup"><span data-stu-id="4fc77-145">Run the CRA server independently</span></span>

<span data-ttu-id="4fc77-146">Le projet est configuré pour démarrer sa propre instance du serveur de développement CRA en arrière-plan quand l’application ASP.NET Core démarre en mode de développement.</span><span class="sxs-lookup"><span data-stu-id="4fc77-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="4fc77-147">Ainsi, vous n’êtes pas obligé d’exécuter un serveur distinct manuellement.</span><span class="sxs-lookup"><span data-stu-id="4fc77-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="4fc77-148">Cette configuration par défaut présente un inconvénient.</span><span class="sxs-lookup"><span data-stu-id="4fc77-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="4fc77-149">Chaque fois que vous modifiez votre code C# et que votre application ASP.NET Core doit redémarrer, le serveur CRA redémarre.</span><span class="sxs-lookup"><span data-stu-id="4fc77-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="4fc77-150">Quelques secondes sont nécessaires avant de démarrer la sauvegarde.</span><span class="sxs-lookup"><span data-stu-id="4fc77-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="4fc77-151">Si vous apportez des modifications fréquentes au code C# et que vous ne souhaitez pas attendre que le serveur CRA redémarre, exécutez ce dernier en externe, indépendamment du processus ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4fc77-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="4fc77-152">Pour ce faire :</span><span class="sxs-lookup"><span data-stu-id="4fc77-152">To do so:</span></span>

1. <span data-ttu-id="4fc77-153">Ajoutez un fichier *. env* au sous-répertoire *ClientApp* avec le paramètre suivant :</span><span class="sxs-lookup"><span data-stu-id="4fc77-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="4fc77-154">Cela empêchera votre navigateur Web de s’ouvrir lors du démarrage du serveur arc en externe.</span><span class="sxs-lookup"><span data-stu-id="4fc77-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="4fc77-155">Dans une invite de commandes, basculez vers le sous-répertoire *ClientApp* et lancez le serveur de développement CRA :</span><span class="sxs-lookup"><span data-stu-id="4fc77-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="4fc77-156">Modifiez votre application ASP.NET Core afin qu’elle utilise l’instance de serveur CRA externe au lieu de lancer une instance propre.</span><span class="sxs-lookup"><span data-stu-id="4fc77-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="4fc77-157">Dans votre classe *Startup* , remplacez l’appel `spa.UseReactDevelopmentServer` par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="4fc77-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="4fc77-158">Quand vous démarrez votre application ASP.NET Core, celle-ci ne lance pas un serveur CRA.</span><span class="sxs-lookup"><span data-stu-id="4fc77-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="4fc77-159">L’instance que vous avez démarrée manuellement est utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="4fc77-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="4fc77-160">Cela lui permet de démarrer et de redémarrer plus rapidement.</span><span class="sxs-lookup"><span data-stu-id="4fc77-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="4fc77-161">Elle n’attend plus que votre application React soit systématiquement regénérée.</span><span class="sxs-lookup"><span data-stu-id="4fc77-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4fc77-162">« Rendu côté serveur » n’est pas une fonctionnalité prise en charge de ce modèle.</span><span class="sxs-lookup"><span data-stu-id="4fc77-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="4fc77-163">L’objectif de ce modèle est de respecter la parité avec « Create-REACT-App ».</span><span class="sxs-lookup"><span data-stu-id="4fc77-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="4fc77-164">Ainsi, les scénarios et les fonctionnalités qui ne sont pas inclus dans un projet « Create-REACT-App » (par exemple, SSR) ne sont pas pris en charge et sont laissés en tant qu’exercice pour l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4fc77-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4fc77-165">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4fc77-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
