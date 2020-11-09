---
title: Créer un serveur et un client gRPC .NET Core dans ASP.NET Core
author: juntaoluo
description: Ce tutoriel montre comment créer un service gRPC et un client gRPC sur ASP.NET Core. Découvrez comment créer un projet de service gRPC, modifier un fichier proto et ajouter un appel duplex de streaming.
ms.author: johluo
ms.date: 10/23/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 9388a2f814008ebb50171f85b8baccf6dadfac27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057022"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="9549e-104">Didacticiel : créer un client et un serveur gRPC dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9549e-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="9549e-105">Par [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="9549e-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="9549e-106">Ce tutoriel montre comment créer un client [gRPC](https://grpc.io/docs/guides/) .NET Core et un serveur gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9549e-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="9549e-107">À la fin, vous disposerez d’un client gRPC qui communique avec le service Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="9549e-108">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9549e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9549e-109">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="9549e-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9549e-110">Créer un serveur gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="9549e-111">Créez un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="9549e-112">Tester le service du client gRPC avec le service Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9549e-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="9549e-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9549e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9549e-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9549e-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9549e-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9549e-116">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9549e-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="9549e-117">Visual Studio pour Mac version 8,7 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="9549e-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="9549e-118">Créer un service gRPC</span><span class="sxs-lookup"><span data-stu-id="9549e-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9549e-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9549e-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9549e-120">Démarrez Visual Studio et sélectionnez **Créer un projet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-120">Start Visual Studio and select **Create a new project** .</span></span> <span data-ttu-id="9549e-121">Vous pouvez également, dans le menu **Fichier** de Visual Studio, sélectionner **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="9549e-122">Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **gRPC service** , puis cliquez sur **suivant** :</span><span class="sxs-lookup"><span data-stu-id="9549e-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next** :</span></span>

  ![Boîte de dialogue créer un nouveau projet dans Visual Studio](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="9549e-124">Nommez le projet **GrpcGreeter** .</span><span class="sxs-lookup"><span data-stu-id="9549e-124">Name the project **GrpcGreeter** .</span></span> <span data-ttu-id="9549e-125">Il est important de nommer le projet *GrpcGreeter* afin que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="9549e-125">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="9549e-126">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="9549e-126">Select **Create** .</span></span>
* <span data-ttu-id="9549e-127">Dans la boîte de dialogue **Créer un service gRPC**  :</span><span class="sxs-lookup"><span data-stu-id="9549e-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="9549e-128">Le modèle **Service gRPC** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="9549e-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="9549e-129">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="9549e-129">Select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9549e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9549e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9549e-131">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9549e-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9549e-132">Remplacez répertoires ( `cd` ) par un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="9549e-132">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="9549e-133">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9549e-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="9549e-134">La commande `dotnet new` crée un nouveau service gRPC dans le dossier *GrpcGreeter* .</span><span class="sxs-lookup"><span data-stu-id="9549e-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="9549e-135">La commande `code` ouvre le dossier *GrpcGreeter* dans une nouvelle instance de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9549e-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="9549e-136">Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’GrpcGreeter'. Ajoutez-les ?**</span><span class="sxs-lookup"><span data-stu-id="9549e-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="9549e-137">Sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="9549e-137">Select **Yes** .</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9549e-138">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9549e-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9549e-139">Démarrez Visual Studio pour Mac et sélectionnez **créer un nouveau projet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-139">Start Visual Studio for Mac and select **Create a new project** .</span></span> <span data-ttu-id="9549e-140">Vous pouvez également, dans le menu **Fichier** de Visual Studio, sélectionner **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="9549e-141">Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **Web et**  >  **application** console  >  **gRPC service** , puis sélectionnez **suivant** :</span><span class="sxs-lookup"><span data-stu-id="9549e-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next** :</span></span>

  ![Boîte de dialogue créer un nouveau projet sur macOS](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="9549e-143">Sélectionnez **.net Core 3,1** pour la version cible de .NET Framework, puis sélectionnez **suivant** .</span><span class="sxs-lookup"><span data-stu-id="9549e-143">Select **.NET Core 3.1** for the target framework and select **Next** .</span></span>
* <span data-ttu-id="9549e-144">Nommez le projet **GrpcGreeter** .</span><span class="sxs-lookup"><span data-stu-id="9549e-144">Name the project **GrpcGreeter** .</span></span> <span data-ttu-id="9549e-145">Il est important de nommer le projet *GrpcGreeter* afin que les espaces de noms correspondent quand vous copiez et collez du code.</span><span class="sxs-lookup"><span data-stu-id="9549e-145">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="9549e-146">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="9549e-146">Select **Create** .</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="9549e-147">Exécuter le service</span><span class="sxs-lookup"><span data-stu-id="9549e-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="9549e-148">Les journaux indiquent que le service écoute sur `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9549e-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="9549e-149">Le modèle gRPC est configuré pour utiliser le protocole [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="9549e-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="9549e-150">Les clients gRPC doivent utiliser le protocole HTTPS pour appeler le serveur.</span><span class="sxs-lookup"><span data-stu-id="9549e-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="9549e-151">MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS.</span><span class="sxs-lookup"><span data-stu-id="9549e-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="9549e-152">Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="9549e-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="9549e-153">Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="9549e-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="9549e-154">Examiner les fichiers projet</span><span class="sxs-lookup"><span data-stu-id="9549e-154">Examine the project files</span></span>

<span data-ttu-id="9549e-155">Fichiers projet *GrpcGreeter*  :</span><span class="sxs-lookup"><span data-stu-id="9549e-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="9549e-156">*Greeter. proto* : le fichier *protos/Greeter. proto* définit le `Greeter` gRPC et est utilisé pour générer les ressources du serveur gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-156">*greet.proto* : The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="9549e-157">Pour plus d’informations, consultez [Introduction à gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="9549e-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="9549e-158">Dossier *services* : contient l’implémentation du `Greeter` service.</span><span class="sxs-lookup"><span data-stu-id="9549e-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="9549e-159">*appSettings.jssur* : contient des données de configuration, telles que le protocole utilisé par Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9549e-159">*appSettings.json* : Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="9549e-160">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9549e-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="9549e-161">*Program.cs* : contient le point d’entrée pour le service gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-161">*Program.cs* : Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="9549e-162">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9549e-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="9549e-163">*Startup.cs* : contient le code qui configure le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="9549e-163">*Startup.cs* : Contains code that configures app behavior.</span></span> <span data-ttu-id="9549e-164">Pour plus d’informations, consultez [Démarrage des applications](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="9549e-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="9549e-165">Créer le client gRPC dans une application console .NET</span><span class="sxs-lookup"><span data-stu-id="9549e-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9549e-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9549e-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9549e-167">Ouvrez une deuxième instance de Visual Studio et sélectionnez **Créer un projet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-167">Open a second instance of Visual Studio and select **Create a new project** .</span></span>
* <span data-ttu-id="9549e-168">Dans la boîte de dialogue **Créer un projet** , sélectionnez **Application console (.NET Core)** , puis sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="9549e-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next** .</span></span>
* <span data-ttu-id="9549e-169">Dans la zone de texte **nom du projet** , entrez **GrpcGreeterClient** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="9549e-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9549e-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9549e-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9549e-171">Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9549e-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9549e-172">Remplacez répertoires ( `cd` ) par un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="9549e-172">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="9549e-173">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9549e-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9549e-174">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9549e-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9549e-175">Suivez les instructions de [Création d’une solution .NET Core complète sur macOS à l’aide de Visual Studio pour Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) pour créer une application console appelée *GrpcGreeterClient* .</span><span class="sxs-lookup"><span data-stu-id="9549e-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient* .</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="9549e-176">Ajouter les packages nécessaires</span><span class="sxs-lookup"><span data-stu-id="9549e-176">Add required packages</span></span>

<span data-ttu-id="9549e-177">Le projet client gRPC requiert les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="9549e-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="9549e-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), qui contient le client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9549e-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="9549e-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), qui contient des API de messages protobuf pour C#.</span><span class="sxs-lookup"><span data-stu-id="9549e-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="9549e-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), qui contient la prise en charge des outils C# pour les fichiers protobuf.</span><span class="sxs-lookup"><span data-stu-id="9549e-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="9549e-181">Le package d’outils n’est pas nécessaire lors de l’exécution. La dépendance est donc marquée avec `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="9549e-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9549e-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9549e-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9549e-183">Installez les packages à l’aide de la console PMC (console du Gestionnaire de package) ou à partir de Gérer les packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="9549e-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="9549e-184">Option de la console du Gestionnaire de package pour installer des packages</span><span class="sxs-lookup"><span data-stu-id="9549e-184">PMC option to install packages</span></span>

* <span data-ttu-id="9549e-185">Dans Visual Studio, sélectionnez **Outils**  >  **Gestionnaire de package NuGet**  >  **console du gestionnaire** de package</span><span class="sxs-lookup"><span data-stu-id="9549e-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="9549e-186">Dans la fenêtre **Gestionnaire de package** , exécutez `cd GrpcGreeterClient` pour accéder au dossier contenant les fichiers *GrpcGreeterClient.csproj* .</span><span class="sxs-lookup"><span data-stu-id="9549e-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="9549e-187">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9549e-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="9549e-188">Option Gérer les packages NuGet pour installer les packages</span><span class="sxs-lookup"><span data-stu-id="9549e-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="9549e-189">Cliquez avec le bouton droit sur le projet dans **Explorateur de solutions**  >  **gérer les packages NuGet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages** .</span></span>
* <span data-ttu-id="9549e-190">Sélectionnez l’onglet **Parcourir** .</span><span class="sxs-lookup"><span data-stu-id="9549e-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="9549e-191">Entrez **Grpc.Net.Client** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="9549e-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="9549e-192">Sélectionnez le package **Grpc.Net.Client** sous l’onglet **Parcourir** et sélectionnez **Installer** .</span><span class="sxs-lookup"><span data-stu-id="9549e-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install** .</span></span>
* <span data-ttu-id="9549e-193">Répétez ces étapes pour `Google.Protobuf` et `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="9549e-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9549e-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9549e-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9549e-195">Exécutez les commandes suivantes à partir du **Terminal intégré**  :</span><span class="sxs-lookup"><span data-stu-id="9549e-195">Run the following commands from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9549e-196">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9549e-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9549e-197">Cliquez avec le bouton droit sur le projet **GrpcGreeterClient** dans le **panneau solutions** , puis sélectionnez **gérer les packages NuGet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages** .</span></span>
* <span data-ttu-id="9549e-198">Entrez **Grpc.Net.Client** dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="9549e-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="9549e-199">Sélectionnez le package **.net. client GRPC** dans le volet des résultats, puis sélectionnez **Ajouter un package** .</span><span class="sxs-lookup"><span data-stu-id="9549e-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package** .</span></span>
* <span data-ttu-id="9549e-200">Sélectionnez le bouton **accepter** dans la boîte de dialogue **accepter la licence** .</span><span class="sxs-lookup"><span data-stu-id="9549e-200">Select the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="9549e-201">Répétez ces étapes pour `Google.Protobuf` et `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="9549e-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="9549e-202">Ajouter greet.proto</span><span class="sxs-lookup"><span data-stu-id="9549e-202">Add greet.proto</span></span>

* <span data-ttu-id="9549e-203">Créez un dossier *Protos* dans le projet du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="9549e-204">Copiez le fichier *Protos\greet.proto* du service Greeter gRPC vers le projet du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="9549e-205">Mettez à jour l’espace de noms à l’intérieur du `greet.proto` fichier vers l’espace de noms du projet :</span><span class="sxs-lookup"><span data-stu-id="9549e-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="9549e-206">Modifiez le fichier projet *GrpcGreeterClient.csproj*  :</span><span class="sxs-lookup"><span data-stu-id="9549e-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="9549e-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9549e-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="9549e-208">Cliquez avec le bouton droit sur le projet et sélectionnez **Modifier le fichier de projet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-208">Right-click the project and select **Edit Project File** .</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="9549e-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9549e-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="9549e-210">Sélectionnez le fichier *GrpcGreeterClient.csproj* .</span><span class="sxs-lookup"><span data-stu-id="9549e-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9549e-211">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9549e-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="9549e-212">Cliquez avec le bouton droit sur le projet et sélectionnez **Modifier le fichier de projet** .</span><span class="sxs-lookup"><span data-stu-id="9549e-212">Right-click the project and select **Edit Project File** .</span></span>

  ---

* <span data-ttu-id="9549e-213">Ajoutez un groupe d’éléments avec un élément `<Protobuf>` qui fait référence au fichier *greet.proto* :</span><span class="sxs-lookup"><span data-stu-id="9549e-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="9549e-214">Créer le client Greeter</span><span class="sxs-lookup"><span data-stu-id="9549e-214">Create the Greeter client</span></span>

<span data-ttu-id="9549e-215">Générez le projet client pour créer les types dans l' `GrpcGreeter` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="9549e-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="9549e-216">Les types `GrpcGreeter` sont générés automatiquement par le processus de génération.</span><span class="sxs-lookup"><span data-stu-id="9549e-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="9549e-217">Mettez à jour le fichier *Program.cs* du client gRPC par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="9549e-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="9549e-218">*Program.cs* contient le point d’entrée et la logique du client gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="9549e-219">Le client Greeter est créé en :</span><span class="sxs-lookup"><span data-stu-id="9549e-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="9549e-220">Instanciant un `GrpcChannel` contenant les informations de création de la connexion au service gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="9549e-221">Utilisant le `GrpcChannel` pour construire le client Greeter :</span><span class="sxs-lookup"><span data-stu-id="9549e-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="9549e-222">Le client Greeter appelle la méthode `SayHello` asynchrone.</span><span class="sxs-lookup"><span data-stu-id="9549e-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="9549e-223">Le résultat de l’appel `SayHello` s’affiche :</span><span class="sxs-lookup"><span data-stu-id="9549e-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="9549e-224">Tester le client gRPC avec le service Greeter gRPC</span><span class="sxs-lookup"><span data-stu-id="9549e-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9549e-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9549e-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9549e-226">Dans le service Greeter, appuyez sur `Ctrl+F5` pour démarrer le serveur sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="9549e-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="9549e-227">Dans le projet `GrpcGreeterClient`, appuyez sur `Ctrl+F5` pour démarrer le client sans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="9549e-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9549e-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9549e-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9549e-229">Démarrez le service Greeter.</span><span class="sxs-lookup"><span data-stu-id="9549e-229">Start the Greeter service.</span></span>
* <span data-ttu-id="9549e-230">Démarrez le client.</span><span class="sxs-lookup"><span data-stu-id="9549e-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9549e-231">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9549e-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9549e-232">En raison du [problème http/2 TLS mentionné précédemment sur la solution de contournement MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), vous devez mettre à jour l’adresse du canal dans le client en « http://localhost:5000 ».</span><span class="sxs-lookup"><span data-stu-id="9549e-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="9549e-233">Mettez à jour la ligne 13 de **GrpcGreeterClient/Program. cs** pour lire :</span><span class="sxs-lookup"><span data-stu-id="9549e-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="9549e-234">Démarrez le service Greeter.</span><span class="sxs-lookup"><span data-stu-id="9549e-234">Start the Greeter service.</span></span>
* <span data-ttu-id="9549e-235">Démarrez le client.</span><span class="sxs-lookup"><span data-stu-id="9549e-235">Start the client.</span></span>

---

<span data-ttu-id="9549e-236">Le client envoie un message d’accueil au service avec un message contenant son nom, *GreeterClient* .</span><span class="sxs-lookup"><span data-stu-id="9549e-236">The client sends a greeting to the service with a message containing its name, *GreeterClient* .</span></span> <span data-ttu-id="9549e-237">Le service envoie le message « Hello GreeterClient » comme réponse.</span><span class="sxs-lookup"><span data-stu-id="9549e-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="9549e-238">La réponse « Hello GreeterClient » s’affiche dans l’invite de commandes :</span><span class="sxs-lookup"><span data-stu-id="9549e-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="9549e-239">Le service gRPC enregistre les détails de l’appel réussi dans les journaux écrits dans l’invite de commandes :</span><span class="sxs-lookup"><span data-stu-id="9549e-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="9549e-240">Le code de cet article requiert le certificat de développement ASP.NET Core HTTPS pour sécuriser le service gRPC.</span><span class="sxs-lookup"><span data-stu-id="9549e-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="9549e-241">Si le client .NET gRPC échoue avec le message `The remote certificate is invalid according to the validation procedure.` ou `The SSL connection could not be established.` , le certificat de développement n’est pas approuvé.</span><span class="sxs-lookup"><span data-stu-id="9549e-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="9549e-242">Pour résoudre ce problème, consultez [appeler un service gRPC avec un certificat non approuvé/non valide](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="9549e-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="9549e-243">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="9549e-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
