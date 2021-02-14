---
title: Outils pour ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur les outils disponibles pour créer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
ms.openlocfilehash: a17b16563ac12d634e6bdc32638991f45e2a66d5
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280675"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="6f774-103">Outils pour ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6f774-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="6f774-104">Installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="6f774-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="6f774-105">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="6f774-105">Create a new project.</span></span>

1. <span data-ttu-id="6f774-106">Sélectionnez **Blazor application**.</span><span class="sxs-lookup"><span data-stu-id="6f774-106">Select **Blazor App**.</span></span> <span data-ttu-id="6f774-107">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="6f774-107">Select **Next**.</span></span>

1. <span data-ttu-id="6f774-108">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="6f774-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="6f774-109">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="6f774-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6f774-110">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="6f774-110">Select **Create**.</span></span>

1. <span data-ttu-id="6f774-111">Pour une Blazor WebAssembly expérience, choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="6f774-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6f774-112">Pour une Blazor Server expérience, choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="6f774-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="6f774-113">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="6f774-113">Select **Create**.</span></span>

   <span data-ttu-id="6f774-114">Pour une expérience hébergée Blazor WebAssembly , activez la case à cocher **ASP.net Core hébergée** .</span><span class="sxs-lookup"><span data-stu-id="6f774-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="6f774-115">Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="6f774-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="6f774-116">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="6f774-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="6f774-117">Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="6f774-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="6f774-118">Installez la dernière version du [Kit SDK .net Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="6f774-118">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="6f774-119">Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="6f774-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="6f774-120">Installez la dernière version de [Visual Studio code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="6f774-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="6f774-121">Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="6f774-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="6f774-122">Pour une Blazor WebAssembly expérience, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="6f774-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="6f774-123">Pour une expérience hébergée Blazor WebAssembly , ajoutez l’option de l’option Hosted ( `-ho` ou `--hosted` ) à la commande :</span><span class="sxs-lookup"><span data-stu-id="6f774-123">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="6f774-124">Pour une Blazor Server expérience, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="6f774-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="6f774-125">Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="6f774-125">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="6f774-126">Ouvrez le dossier `WebApplication1` dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6f774-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="6f774-127">L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet.</span><span class="sxs-lookup"><span data-stu-id="6f774-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="6f774-128">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="6f774-128">Select **Yes**.</span></span>

1. <span data-ttu-id="6f774-129">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="6f774-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="6f774-130">Approuver un certificat de développement</span><span class="sxs-lookup"><span data-stu-id="6f774-130">Trust a development certificate</span></span>

<span data-ttu-id="6f774-131">Il n’existe pas de méthode centralisée pour approuver un certificat sur Linux.</span><span class="sxs-lookup"><span data-stu-id="6f774-131">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="6f774-132">En règle générale, l’une des approches suivantes est adoptée :</span><span class="sxs-lookup"><span data-stu-id="6f774-132">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="6f774-133">Excluez l’URL de l’application dans la liste d’exclusion du navigateur.</span><span class="sxs-lookup"><span data-stu-id="6f774-133">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="6f774-134">Faites confiance à tous les certificats auto-signés pour `localhost` .</span><span class="sxs-lookup"><span data-stu-id="6f774-134">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="6f774-135">Ajoutez le certificat à la liste des certificats approuvés dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="6f774-135">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="6f774-136">Pour plus d’informations, consultez les conseils fournis par le fabricant de votre navigateur et la distribution Linux.</span><span class="sxs-lookup"><span data-stu-id="6f774-136">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="6f774-137">Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="6f774-137">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="6f774-138">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="6f774-138">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="6f774-139">Dans la barre latérale, sélectionnez application **Web et console**  >  .</span><span class="sxs-lookup"><span data-stu-id="6f774-139">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="6f774-140">Pour une Blazor WebAssembly expérience, choisissez le modèle d' **Blazor WebAssembly application** .</span><span class="sxs-lookup"><span data-stu-id="6f774-140">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6f774-141">Pour une Blazor Server expérience, choisissez le modèle d' **Blazor Server application** .</span><span class="sxs-lookup"><span data-stu-id="6f774-141">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="6f774-142">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="6f774-142">Select **Next**.</span></span>

   <span data-ttu-id="6f774-143">Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="6f774-143">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="6f774-144">Vérifiez que **l’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="6f774-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="6f774-145">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="6f774-145">Select **Next**.</span></span>

1. <span data-ttu-id="6f774-146">Pour une expérience hébergée Blazor WebAssembly , activez la case à cocher **ASP.net Core hébergée** .</span><span class="sxs-lookup"><span data-stu-id="6f774-146">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="6f774-147">Dans le champ **nom du projet** , nommez l’application `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="6f774-147">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="6f774-148">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="6f774-148">Select **Create**.</span></span>

1. <span data-ttu-id="6f774-149">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="6f774-149">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="6f774-150">Exécutez l’application avec   >  le bouton exécuter **Démarrer le débogage** ou exécuter (&#9654;) pour exécuter l’application *avec le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="6f774-150">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="6f774-151">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="6f774-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="6f774-152">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="6f774-152">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="6f774-153">Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="6f774-153">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="6f774-154">Utiliser Visual Studio Code pour le développement multiplateforme Blazor</span><span class="sxs-lookup"><span data-stu-id="6f774-154">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="6f774-155">[Visual Studio code](https://code.visualstudio.com/) est un environnement de développement intégré (IDE) Open source et multiplateforme qui peut être utilisé pour développer des Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="6f774-155">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="6f774-156">Utilisez l’interface CLI .NET pour créer une nouvelle Blazor application en vue du développement avec Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="6f774-156">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="6f774-157">Pour plus d’informations, consultez la [version Linux de cet article](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="6f774-157">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="6f774-158">Blazor options de modèle</span><span class="sxs-lookup"><span data-stu-id="6f774-158">Blazor template options</span></span>

<span data-ttu-id="6f774-159">Le Blazor Framework fournit des modèles pour créer des applications pour chacun des deux Blazor modèles d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="6f774-159">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="6f774-160">Les modèles sont utilisés pour créer des Blazor projets et des solutions, quels que soient les outils que vous sélectionnez pour le Blazor développement (Visual Studio, Visual Studio pour Mac, Visual Studio code ou l’interface de commande .net) :</span><span class="sxs-lookup"><span data-stu-id="6f774-160">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="6f774-161">Blazor WebAssembly modèle de projet : `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="6f774-161">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="6f774-162">Blazor Server modèle de projet : `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="6f774-162">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="6f774-163">Pour plus d’informations sur les Blazor modèles d’hébergement de, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="6f774-163">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="6f774-164">Les options de modèle sont disponibles en passant l’option d’aide ( `-h` ou `--help` ) à la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande CLI dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="6f774-164">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```