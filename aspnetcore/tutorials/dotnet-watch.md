---
title: Développer des applications ASP.NET Core à l’aide d’un observateur de fichiers
author: rick-anderson
description: Ce tutoriel montre comment installer et utiliser l’outil Observateur de fichiers (dotnet watch) de l’interface de ligne de commande .NET Core dans une application ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060038"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="de255-103">Développer des applications ASP.NET Core à l’aide d’un observateur de fichiers</span><span class="sxs-lookup"><span data-stu-id="de255-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="de255-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="de255-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="de255-105">`dotnet watch` est un outil qui exécute une commande [CLI .net Core](/dotnet/core/tools) lorsque les fichiers sources changent.</span><span class="sxs-lookup"><span data-stu-id="de255-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="de255-106">Par exemple, un changement de fichier peut déclencher la compilation, l’exécution de tests ou le déploiement.</span><span class="sxs-lookup"><span data-stu-id="de255-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="de255-107">Ce tutoriel utilise une API web existante avec deux points de terminaison : un qui retourne une somme et un qui retourne un produit.</span><span class="sxs-lookup"><span data-stu-id="de255-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="de255-108">La méthode du produit comporte un bogue, qui est résolu dans ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="de255-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="de255-109">Téléchargez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="de255-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="de255-110">Elle se compose de deux projets : *WebApp* (API web ASP.NET Core) et *WebAppTests* (API de tests unitaires pour le web).</span><span class="sxs-lookup"><span data-stu-id="de255-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="de255-111">Dans une interface de commande, accédez au dossier *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="de255-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="de255-112">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="de255-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="de255-113">Vous pouvez utiliser `dotnet run --project <PROJECT>` pour spécifier un projet à exécuter.</span><span class="sxs-lookup"><span data-stu-id="de255-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="de255-114">Par exemple, `dotnet run --project WebApp` à la racine de l’exemple d’application aura également pour effet d’exécuter le projet *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="de255-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="de255-115">La sortie de la console affiche des messages semblables à ce qui suit (indiquant que l’application est en cours d’exécution et en attente de demandes) :</span><span class="sxs-lookup"><span data-stu-id="de255-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="de255-116">Dans un navigateur web, accédez à `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="de255-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="de255-117">Vous devez voir le résultat `9`.</span><span class="sxs-lookup"><span data-stu-id="de255-117">You should see the result of `9`.</span></span>

