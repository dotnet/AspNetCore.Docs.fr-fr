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
ms.openlocfilehash: 355db5ad5462747be0058096bc0132ed1071f290
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280991"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="f13d4-103">Utiliser ASP.NET Core SignalR avec Blazor</span><span class="sxs-lookup"><span data-stu-id="f13d4-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="f13d4-104">Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de SignalR avec Blazor .</span><span class="sxs-lookup"><span data-stu-id="f13d4-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="f13d4-105">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="f13d4-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f13d4-106">Créer un Blazor projet</span><span class="sxs-lookup"><span data-stu-id="f13d4-106">Create a Blazor project</span></span>
> * <span data-ttu-id="f13d4-107">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="f13d4-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="f13d4-108">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f13d4-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="f13d4-109">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f13d4-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f13d4-110">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="f13d4-110">Add Razor component code for chat</span></span>

<span data-ttu-id="f13d4-111">À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.</span><span class="sxs-lookup"><span data-stu-id="f13d4-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="f13d4-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f13d4-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f13d4-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f13d4-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f13d4-115">[Visual Studio 2019 16,8 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="f13d4-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-117">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f13d4-118">Visual Studio pour Mac version 8,8 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="f13d4-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-119">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f13d4-121">[Visual Studio 2019 16,6 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="f13d4-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-123">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f13d4-124">Visual Studio pour Mac version 8,6 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="f13d4-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-125">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="f13d4-126">Créer une application hébergée Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f13d4-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="f13d4-127">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="f13d4-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f13d4-129">Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="f13d4-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f13d4-130">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="f13d4-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="f13d4-131">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-131">Create a new project.</span></span>

1. <span data-ttu-id="f13d4-132">Sélectionnez **Blazor application** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="f13d4-133">Tapez `BlazorWebAssemblySignalRApp` dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="f13d4-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="f13d4-134">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f13d4-135">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f13d4-135">Select **Create**.</span></span>

1. <span data-ttu-id="f13d4-136">Choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="f13d4-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="f13d4-137">Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="f13d4-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f13d4-138">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f13d4-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f13d4-140">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="f13d4-141">L' `-ho|--hosted` option crée une solution hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f13d4-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

   <span data-ttu-id="f13d4-142">L' `-o|--output` option crée un dossier pour la solution.</span><span class="sxs-lookup"><span data-stu-id="f13d4-142">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="f13d4-143">Si vous avez créé un dossier pour la solution et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="f13d4-143">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="f13d4-144">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="f13d4-144">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="f13d4-145">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-145">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="f13d4-146">Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-146">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-147">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-147">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f13d4-148">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="f13d4-148">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="f13d4-149">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-149">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f13d4-150">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="f13d4-150">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="f13d4-151">Choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="f13d4-151">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f13d4-152">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-152">Select **Next**.</span></span>

1. <span data-ttu-id="f13d4-153">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-153">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f13d4-154">Activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="f13d4-154">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="f13d4-155">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-155">Select **Next**.</span></span>

