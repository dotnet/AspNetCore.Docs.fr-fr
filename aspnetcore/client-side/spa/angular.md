---
title: Utiliser le modèle de projet Angular avec ASP.NET Core
author: SteveSandersonMS
description: Découvrez comment démarrer avec le modèle de projet d’application à page unique ASP.NET Core pour Angular et la CLI Angular.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
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
uid: spa/angular
ms.openlocfilehash: 2fff0d60b71bbbab9347dbe74cad023264247388
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054565"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="f8856-103">Utiliser le modèle de projet Angular avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8856-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="f8856-104">Le modèle de projet Angular mis à jour fournit un point de départ pratique pour les applications ASP.NET Core utilisant Angular et la CLI Angular pour implémenter une interface utilisateur (IU) côté client enrichie.</span><span class="sxs-lookup"><span data-stu-id="f8856-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="f8856-105">Le modèle est équivalent à la création d’un projet ASP.NET Core en tant que back-end d’API et d’un projet CLI Angular en tant qu’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f8856-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="f8856-106">Le modèle offre l’avantage d’héberger les deux types de projets dans un projet d’application unique.</span><span class="sxs-lookup"><span data-stu-id="f8856-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="f8856-107">Par conséquent, le projet d’application peut être généré et publié sous la forme d’une unité unique.</span><span class="sxs-lookup"><span data-stu-id="f8856-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="f8856-108">Créer une application</span><span class="sxs-lookup"><span data-stu-id="f8856-108">Create a new app</span></span>

<span data-ttu-id="f8856-109">Si ASP.NET Core 2.1 est installé, il est inutile d’installer le modèle de projet Angular.</span><span class="sxs-lookup"><span data-stu-id="f8856-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="f8856-110">Créez un projet à partir d’une invite de commandes à l’aide de la commande `dotnet new angular` dans un répertoire vide.</span><span class="sxs-lookup"><span data-stu-id="f8856-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="f8856-111">Par exemple, les commandes suivantes créent l’application dans un répertoire *my-new-app* et basculent vers ce répertoire :</span><span class="sxs-lookup"><span data-stu-id="f8856-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="f8856-112">Exécutez l’application à partir de Visual Studio ou de CLI .NET Core :</span><span class="sxs-lookup"><span data-stu-id="f8856-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8856-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8856-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="f8856-114">Ouvrez le fichier *.csproj* généré, puis exécutez l’application normalement à partir de là.</span><span class="sxs-lookup"><span data-stu-id="f8856-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="f8856-115">Le processus de génération restaure les dépendances npm à la première exécution, ce qui peut prendre plusieurs minutes.</span><span class="sxs-lookup"><span data-stu-id="f8856-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="f8856-116">Les générations suivantes sont beaucoup plus rapides.</span><span class="sxs-lookup"><span data-stu-id="f8856-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f8856-117">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f8856-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f8856-118">Vérifiez que vous avez une variable d’environnement `ASPNETCORE_Environment` ayant pour valeur `Development`.</span><span class="sxs-lookup"><span data-stu-id="f8856-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="f8856-119">Sur Windows (dans les invites non-PowerShell), exécutez `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="f8856-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="f8856-120">Sur Linux ou macOS, exécutez `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="f8856-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="f8856-121">Exécutez [dotnet build](/dotnet/core/tools/dotnet-build) pour vérifier que l’application se génère correctement.</span><span class="sxs-lookup"><span data-stu-id="f8856-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="f8856-122">À la première exécution, le processus de génération restaure les dépendances npm, ce qui peut prendre plusieurs minutes.</span><span class="sxs-lookup"><span data-stu-id="f8856-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="f8856-123">Les générations suivantes sont beaucoup plus rapides.</span><span class="sxs-lookup"><span data-stu-id="f8856-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="f8856-124">Exécutez [dotnet run](/dotnet/core/tools/dotnet-run) pour démarrer l’application.</span><span class="sxs-lookup"><span data-stu-id="f8856-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="f8856-125">Un message semblable au message suivant est journalisé :</span><span class="sxs-lookup"><span data-stu-id="f8856-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="f8856-126">Accédez à cette URL dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="f8856-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="f8856-127">L’application démarre une instance du serveur CLI Angular en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="f8856-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="f8856-128">Un message semblable au suivant est consigné : *ng Live serveur de développement écoute sur localhost : &lt; otherport &gt; , ouvrez un navigateur sur http://localhost:&lt ; otherport &gt; /* .</span><span class="sxs-lookup"><span data-stu-id="f8856-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/* .</span></span> <span data-ttu-id="f8856-129">Ignorez ce message&mdash;ce n’est **pas** l’URL de l’application ASP.NET Core et CLI Angular combinée.</span><span class="sxs-lookup"><span data-stu-id="f8856-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="f8856-130">Le modèle de projet crée une application ASP.NET Core et une application Angular.</span><span class="sxs-lookup"><span data-stu-id="f8856-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="f8856-131">L’application ASP.NET Core est destinée à être utilisée pour tous les aspects liés au serveur, tels que l’accès aux données et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="f8856-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="f8856-132">L’application Angular, qui réside dans le sous-répertoire *ClientApp* , est destinée à être utilisée pour tout ce qui touche l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f8856-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="f8856-133">Ajouter des pages, des images, des styles, des modules, etc.</span><span class="sxs-lookup"><span data-stu-id="f8856-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="f8856-134">Le répertoire *ClientApp* contient une application CLI Angular standard.</span><span class="sxs-lookup"><span data-stu-id="f8856-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="f8856-135">Pour plus d’informations, consultez la [documentation Angular](https://angular.io) officielle.</span><span class="sxs-lookup"><span data-stu-id="f8856-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="f8856-136">Il existe de légères différences entre l’application Angular créée par ce modèle et celle créée par la CLI Angular (via `ng new`) ; toutefois, les fonctionnalités de l’application sont identiques.</span><span class="sxs-lookup"><span data-stu-id="f8856-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="f8856-137">L’application créée par le modèle contient une mise en page basée sur [Bootstrap](https://getbootstrap.com/) et un exemple de routage de base.</span><span class="sxs-lookup"><span data-stu-id="f8856-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="f8856-138">Exécuter des commandes ng</span><span class="sxs-lookup"><span data-stu-id="f8856-138">Run ng commands</span></span>

