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
ms.openlocfilehash: 5bf80bd9147b6210fd2b115f7c009cc518f6f5cb
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99579125"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="f7298-103">Utiliser ASP.NET Core SignalR avec Blazor</span><span class="sxs-lookup"><span data-stu-id="f7298-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="f7298-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f7298-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f7298-105">Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de SignalR avec Blazor .</span><span class="sxs-lookup"><span data-stu-id="f7298-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="f7298-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="f7298-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f7298-107">Créer un Blazor projet</span><span class="sxs-lookup"><span data-stu-id="f7298-107">Create a Blazor project</span></span>
> * <span data-ttu-id="f7298-108">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="f7298-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="f7298-109">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f7298-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="f7298-110">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f7298-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f7298-111">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="f7298-111">Add Razor component code for chat</span></span>

<span data-ttu-id="f7298-112">À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.</span><span class="sxs-lookup"><span data-stu-id="f7298-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="f7298-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7298-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f7298-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f7298-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f7298-116">[Visual Studio 2019 16,8 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="f7298-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f7298-119">Visual Studio pour Mac version 8,8 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="f7298-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-120">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f7298-122">[Visual Studio 2019 16,6 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="f7298-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-124">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f7298-125">Visual Studio pour Mac version 8,6 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="f7298-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-126">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="f7298-127">Créer une application hébergée Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f7298-127">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="f7298-128">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="f7298-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f7298-130">Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="f7298-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f7298-131">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="f7298-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="f7298-132">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-132">Create a new project.</span></span>

1. <span data-ttu-id="f7298-133">Sélectionnez **Blazor application** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f7298-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="f7298-134">Tapez `BlazorWebAssemblySignalRApp` dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="f7298-134">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="f7298-135">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f7298-136">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f7298-136">Select **Create**.</span></span>

1. <span data-ttu-id="f7298-137">Choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="f7298-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="f7298-138">Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="f7298-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f7298-139">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f7298-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f7298-141">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="f7298-142">L' `-ho|--hosted` option crée une solution hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f7298-142">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

   <span data-ttu-id="f7298-143">L' `-o|--output` option crée un dossier pour la solution.</span><span class="sxs-lookup"><span data-stu-id="f7298-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="f7298-144">Si vous avez créé un dossier pour la solution et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="f7298-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="f7298-145">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="f7298-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="f7298-146">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f7298-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="f7298-147">Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="f7298-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-148">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7298-149">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="f7298-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="f7298-150">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="f7298-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f7298-151">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="f7298-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="f7298-152">Choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="f7298-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f7298-153">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f7298-153">Select **Next**.</span></span>

1. <span data-ttu-id="f7298-154">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="f7298-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f7298-155">Activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="f7298-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="f7298-156">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f7298-156">Select **Next**.</span></span>

1. <span data-ttu-id="f7298-157">Dans le champ **nom du projet** , nommez l’application `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="f7298-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="f7298-158">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f7298-158">Select **Create**.</span></span>

   <span data-ttu-id="f7298-159">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="f7298-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f7298-160">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="f7298-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="f7298-161">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="f7298-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-162">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f7298-163">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="f7298-164">L' `-ho|--hosted` option crée une solution hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f7298-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="f7298-165">L' `-o|--output` option crée un dossier pour la solution.</span><span class="sxs-lookup"><span data-stu-id="f7298-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="f7298-166">Si vous avez créé un dossier pour la solution et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="f7298-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f7298-167">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="f7298-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f7298-169">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f7298-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f7298-170">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f7298-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f7298-171">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7298-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f7298-172">Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="f7298-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f7298-173">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="f7298-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f7298-174">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f7298-174">Select **Install**.</span></span>

1. <span data-ttu-id="f7298-175">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="f7298-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f7298-176">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f7298-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f7298-178">Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f7298-179">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f7298-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-180">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7298-181">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f7298-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f7298-182">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f7298-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f7298-183">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7298-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f7298-184">Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="f7298-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f7298-185">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="f7298-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f7298-186">Sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="f7298-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="f7298-187">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f7298-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-188">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f7298-189">Dans une interface de commande à partir du dossier de la solution, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f7298-190">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f7298-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="f7298-191">Ajouter le package System. Text. encoders. Web</span><span class="sxs-lookup"><span data-stu-id="f7298-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="f7298-192">*Cette section s’applique uniquement aux applications pour ASP.NET Core version 3. x.*</span><span class="sxs-lookup"><span data-stu-id="f7298-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="f7298-193">En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5. x dans une application ASP.net Core 3. x, le `BlazorWebAssemblySignalRApp.Server` projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="f7298-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="f7298-194">Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="f7298-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="f7298-195">Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="f7298-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="f7298-196">Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au `BlazorWebAssemblySignalRApp.Server` projet de la solution ASP.net Core hébergée 3,1 Blazor , suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="f7298-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f7298-198">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f7298-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f7298-199">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f7298-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f7298-200">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7298-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f7298-201">Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) Package.</span><span class="sxs-lookup"><span data-stu-id="f7298-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="f7298-202">Sélectionnez la version du package qui correspond à l’infrastructure partagée en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="f7298-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="f7298-203">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f7298-203">Select **Install**.</span></span>

1. <span data-ttu-id="f7298-204">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="f7298-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f7298-205">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f7298-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f7298-207">Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f7298-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="f7298-208">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f7298-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-209">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7298-210">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f7298-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f7298-211">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f7298-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f7298-212">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7298-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f7298-213">Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, sélectionnez la version appropriée du package qui correspond à l’infrastructure partagée en cours d’utilisation, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="f7298-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="f7298-214">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f7298-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-215">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f7298-216">Dans une interface de commande à partir du dossier de la solution, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="f7298-217">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f7298-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="f7298-218">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f7298-218">Add a SignalR hub</span></span>

<span data-ttu-id="f7298-219">Dans le `BlazorWebAssemblySignalRApp.Server` projet, créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="f7298-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="f7298-220">Ajouter des services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f7298-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="f7298-221">Dans le projet `BlazorWebAssemblySignalRApp.Server`, ouvrez le fichier `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="f7298-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="f7298-222">Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="f7298-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f7298-223">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f7298-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="f7298-224">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f7298-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f7298-225">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="f7298-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f7298-226">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="f7298-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f7298-227">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f7298-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="f7298-228">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f7298-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f7298-229">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="f7298-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f7298-230">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="f7298-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="f7298-231">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="f7298-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="f7298-232">Dans le projet `BlazorWebAssemblySignalRApp.Client`, ouvrez le fichier `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="f7298-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f7298-233">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7298-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f7298-234">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7298-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="f7298-235">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="f7298-235">Run the app</span></span>

<span data-ttu-id="f7298-236">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="f7298-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f7298-238">Dans **Explorateur de solutions**, sélectionnez le `BlazorWebAssemblySignalRApp.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="f7298-239">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f7298-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f7298-240">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f7298-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f7298-241">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f7298-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f7298-242">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f7298-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f7298-244">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f7298-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f7298-246">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f7298-246">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f7298-247">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f7298-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f7298-248">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f7298-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f7298-249">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f7298-249">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f7298-251">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f7298-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-252">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-252">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7298-253">Dans la barre latérale **solution** , sélectionnez le `BlazorWebAssemblySignalRApp.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-253">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="f7298-254">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f7298-254">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f7298-255">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f7298-255">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f7298-256">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f7298-256">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f7298-257">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f7298-257">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f7298-259">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f7298-259">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-260">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-260">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f7298-261">Dans une interface de commande à partir du dossier de la solution, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f7298-261">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="f7298-262">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f7298-262">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f7298-263">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f7298-263">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f7298-264">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f7298-264">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f7298-266">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f7298-266">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="f7298-267">Créer une Blazor Server application</span><span class="sxs-lookup"><span data-stu-id="f7298-267">Create a Blazor Server app</span></span>

<span data-ttu-id="f7298-268">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="f7298-268">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-269">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f7298-270">Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="f7298-270">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f7298-271">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="f7298-271">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="f7298-272">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-272">Create a new project.</span></span>

1. <span data-ttu-id="f7298-273">Sélectionnez **Blazor application** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f7298-273">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="f7298-274">Tapez `BlazorServerSignalRApp` dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="f7298-274">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="f7298-275">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-275">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f7298-276">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f7298-276">Select **Create**.</span></span>

1. <span data-ttu-id="f7298-277">Choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="f7298-277">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="f7298-278">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f7298-278">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f7298-280">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-280">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="f7298-281">L' `-o|--output` option crée un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-281">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="f7298-282">Si vous avez créé un dossier pour le projet et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer le projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-282">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="f7298-283">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="f7298-283">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="f7298-284">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f7298-284">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="f7298-285">Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="f7298-285">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-286">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7298-287">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="f7298-287">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="f7298-288">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="f7298-288">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f7298-289">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="f7298-289">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="f7298-290">Choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="f7298-290">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f7298-291">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f7298-291">Select **Next**.</span></span>

1. <span data-ttu-id="f7298-292">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="f7298-292">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f7298-293">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f7298-293">Select **Next**.</span></span>

1. <span data-ttu-id="f7298-294">Dans le champ **nom du projet** , nommez l’application `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="f7298-294">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="f7298-295">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f7298-295">Select **Create**.</span></span>

   <span data-ttu-id="f7298-296">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="f7298-296">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f7298-297">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="f7298-297">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="f7298-298">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="f7298-298">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-299">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-299">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f7298-300">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-300">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="f7298-301">L' `-o|--output` option crée un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-301">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="f7298-302">Si vous avez créé un dossier pour le projet et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer le projet.</span><span class="sxs-lookup"><span data-stu-id="f7298-302">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f7298-303">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="f7298-303">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-304">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-304">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f7298-305">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f7298-305">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f7298-306">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f7298-306">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f7298-307">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7298-307">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f7298-308">Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="f7298-308">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f7298-309">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="f7298-309">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f7298-310">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f7298-310">Select **Install**.</span></span>

1. <span data-ttu-id="f7298-311">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="f7298-311">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f7298-312">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f7298-312">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-313">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f7298-314">Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-314">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f7298-315">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f7298-315">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-316">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7298-317">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f7298-317">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f7298-318">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f7298-318">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f7298-319">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7298-319">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f7298-320">Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="f7298-320">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f7298-321">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="f7298-321">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f7298-322">Sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="f7298-322">Select **Add Package**.</span></span>

1. <span data-ttu-id="f7298-323">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f7298-323">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-324">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-324">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f7298-325">Dans une interface de commande à partir du dossier du projet, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-325">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f7298-326">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f7298-326">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="f7298-327">Ajouter le package System. Text. encoders. Web</span><span class="sxs-lookup"><span data-stu-id="f7298-327">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="f7298-328">*Cette section s’applique uniquement aux applications pour ASP.NET Core version 3. x.*</span><span class="sxs-lookup"><span data-stu-id="f7298-328">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="f7298-329">En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5. x dans une application ASP.net Core 3. x, le projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="f7298-329">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="f7298-330">Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="f7298-330">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="f7298-331">Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="f7298-331">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="f7298-332">Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au projet, suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="f7298-332">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-333">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f7298-334">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f7298-334">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f7298-335">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f7298-335">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f7298-336">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7298-336">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f7298-337">Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) Package.</span><span class="sxs-lookup"><span data-stu-id="f7298-337">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="f7298-338">Sélectionnez la version du package qui correspond à l’infrastructure partagée en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="f7298-338">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="f7298-339">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f7298-339">Select **Install**.</span></span>

1. <span data-ttu-id="f7298-340">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="f7298-340">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f7298-341">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f7298-341">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-342">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-342">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f7298-343">Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f7298-343">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="f7298-344">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f7298-344">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-345">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-345">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7298-346">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f7298-346">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f7298-347">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f7298-347">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f7298-348">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f7298-348">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f7298-349">Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, sélectionnez la version appropriée du package qui correspond à l’infrastructure partagée en cours d’utilisation, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="f7298-349">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="f7298-350">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f7298-350">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-351">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-351">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f7298-352">Dans une interface de commande à partir du dossier du projet, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f7298-352">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="f7298-353">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f7298-353">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="f7298-354">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f7298-354">Add a SignalR hub</span></span>

<span data-ttu-id="f7298-355">Créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="f7298-355">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="f7298-356">Ajouter des services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f7298-356">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="f7298-357">Ouvrez le fichier `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="f7298-357">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="f7298-358">Ajoutez les espaces de noms pour <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> et la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="f7298-358">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f7298-359">Ajoutez des services d’intergiciels (middleware) de compression des réponses à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f7298-359">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="f7298-360">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f7298-360">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f7298-361">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="f7298-361">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f7298-362">Entre les points de terminaison pour le mappage du Blazor Hub et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="f7298-362">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f7298-363">Ajoutez des services d’intergiciels (middleware) de compression des réponses à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f7298-363">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="f7298-364">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f7298-364">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f7298-365">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="f7298-365">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f7298-366">Entre les points de terminaison pour le mappage du Blazor Hub et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="f7298-366">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="f7298-367">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="f7298-367">Add Razor component code for chat</span></span>

1. <span data-ttu-id="f7298-368">Ouvrez le fichier `Pages/Index.razor` .</span><span class="sxs-lookup"><span data-stu-id="f7298-368">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f7298-369">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7298-369">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f7298-370">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f7298-370">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="f7298-371">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="f7298-371">Run the app</span></span>

<span data-ttu-id="f7298-372">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="f7298-372">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f7298-373">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7298-373">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f7298-374">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f7298-374">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f7298-375">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f7298-375">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f7298-376">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f7298-376">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f7298-377">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f7298-377">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f7298-379">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f7298-379">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f7298-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f7298-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f7298-381">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f7298-381">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f7298-382">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f7298-382">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f7298-383">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f7298-383">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f7298-384">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f7298-384">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f7298-386">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f7298-386">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7298-387">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f7298-387">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7298-388">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f7298-388">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f7298-389">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f7298-389">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f7298-390">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f7298-390">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f7298-391">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f7298-391">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f7298-393">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f7298-393">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7298-394">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7298-394">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f7298-395">Dans une interface de commande à partir du dossier du projet, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f7298-395">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="f7298-396">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f7298-396">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f7298-397">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f7298-397">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f7298-398">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f7298-398">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f7298-400">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f7298-400">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="f7298-401">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f7298-401">Next steps</span></span>

<span data-ttu-id="f7298-402">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="f7298-402">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f7298-403">Créer un Blazor projet</span><span class="sxs-lookup"><span data-stu-id="f7298-403">Create a Blazor project</span></span>
> * <span data-ttu-id="f7298-404">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="f7298-404">Add the SignalR client library</span></span>
> * <span data-ttu-id="f7298-405">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f7298-405">Add a SignalR hub</span></span>
> * <span data-ttu-id="f7298-406">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f7298-406">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f7298-407">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="f7298-407">Add Razor component code for chat</span></span>

<span data-ttu-id="f7298-408">Pour en savoir plus sur Blazor la création d’applications, consultez la Blazor documentation :</span><span class="sxs-lookup"><span data-stu-id="f7298-408">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="f7298-409"><xref:blazor/index>
> [Authentification du jeton du porteur avec les Identity événements serveur, WebSocket et Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="f7298-409"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7298-410">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f7298-410">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="f7298-411">SignalR négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="f7298-411">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
