---
title: Utiliser ASP.NET Core SignalR avec Blazor
author: guardrex
description: Créez une application de conversation qui utilise ASP.NET Core SignalR avec Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 50e5f2d5b1f9d0bf229bc57e6a1f599f9574b27c
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395225"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="af778-103">Utiliser ASP.NET Core SignalR avec Blazor</span><span class="sxs-lookup"><span data-stu-id="af778-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="af778-104">Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de SignalR avec Blazor .</span><span class="sxs-lookup"><span data-stu-id="af778-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="af778-105">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="af778-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="af778-106">Créer un Blazor projet</span><span class="sxs-lookup"><span data-stu-id="af778-106">Create a Blazor project</span></span>
> * <span data-ttu-id="af778-107">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="af778-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="af778-108">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="af778-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="af778-109">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="af778-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="af778-110">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="af778-110">Add Razor component code for chat</span></span>

<span data-ttu-id="af778-111">À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.</span><span class="sxs-lookup"><span data-stu-id="af778-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="af778-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af778-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="af778-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="af778-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="af778-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af778-115">[Visual Studio 2019 16,8 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="af778-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-117">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="af778-118">Visual Studio pour Mac version 8,8 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="af778-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-119">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="af778-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af778-121">[Visual Studio 2019 16,6 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="af778-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-123">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="af778-124">Visual Studio pour Mac version 8,6 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="af778-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-125">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="af778-126">Créer une application hébergée Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="af778-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="af778-127">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="af778-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af778-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="af778-129">Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="af778-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="af778-130">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="af778-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="af778-131">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="af778-131">Create a new project.</span></span>

1. <span data-ttu-id="af778-132">Sélectionnez **Blazor application** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="af778-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="af778-133">Tapez `BlazorWebAssemblySignalRApp` dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="af778-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="af778-134">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="af778-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="af778-135">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="af778-135">Select **Create**.</span></span>

1. <span data-ttu-id="af778-136">Choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="af778-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="af778-137">Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="af778-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="af778-138">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="af778-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="af778-140">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="af778-141">L' `-ho|--hosted` option crée une solution hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="af778-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="af778-142">Pour plus d’informations sur la configuration des ressources VS Code dans le `.vscode` dossier, consultez le Guide du système d’exploitation **Linux** dans <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="af778-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="af778-143">L' `-o|--output` option crée un dossier pour la solution.</span><span class="sxs-lookup"><span data-stu-id="af778-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="af778-144">Si vous avez créé un dossier pour la solution et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="af778-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="af778-145">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="af778-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="af778-146">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="af778-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="af778-147">Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="af778-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-148">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af778-149">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="af778-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="af778-150">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="af778-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="af778-151">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="af778-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="af778-152">Choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="af778-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="af778-153">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="af778-153">Select **Next**.</span></span>

1. <span data-ttu-id="af778-154">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="af778-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="af778-155">Activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="af778-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="af778-156">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="af778-156">Select **Next**.</span></span>

1. <span data-ttu-id="af778-157">Dans le champ **nom du projet** , nommez l’application `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="af778-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="af778-158">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="af778-158">Select **Create**.</span></span>

   <span data-ttu-id="af778-159">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="af778-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="af778-160">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="af778-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="af778-161">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="af778-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-162">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="af778-163">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="af778-164">L' `-ho|--hosted` option crée une solution hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="af778-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="af778-165">L' `-o|--output` option crée un dossier pour la solution.</span><span class="sxs-lookup"><span data-stu-id="af778-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="af778-166">Si vous avez créé un dossier pour la solution et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="af778-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="af778-167">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="af778-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af778-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="af778-169">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="af778-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="af778-170">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="af778-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="af778-171">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="af778-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="af778-172">Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="af778-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="af778-173">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="af778-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="af778-174">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="af778-174">Select **Install**.</span></span>

1. <span data-ttu-id="af778-175">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="af778-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="af778-176">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="af778-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="af778-178">Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="af778-179">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="af778-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-180">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af778-181">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="af778-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="af778-182">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="af778-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="af778-183">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="af778-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="af778-184">Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="af778-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="af778-185">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="af778-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="af778-186">Sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="af778-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="af778-187">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="af778-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-188">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="af778-189">Dans une interface de commande à partir du dossier de la solution, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="af778-190">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="af778-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="af778-191">Ajouter le package System. Text. encoders. Web</span><span class="sxs-lookup"><span data-stu-id="af778-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="af778-192">*Cette section s’applique uniquement aux applications pour ASP.NET Core version 3. x.*</span><span class="sxs-lookup"><span data-stu-id="af778-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="af778-193">En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5. x dans une application ASP.net Core 3. x, le `BlazorWebAssemblySignalRApp.Server` projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="af778-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="af778-194">Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="af778-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="af778-195">Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="af778-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="af778-196">Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au `BlazorWebAssemblySignalRApp.Server` projet de la solution ASP.net Core hébergée 3,1 Blazor , suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="af778-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af778-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="af778-198">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="af778-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="af778-199">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="af778-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="af778-200">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="af778-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="af778-201">Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) Package.</span><span class="sxs-lookup"><span data-stu-id="af778-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="af778-202">Sélectionnez la version du package qui correspond à l’infrastructure partagée en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="af778-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="af778-203">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="af778-203">Select **Install**.</span></span>

1. <span data-ttu-id="af778-204">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="af778-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="af778-205">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="af778-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="af778-207">Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="af778-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="af778-208">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="af778-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-209">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af778-210">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="af778-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="af778-211">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="af778-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="af778-212">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="af778-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="af778-213">Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, sélectionnez la version appropriée du package qui correspond à l’infrastructure partagée en cours d’utilisation, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="af778-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="af778-214">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="af778-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-215">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="af778-216">Dans une interface de commande à partir du dossier de la solution, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="af778-217">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="af778-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="af778-218">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="af778-218">Add a SignalR hub</span></span>

<span data-ttu-id="af778-219">Dans le `BlazorWebAssemblySignalRApp.Server` projet, créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="af778-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="af778-220">Ajouter des services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="af778-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="af778-221">Dans le projet `BlazorWebAssemblySignalRApp.Server`, ouvrez le fichier `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="af778-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="af778-222">Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="af778-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="af778-223">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="af778-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="af778-224">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="af778-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="af778-225">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="af778-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="af778-226">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="af778-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="af778-227">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="af778-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="af778-228">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="af778-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="af778-229">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="af778-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="af778-230">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="af778-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="af778-231">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="af778-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="af778-232">Dans le projet `BlazorWebAssemblySignalRApp.Client`, ouvrez le fichier `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="af778-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="af778-233">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="af778-233">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="af778-234">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="af778-234">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="af778-235">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="af778-235">Run the app</span></span>

<span data-ttu-id="af778-236">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="af778-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af778-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="af778-238">Dans **Explorateur de solutions**, sélectionnez le `BlazorWebAssemblySignalRApp.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="af778-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="af778-239">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="af778-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="af778-240">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="af778-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="af778-241">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="af778-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="af778-242">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="af778-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="af778-244">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="af778-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="af778-246">Pour plus d’informations sur la configuration des ressources VS Code dans le `.vscode` dossier, consultez le Guide du système d’exploitation **Linux** dans <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="af778-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="af778-247">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="af778-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="af778-248">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="af778-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="af778-249">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="af778-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="af778-250">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="af778-250">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="af778-252">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="af778-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-253">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af778-254">Dans la barre latérale **solution** , sélectionnez le `BlazorWebAssemblySignalRApp.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="af778-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="af778-255">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="af778-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="af778-256">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="af778-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="af778-257">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="af778-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="af778-258">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="af778-258">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="af778-260">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="af778-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-261">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="af778-262">Dans une interface de commande à partir du dossier de la solution, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="af778-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="af778-263">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="af778-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="af778-264">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="af778-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="af778-265">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="af778-265">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="af778-267">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="af778-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="af778-268">Créer une Blazor Server application</span><span class="sxs-lookup"><span data-stu-id="af778-268">Create a Blazor Server app</span></span>

<span data-ttu-id="af778-269">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="af778-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af778-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="af778-271">Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="af778-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="af778-272">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="af778-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="af778-273">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="af778-273">Create a new project.</span></span>

1. <span data-ttu-id="af778-274">Sélectionnez **Blazor application** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="af778-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="af778-275">Tapez `BlazorServerSignalRApp` dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="af778-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="af778-276">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="af778-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="af778-277">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="af778-277">Select **Create**.</span></span>

1. <span data-ttu-id="af778-278">Choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="af778-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="af778-279">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="af778-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="af778-281">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="af778-282">L' `-o|--output` option crée un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="af778-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="af778-283">Si vous avez créé un dossier pour le projet et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer le projet.</span><span class="sxs-lookup"><span data-stu-id="af778-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="af778-284">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="af778-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="af778-285">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="af778-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="af778-286">Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="af778-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="af778-287">Pour plus d’informations sur la configuration des ressources VS Code dans le `.vscode` dossier, consultez le Guide du système d’exploitation **Linux** dans <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="af778-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-288">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af778-289">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="af778-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="af778-290">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="af778-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="af778-291">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="af778-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="af778-292">Choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="af778-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="af778-293">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="af778-293">Select **Next**.</span></span>

1. <span data-ttu-id="af778-294">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="af778-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="af778-295">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="af778-295">Select **Next**.</span></span>

1. <span data-ttu-id="af778-296">Dans le champ **nom du projet** , nommez l’application `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="af778-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="af778-297">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="af778-297">Select **Create**.</span></span>

   <span data-ttu-id="af778-298">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="af778-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="af778-299">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="af778-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="af778-300">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="af778-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-301">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="af778-302">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="af778-303">L' `-o|--output` option crée un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="af778-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="af778-304">Si vous avez créé un dossier pour le projet et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer le projet.</span><span class="sxs-lookup"><span data-stu-id="af778-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="af778-305">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="af778-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af778-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="af778-307">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="af778-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="af778-308">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="af778-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="af778-309">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="af778-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="af778-310">Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="af778-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="af778-311">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="af778-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="af778-312">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="af778-312">Select **Install**.</span></span>

1. <span data-ttu-id="af778-313">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="af778-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="af778-314">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="af778-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="af778-316">Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="af778-317">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="af778-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-318">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af778-319">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="af778-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="af778-320">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="af778-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="af778-321">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="af778-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="af778-322">Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="af778-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="af778-323">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="af778-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="af778-324">Sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="af778-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="af778-325">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="af778-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-326">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="af778-327">Dans une interface de commande à partir du dossier du projet, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="af778-328">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="af778-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="af778-329">Ajouter le package System. Text. encoders. Web</span><span class="sxs-lookup"><span data-stu-id="af778-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="af778-330">*Cette section s’applique uniquement aux applications pour ASP.NET Core version 3. x.*</span><span class="sxs-lookup"><span data-stu-id="af778-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="af778-331">En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5. x dans une application ASP.net Core 3. x, le projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="af778-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="af778-332">Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="af778-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="af778-333">Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="af778-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="af778-334">Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au projet, suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="af778-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af778-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="af778-336">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="af778-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="af778-337">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="af778-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="af778-338">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="af778-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="af778-339">Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) Package.</span><span class="sxs-lookup"><span data-stu-id="af778-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="af778-340">Sélectionnez la version du package qui correspond à l’infrastructure partagée en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="af778-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="af778-341">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="af778-341">Select **Install**.</span></span>

1. <span data-ttu-id="af778-342">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="af778-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="af778-343">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="af778-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="af778-345">Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="af778-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="af778-346">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="af778-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-347">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af778-348">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="af778-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="af778-349">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="af778-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="af778-350">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="af778-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="af778-351">Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, sélectionnez la version appropriée du package qui correspond à l’infrastructure partagée en cours d’utilisation, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="af778-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="af778-352">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="af778-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-353">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="af778-354">Dans une interface de commande à partir du dossier du projet, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="af778-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="af778-355">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="af778-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="af778-356">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="af778-356">Add a SignalR hub</span></span>

<span data-ttu-id="af778-357">Créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="af778-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="af778-358">Ajouter des services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="af778-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="af778-359">Ouvrez le fichier `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="af778-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="af778-360">Ajoutez les espaces de noms pour <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> et la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="af778-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="af778-361">Ajoutez des services d’intergiciels (middleware) de compression des réponses à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="af778-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="af778-362">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="af778-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="af778-363">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="af778-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="af778-364">Entre les points de terminaison pour le mappage du Blazor Hub et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="af778-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="af778-365">Ajoutez des services d’intergiciels (middleware) de compression des réponses à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="af778-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="af778-366">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="af778-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="af778-367">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="af778-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="af778-368">Entre les points de terminaison pour le mappage du Blazor Hub et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="af778-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="af778-369">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="af778-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="af778-370">Ouvrez le fichier `Pages/Index.razor` .</span><span class="sxs-lookup"><span data-stu-id="af778-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="af778-371">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="af778-371">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="af778-372">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="af778-372">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="af778-373">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="af778-373">Run the app</span></span>

<span data-ttu-id="af778-374">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="af778-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="af778-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af778-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="af778-376">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="af778-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="af778-377">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="af778-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="af778-378">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="af778-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="af778-379">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="af778-379">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="af778-381">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="af778-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="af778-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af778-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="af778-383">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="af778-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="af778-384">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="af778-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="af778-385">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="af778-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="af778-386">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="af778-386">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="af778-388">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="af778-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="af778-389">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="af778-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="af778-390">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="af778-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="af778-391">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="af778-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="af778-392">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="af778-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="af778-393">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="af778-393">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="af778-395">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="af778-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="af778-396">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="af778-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="af778-397">Dans une interface de commande à partir du dossier du projet, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="af778-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="af778-398">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="af778-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="af778-399">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="af778-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="af778-400">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="af778-400">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="af778-402">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="af778-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="af778-403">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="af778-403">Next steps</span></span>

<span data-ttu-id="af778-404">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="af778-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="af778-405">Créer un Blazor projet</span><span class="sxs-lookup"><span data-stu-id="af778-405">Create a Blazor project</span></span>
> * <span data-ttu-id="af778-406">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="af778-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="af778-407">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="af778-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="af778-408">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="af778-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="af778-409">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="af778-409">Add Razor component code for chat</span></span>

<span data-ttu-id="af778-410">Pour en savoir plus sur Blazor la création d’applications, consultez la Blazor documentation :</span><span class="sxs-lookup"><span data-stu-id="af778-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="af778-411"><xref:blazor/index>
> [Authentification du jeton du porteur avec les Identity événements serveur, WebSocket et Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="af778-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af778-412">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="af778-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="af778-413">SignalR négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="af778-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