<span data-ttu-id="f8856-139">Dans une invite de commandes, basculez vers le sous-répertoire *ClientApp*  :</span><span class="sxs-lookup"><span data-stu-id="f8856-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="f8856-140">Si l’outil `ng` est installé de manière globale, vous pouvez exécuter n’importe laquelle de ses commandes.</span><span class="sxs-lookup"><span data-stu-id="f8856-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="f8856-141">Par exemple, vous pouvez exécuter `ng lint`, `ng test` ou toute autre [commande CLI Angular](https://angular.io/cli).</span><span class="sxs-lookup"><span data-stu-id="f8856-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="f8856-142">Mais il est inutile d’exécuter `ng serve`, car votre application ASP.NET Core se charge de traiter les parties côté serveur et côté client de votre application.</span><span class="sxs-lookup"><span data-stu-id="f8856-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="f8856-143">En interne, elle utilise `ng serve` dans le développement.</span><span class="sxs-lookup"><span data-stu-id="f8856-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="f8856-144">Si l’outil `ng` n’est pas installé, exécutez `npm run ng` à la place.</span><span class="sxs-lookup"><span data-stu-id="f8856-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="f8856-145">Par exemple, vous pouvez exécuter `npm run ng lint` ou `npm run ng test`.</span><span class="sxs-lookup"><span data-stu-id="f8856-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="f8856-146">Installer les packages npm</span><span class="sxs-lookup"><span data-stu-id="f8856-146">Install npm packages</span></span>

<span data-ttu-id="f8856-147">Pour installer des packages npm tiers, utilisez une invite de commandes dans le sous-répertoire *ClientApp* .</span><span class="sxs-lookup"><span data-stu-id="f8856-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="f8856-148">Exemple :</span><span class="sxs-lookup"><span data-stu-id="f8856-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="f8856-149">Publier et déployer</span><span class="sxs-lookup"><span data-stu-id="f8856-149">Publish and deploy</span></span>

<span data-ttu-id="f8856-150">Pendant le développement, l’application s’exécute en mode optimisé pour des raisons pratiques.</span><span class="sxs-lookup"><span data-stu-id="f8856-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="f8856-151">Par exemple, les bundles JavaScript incluent des mappages de sources (ce qui vous permet de voir votre code TypeScript d’origine pendant le débogage).</span><span class="sxs-lookup"><span data-stu-id="f8856-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="f8856-152">L’application se recompile et se recharge automatiquement en cas de modification des fichiers TypeScript, HTML et CSS sur le disque.</span><span class="sxs-lookup"><span data-stu-id="f8856-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="f8856-153">Dans un environnement de production, fournissez une version de votre application qui est optimisée pour les performances.</span><span class="sxs-lookup"><span data-stu-id="f8856-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="f8856-154">Ce comportement est configuré pour se produire automatiquement.</span><span class="sxs-lookup"><span data-stu-id="f8856-154">This is configured to happen automatically.</span></span> <span data-ttu-id="f8856-155">Quand vous publiez, la configuration de build émet une build compilée AoT (Ahead-of-Time) réduite de votre code côté client.</span><span class="sxs-lookup"><span data-stu-id="f8856-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="f8856-156">Contrairement à la version de développement, la génération de production ne nécessite pas l’installation de Node.js sur le serveur (sauf si vous avez activé le rendu côté serveur (SSR)).</span><span class="sxs-lookup"><span data-stu-id="f8856-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="f8856-157">Vous pouvez utiliser des [méthodes d’hébergement et de déploiement ASP.NET Core](xref:host-and-deploy/index) standard.</span><span class="sxs-lookup"><span data-stu-id="f8856-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="f8856-158">Exécuter « ng serve » indépendamment</span><span class="sxs-lookup"><span data-stu-id="f8856-158">Run "ng serve" independently</span></span>

<span data-ttu-id="f8856-159">Le projet est configuré pour démarrer sa propre instance du serveur CLI Angular en arrière-plan quand l’application ASP.NET Core démarre en mode de développement.</span><span class="sxs-lookup"><span data-stu-id="f8856-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="f8856-160">Ainsi, vous n’êtes pas obligé d’exécuter un serveur distinct manuellement.</span><span class="sxs-lookup"><span data-stu-id="f8856-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="f8856-161">Cette configuration par défaut présente un inconvénient.</span><span class="sxs-lookup"><span data-stu-id="f8856-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="f8856-162">Chaque fois que vous modifiez votre code C# et que votre application ASP.NET Core doit redémarrer, le serveur CLI Angular redémarre.</span><span class="sxs-lookup"><span data-stu-id="f8856-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="f8856-163">Environ 10 secondes sont requises pour démarrer la sauvegarde.</span><span class="sxs-lookup"><span data-stu-id="f8856-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="f8856-164">Si vous apportez des modifications fréquentes au code C# et que vous ne souhaitez pas attendre que la CLI Angular redémarre, exécutez le serveur CLI Angular en externe, indépendamment du processus ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8856-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="f8856-165">Pour ce faire :</span><span class="sxs-lookup"><span data-stu-id="f8856-165">To do so:</span></span>

1. <span data-ttu-id="f8856-166">Dans une invite de commandes, basculez vers le sous-répertoire *ClientApp* et lancez le serveur de développement CLI Angular :</span><span class="sxs-lookup"><span data-stu-id="f8856-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="f8856-167">Utilisez `npm start` pour lancer le serveur de développement CLI Angular, et non `ng serve`, de sorte que la configuration dans *package.json* soit respectée.</span><span class="sxs-lookup"><span data-stu-id="f8856-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="f8856-168">Pour transmettre des paramètres supplémentaires au serveur CLI Angular, ajoutez-les à la ligne `scripts` pertinente dans votre fichier *package.json* .</span><span class="sxs-lookup"><span data-stu-id="f8856-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="f8856-169">Modifiez votre application ASP.NET Core afin qu’elle utilise l’instance CLI Angular externe au lieu de lancer une instance propre.</span><span class="sxs-lookup"><span data-stu-id="f8856-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="f8856-170">Dans votre classe *Startup* , remplacez l’appel `spa.UseAngularCliServer` par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="f8856-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="f8856-171">Quand vous démarrez votre application ASP.NET Core, elle ne lance pas un serveur CLI Angular.</span><span class="sxs-lookup"><span data-stu-id="f8856-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="f8856-172">L’instance que vous avez démarrée manuellement est utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="f8856-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="f8856-173">Cela lui permet de démarrer et de redémarrer plus rapidement.</span><span class="sxs-lookup"><span data-stu-id="f8856-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="f8856-174">Elle n’attend plus que votre CLI Angular regénère votre application cliente à chaque fois.</span><span class="sxs-lookup"><span data-stu-id="f8856-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="f8856-175">Transmettre des données à partir du code .NET dans le code TypeScript</span><span class="sxs-lookup"><span data-stu-id="f8856-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="f8856-176">Au cours du rendu côté serveur (SSR), vous pouvez souhaiter transmettre des données par requête à partir de votre application ASP.NET Core dans votre application Angular.</span><span class="sxs-lookup"><span data-stu-id="f8856-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="f8856-177">Par exemple, vous pouvez transmettre des :::no-loc(cookie)::: informations ou des éléments lus à partir d’une base de données.</span><span class="sxs-lookup"><span data-stu-id="f8856-177">For example, you could pass :::no-loc(cookie)::: information or something read from a database.</span></span> <span data-ttu-id="f8856-178">Pour ce faire, modifiez votre classe *Démarrer* .</span><span class="sxs-lookup"><span data-stu-id="f8856-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="f8856-179">Dans le rappel pour `UseSpaPrerendering`, définissez une valeur pour `options.SupplyData` telle que la suivante :</span><span class="sxs-lookup"><span data-stu-id="f8856-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="f8856-180">Le rappel `SupplyData` vous permet de transmettre des données sérialisables JSON, arbitraires, par requête (par exemple, chaînes, valeurs booléennes ou nombres).</span><span class="sxs-lookup"><span data-stu-id="f8856-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="f8856-181">Votre code *main.server.ts* les reçoit en tant que `params.data`.</span><span class="sxs-lookup"><span data-stu-id="f8856-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="f8856-182">Ainsi, l’exemple de code précédent transmet une valeur booléenne en tant que `params.data.isHttpsRequest` dans le rappel `createServerRenderer`.</span><span class="sxs-lookup"><span data-stu-id="f8856-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="f8856-183">Vous pouvez transmettre ceci à d’autres parties de votre application prises en charge par Angular.</span><span class="sxs-lookup"><span data-stu-id="f8856-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="f8856-184">Par exemple, consultez la façon dont *main.server.ts* transmet la valeur `BASE_URL` à un composant dont le constructeur est déclaré pour le recevoir.</span><span class="sxs-lookup"><span data-stu-id="f8856-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="f8856-185">Inconvénients du SSR</span><span class="sxs-lookup"><span data-stu-id="f8856-185">Drawbacks of SSR</span></span>

<span data-ttu-id="f8856-186">Toutes les applications ne bénéficient pas du rendu côté serveur (SSR).</span><span class="sxs-lookup"><span data-stu-id="f8856-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="f8856-187">Le principal avantage concerne les performances.</span><span class="sxs-lookup"><span data-stu-id="f8856-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="f8856-188">Les visiteurs consultant votre application via une connexion réseau lente ou sur des appareils mobiles lents voient l’interface utilisateur initiale rapidement, même si l’extraction ou l’analyse des bundles JavaScript prend du temps.</span><span class="sxs-lookup"><span data-stu-id="f8856-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="f8856-189">Toutefois, de nombreuses applications monopage sont principalement utilisées via des réseaux d’entreprise internes rapides sur des ordinateurs rapides où l’application s’affiche presque instantanément.</span><span class="sxs-lookup"><span data-stu-id="f8856-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="f8856-190">En même temps, il existe des inconvénients importants concernant l’activation du SSR.</span><span class="sxs-lookup"><span data-stu-id="f8856-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="f8856-191">Il ajoute une complexité au processus de développement.</span><span class="sxs-lookup"><span data-stu-id="f8856-191">It adds complexity to your development process.</span></span> <span data-ttu-id="f8856-192">Votre code doit s’exécuter dans deux environnements différents : côté client et côté serveur (dans un environnement Node.js appelé à partir d’ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="f8856-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="f8856-193">Voici quelques éléments à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="f8856-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="f8856-194">Le SSR requiert une installation Node.js sur vos serveurs de production.</span><span class="sxs-lookup"><span data-stu-id="f8856-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="f8856-195">C’est automatiquement le cas pour certains scénarios de déploiement, comme Azure App Services, mais pas pour d’autres, comme Azure Service Fabric.</span><span class="sxs-lookup"><span data-stu-id="f8856-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="f8856-196">L’activation de l’indicateur de génération `BuildServerSideRenderer` entraîne votre répertoire *node_modules* à publier.</span><span class="sxs-lookup"><span data-stu-id="f8856-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="f8856-197">Ce dossier contient plus de 20 000 fichiers, ce qui allonge le temps de déploiement.</span><span class="sxs-lookup"><span data-stu-id="f8856-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="f8856-198">Pour exécuter votre code dans un environnement Node.js, il ne peut pas s’appuyer sur l’existence d’API JavaScript spécifiques à un navigateur comme `window` ou `localStorage`.</span><span class="sxs-lookup"><span data-stu-id="f8856-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="f8856-199">Si votre code (ou une bibliothèque tierce que vous référencez) tente d’utiliser ces API, vous obtiendrez une erreur au cours du SSR.</span><span class="sxs-lookup"><span data-stu-id="f8856-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="f8856-200">Par exemple, n’utilisez pas jQuery, car il fait référence à des API spécifiques au navigateur dans de nombreux endroits.</span><span class="sxs-lookup"><span data-stu-id="f8856-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="f8856-201">Pour éviter les erreurs, vous devez éviter le SSR ou éviter les bibliothèques ou les API spécifiques au navigateur.</span><span class="sxs-lookup"><span data-stu-id="f8856-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="f8856-202">Vous pouvez encapsuler tous les appels à ces API dans des vérifications pour vous assurer qu’ils ne sont pas appelés au cours du SSR.</span><span class="sxs-lookup"><span data-stu-id="f8856-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="f8856-203">Par exemple, utilisez une vérification telle que la suivante dans le code JavaScript ou TypeScript :</span><span class="sxs-lookup"><span data-stu-id="f8856-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="f8856-204">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f8856-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
