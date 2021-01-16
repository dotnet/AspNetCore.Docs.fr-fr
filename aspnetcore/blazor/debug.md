---
title: ASP.NET Core de débogage Blazor WebAssembly
author: guardrex
description: Découvrez comment déboguer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 5bdfcc5660b4c897d3552d4cf25e43dade71541c
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252511"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="19a6e-103">ASP.NET Core de débogage Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="19a6e-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="19a6e-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="19a6e-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="19a6e-105">Blazor WebAssembly les applications peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome).</span><span class="sxs-lookup"><span data-stu-id="19a6e-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="19a6e-106">Vous pouvez également déboguer votre application à l’aide des environnements de développement intégré (IDE) suivants :</span><span class="sxs-lookup"><span data-stu-id="19a6e-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="19a6e-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19a6e-107">Visual Studio</span></span>
* <span data-ttu-id="19a6e-108">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="19a6e-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="19a6e-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19a6e-109">Visual Studio Code</span></span>

<span data-ttu-id="19a6e-110">Les scénarios disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="19a6e-110">Available scenarios include:</span></span>

* <span data-ttu-id="19a6e-111">Définir et supprimer des points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="19a6e-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="19a6e-112">Exécutez l’application avec prise en charge du débogage dans les IDE.</span><span class="sxs-lookup"><span data-stu-id="19a6e-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="19a6e-113">Pas à pas détaillé dans le code.</span><span class="sxs-lookup"><span data-stu-id="19a6e-113">Single-step through the code.</span></span>
* <span data-ttu-id="19a6e-114">Reprendre l’exécution du code à l’aide d’un raccourci clavier dans IDE.</span><span class="sxs-lookup"><span data-stu-id="19a6e-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="19a6e-115">Dans la fenêtre variables *locales* , observez les valeurs des variables locales.</span><span class="sxs-lookup"><span data-stu-id="19a6e-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="19a6e-116">Consultez la pile des appels, y compris les chaînes d’appels entre JavaScript et .NET.</span><span class="sxs-lookup"><span data-stu-id="19a6e-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="19a6e-117">Pour le moment, vous *ne pouvez pas*:</span><span class="sxs-lookup"><span data-stu-id="19a6e-117">For now, you *can't*:</span></span>

