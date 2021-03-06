---
title: Outils pour ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur les outils disponibles pour créer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 19270bb74326dccfee9466b7c1fa61daeab805a2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394458"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="7792b-103">Outils pour ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7792b-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="7792b-104">Installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="7792b-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="7792b-105">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="7792b-105">Create a new project.</span></span>

1. <span data-ttu-id="7792b-106">Sélectionnez **Blazor application**.</span><span class="sxs-lookup"><span data-stu-id="7792b-106">Select **Blazor App**.</span></span> <span data-ttu-id="7792b-107">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="7792b-107">Select **Next**.</span></span>

1. <span data-ttu-id="7792b-108">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="7792b-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="7792b-109">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="7792b-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="7792b-110">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="7792b-110">Select **Create**.</span></span>

1. <span data-ttu-id="7792b-111">Pour une Blazor WebAssembly expérience, choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="7792b-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="7792b-112">Pour une Blazor Server expérience, choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="7792b-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="7792b-113">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="7792b-113">Select **Create**.</span></span>

   <span data-ttu-id="7792b-114">Pour une expérience hébergée Blazor WebAssembly , activez la case à cocher **ASP.net Core hébergée** .</span><span class="sxs-lookup"><span data-stu-id="7792b-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="7792b-115">Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="7792b-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="7792b-116">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="7792b-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="7792b-117">Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="7792b-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="7792b-118">Lors de l’exécution d’une application hébergée Blazor WebAssembly , exécutez l’application à partir du projet de la solution **`Server`** .</span><span class="sxs-lookup"><span data-stu-id="7792b-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="7792b-119">Installez la dernière version du [Kit SDK .net Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="7792b-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="7792b-120">Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="7792b-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="7792b-121">Installez la dernière version de [Visual Studio code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="7792b-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="7792b-122">Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="7792b-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="7792b-123">Pour une Blazor WebAssembly expérience, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="7792b-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="7792b-124">Pour une expérience hébergée Blazor WebAssembly , ajoutez l’option de l’option Hosted ( `-ho` ou `--hosted` ) à la commande :</span><span class="sxs-lookup"><span data-stu-id="7792b-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="7792b-125">Pour une Blazor Server expérience, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="7792b-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="7792b-126">Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="7792b-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="7792b-127">Ouvrez le dossier `WebApplication1` dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7792b-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="7792b-128">L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet.</span><span class="sxs-lookup"><span data-stu-id="7792b-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="7792b-129">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="7792b-129">Select **Yes**.</span></span>

   <span data-ttu-id="7792b-130">**Blazor WebAssemblyConfiguration de lancement et de tâche hébergée**</span><span class="sxs-lookup"><span data-stu-id="7792b-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="7792b-131">Pour les Blazor WebAssembly solutions hébergées, ajoutez (ou déplacez) le `.vscode` dossier avec les `launch.json` `tasks.json` fichiers et dans le dossier parent de la solution, qui est le dossier qui contient les noms de dossier de projet standard `Client` , `Server` et `Shared` .</span><span class="sxs-lookup"><span data-stu-id="7792b-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="7792b-132">Mettez à jour ou confirmez que la configuration dans les `launch.json` `tasks.json` fichiers et exécute une application hébergée Blazor WebAssembly à partir du **`Server`** projet.</span><span class="sxs-lookup"><span data-stu-id="7792b-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="7792b-133">**`.vscode/launch.json`** ( `launch` Configuration) :</span><span class="sxs-lookup"><span data-stu-id="7792b-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="7792b-134">Dans la configuration précédente du répertoire de travail actuel ( `cwd` ), l' `{SERVER APP FOLDER}` espace réservé est le **`Server`** dossier du projet, généralement « `Server` ».</span><span class="sxs-lookup"><span data-stu-id="7792b-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="7792b-135">Si Microsoft Edge est utilisé et que Google Chrome n’est pas installé sur le système, ajoutez une propriété supplémentaire de `"browser": "edge"` à la configuration.</span><span class="sxs-lookup"><span data-stu-id="7792b-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="7792b-136">Exemple de dossier de projet de `Server` et qui génère Microsoft Edge comme navigateur pour les exécutions de débogage au lieu du navigateur par défaut Google Chrome :</span><span class="sxs-lookup"><span data-stu-id="7792b-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="7792b-137">**`.vscode/tasks.json`**(arguments de [ `dotnet` commande](/dotnet/core/tools/dotnet) ) :</span><span class="sxs-lookup"><span data-stu-id="7792b-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="7792b-138">Dans l’argument précédent :</span><span class="sxs-lookup"><span data-stu-id="7792b-138">In the preceding argument:</span></span>

   * <span data-ttu-id="7792b-139">L' `{SERVER APP FOLDER}` espace réservé est le **`Server`** dossier du projet, généralement « `Server` ».</span><span class="sxs-lookup"><span data-stu-id="7792b-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="7792b-140">L' `{PROJECT NAME}` espace réservé est le nom de l’application, généralement basé sur le nom de la solution suivi de « `.Server` » dans une application générée à partir du [ Blazor modèle de projet](xref:blazor/project-structure).</span><span class="sxs-lookup"><span data-stu-id="7792b-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the [Blazor project template](xref:blazor/project-structure).</span></span>

   <span data-ttu-id="7792b-141">L’exemple suivant du [didacticiel sur l’utilisation de SignalR avec une Blazor WebAssembly application](xref:tutorials/signalr-blazor) utilise un nom de dossier de projet `Server` et un nom de projet `BlazorWebAssemblySignalRApp.Server` :</span><span class="sxs-lookup"><span data-stu-id="7792b-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="7792b-142">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="7792b-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="7792b-143">Approuver un certificat de développement</span><span class="sxs-lookup"><span data-stu-id="7792b-143">Trust a development certificate</span></span>