1. <span data-ttu-id="f13d4-156">Dans le champ **nom du projet** , nommez l’application `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-156">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="f13d4-157">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f13d4-157">Select **Create**.</span></span>

   <span data-ttu-id="f13d4-158">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="f13d4-158">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f13d4-159">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="f13d4-159">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="f13d4-160">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="f13d4-160">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-161">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-161">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f13d4-162">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-162">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="f13d4-163">L' `-ho|--hosted` option crée une solution hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f13d4-163">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="f13d4-164">L' `-o|--output` option crée un dossier pour la solution.</span><span class="sxs-lookup"><span data-stu-id="f13d4-164">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="f13d4-165">Si vous avez créé un dossier pour la solution et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="f13d4-165">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f13d4-166">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="f13d4-166">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-167">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-167">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f13d4-168">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-168">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f13d4-169">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-169">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f13d4-170">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f13d4-170">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f13d4-171">Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="f13d4-171">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f13d4-172">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="f13d4-172">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f13d4-173">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-173">Select **Install**.</span></span>

1. <span data-ttu-id="f13d4-174">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-174">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f13d4-175">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f13d4-175">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-176">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-176">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f13d4-177">Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-177">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f13d4-178">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f13d4-178">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-179">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f13d4-180">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-180">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f13d4-181">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-181">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f13d4-182">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f13d4-182">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f13d4-183">Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="f13d4-183">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f13d4-184">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="f13d4-184">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f13d4-185">Sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-185">Select **Add Package**.</span></span>

1. <span data-ttu-id="f13d4-186">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f13d4-186">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-187">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-187">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f13d4-188">Dans une interface de commande à partir du dossier de la solution, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-188">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f13d4-189">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f13d4-189">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="f13d4-190">Ajouter le package System. Text. encoders. Web</span><span class="sxs-lookup"><span data-stu-id="f13d4-190">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="f13d4-191">*Cette section s’applique uniquement aux applications pour ASP.NET Core version 3. x.*</span><span class="sxs-lookup"><span data-stu-id="f13d4-191">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="f13d4-192">En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5. x dans une application ASP.net Core 3. x, le `BlazorWebAssemblySignalRApp.Server` projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="f13d4-192">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="f13d4-193">Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="f13d4-193">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="f13d4-194">Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="f13d4-194">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="f13d4-195">Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au `BlazorWebAssemblySignalRApp.Server` projet de la solution ASP.net Core hébergée 3,1 Blazor , suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="f13d4-195">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-196">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f13d4-197">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-197">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f13d4-198">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-198">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f13d4-199">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f13d4-199">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f13d4-200">Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) Package.</span><span class="sxs-lookup"><span data-stu-id="f13d4-200">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="f13d4-201">Sélectionnez la version du package qui correspond à l’infrastructure partagée en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="f13d4-201">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="f13d4-202">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-202">Select **Install**.</span></span>

1. <span data-ttu-id="f13d4-203">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-203">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f13d4-204">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f13d4-204">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-205">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f13d4-206">Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f13d4-206">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="f13d4-207">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f13d4-207">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-208">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f13d4-209">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-209">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f13d4-210">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-210">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f13d4-211">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f13d4-211">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f13d4-212">Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, sélectionnez la version appropriée du package qui correspond à l’infrastructure partagée en cours d’utilisation, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-212">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="f13d4-213">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f13d4-213">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-214">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-214">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f13d4-215">Dans une interface de commande à partir du dossier de la solution, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-215">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="f13d4-216">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f13d4-216">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="f13d4-217">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f13d4-217">Add a SignalR hub</span></span>

<span data-ttu-id="f13d4-218">Dans le `BlazorWebAssemblySignalRApp.Server` projet, créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="f13d4-218">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="f13d4-219">Ajouter des services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f13d4-219">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="f13d4-220">Dans le projet `BlazorWebAssemblySignalRApp.Server`, ouvrez le fichier `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="f13d4-220">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="f13d4-221">Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="f13d4-221">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f13d4-222">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f13d4-222">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="f13d4-223">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f13d4-223">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f13d4-224">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="f13d4-224">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f13d4-225">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="f13d4-225">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f13d4-226">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f13d4-226">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="f13d4-227">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f13d4-227">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f13d4-228">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="f13d4-228">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f13d4-229">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="f13d4-229">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="f13d4-230">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="f13d4-230">Add Razor component code for chat</span></span>