<span data-ttu-id="de255-118">Accédez à l’API du produit (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="de255-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="de255-119">Elle retourne `9`, et non pas `20` comme prévu.</span><span class="sxs-lookup"><span data-stu-id="de255-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="de255-120">Ce problème est résolu plus tard dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="de255-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="de255-121">Ajouter `dotnet watch` à un projet</span><span class="sxs-lookup"><span data-stu-id="de255-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="de255-122">L’outil Observateur de fichiers `dotnet watch` est inclus dans la version 2.1.300 du kit SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="de255-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="de255-123">Les étapes suivantes sont requises quand vous utilisez une version antérieure du kit SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="de255-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="de255-124">Ajoutez une référence de package `Microsoft.DotNet.Watcher.Tools` dans le fichier *.csproj*  :</span><span class="sxs-lookup"><span data-stu-id="de255-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="de255-125">Installez le package `Microsoft.DotNet.Watcher.Tools` en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="de255-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="de255-126">Exécuter les commandes de l’interface CLI de .NET Core avec `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="de255-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="de255-127">Toutes les [commandes de l’interface CLI de .NET Core](/dotnet/core/tools#cli-commands) peuvent être exécutées avec `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="de255-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="de255-128">Exemple :</span><span class="sxs-lookup"><span data-stu-id="de255-128">For example:</span></span>

| <span data-ttu-id="de255-129">Commande</span><span class="sxs-lookup"><span data-stu-id="de255-129">Command</span></span> | <span data-ttu-id="de255-130">Commande avec watch</span><span class="sxs-lookup"><span data-stu-id="de255-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="de255-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="de255-131">dotnet run</span></span> | <span data-ttu-id="de255-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="de255-132">dotnet watch run</span></span> |
| <span data-ttu-id="de255-133">dotnet Run-f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="de255-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="de255-134">exécution de dotnet Watch-f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="de255-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="de255-135">dotnet Run-f netcoreapp 3.1----Arg1</span><span class="sxs-lookup"><span data-stu-id="de255-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="de255-136">dotnet Watch Run-f netcoreapp 3.1----Arg1</span><span class="sxs-lookup"><span data-stu-id="de255-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="de255-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="de255-137">dotnet test</span></span> | <span data-ttu-id="de255-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="de255-138">dotnet watch test</span></span> |

<span data-ttu-id="de255-139">Exécutez `dotnet watch run` dans le dossier *WebApp* .</span><span class="sxs-lookup"><span data-stu-id="de255-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="de255-140">La sortie de la console indique que `watch` a démarré.</span><span class="sxs-lookup"><span data-stu-id="de255-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="de255-141">`dotnet watch run`L’exécution sur une application Web lance un navigateur qui navigue jusqu’à l’URL de l’application une fois que vous êtes prêt.</span><span class="sxs-lookup"><span data-stu-id="de255-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="de255-142">`dotnet watch` pour cela, il lit la sortie de la console de l’application et attend le message Ready affiché par <xref:Microsoft.AspNetCore.WebHost> .</span><span class="sxs-lookup"><span data-stu-id="de255-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="de255-143">`dotnet watch` actualise le navigateur lorsqu’il détecte des modifications apportées aux fichiers surveillés.</span><span class="sxs-lookup"><span data-stu-id="de255-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="de255-144">Pour ce faire, la commande Watch injecte un intergiciel (middleware) à l’application qui modifie les réponses HTML créées par l’application.</span><span class="sxs-lookup"><span data-stu-id="de255-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="de255-145">L’intergiciel ajoute un bloc de script JavaScript à la page qui permet `dotnet watch` d’indiquer au navigateur d’actualiser.</span><span class="sxs-lookup"><span data-stu-id="de255-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="de255-146">Actuellement, les modifications apportées à tous les fichiers surveillés, y compris le contenu statique tel que les fichiers. *HTML* et *. CSS* , provoquent la reconstruction de l’application.</span><span class="sxs-lookup"><span data-stu-id="de255-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="de255-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="de255-147">`dotnet watch`:</span></span>

* <span data-ttu-id="de255-148">Surveille uniquement les fichiers qui ont un impact sur les builds par défaut.</span><span class="sxs-lookup"><span data-stu-id="de255-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="de255-149">Tous les fichiers surveillés en plus (via la configuration) entraînent toujours une génération.</span><span class="sxs-lookup"><span data-stu-id="de255-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="de255-150">Pour plus d’informations sur la configuration, consultez la section relative à la [configuration de dotnet-Watch](#dotnet-watch-configuration) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="de255-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="de255-151">Vous pouvez utiliser `dotnet watch --project <PROJECT>` pour spécifier un projet à surveiller.</span><span class="sxs-lookup"><span data-stu-id="de255-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="de255-152">Par exemple, `dotnet watch --project WebApp run` à la racine de l’exemple d’application aura également pour effet d’exécuter le projet *WebApp* et d’en effectuer le suivi.</span><span class="sxs-lookup"><span data-stu-id="de255-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="de255-153">Apporter des modifications avec `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="de255-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="de255-154">Vérifiez que `dotnet watch` est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="de255-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="de255-155">Corrigez le bogue présent dans la méthode `Product` de *MathController.cs* afin qu’elle retourne le produit et non la somme :</span><span class="sxs-lookup"><span data-stu-id="de255-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="de255-156">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="de255-156">Save the file.</span></span> <span data-ttu-id="de255-157">La sortie de la console indique que `dotnet watch` a détecté un changement de fichier et a redémarré l’application.</span><span class="sxs-lookup"><span data-stu-id="de255-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="de255-158">Vérifiez que `http://localhost:<port number>/api/math/product?a=4&b=5` retourne le résultat correct.</span><span class="sxs-lookup"><span data-stu-id="de255-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="de255-159">Exécuter les tests à l’aide de `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="de255-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="de255-160">Changez la méthode `Product` de *MathController.cs* pour qu’elle retourne à nouveau la somme.</span><span class="sxs-lookup"><span data-stu-id="de255-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="de255-161">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="de255-161">Save the file.</span></span>
1. <span data-ttu-id="de255-162">Dans une interface de commande, accédez au dossier *WebAppTests* .</span><span class="sxs-lookup"><span data-stu-id="de255-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="de255-163">Exécutez [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="de255-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="de255-164">Exécutez `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="de255-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="de255-165">Sa sortie indique qu’un test a échoué et que l’observateur est en attente de changement de fichier :</span><span class="sxs-lookup"><span data-stu-id="de255-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="de255-166">Corrigez le code de la méthode `Product` afin qu’elle retourne le produit.</span><span class="sxs-lookup"><span data-stu-id="de255-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="de255-167">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="de255-167">Save the file.</span></span>

<span data-ttu-id="de255-168">`dotnet watch` détecte le changement de fichier et réexécute les tests.</span><span class="sxs-lookup"><span data-stu-id="de255-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="de255-169">La sortie de la console indique que les tests ont réussi.</span><span class="sxs-lookup"><span data-stu-id="de255-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="de255-170">Personnaliser la liste de fichiers à observer</span><span class="sxs-lookup"><span data-stu-id="de255-170">Customize files list to watch</span></span>

<span data-ttu-id="de255-171">Par défaut, `dotnet-watch` effectue le suivi de tous les fichiers qui correspondent aux modèles Glob suivants :</span><span class="sxs-lookup"><span data-stu-id="de255-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="de255-172">Vous pouvez ajouter plusieurs éléments à la liste de suivi en modifiant le fichier *.csproj* .</span><span class="sxs-lookup"><span data-stu-id="de255-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="de255-173">Vous pouvez spécifier des éléments individuellement ou en utilisant des modèles Glob.</span><span class="sxs-lookup"><span data-stu-id="de255-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="de255-174">Exclusion de fichiers à observer</span><span class="sxs-lookup"><span data-stu-id="de255-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="de255-175">`dotnet-watch` peut être configuré pour ignorer ses paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="de255-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="de255-176">Pour ignorer des fichiers spécifiques, ajoutez l’attribut `Watch="false"` à la définition d’un élément dans le fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="de255-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="de255-177">Personnaliser les projets de suivi</span><span class="sxs-lookup"><span data-stu-id="de255-177">Custom watch projects</span></span>

<span data-ttu-id="de255-178">`dotnet-watch` n’est pas limité aux projets C#.</span><span class="sxs-lookup"><span data-stu-id="de255-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="de255-179">Vous pouvez créer des projets de suivi personnalisés pour gérer différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="de255-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="de255-180">Considérez l’organisation de projets suivante :</span><span class="sxs-lookup"><span data-stu-id="de255-180">Consider the following project layout:</span></span>

* <span data-ttu-id="de255-181">**test**</span><span class="sxs-lookup"><span data-stu-id="de255-181">**test/**</span></span>
  * <span data-ttu-id="de255-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="de255-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="de255-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="de255-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="de255-184">Si l’objectif est d’observer les deux projets, créez un fichier projet personnalisé configuré à cette fin :</span><span class="sxs-lookup"><span data-stu-id="de255-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="de255-185">Pour démarrer l’observation de fichiers sur les deux projets, passez au dossier *test* .</span><span class="sxs-lookup"><span data-stu-id="de255-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="de255-186">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="de255-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="de255-187">VSTest s’exécute quand n’importe quel fichier change dans un des projets de test.</span><span class="sxs-lookup"><span data-stu-id="de255-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="de255-188">configuration de dotnet-Watch</span><span class="sxs-lookup"><span data-stu-id="de255-188">dotnet-watch configuration</span></span>

<span data-ttu-id="de255-189">Certaines options de configuration peuvent être passées à `dotnet watch` via des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de255-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="de255-190">Les variables disponibles sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="de255-190">The available variables are:</span></span>

| <span data-ttu-id="de255-191">Paramètre</span><span class="sxs-lookup"><span data-stu-id="de255-191">Setting</span></span>  | <span data-ttu-id="de255-192">Description</span><span class="sxs-lookup"><span data-stu-id="de255-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="de255-193">Si la valeur est « 1 » ou « true », `dotnet watch` utilise un observateur de fichiers d’interrogation au lieu de CoreFx `FileSystemWatcher` .</span><span class="sxs-lookup"><span data-stu-id="de255-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="de255-194">Utilisé lors de l’observation de fichiers sur des partages réseau ou des volumes montés par l’amarrage.</span><span class="sxs-lookup"><span data-stu-id="de255-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="de255-195">Par défaut, `dotnet watch` optimise la génération en évitant certaines opérations, telles que l’exécution de la restauration ou la réévaluation de l’ensemble des fichiers surveillés à chaque modification de fichier.</span><span class="sxs-lookup"><span data-stu-id="de255-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="de255-196">Si la valeur est « 1 » ou « true », ces optimisations sont désactivées.</span><span class="sxs-lookup"><span data-stu-id="de255-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="de255-197">`dotnet watch run` tente de lancer des navigateurs pour les applications Web avec `launchBrowser` configuré dans *launchSettings.jssur* .</span><span class="sxs-lookup"><span data-stu-id="de255-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json* .</span></span> <span data-ttu-id="de255-198">Si la valeur est « 1 » ou « true », ce comportement est supprimé.</span><span class="sxs-lookup"><span data-stu-id="de255-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="de255-199">`dotnet watch run` tente d’actualiser les navigateurs lorsqu’il détecte des modifications de fichier.</span><span class="sxs-lookup"><span data-stu-id="de255-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="de255-200">Si la valeur est « 1 » ou « true », ce comportement est supprimé.</span><span class="sxs-lookup"><span data-stu-id="de255-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="de255-201">Ce comportement est également supprimé si `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` est défini.</span><span class="sxs-lookup"><span data-stu-id="de255-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="de255-202">`dotnet-watch` dans GitHub</span><span class="sxs-lookup"><span data-stu-id="de255-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="de255-203">`dotnet-watch` fait partie du référentiel GitHub [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="de255-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
