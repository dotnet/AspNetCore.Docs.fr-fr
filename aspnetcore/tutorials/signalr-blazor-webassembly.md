---
title: Utiliser ASP.NET Core SignalR avec une application hébergée Blazor WebAssembly
author: guardrex
description: Créez une application de conversation qui utilise ASP.NET Core SignalR avec Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797351"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="732eb-103">Utiliser ASP.NET Core SignalR avec une application hébergée Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="732eb-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="732eb-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="732eb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="732eb-105">Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de SignalR avec Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="732eb-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="732eb-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="732eb-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="732eb-107">Créer un Blazor WebAssembly projet d’application hébergée</span><span class="sxs-lookup"><span data-stu-id="732eb-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="732eb-108">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="732eb-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="732eb-109">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="732eb-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="732eb-110">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="732eb-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="732eb-111">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="732eb-111">Add Razor component code for chat</span></span>

<span data-ttu-id="732eb-112">À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.</span><span class="sxs-lookup"><span data-stu-id="732eb-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="732eb-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="732eb-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="732eb-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="732eb-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="732eb-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="732eb-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="732eb-116">[Visual Studio 2019 16,8 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="732eb-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="732eb-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="732eb-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="732eb-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="732eb-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="732eb-119">Visual Studio pour Mac version 8,8 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="732eb-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="732eb-120">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="732eb-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="732eb-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="732eb-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="732eb-122">[Visual Studio 2019 16,6 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="732eb-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="732eb-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="732eb-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="732eb-124">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="732eb-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="732eb-125">Visual Studio pour Mac version 8,6 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="732eb-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="732eb-126">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="732eb-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="732eb-127">Créer un projet d’application hébergée Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="732eb-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="732eb-128">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="732eb-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="732eb-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="732eb-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="732eb-130">Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="732eb-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="732eb-131">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="732eb-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="732eb-132">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="732eb-132">Create a new project.</span></span>

1. <span data-ttu-id="732eb-133">Sélectionnez **Blazor application** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="732eb-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="732eb-134">Tapez `BlazorSignalRApp` dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="732eb-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="732eb-135">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="732eb-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="732eb-136">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="732eb-136">Select **Create**.</span></span>

1. <span data-ttu-id="732eb-137">Choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="732eb-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="732eb-138">Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="732eb-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="732eb-139">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="732eb-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="732eb-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="732eb-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="732eb-141">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="732eb-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="732eb-142">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="732eb-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="732eb-143">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="732eb-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="732eb-144">Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="732eb-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="732eb-145">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="732eb-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="732eb-146">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="732eb-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="732eb-147">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="732eb-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="732eb-148">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="732eb-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="732eb-149">Choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="732eb-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="732eb-150">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="732eb-150">Select **Next**.</span></span>

1. <span data-ttu-id="732eb-151">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="732eb-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="732eb-152">Activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="732eb-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="732eb-153">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="732eb-153">Select **Next**.</span></span>

1. <span data-ttu-id="732eb-154">Dans le champ **nom du projet** , nommez l’application `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="732eb-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="732eb-155">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="732eb-155">Select **Create**.</span></span>

   <span data-ttu-id="732eb-156">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="732eb-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="732eb-157">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="732eb-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="732eb-158">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="732eb-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="732eb-159">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="732eb-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="732eb-160">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="732eb-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="732eb-161">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="732eb-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="732eb-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="732eb-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="732eb-163">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorSignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="732eb-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="732eb-164">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="732eb-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="732eb-165">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="732eb-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="732eb-166">Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package et sélectionnez **installer**.</span><span class="sxs-lookup"><span data-stu-id="732eb-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="732eb-167">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="732eb-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="732eb-168">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="732eb-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="732eb-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="732eb-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="732eb-170">Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="732eb-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="732eb-171">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="732eb-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="732eb-172">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorSignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="732eb-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="732eb-173">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="732eb-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="732eb-174">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="732eb-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="732eb-175">Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="732eb-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="732eb-176">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="732eb-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="732eb-177">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="732eb-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="732eb-178">Dans une interface de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="732eb-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="732eb-179">Ajouter le package System. Text. encoders. Web</span><span class="sxs-lookup"><span data-stu-id="732eb-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="732eb-180">En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5.0.0 dans une application ASP.NET Core 3,1, le `BlazorSignalRApp.Server` projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="732eb-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="732eb-181">Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="732eb-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="732eb-182">Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="732eb-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="732eb-183">Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au `BlazorSignalRApp.Server` projet de la solution ASP.net Core hébergée 3,1 Blazor , suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="732eb-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="732eb-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="732eb-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="732eb-185">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorSignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="732eb-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="732eb-186">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="732eb-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="732eb-187">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="732eb-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="732eb-188">Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package et sélectionnez **installer**.</span><span class="sxs-lookup"><span data-stu-id="732eb-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="732eb-189">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="732eb-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="732eb-190">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="732eb-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="732eb-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="732eb-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="732eb-192">Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="732eb-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="732eb-193">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="732eb-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="732eb-194">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorSignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="732eb-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="732eb-195">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="732eb-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="732eb-196">Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="732eb-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="732eb-197">Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="732eb-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="732eb-198">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="732eb-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="732eb-199">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="732eb-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="732eb-200">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="732eb-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="732eb-201">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="732eb-201">Add a SignalR hub</span></span>

<span data-ttu-id="732eb-202">Dans le `BlazorSignalRApp.Server` projet, créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="732eb-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="732eb-203">Ajouter des services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="732eb-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="732eb-204">Dans le projet `BlazorSignalRApp.Server`, ouvrez le fichier `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="732eb-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="732eb-205">Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="732eb-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="732eb-206">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="732eb-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="732eb-207">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="732eb-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="732eb-208">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="732eb-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="732eb-209">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="732eb-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="732eb-210">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="732eb-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="732eb-211">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="732eb-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="732eb-212">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="732eb-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="732eb-213">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="732eb-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="732eb-214">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="732eb-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="732eb-215">Dans le projet `BlazorSignalRApp.Client`, ouvrez le fichier `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="732eb-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="732eb-216">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="732eb-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="732eb-217">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="732eb-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="732eb-218">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="732eb-218">Run the app</span></span>

<span data-ttu-id="732eb-219">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="732eb-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="732eb-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="732eb-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="732eb-221">Dans **Explorateur de solutions**, sélectionnez le `BlazorSignalRApp.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="732eb-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="732eb-222">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="732eb-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="732eb-223">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="732eb-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="732eb-224">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="732eb-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="732eb-225">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="732eb-225">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="732eb-227">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="732eb-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="732eb-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="732eb-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="732eb-229">Lorsque VS Code propose de créer un profil de lancement pour l’application serveur ( `.vscode/launch.json` ), l' `program` entrée ressemble à ce qui suit pour pointer vers l’assembly de l’application ( `{APPLICATION NAME}.Server.dll` ) :</span><span class="sxs-lookup"><span data-stu-id="732eb-229">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="732eb-230">Lorsque VS Code propose de créer un profil de lancement pour l’application serveur ( `.vscode/launch.json` ), l' `program` entrée ressemble à ce qui suit pour pointer vers l’assembly de l’application ( `{APPLICATION NAME}.Server.dll` ) :</span><span class="sxs-lookup"><span data-stu-id="732eb-230">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="732eb-231">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="732eb-231">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="732eb-232">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="732eb-232">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="732eb-233">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="732eb-233">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="732eb-234">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="732eb-234">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="732eb-236">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="732eb-236">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="732eb-237">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="732eb-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="732eb-238">Dans la barre latérale **solution** , sélectionnez le `BlazorSignalRApp.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="732eb-238">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="732eb-239">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="732eb-239">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="732eb-240">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="732eb-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="732eb-241">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="732eb-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="732eb-242">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="732eb-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="732eb-244">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="732eb-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="732eb-245">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="732eb-245">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="732eb-246">Dans une interface de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="732eb-246">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="732eb-247">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="732eb-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="732eb-248">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="732eb-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="732eb-249">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="732eb-249">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="732eb-251">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="732eb-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="732eb-252">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="732eb-252">Next steps</span></span>

<span data-ttu-id="732eb-253">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="732eb-253">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="732eb-254">Créer un Blazor WebAssembly projet d’application hébergée</span><span class="sxs-lookup"><span data-stu-id="732eb-254">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="732eb-255">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="732eb-255">Add the SignalR client library</span></span>
> * <span data-ttu-id="732eb-256">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="732eb-256">Add a SignalR hub</span></span>
> * <span data-ttu-id="732eb-257">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="732eb-257">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="732eb-258">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="732eb-258">Add Razor component code for chat</span></span>

<span data-ttu-id="732eb-259">Pour en savoir plus sur Blazor la création d’applications, consultez la Blazor documentation :</span><span class="sxs-lookup"><span data-stu-id="732eb-259">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="732eb-260"><xref:blazor/index>
> [Authentification du jeton du porteur avec les Identity événements serveur, WebSocket et Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="732eb-260"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="732eb-261">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="732eb-261">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="732eb-262">SignalR négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="732eb-262">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