1. <span data-ttu-id="f13d4-231">Dans le projet `BlazorWebAssemblySignalRApp.Client`, ouvrez le fichier `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="f13d4-231">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f13d4-232">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f13d4-232">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f13d4-233">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f13d4-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="f13d4-234">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="f13d4-234">Run the app</span></span>

<span data-ttu-id="f13d4-235">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="f13d4-235">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-236">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f13d4-237">Dans **Explorateur de solutions**, sélectionnez le `BlazorWebAssemblySignalRApp.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-237">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="f13d4-238">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f13d4-238">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f13d4-239">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f13d4-239">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f13d4-240">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f13d4-240">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f13d4-241">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f13d4-241">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f13d4-243">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f13d4-243">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-244">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-244">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f13d4-245">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f13d4-245">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f13d4-246">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f13d4-246">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f13d4-247">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f13d4-247">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f13d4-248">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f13d4-248">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f13d4-250">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f13d4-250">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-251">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f13d4-252">Dans la barre latérale **solution** , sélectionnez le `BlazorWebAssemblySignalRApp.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-252">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="f13d4-253">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f13d4-253">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f13d4-254">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f13d4-254">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f13d4-255">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f13d4-255">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f13d4-256">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f13d4-256">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f13d4-258">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f13d4-258">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-259">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-259">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f13d4-260">Dans une interface de commande à partir du dossier de la solution, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f13d4-260">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="f13d4-261">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f13d4-261">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f13d4-262">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f13d4-262">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f13d4-263">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f13d4-263">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f13d4-265">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f13d4-265">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="f13d4-266">Créer une Blazor Server application</span><span class="sxs-lookup"><span data-stu-id="f13d4-266">Create a Blazor Server app</span></span>

<span data-ttu-id="f13d4-267">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="f13d4-267">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-268">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f13d4-269">Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="f13d4-269">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f13d4-270">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="f13d4-270">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="f13d4-271">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-271">Create a new project.</span></span>

1. <span data-ttu-id="f13d4-272">Sélectionnez **Blazor application** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-272">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="f13d4-273">Tapez `BlazorServerSignalRApp` dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="f13d4-273">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="f13d4-274">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-274">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f13d4-275">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f13d4-275">Select **Create**.</span></span>

1. <span data-ttu-id="f13d4-276">Choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="f13d4-276">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="f13d4-277">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f13d4-277">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-278">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-278">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f13d4-279">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-279">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="f13d4-280">L' `-o|--output` option crée un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-280">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="f13d4-281">Si vous avez créé un dossier pour le projet et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer le projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-281">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="f13d4-282">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="f13d4-282">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="f13d4-283">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-283">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="f13d4-284">Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-284">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-285">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-285">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f13d4-286">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="f13d4-286">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="f13d4-287">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-287">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f13d4-288">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="f13d4-288">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="f13d4-289">Choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="f13d4-289">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f13d4-290">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-290">Select **Next**.</span></span>

1. <span data-ttu-id="f13d4-291">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-291">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f13d4-292">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-292">Select **Next**.</span></span>

1. <span data-ttu-id="f13d4-293">Dans le champ **nom du projet** , nommez l’application `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-293">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="f13d4-294">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f13d4-294">Select **Create**.</span></span>

   <span data-ttu-id="f13d4-295">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="f13d4-295">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f13d4-296">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="f13d4-296">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="f13d4-297">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="f13d4-297">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-298">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-298">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f13d4-299">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-299">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="f13d4-300">L' `-o|--output` option crée un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-300">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="f13d4-301">Si vous avez créé un dossier pour le projet et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer le projet.</span><span class="sxs-lookup"><span data-stu-id="f13d4-301">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f13d4-302">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="f13d4-302">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-303">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-303">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f13d4-304">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-304">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f13d4-305">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-305">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f13d4-306">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f13d4-306">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f13d4-307">Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="f13d4-307">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f13d4-308">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="f13d4-308">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f13d4-309">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-309">Select **Install**.</span></span>

1. <span data-ttu-id="f13d4-310">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-310">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f13d4-311">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f13d4-311">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-312">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-312">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f13d4-313">Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-313">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f13d4-314">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f13d4-314">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-315">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-315">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f13d4-316">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-316">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f13d4-317">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-317">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f13d4-318">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f13d4-318">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f13d4-319">Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package.</span><span class="sxs-lookup"><span data-stu-id="f13d4-319">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f13d4-320">Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application.</span><span class="sxs-lookup"><span data-stu-id="f13d4-320">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f13d4-321">Sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-321">Select **Add Package**.</span></span>

1. <span data-ttu-id="f13d4-322">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f13d4-322">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-323">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-323">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f13d4-324">Dans une interface de commande à partir du dossier du projet, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-324">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f13d4-325">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f13d4-325">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="f13d4-326">Ajouter le package System. Text. encoders. Web</span><span class="sxs-lookup"><span data-stu-id="f13d4-326">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="f13d4-327">*Cette section s’applique uniquement aux applications pour ASP.NET Core version 3. x.*</span><span class="sxs-lookup"><span data-stu-id="f13d4-327">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="f13d4-328">En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5. x dans une application ASP.net Core 3. x, le projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="f13d4-328">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="f13d4-329">Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="f13d4-329">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="f13d4-330">Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="f13d4-330">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="f13d4-331">Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au projet, suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="f13d4-331">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-332">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f13d4-333">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-333">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f13d4-334">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-334">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f13d4-335">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f13d4-335">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f13d4-336">Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) Package.</span><span class="sxs-lookup"><span data-stu-id="f13d4-336">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="f13d4-337">Sélectionnez la version du package qui correspond à l’infrastructure partagée en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="f13d4-337">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="f13d4-338">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-338">Select **Install**.</span></span>