<span data-ttu-id="7792b-144">Il n’existe pas de méthode centralisée pour approuver un certificat sur Linux.</span><span class="sxs-lookup"><span data-stu-id="7792b-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="7792b-145">En règle générale, l’une des approches suivantes est adoptée :</span><span class="sxs-lookup"><span data-stu-id="7792b-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="7792b-146">Excluez l’URL de l’application dans la liste d’exclusion du navigateur.</span><span class="sxs-lookup"><span data-stu-id="7792b-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="7792b-147">Faites confiance à tous les certificats auto-signés pour `localhost` .</span><span class="sxs-lookup"><span data-stu-id="7792b-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="7792b-148">Ajoutez le certificat à la liste des certificats approuvés dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="7792b-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="7792b-149">Pour plus d’informations, consultez les conseils fournis par le fabricant de votre navigateur et la distribution Linux.</span><span class="sxs-lookup"><span data-stu-id="7792b-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="7792b-150">Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="7792b-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="7792b-151">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="7792b-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="7792b-152">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="7792b-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="7792b-153">Pour une Blazor WebAssembly expérience, choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="7792b-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="7792b-154">Pour une Blazor Server expérience, choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="7792b-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="7792b-155">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="7792b-155">Select **Next**.</span></span>

   <span data-ttu-id="7792b-156">Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="7792b-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="7792b-157">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="7792b-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="7792b-158">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="7792b-158">Select **Next**.</span></span>

1. <span data-ttu-id="7792b-159">Pour une expérience hébergée Blazor WebAssembly , activez la case à cocher **ASP.net Core hébergée** .</span><span class="sxs-lookup"><span data-stu-id="7792b-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="7792b-160">Dans le champ **nom du projet** , nommez l’application `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="7792b-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="7792b-161">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="7792b-161">Select **Create**.</span></span>

1. <span data-ttu-id="7792b-162">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="7792b-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="7792b-163">Exécutez l’application avec   >  le bouton exécuter **Démarrer le débogage** ou exécuter (&#9654;) pour exécuter l’application *avec le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="7792b-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="7792b-164">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="7792b-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="7792b-165">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="7792b-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="7792b-166">Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="7792b-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="7792b-167">Lors de l’exécution d’une application hébergée Blazor WebAssembly , exécutez l’application à partir du projet de la solution **`Server`** .</span><span class="sxs-lookup"><span data-stu-id="7792b-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="7792b-168">Utiliser Visual Studio Code pour le développement multiplateforme Blazor</span><span class="sxs-lookup"><span data-stu-id="7792b-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="7792b-169">[Visual Studio code](https://code.visualstudio.com/) est un environnement de développement intégré (IDE) Open source et multiplateforme qui peut être utilisé pour développer des Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="7792b-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="7792b-170">Utilisez l’interface CLI .NET pour créer une nouvelle Blazor application en vue du développement avec Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="7792b-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="7792b-171">Pour plus d’informations, consultez la [version Linux de cet article](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="7792b-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="7792b-172">Blazor options de modèle</span><span class="sxs-lookup"><span data-stu-id="7792b-172">Blazor template options</span></span>

<span data-ttu-id="7792b-173">Le Blazor Framework fournit des modèles pour créer des applications pour chacun des deux Blazor modèles d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="7792b-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="7792b-174">Les modèles sont utilisés pour créer des Blazor projets et des solutions, quels que soient les outils que vous sélectionnez pour le Blazor développement (Visual Studio, Visual Studio pour Mac, Visual Studio code ou l’interface de commande .net) :</span><span class="sxs-lookup"><span data-stu-id="7792b-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="7792b-175">Blazor WebAssembly modèle de projet : `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="7792b-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="7792b-176">Blazor Server modèle de projet : `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="7792b-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="7792b-177">Pour plus d’informations sur les Blazor modèles d’hébergement de, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="7792b-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="7792b-178">Pour plus d’informations sur les Blazor modèles de projet, consultez <xref:blazor/project-structure> .</span><span class="sxs-lookup"><span data-stu-id="7792b-178">For more information on Blazor project templates, see <xref:blazor/project-structure>.</span></span>

<span data-ttu-id="7792b-179">Les options de modèle sont disponibles en passant l’option d’aide ( `-h` ou `--help` ) à la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande CLI dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="7792b-179">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```

## <a name="additional-resources"></a><span data-ttu-id="7792b-180">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7792b-180">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/project-structure>