* <span data-ttu-id="19a6e-118">Arrêt sur les exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="19a6e-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="19a6e-119">Atteindre les points d’arrêt pendant le démarrage de l’application avant l’exécution du proxy de débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="19a6e-120">Cela comprend les points d’arrêt dans `Program.Main` ( `Program.cs` ) et les points d’arrêt dans les [ `OnInitialized{Async}` méthodes](xref:blazor/components/lifecycle#component-initialization-methods) des composants qui sont chargés par la première page demandée à partir de l’application.</span><span class="sxs-lookup"><span data-stu-id="19a6e-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="19a6e-121">Déboguez dans des scénarios non locaux (par exemple, [sous-système Windows pour Linux (WSL)](/windows/wsl/) ou [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span><span class="sxs-lookup"><span data-stu-id="19a6e-121">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>
* <span data-ttu-id="19a6e-122">Régénérez automatiquement l' `*Server*` application principale d’une solution hébergée Blazor pendant le débogage, par exemple en exécutant l’application avec [`dotnet watch run`](xref:tutorials/dotnet-watch) .</span><span class="sxs-lookup"><span data-stu-id="19a6e-122">Automatically rebuild the backend `*Server*` app of a hosted Blazor solution during debugging, for example by running the app with [`dotnet watch run`](xref:tutorials/dotnet-watch).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="19a6e-123">Prérequis</span><span class="sxs-lookup"><span data-stu-id="19a6e-123">Prerequisites</span></span>

<span data-ttu-id="19a6e-124">Le débogage requiert l’un des navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="19a6e-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="19a6e-125">Google Chrome (version 70 ou ultérieure) (par défaut)</span><span class="sxs-lookup"><span data-stu-id="19a6e-125">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="19a6e-126">Microsoft Edge (version 80 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="19a6e-126">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="19a6e-127">Assurez-vous que les pare-feu ou les proxys ne bloquent pas la communication avec le proxy de débogage ( `NodeJS` processus).</span><span class="sxs-lookup"><span data-stu-id="19a6e-127">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="19a6e-128">Pour plus d’informations, consultez la section [configuration du pare-feu](#firewall-configuration) .</span><span class="sxs-lookup"><span data-stu-id="19a6e-128">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="19a6e-129">Visual Studio pour Mac nécessite la version 8,8 (Build 1532) ou version ultérieure :</span><span class="sxs-lookup"><span data-stu-id="19a6e-129">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="19a6e-130">Installez la dernière version de Visual Studio pour Mac en sélectionnant le bouton **télécharger Visual Studio pour Mac** sur [Microsoft : Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="19a6e-130">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="19a6e-131">Sélectionnez le canal de l' *Aperçu* dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="19a6e-131">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="19a6e-132">Pour plus d’informations, consultez [installer une version préliminaire de Visual Studio pour Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="19a6e-132">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="19a6e-133">Apple Safari sur macOS n’est pas pris en charge actuellement.</span><span class="sxs-lookup"><span data-stu-id="19a6e-133">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="19a6e-134">Activer le débogage</span><span class="sxs-lookup"><span data-stu-id="19a6e-134">Enable debugging</span></span>

<span data-ttu-id="19a6e-135">Pour activer le débogage d’une Blazor WebAssembly application existante, mettez à jour le `launchSettings.json` fichier dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :</span><span class="sxs-lookup"><span data-stu-id="19a6e-135">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="19a6e-136">Une fois mis à jour, le `launchSettings.json` fichier doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="19a6e-136">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="19a6e-137">La `inspectUri` propriété :</span><span class="sxs-lookup"><span data-stu-id="19a6e-137">The `inspectUri` property:</span></span>

* <span data-ttu-id="19a6e-138">Permet à l’IDE de détecter que l’application est une Blazor WebAssembly application.</span><span class="sxs-lookup"><span data-stu-id="19a6e-138">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="19a6e-139">Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.</span><span class="sxs-lookup"><span data-stu-id="19a6e-139">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="19a6e-140">Les valeurs d’espace réservé pour le protocole WebSockets ( `wsProtocol` ), l’hôte ( `url.hostname` ), le port ( `url.port` ) et l’URI de l’inspecteur sur le navigateur lancé ( `browserInspectUri` ) sont fournies par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="19a6e-140">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19a6e-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19a6e-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19a6e-142">Pour déboguer une Blazor WebAssembly application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="19a6e-142">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="19a6e-143">Créez une nouvelle ASP.NET Core application hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="19a6e-143">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="19a6e-144">Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-144">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="19a6e-145">**Exécuter sans débogage** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="19a6e-145">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="19a6e-146">Lorsque l’application est exécutée dans la configuration Debug, le débogage entraîne toujours une réduction des performances minime.</span><span class="sxs-lookup"><span data-stu-id="19a6e-146">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="19a6e-147">Dans l' `*Client*` application, définissez un point d’arrêt sur la `currentCount++;` ligne dans `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-147">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="19a6e-148">Dans le navigateur, accédez à la `Counter` page et sélectionnez le bouton **Click Me** pour atteindre le point d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="19a6e-148">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="19a6e-149">Dans Visual Studio, examinez la valeur du `currentCount` champ dans la fenêtre **variables locales** .</span><span class="sxs-lookup"><span data-stu-id="19a6e-149">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="19a6e-150">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.</span><span class="sxs-lookup"><span data-stu-id="19a6e-150">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="19a6e-151">Lors du débogage d’une Blazor WebAssembly application, vous pouvez également déboguer le code serveur :</span><span class="sxs-lookup"><span data-stu-id="19a6e-151">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="19a6e-152">Définissez un point d’arrêt dans la `Pages/FetchData.razor` page de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="19a6e-152">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="19a6e-153">Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="19a6e-153">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="19a6e-154">Accédez à la `Fetch Data` page pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il ne envoie une requête HTTP au serveur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-154">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="19a6e-155">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-155">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="19a6e-156">Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-156">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="19a6e-157">Les points d’arrêt ne sont **pas** atteints pendant le démarrage de l’application avant l’exécution du proxy de débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-157">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="19a6e-158">Cela comprend les points d’arrêt dans `Program.Main` ( `Program.cs` ) et les points d’arrêt dans les [ `OnInitialized{Async}` méthodes](xref:blazor/components/lifecycle#component-initialization-methods) des composants qui sont chargés par la première page demandée à partir de l’application.</span><span class="sxs-lookup"><span data-stu-id="19a6e-158">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="19a6e-159">Si l’application est hébergée dans un [chemin d’accès de base](xref:blazor/host-and-deploy/index#app-base-path) différent de `/` , mettez à jour les propriétés suivantes dans `Properties/launchSettings.json` pour refléter le chemin de base de l’application :</span><span class="sxs-lookup"><span data-stu-id="19a6e-159">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="19a6e-160">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="19a6e-160">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="19a6e-161">`inspectUri` de chaque profil :</span><span class="sxs-lookup"><span data-stu-id="19a6e-161">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="19a6e-162">Les espaces réservés dans les paramètres précédents :</span><span class="sxs-lookup"><span data-stu-id="19a6e-162">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="19a6e-163">`{INSECURE PORT}`: Le port non sécurisé.</span><span class="sxs-lookup"><span data-stu-id="19a6e-163">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="19a6e-164">Une valeur aléatoire est fournie par défaut, mais un port personnalisé est autorisé.</span><span class="sxs-lookup"><span data-stu-id="19a6e-164">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="19a6e-165">`{APP BASE PATH}`: Chemin d’accès de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="19a6e-165">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="19a6e-166">`{SECURE PORT}`: Le port sécurisé.</span><span class="sxs-lookup"><span data-stu-id="19a6e-166">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="19a6e-167">Une valeur aléatoire est fournie par défaut, mais un port personnalisé est autorisé.</span><span class="sxs-lookup"><span data-stu-id="19a6e-167">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="19a6e-168">`{PROFILE 1, 2, ... N}`: Lance les profils de paramètres.</span><span class="sxs-lookup"><span data-stu-id="19a6e-168">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="19a6e-169">En règle générale, une application spécifie plusieurs profils par défaut (par exemple, un profil pour IIS Express et un profil de projet, qui est utilisé par le serveur Kestrel).</span><span class="sxs-lookup"><span data-stu-id="19a6e-169">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="19a6e-170">Dans les exemples suivants, l’application est hébergée à l' `/OAT` aide d’un chemin d’accès de base d’application configuré dans `wwwroot/index.html` en tant que `<base href="/OAT/">` :</span><span class="sxs-lookup"><span data-stu-id="19a6e-170">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="19a6e-171">Pour plus d’informations sur l’utilisation d’un chemin d’accès de base d’application personnalisé pour les Blazor WebAssembly applications, consultez <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="19a6e-171">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="19a6e-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19a6e-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="19a6e-173">Déboguer autonome Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="19a6e-173">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="19a6e-174">Ouvrez l' Blazor WebAssembly application autonome dans vs code.</span><span class="sxs-lookup"><span data-stu-id="19a6e-174">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="19a6e-175">Vous pouvez recevoir une notification indiquant qu’une configuration supplémentaire est requise pour activer le débogage :</span><span class="sxs-lookup"><span data-stu-id="19a6e-175">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="19a6e-176">Une configuration supplémentaire est requise pour déboguer des Blazor WebAssembly applications.</span><span class="sxs-lookup"><span data-stu-id="19a6e-176">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="19a6e-177">Si vous recevez la notification :</span><span class="sxs-lookup"><span data-stu-id="19a6e-177">If you receive the notification:</span></span>

   * <span data-ttu-id="19a6e-178">Vérifiez que la dernière [extension C# pour Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) est installée.</span><span class="sxs-lookup"><span data-stu-id="19a6e-178">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="19a6e-179">Pour inspecter les extensions installées, ouvrez **Afficher** les  >  **Extensions** à partir de la barre de menus ou sélectionnez l’icône **Extensions** dans l’encadré **activité** .</span><span class="sxs-lookup"><span data-stu-id="19a6e-179">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="19a6e-180">Confirmez que le débogage de l’aperçu JavaScript est activé.</span><span class="sxs-lookup"><span data-stu-id="19a6e-180">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="19a6e-181">Ouvrez les paramètres à partir de la barre de menus (paramètres préférences de **fichiers**  >    >  ).</span><span class="sxs-lookup"><span data-stu-id="19a6e-181">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="19a6e-182">Recherchez à l’aide des mots clés `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-182">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="19a6e-183">Dans les résultats de la recherche, vérifiez que la case à cocher **Déboguer > JavaScript : utiliser l’aperçu** est activée.</span><span class="sxs-lookup"><span data-stu-id="19a6e-183">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="19a6e-184">Si l’option permettant d’activer le débogage de l’aperçu n’est pas présente, effectuez une mise à niveau vers la dernière version de VS Code ou installez l' [extension de débogueur JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1,46 ou antérieures).</span><span class="sxs-lookup"><span data-stu-id="19a6e-184">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="19a6e-185">Rechargez la fenêtre.</span><span class="sxs-lookup"><span data-stu-id="19a6e-185">Reload the window.</span></span>

1. <span data-ttu-id="19a6e-186">Démarrez le débogage à l’aide du raccourci clavier <kbd>F5</kbd> ou de l’élément de menu.</span><span class="sxs-lookup"><span data-stu-id="19a6e-186">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="19a6e-187">**Exécuter sans débogage** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="19a6e-187">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="19a6e-188">Lorsque l’application est exécutée dans la configuration Debug, le débogage entraîne toujours une réduction des performances minime.</span><span class="sxs-lookup"><span data-stu-id="19a6e-188">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="19a6e-189">Quand vous y êtes invité, sélectionnez l’option de **Blazor WebAssembly débogage** pour démarrer le débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-189">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="19a6e-190">L’application autonome est lancée et un navigateur de débogage est ouvert.</span><span class="sxs-lookup"><span data-stu-id="19a6e-190">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="19a6e-191">Dans l' `*Client*` application, définissez un point d’arrêt sur la `currentCount++;` ligne dans `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-191">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="19a6e-192">Dans le navigateur, accédez à la `Counter` page et sélectionnez le bouton **Click Me** pour atteindre le point d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="19a6e-192">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="19a6e-193">Les points d’arrêt ne sont **pas** atteints pendant le démarrage de l’application avant l’exécution du proxy de débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-193">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="19a6e-194">Cela comprend les points d’arrêt dans `Program.Main` ( `Program.cs` ) et les points d’arrêt dans les [ `OnInitialized{Async}` méthodes](xref:blazor/components/lifecycle#component-initialization-methods) des composants qui sont chargés par la première page demandée à partir de l’application.</span><span class="sxs-lookup"><span data-stu-id="19a6e-194">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="19a6e-195">Débogage hébergé Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="19a6e-195">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="19a6e-196">Ouvrez le Blazor WebAssembly dossier de solution de l’application hébergée dans vs code.</span><span class="sxs-lookup"><span data-stu-id="19a6e-196">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="19a6e-197">Si aucune configuration de lancement n’est définie pour le projet, la notification suivante s’affiche.</span><span class="sxs-lookup"><span data-stu-id="19a6e-197">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="19a6e-198">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="19a6e-198">Select **Yes**.</span></span>

   > <span data-ttu-id="19a6e-199">Les ressources requises pour la génération et le débogage sont manquantes dans « {nom de l’APPLICATION} ».</span><span class="sxs-lookup"><span data-stu-id="19a6e-199">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="19a6e-200">Faut-il les ajouter ?  »</span><span class="sxs-lookup"><span data-stu-id="19a6e-200">Add them?</span></span>

1. <span data-ttu-id="19a6e-201">Dans la palette de commandes en haut de la fenêtre, sélectionnez le projet *serveur* dans la solution hébergée.</span><span class="sxs-lookup"><span data-stu-id="19a6e-201">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="19a6e-202">Un `launch.json` fichier est généré à l’aide de la configuration de lancement pour le lancement du débogueur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-202">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="19a6e-203">Attacher à une session de débogage existante</span><span class="sxs-lookup"><span data-stu-id="19a6e-203">Attach to an existing debugging session</span></span>

<span data-ttu-id="19a6e-204">Pour attacher une application en cours d’exécution Blazor , créez un `launch.json` fichier avec la configuration suivante :</span><span class="sxs-lookup"><span data-stu-id="19a6e-204">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="19a6e-205">L’attachement à une session de débogage est pris en charge uniquement pour les applications autonomes.</span><span class="sxs-lookup"><span data-stu-id="19a6e-205">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="19a6e-206">Pour utiliser le débogage de pile complète, vous devez lancer l’application à partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="19a6e-206">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="19a6e-207">Lancer les options de configuration</span><span class="sxs-lookup"><span data-stu-id="19a6e-207">Launch configuration options</span></span>

<span data-ttu-id="19a6e-208">Les options de configuration de lancement suivantes sont prises en charge pour le `blazorwasm` type de débogage ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="19a6e-208">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="19a6e-209">Option</span><span class="sxs-lookup"><span data-stu-id="19a6e-209">Option</span></span>    | <span data-ttu-id="19a6e-210">Description</span><span class="sxs-lookup"><span data-stu-id="19a6e-210">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="19a6e-211">Utilisez `launch` pour lancer et attacher une session de débogage à une Blazor WebAssembly application ou `attach` pour attacher une session de débogage à une application déjà en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="19a6e-211">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="19a6e-212">URL à ouvrir dans le navigateur lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-212">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="19a6e-213">La valeur par défaut est `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="19a6e-213">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="19a6e-214">Navigateur à lancer pour la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-214">The browser to launch for the debugging session.</span></span> <span data-ttu-id="19a6e-215">A la valeur `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="19a6e-215">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="19a6e-216">La valeur par défaut est `chrome`.</span><span class="sxs-lookup"><span data-stu-id="19a6e-216">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="19a6e-217">Utilisé pour générer des journaux à partir du débogueur JS.</span><span class="sxs-lookup"><span data-stu-id="19a6e-217">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="19a6e-218">Définissez sur `true` pour générer des journaux.</span><span class="sxs-lookup"><span data-stu-id="19a6e-218">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="19a6e-219">Doit avoir la valeur en `true` cas de lancement et de débogage d’une application hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="19a6e-219">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="19a6e-220">Spécifie le chemin d’accès absolu du serveur Web.</span><span class="sxs-lookup"><span data-stu-id="19a6e-220">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="19a6e-221">Doit être défini si une application est servie à partir d’un sous-itinéraire.</span><span class="sxs-lookup"><span data-stu-id="19a6e-221">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="19a6e-222">Nombre de millisecondes d’attente de l’attachement de la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-222">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="19a6e-223">La valeur par défaut est 30 000 millisecondes (30 secondes).</span><span class="sxs-lookup"><span data-stu-id="19a6e-223">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="19a6e-224">Référence au fichier exécutable pour exécuter le serveur de l’application hébergée.</span><span class="sxs-lookup"><span data-stu-id="19a6e-224">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="19a6e-225">Doit être défini si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-225">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="19a6e-226">Répertoire de travail dans lequel l’application doit être lancée.</span><span class="sxs-lookup"><span data-stu-id="19a6e-226">The working directory to launch the app under.</span></span> <span data-ttu-id="19a6e-227">Doit être défini si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-227">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="19a6e-228">Variables d’environnement à fournir au processus lancé.</span><span class="sxs-lookup"><span data-stu-id="19a6e-228">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="19a6e-229">Applicable uniquement si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-229">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="19a6e-230">Exemples de configurations de lancement</span><span class="sxs-lookup"><span data-stu-id="19a6e-230">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="19a6e-231">Lancer et déboguer une Blazor WebAssembly application autonome</span><span class="sxs-lookup"><span data-stu-id="19a6e-231">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="19a6e-232">Attacher à une application en cours d’exécution à une URL spécifiée</span><span class="sxs-lookup"><span data-stu-id="19a6e-232">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="19a6e-233">Lancer et déboguer une application hébergée Blazor WebAssembly avec Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="19a6e-233">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="19a6e-234">La configuration du navigateur est par défaut Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="19a6e-234">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="19a6e-235">Lorsque vous utilisez Microsoft Edge pour le débogage, affectez à la valeur `browser` `edge` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-235">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="19a6e-236">Pour utiliser Google Chrome, vous ne devez pas définir l' `browser` option ou définir la valeur de l’option sur `chrome` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-236">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="19a6e-237">Dans l’exemple précédent, `MyHostedApp.Server.dll` est l’assembly de l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="19a6e-237">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="19a6e-238">Le `.vscode` dossier se trouve dans le dossier de la solution, en regard des `Client` `Server` dossiers, et `Shared` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-238">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19a6e-239">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="19a6e-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="19a6e-240">Pour déboguer une Blazor WebAssembly application dans Visual Studio pour Mac :</span><span class="sxs-lookup"><span data-stu-id="19a6e-240">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="19a6e-241">Créez une nouvelle ASP.NET Core application hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="19a6e-241">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="19a6e-242">Appuyez sur <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> pour exécuter l’application dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-242">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="19a6e-243">**Exécuter sans débogage** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="19a6e-243">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="19a6e-244">Lorsque l’application est exécutée dans la configuration Debug, le débogage entraîne toujours une réduction des performances minime.</span><span class="sxs-lookup"><span data-stu-id="19a6e-244">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="19a6e-245">Google Chrome ou Microsoft Edge doit être le navigateur sélectionné pour la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-245">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="19a6e-246">Dans l' `*Client*` application, définissez un point d’arrêt sur la `currentCount++;` ligne dans `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-246">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="19a6e-247">Dans le navigateur, accédez à la `Counter` page, puis sélectionnez le bouton **Click Me** pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="19a6e-247">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="19a6e-248">Dans Visual Studio, examinez la valeur du `currentCount` champ dans la fenêtre **variables locales** .</span><span class="sxs-lookup"><span data-stu-id="19a6e-248">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="19a6e-249">Appuyez sur <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> pour poursuivre l’exécution.</span><span class="sxs-lookup"><span data-stu-id="19a6e-249">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="19a6e-250">Lors du débogage d’une Blazor WebAssembly application, vous pouvez également déboguer le code serveur :</span><span class="sxs-lookup"><span data-stu-id="19a6e-250">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="19a6e-251">Définissez un point d’arrêt dans la `Pages/FetchData.razor` page de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="19a6e-251">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="19a6e-252">Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="19a6e-252">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="19a6e-253">Accédez à la `Fetch Data` page pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il ne envoie une requête HTTP au serveur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-253">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="19a6e-254">Appuyez sur <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-254">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="19a6e-255">Appuyez de nouveau sur <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météorologiques rendu dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-255">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="19a6e-256">Les points d’arrêt ne sont **pas** atteints pendant le démarrage de l’application avant l’exécution du proxy de débogage.</span><span class="sxs-lookup"><span data-stu-id="19a6e-256">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="19a6e-257">Cela comprend les points d’arrêt dans `Program.Main` ( `Program.cs` ) et les points d’arrêt dans les [ `OnInitialized{Async}` méthodes](xref:blazor/components/lifecycle#component-initialization-methods) des composants qui sont chargés par la première page demandée à partir de l’application.</span><span class="sxs-lookup"><span data-stu-id="19a6e-257">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="19a6e-258">Pour plus d’informations, consultez [débogage avec Visual Studio pour Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="19a6e-258">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="19a6e-259">Déboguer dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="19a6e-259">Debug in the browser</span></span>

<span data-ttu-id="19a6e-260">*Les instructions de cette section s’appliquent à Google Chrome et à Microsoft Edge s’exécutant sur Windows.*</span><span class="sxs-lookup"><span data-stu-id="19a6e-260">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="19a6e-261">Exécutez une version Debug de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="19a6e-261">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="19a6e-262">Lancez un navigateur et accédez à l’URL de l’application (par exemple, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="19a6e-262">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="19a6e-263">Dans le navigateur, essayez de commencer le débogage à distance en appuyant sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.</span><span class="sxs-lookup"><span data-stu-id="19a6e-263">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="19a6e-264">Le navigateur doit s’exécuter avec le débogage à distance activé, qui n’est pas le paramètre par défaut.</span><span class="sxs-lookup"><span data-stu-id="19a6e-264">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="19a6e-265">Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est affichée avec des instructions pour lancer le navigateur avec le port de débogage ouvert.</span><span class="sxs-lookup"><span data-stu-id="19a6e-265">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="19a6e-266">Suivez les instructions de votre navigateur pour ouvrir une nouvelle fenêtre de navigateur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-266">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="19a6e-267">Fermez la fenêtre de navigateur précédente.</span><span class="sxs-lookup"><span data-stu-id="19a6e-267">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="19a6e-268">Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage de l’étape précédente ouvre un nouvel onglet du débogueur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-268">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="19a6e-269">Après un moment, l’onglet **sources** affiche une liste des assemblys .net de l’application dans le `file://` nœud.</span><span class="sxs-lookup"><span data-stu-id="19a6e-269">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="19a6e-270">Dans le code du composant ( `.razor` fichiers) et les fichiers de code C# ( `.cs` ), les points d’arrêt que vous définissez sont atteints lors de l’exécution du code.</span><span class="sxs-lookup"><span data-stu-id="19a6e-270">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="19a6e-271">Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="19a6e-271">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="19a6e-272">Blazor fournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net.</span><span class="sxs-lookup"><span data-stu-id="19a6e-272">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="19a6e-273">Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy.</span><span class="sxs-lookup"><span data-stu-id="19a6e-273">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="19a6e-274">Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).</span><span class="sxs-lookup"><span data-stu-id="19a6e-274">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="19a6e-275">Mappages des sources du navigateur</span><span class="sxs-lookup"><span data-stu-id="19a6e-275">Browser source maps</span></span>

<span data-ttu-id="19a6e-276">Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client.</span><span class="sxs-lookup"><span data-stu-id="19a6e-276">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="19a6e-277">Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="19a6e-277">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="19a6e-278">Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="19a6e-278">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="19a6e-279">Configuration du pare-feu</span><span class="sxs-lookup"><span data-stu-id="19a6e-279">Firewall configuration</span></span>

<span data-ttu-id="19a6e-280">Si un pare-feu bloque la communication avec le proxy de débogage, créez une règle d’exception de pare-feu qui autorise la communication entre le navigateur et le `NodeJS` processus.</span><span class="sxs-lookup"><span data-stu-id="19a6e-280">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="19a6e-281">La modification d’une configuration de pare-feu doit être effectuée avec précaution pour éviter de créer des vulnerablities de sécurité.</span><span class="sxs-lookup"><span data-stu-id="19a6e-281">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="19a6e-282">Appliquez avec prudence des conseils de sécurité, suivez les meilleures pratiques de sécurité et respectez les avertissements émis par le fabricant du pare-feu.</span><span class="sxs-lookup"><span data-stu-id="19a6e-282">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="19a6e-283">Autorisation de la communication ouverte avec le `NodeJS` processus :</span><span class="sxs-lookup"><span data-stu-id="19a6e-283">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="19a6e-284">Ouvre le serveur de nœud à toute connexion, en fonction des capacités et de la configuration du pare-feu.</span><span class="sxs-lookup"><span data-stu-id="19a6e-284">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="19a6e-285">Peut être risqué en fonction de votre réseau.</span><span class="sxs-lookup"><span data-stu-id="19a6e-285">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="19a6e-286">**Est recommandé uniquement sur les ordinateurs de développement.**</span><span class="sxs-lookup"><span data-stu-id="19a6e-286">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="19a6e-287">Si possible, autorisez uniquement la communication ouverte avec le `NodeJS` processus **sur des réseaux approuvés ou privés**.</span><span class="sxs-lookup"><span data-stu-id="19a6e-287">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="19a6e-288">Pour obtenir des instructions de configuration du [pare-feu Windows](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) , consultez [créer une règle de service ou de programme entrant](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span><span class="sxs-lookup"><span data-stu-id="19a6e-288">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="19a6e-289">Pour plus d’informations, consultez [pare-feu Windows Defender avec fonctions avancées de sécurité](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) et Articles connexes dans la documentation du pare-feu Windows.</span><span class="sxs-lookup"><span data-stu-id="19a6e-289">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="19a6e-290">Dépanner</span><span class="sxs-lookup"><span data-stu-id="19a6e-290">Troubleshoot</span></span>

<span data-ttu-id="19a6e-291">Si vous rencontrez des erreurs, les conseils suivants peuvent vous aider :</span><span class="sxs-lookup"><span data-stu-id="19a6e-291">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="19a6e-292">Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="19a6e-292">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="19a6e-293">Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="19a6e-293">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="19a6e-294">Confirmez que vous avez installé et approuvé le certificat de développement ASP.NET Core HTTPs.</span><span class="sxs-lookup"><span data-stu-id="19a6e-294">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="19a6e-295">Pour plus d'informations, consultez <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="19a6e-295">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="19a6e-296">Visual Studio requiert l’option **activer le débogage JavaScript pour ASP.net (chrome, Edge et IE)** dans **Outils**  >  **options**  >  **débogage**  >  **général**.</span><span class="sxs-lookup"><span data-stu-id="19a6e-296">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="19a6e-297">Il s’agit du paramètre par défaut pour Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="19a6e-297">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="19a6e-298">Si le débogage ne fonctionne pas, vérifiez que l’option est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="19a6e-298">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="19a6e-299">Si votre environnement utilise un proxy HTTP, assurez-vous qu' `localhost` il est inclus dans les paramètres de contournement du proxy.</span><span class="sxs-lookup"><span data-stu-id="19a6e-299">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="19a6e-300">Pour ce faire, vous pouvez définir la `NO_PROXY` variable d’environnement dans l’un ou l’autre des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="19a6e-300">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="19a6e-301">`launchSettings.json`Fichier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="19a6e-301">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="19a6e-302">Au niveau des variables d’environnement système ou utilisateur pour qu’il s’applique à toutes les applications.</span><span class="sxs-lookup"><span data-stu-id="19a6e-302">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="19a6e-303">Quand vous utilisez une variable d’environnement, redémarrez Visual Studio pour que la modification prenne effet.</span><span class="sxs-lookup"><span data-stu-id="19a6e-303">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="19a6e-304">Assurez-vous que les pare-feu ou les proxys ne bloquent pas la communication avec le proxy de débogage ( `NodeJS` processus).</span><span class="sxs-lookup"><span data-stu-id="19a6e-304">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="19a6e-305">Pour plus d’informations, consultez la section [configuration du pare-feu](#firewall-configuration) .</span><span class="sxs-lookup"><span data-stu-id="19a6e-305">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="19a6e-306">Points d’arrêt `OnInitialized{Async}` non atteints</span><span class="sxs-lookup"><span data-stu-id="19a6e-306">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="19a6e-307">Le Blazor proxy de débogage du Framework prend un peu de temps, il est donc possible que les points d’arrêt dans la [ `OnInitialized{Async}` méthode Lifecycle](xref:blazor/components/lifecycle#component-initialization-methods) ne soient pas atteints.</span><span class="sxs-lookup"><span data-stu-id="19a6e-307">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="19a6e-308">Nous vous recommandons d’ajouter un délai au début du corps de la méthode pour permettre au proxy de débogage de se lancer avant que le point d’arrêt ne soit atteint.</span><span class="sxs-lookup"><span data-stu-id="19a6e-308">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="19a6e-309">Vous pouvez inclure le délai en fonction d’une [ `if` directive de compilateur](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) pour vous assurer que le délai n’est pas présent pour une version Release de l’application.</span><span class="sxs-lookup"><span data-stu-id="19a6e-309">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="19a6e-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="19a6e-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="19a6e-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="19a6e-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="19a6e-312">Délai d’attente de Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="19a6e-312">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="19a6e-313">Si Visual Studio lève une exception indiquant que l’adaptateur de débogage n’a pas pu lancer la mention indiquant que le délai d’attente a été atteint, vous pouvez ajuster le délai d’expiration à l’aide d’un paramètre de Registre :</span><span class="sxs-lookup"><span data-stu-id="19a6e-313">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="19a6e-314">L' `{TIMEOUT}` espace réservé dans la commande précédente est exprimé en millisecondes.</span><span class="sxs-lookup"><span data-stu-id="19a6e-314">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="19a6e-315">Par exemple, une minute est affectée en tant que `60000` .</span><span class="sxs-lookup"><span data-stu-id="19a6e-315">For example, one minute is assigned as `60000`.</span></span>
