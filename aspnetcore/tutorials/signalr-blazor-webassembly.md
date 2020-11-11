---
title: 'Utiliser ASP.NET Core :::no-loc(SignalR)::: avec :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Créez une application de conversation qui utilise ASP.NET Core :::no-loc(SignalR)::: avec :::no-loc(Blazor WebAssembly)::: .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: afbfc44db8cb833df7a79061f9bd73052859fec2
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508173"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="72658-103">Utiliser ASP.NET Core :::no-loc(SignalR)::: avec :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="72658-103">Use ASP.NET Core :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="72658-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="72658-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="72658-105">Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de :::no-loc(SignalR)::: avec :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="72658-105">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="72658-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="72658-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="72658-107">Créer un :::no-loc(Blazor WebAssembly)::: projet d’application hébergée</span><span class="sxs-lookup"><span data-stu-id="72658-107">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="72658-108">Ajouter la :::no-loc(SignalR)::: bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="72658-108">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="72658-109">Ajouter un :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="72658-109">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="72658-110">Ajouter des :::no-loc(SignalR)::: services et un point de terminaison pour le :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="72658-110">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="72658-111">Ajouter :::no-loc(Razor)::: du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="72658-111">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="72658-112">À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.</span><span class="sxs-lookup"><span data-stu-id="72658-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="72658-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72658-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="72658-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="72658-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="72658-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72658-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="72658-116">[Visual Studio 2019 16,8 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="72658-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="72658-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72658-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="72658-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="72658-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="72658-119">Visual Studio pour Mac version 8,8 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="72658-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="72658-120">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="72658-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="72658-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72658-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="72658-122">[Visual Studio 2019 16,6 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="72658-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="72658-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72658-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="72658-124">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="72658-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="72658-125">Visual Studio pour Mac version 8,6 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="72658-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="72658-126">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="72658-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="72658-127">Créer un projet d’application hébergée :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="72658-127">Create a hosted :::no-loc(Blazor WebAssembly)::: app project</span></span>

<span data-ttu-id="72658-128">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="72658-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="72658-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72658-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="72658-130">Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="72658-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="72658-131">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="72658-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="72658-132">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="72658-132">Create a new project.</span></span>

1. <span data-ttu-id="72658-133">Sélectionnez **:::no-loc(Blazor)::: application** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="72658-133">Select **:::no-loc(Blazor)::: App** and select **Next**.</span></span>

1. <span data-ttu-id="72658-134">Tapez `:::no-loc(Blazor)::::::no-loc(SignalR):::App` dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="72658-134">Type `:::no-loc(Blazor)::::::no-loc(SignalR):::App` in the **Project name** field.</span></span> <span data-ttu-id="72658-135">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="72658-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="72658-136">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="72658-136">Select **Create**.</span></span>

1. <span data-ttu-id="72658-137">Choisissez le modèle d' **:::no-loc(Blazor WebAssembly)::: application** .</span><span class="sxs-lookup"><span data-stu-id="72658-137">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span>

1. <span data-ttu-id="72658-138">Sous **avancé** , activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="72658-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="72658-139">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="72658-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="72658-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72658-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="72658-141">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="72658-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
   ```

1. <span data-ttu-id="72658-142">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="72658-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="72658-143">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="72658-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="72658-144">Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="72658-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="72658-145">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="72658-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="72658-146">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="72658-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="72658-147">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="72658-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="72658-148">Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="72658-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="72658-149">Choisissez le modèle d' **:::no-loc(Blazor WebAssembly)::: application** .</span><span class="sxs-lookup"><span data-stu-id="72658-149">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="72658-150">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="72658-150">Select **Next**.</span></span>

1. <span data-ttu-id="72658-151">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="72658-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="72658-152">Activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="72658-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="72658-153">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="72658-153">Select **Next**.</span></span>

1. <span data-ttu-id="72658-154">Dans le champ **nom du projet** , nommez l’application `:::no-loc(Blazor)::::::no-loc(SignalR):::App` .</span><span class="sxs-lookup"><span data-stu-id="72658-154">In the **Project Name** field, name the app `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span></span> <span data-ttu-id="72658-155">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="72658-155">Select **Create**.</span></span>

   <span data-ttu-id="72658-156">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="72658-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="72658-157">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="72658-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="72658-158">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="72658-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="72658-159">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="72658-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="72658-160">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="72658-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="72658-161">Ajouter la :::no-loc(SignalR)::: bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="72658-161">Add the :::no-loc(SignalR)::: client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="72658-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72658-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="72658-163">Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="72658-163">In **Solution Explorer** , right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="72658-164">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="72658-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="72658-165">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="72658-165">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="72658-166">Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package et sélectionnez **installer**.</span><span class="sxs-lookup"><span data-stu-id="72658-166">In the search results, select the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="72658-167">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="72658-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="72658-168">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="72658-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="72658-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72658-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="72658-170">Dans le **Terminal intégré** ( **Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="72658-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="72658-171">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="72658-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="72658-172">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` projet et sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="72658-172">In the **Solution** sidebar, right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="72658-173">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="72658-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="72658-174">Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="72658-174">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="72658-175">Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="72658-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="72658-176">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="72658-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="72658-177">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="72658-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="72658-178">Dans une interface de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="72658-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd :::no-loc(Blazor)::::::no-loc(SignalR):::App
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="72658-179">Ajouter un :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="72658-179">Add a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="72658-180">Dans le `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` projet, créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="72658-180">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="72658-181">Ajouter des services et un point de terminaison pour le :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="72658-181">Add services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>

1. <span data-ttu-id="72658-182">Dans le projet `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`, ouvrez le fichier `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="72658-182">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="72658-183">Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="72658-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using :::no-loc(Blazor)::::::no-loc(SignalR):::App.Server.Hubs;
   ```

1. <span data-ttu-id="72658-184">Ajoutez :::no-loc(SignalR)::: et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="72658-184">Add :::no-loc(SignalR)::: and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="72658-185">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="72658-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="72658-186">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="72658-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="72658-187">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="72658-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="72658-188">Ajouter :::no-loc(Razor)::: du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="72658-188">Add :::no-loc(Razor)::: component code for chat</span></span>

1. <span data-ttu-id="72658-189">Dans le projet `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client`, ouvrez le fichier `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="72658-189">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="72658-190">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="72658-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="72658-191">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="72658-191">Run the app</span></span>

1. <span data-ttu-id="72658-192">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="72658-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="72658-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72658-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="72658-194">Dans **Explorateur de solutions** , sélectionnez le `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="72658-194">In **Solution Explorer** , select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="72658-195">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="72658-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="72658-196">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="72658-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="72658-197">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="72658-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="72658-198">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="72658-198">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="72658-200">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="72658-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="72658-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72658-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="72658-202">Lorsque VS Code propose de créer un profil de lancement pour l’application serveur ( `.vscode/launch.json` ), l' `program` entrée ressemble à ce qui suit pour pointer vers l’assembly de l’application ( `{APPLICATION NAME}.Server.dll` ) :</span><span class="sxs-lookup"><span data-stu-id="72658-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="72658-203">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="72658-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="72658-204">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="72658-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="72658-205">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="72658-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="72658-206">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="72658-206">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="72658-208">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="72658-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="72658-209">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="72658-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="72658-210">Dans la barre latérale **solution** , sélectionnez le `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` projet.</span><span class="sxs-lookup"><span data-stu-id="72658-210">In the **Solution** sidebar, select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="72658-211">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="72658-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="72658-212">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="72658-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="72658-213">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="72658-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="72658-214">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="72658-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="72658-216">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="72658-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="72658-217">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="72658-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="72658-218">Dans une interface de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="72658-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="72658-219">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="72658-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="72658-220">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message.</span><span class="sxs-lookup"><span data-stu-id="72658-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="72658-221">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="72658-221">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="72658-223">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="72658-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="72658-224">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="72658-224">Next steps</span></span>

<span data-ttu-id="72658-225">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="72658-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="72658-226">Créer un :::no-loc(Blazor WebAssembly)::: projet d’application hébergée</span><span class="sxs-lookup"><span data-stu-id="72658-226">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="72658-227">Ajouter la :::no-loc(SignalR)::: bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="72658-227">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="72658-228">Ajouter un :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="72658-228">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="72658-229">Ajouter des :::no-loc(SignalR)::: services et un point de terminaison pour le :::no-loc(SignalR)::: Hub</span><span class="sxs-lookup"><span data-stu-id="72658-229">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="72658-230">Ajouter :::no-loc(Razor)::: du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="72658-230">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="72658-231">Pour en savoir plus sur :::no-loc(Blazor)::: la création d’applications, consultez la :::no-loc(Blazor)::: documentation :</span><span class="sxs-lookup"><span data-stu-id="72658-231">To learn more about building :::no-loc(Blazor)::: apps, see the :::no-loc(Blazor)::: documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="72658-232"><xref:blazor/index>
> [Authentification du jeton du porteur avec les :::no-loc(Identity)::: événements serveur, WebSocket et Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="72658-232"><xref:blazor/index>
[Bearer token authentication with :::no-loc(Identity)::: Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72658-233">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="72658-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="72658-234">:::no-loc(SignalR)::: négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="72658-234">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