1. <span data-ttu-id="f13d4-339">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-339">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f13d4-340">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f13d4-340">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-341">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-341">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f13d4-342">Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f13d4-342">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="f13d4-343">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f13d4-343">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-344">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-344">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f13d4-345">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-345">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f13d4-346">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-346">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f13d4-347">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="f13d4-347">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f13d4-348">Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, sélectionnez la version appropriée du package qui correspond à l’infrastructure partagée en cours d’utilisation, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="f13d4-348">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="f13d4-349">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="f13d4-349">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-350">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-350">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f13d4-351">Dans une interface de commande à partir du dossier du projet, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f13d4-351">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="f13d4-352">Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.</span><span class="sxs-lookup"><span data-stu-id="f13d4-352">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="f13d4-353">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f13d4-353">Add a SignalR hub</span></span>

<span data-ttu-id="f13d4-354">Créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="f13d4-354">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="f13d4-355">Ajouter des services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f13d4-355">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="f13d4-356">Ouvrez le fichier `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-356">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="f13d4-357">Ajoutez les espaces de noms pour <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> et la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="f13d4-357">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f13d4-358">Ajoutez des services d’intergiciels (middleware) de compression des réponses à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f13d4-358">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="f13d4-359">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f13d4-359">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f13d4-360">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="f13d4-360">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f13d4-361">Entre les points de terminaison pour le mappage du Blazor Hub et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="f13d4-361">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f13d4-362">Ajoutez des services d’intergiciels (middleware) de compression des réponses à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f13d4-362">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="f13d4-363">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f13d4-363">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f13d4-364">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="f13d4-364">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f13d4-365">Entre les points de terminaison pour le mappage du Blazor Hub et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="f13d4-365">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="f13d4-366">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="f13d4-366">Add Razor component code for chat</span></span>

1. <span data-ttu-id="f13d4-367">Ouvrez le fichier `Pages/Index.razor` .</span><span class="sxs-lookup"><span data-stu-id="f13d4-367">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f13d4-368">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f13d4-368">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f13d4-369">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f13d4-369">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="f13d4-370">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="f13d4-370">Run the app</span></span>

<span data-ttu-id="f13d4-371">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="f13d4-371">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f13d4-372">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f13d4-372">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f13d4-373">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f13d4-373">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f13d4-374">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f13d4-374">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f13d4-375">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f13d4-375">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f13d4-376">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f13d4-376">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f13d4-378">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f13d4-378">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f13d4-379">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f13d4-379">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f13d4-380">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f13d4-380">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f13d4-381">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f13d4-381">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f13d4-382">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f13d4-382">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f13d4-383">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f13d4-383">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f13d4-385">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f13d4-385">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f13d4-386">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f13d4-386">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f13d4-387">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="f13d4-387">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f13d4-388">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f13d4-388">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f13d4-389">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f13d4-389">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f13d4-390">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f13d4-390">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f13d4-392">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f13d4-392">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f13d4-393">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f13d4-393">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f13d4-394">Dans une interface de commande à partir du dossier du projet, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f13d4-394">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="f13d4-395">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="f13d4-395">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f13d4-396">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="f13d4-396">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f13d4-397">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="f13d4-397">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f13d4-399">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f13d4-399">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="f13d4-400">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f13d4-400">Next steps</span></span>

<span data-ttu-id="f13d4-401">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="f13d4-401">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f13d4-402">Créer un Blazor projet</span><span class="sxs-lookup"><span data-stu-id="f13d4-402">Create a Blazor project</span></span>
> * <span data-ttu-id="f13d4-403">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="f13d4-403">Add the SignalR client library</span></span>
> * <span data-ttu-id="f13d4-404">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f13d4-404">Add a SignalR hub</span></span>
> * <span data-ttu-id="f13d4-405">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="f13d4-405">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f13d4-406">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="f13d4-406">Add Razor component code for chat</span></span>

<span data-ttu-id="f13d4-407">Pour en savoir plus sur Blazor la création d’applications, consultez la Blazor documentation :</span><span class="sxs-lookup"><span data-stu-id="f13d4-407">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="f13d4-408"><xref:blazor/index>
> [Authentification du jeton du porteur avec les Identity événements serveur, WebSocket et Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="f13d4-408"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f13d4-409">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f13d4-409">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="f13d4-410">SignalR négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="f13d4-410">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
