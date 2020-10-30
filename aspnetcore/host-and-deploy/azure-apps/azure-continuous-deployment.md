---
title: Déploiement continu sur Azure avec Visual Studio et Git avec ASP.NET Core
author: rick-anderson
description: Découvrez comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer sur Azure App Service en utilisant Git pour le déploiement continu.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
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
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 1485e1a97e51bb4d12d0d74ca1550e5cf18258d8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053785"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="579b8-103">Déploiement continu sur Azure avec Visual Studio et Git avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="579b8-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="579b8-104">Par [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="579b8-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="579b8-105">Ce tutoriel montre comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer à partir de Visual Studio sur Azure App Service en utilisant le déploiement continu.</span><span class="sxs-lookup"><span data-stu-id="579b8-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="579b8-106">Voir aussi [Créer votre premier pipeline avec Azure Pipelines](/azure/devops/pipelines/get-started-yaml), qui indique comment configurer un flux de travail de livraison continue pour [Azure App Service](/azure/app-service/app-service-web-overview) à l’aide d’Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="579b8-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="579b8-107">Azure Pipelines (un service Azure DevOps Services) simplifie la configuration d’un pipeline de déploiement fiable pour publier les mises à jour des applications hébergées dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="579b8-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="579b8-108">Le pipeline peut être configuré à partir du portail Azure pour générer, exécuter des tests, déployer sur un emplacement de préproduction, puis déployer en production.</span><span class="sxs-lookup"><span data-stu-id="579b8-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="579b8-109">Pour la réalisation de ce tutoriel, un compte Microsoft Azure est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="579b8-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="579b8-110">Pour obtenir un compte, [activez les avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) ou [inscrivez-vous pour un essai gratuit](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="579b8-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="579b8-111">Prérequis</span><span class="sxs-lookup"><span data-stu-id="579b8-111">Prerequisites</span></span>

<span data-ttu-id="579b8-112">Ce tutoriel suppose que les logiciels suivants sont installés :</span><span class="sxs-lookup"><span data-stu-id="579b8-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="579b8-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="579b8-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="579b8-114">[Git](https://git-scm.com/downloads) pour Windows</span><span class="sxs-lookup"><span data-stu-id="579b8-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="579b8-115">Créez une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="579b8-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="579b8-116">Démarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="579b8-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="579b8-117">Dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet** .</span><span class="sxs-lookup"><span data-stu-id="579b8-117">From the **File** menu, select **New** > **Project** .</span></span>

1. <span data-ttu-id="579b8-118">Sélectionnez le modèle de projet **Application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="579b8-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="579b8-119">Il apparaît sous **Installed**  >  **modèles** installés  >  **Visual C#**  >  **.net Core** .</span><span class="sxs-lookup"><span data-stu-id="579b8-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core** .</span></span> <span data-ttu-id="579b8-120">Nommez le projet `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="579b8-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="579b8-121">Sélectionnez l’option **Créer un dépôt Git** , puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="579b8-121">Select the **Create new Git repository** option and click **OK** .</span></span>

   ![Boîte de dialogue Nouveau projet](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="579b8-123">Dans la boîte de dialogue **Nouveau projet ASP.NET Core** , sélectionnez le modèle ASP.NET Core **Vide** , puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="579b8-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK** .</span></span>

   ![Boîte de dialogue Nouveau projet ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="579b8-125">La dernière version de .NET Core est 2.0.</span><span class="sxs-lookup"><span data-stu-id="579b8-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="579b8-126">Exécution de l’application web localement</span><span class="sxs-lookup"><span data-stu-id="579b8-126">Running the web app locally</span></span>

1. <span data-ttu-id="579b8-127">Une fois que Visual Studio a terminé la création de l’application, exécutez l’application en sélectionnant **Déboguer**  >  **Démarrer le débogage** .</span><span class="sxs-lookup"><span data-stu-id="579b8-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging** .</span></span> <span data-ttu-id="579b8-128">Vous pouvez aussi appuyer sur **F5** .</span><span class="sxs-lookup"><span data-stu-id="579b8-128">As an alternative, press **F5** .</span></span>

   <span data-ttu-id="579b8-129">L’initialisation de Visual Studio et de la nouvelle application peut prendre un certain temps.</span><span class="sxs-lookup"><span data-stu-id="579b8-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="579b8-130">Une fois qu’elle est terminée, le navigateur affiche l’application en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="579b8-130">Once it's complete, the browser shows the running app.</span></span>

   ![Fenêtre de navigateur montrant l’application en cours d’exécution qui affiche « Hello World! »](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="579b8-132">Après avoir examiné l’application web en cours d’exécution, fermez le navigateur et sélectionnez l’icône « Arrêter le débogage » dans la barre d’outils de Visual Studio pour arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="579b8-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="579b8-133">Créer une application web dans le portail Azure</span><span class="sxs-lookup"><span data-stu-id="579b8-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="579b8-134">Les étapes suivantes permettent de créer une application web dans le portail Azure :</span><span class="sxs-lookup"><span data-stu-id="579b8-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="579b8-135">Connectez-vous au [portail Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="579b8-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="579b8-136">Sélectionnez **NOUVEAU** en haut à gauche de l’interface du portail.</span><span class="sxs-lookup"><span data-stu-id="579b8-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="579b8-137">Sélectionnez **Web + mobile**  >  **application Web** .</span><span class="sxs-lookup"><span data-stu-id="579b8-137">Select **Web + Mobile** > **Web App** .</span></span>

   ![Portail Microsoft Azure : Nouveau bouton Web + Mobile sous Place de marché - Bouton Application Web sous Applications à la une](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="579b8-139">Dans le panneau **Application web** , entrez une valeur unique pour le **Nom App Service** .</span><span class="sxs-lookup"><span data-stu-id="579b8-139">In the **Web App** blade, enter a unique value for the **App Service Name** .</span></span>

   ![Panneau Application web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="579b8-141">Le nom dans **Nom App Service** doit être unique.</span><span class="sxs-lookup"><span data-stu-id="579b8-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="579b8-142">Le portail applique cette règle quand le nom est fourni.</span><span class="sxs-lookup"><span data-stu-id="579b8-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="579b8-143">Si vous indiquez une valeur différente, substituez cette valeur pour chaque occurrence de **SampleWebAppDemo** dans ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="579b8-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="579b8-144">Dans le panneau **Application web** , sélectionnez un **Plan/emplacement App Service** existant ou créez-en un.</span><span class="sxs-lookup"><span data-stu-id="579b8-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="579b8-145">Si vous créez un plan, sélectionnez le niveau tarifaire, l’emplacement et les autres options.</span><span class="sxs-lookup"><span data-stu-id="579b8-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="579b8-146">Pour plus d’informations sur les plans App Service, consultez [Présentation détaillée des plans Azure App Service](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="579b8-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="579b8-147">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="579b8-147">Select **Create** .</span></span> <span data-ttu-id="579b8-148">Azure provisionne et démarre l’application web.</span><span class="sxs-lookup"><span data-stu-id="579b8-148">Azure will provision and start the web app.</span></span>

   ![Portail Azure : Panneau Exemple d’application web Demo 01 Essentials](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="579b8-150">Activer la publication Git pour la nouvelle application Web</span><span class="sxs-lookup"><span data-stu-id="579b8-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="579b8-151">GIT est un système de gestion de versions distribué qui permet de déployer une application web Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="579b8-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="579b8-152">Le code de l’application web est stocké dans un dépôt Git local, et est déployé sur Azure par le biais d’un envoi (push) vers un dépôt distant.</span><span class="sxs-lookup"><span data-stu-id="579b8-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="579b8-153">Connectez-vous au [portail Azure](https://portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="579b8-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="579b8-154">Sélectionnez **App Services** pour afficher la liste des services App Services associées à l’abonnement Azure.</span><span class="sxs-lookup"><span data-stu-id="579b8-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="579b8-155">Sélectionnez l’application web créée dans la section précédente de ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="579b8-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="579b8-156">Dans le panneau **Déploiement** , sélectionnez **Options de déploiement** > **Choisir une source** > **Référentiel Git local** .</span><span class="sxs-lookup"><span data-stu-id="579b8-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository** .</span></span>

   ![Panneau Paramètres : Panneau Source de déploiement : Panneau Choisir une source](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="579b8-158">Sélectionnez **OK** .</span><span class="sxs-lookup"><span data-stu-id="579b8-158">Select **OK** .</span></span>

1. <span data-ttu-id="579b8-159">Si les informations d’identification de déploiement pour la publication d’une application web ou d’une autre application App Service n’ont pas été configurées, configurez-les maintenant :</span><span class="sxs-lookup"><span data-stu-id="579b8-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="579b8-160">Sélectionnez **paramètres**  >  **informations d’identification de déploiement** .</span><span class="sxs-lookup"><span data-stu-id="579b8-160">Select **Settings** > **Deployment credentials** .</span></span> <span data-ttu-id="579b8-161">Le panneau **Définir les informations d’identification de déploiement** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="579b8-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="579b8-162">Créez un nom d'utilisateur et un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="579b8-162">Create a user name and password.</span></span> <span data-ttu-id="579b8-163">Enregistrez le mot de passe en vue de l’utiliser au moment de la configuration de Git.</span><span class="sxs-lookup"><span data-stu-id="579b8-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="579b8-164">Sélectionnez **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="579b8-164">Select **Save** .</span></span>

1. <span data-ttu-id="579b8-165">Dans le panneau **Application web** , sélectionnez **Paramètres** > **Propriétés** .</span><span class="sxs-lookup"><span data-stu-id="579b8-165">In the **Web App** blade, select **Settings** > **Properties** .</span></span> <span data-ttu-id="579b8-166">L’URL du dépôt Git distant destinataire du déploiement est affiché sous **URL Git** .</span><span class="sxs-lookup"><span data-stu-id="579b8-166">The URL of the remote Git repository to deploy to is shown under **GIT URL** .</span></span>

1. <span data-ttu-id="579b8-167">Copiez la valeur **URL GIT** pour pouvoir l’utiliser plus tard dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="579b8-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Portail Azure : Panneau Propriétés de l’application](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="579b8-169">Publication de l’application web dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="579b8-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="579b8-170">Dans cette section, créez un dépôt Git local à l’aide de Visual Studio, et envoyez (push) de ce dépôt vers Azure pour déployer l’application web.</span><span class="sxs-lookup"><span data-stu-id="579b8-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="579b8-171">Les étapes impliquées sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="579b8-171">The steps involved include the following:</span></span>

* <span data-ttu-id="579b8-172">Ajoutez le paramètre de dépôt distant en utilisant la valeur de l’URL Git, afin que le dépôt local puisse être déployé sur Azure.</span><span class="sxs-lookup"><span data-stu-id="579b8-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="579b8-173">Validez les modifications du projet.</span><span class="sxs-lookup"><span data-stu-id="579b8-173">Commit project changes.</span></span>
* <span data-ttu-id="579b8-174">Envoyez (push) les modifications du projet depuis le dépôt local vers le dépôt distant sur Azure.</span><span class="sxs-lookup"><span data-stu-id="579b8-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="579b8-175">Dans **l’Explorateur de solutions** , cliquez avec le bouton droit sur **Solution 'SampleWebAppDemo'** et sélectionnez **Valider** .</span><span class="sxs-lookup"><span data-stu-id="579b8-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit** .</span></span> <span data-ttu-id="579b8-176">**Team Explorer** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="579b8-176">The **Team Explorer** is displayed.</span></span>

   ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="579b8-178">Dans **Team Explorer** , sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres du dépôt** .</span><span class="sxs-lookup"><span data-stu-id="579b8-178">In **Team Explorer** , select the **Home** (home icon) > **Settings** > **Repository Settings** .</span></span>

1. <span data-ttu-id="579b8-179">Dans la section **Distants** des **Paramètres du référentiel** , sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="579b8-179">In the **Remotes** section of the **Repository Settings** , select **Add** .</span></span> <span data-ttu-id="579b8-180">La boîte de dialogue **Ajouter un élément distant** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="579b8-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="579b8-181">Définissez le **Nom** de l’élément distant sur **Azure-SampleApp** .</span><span class="sxs-lookup"><span data-stu-id="579b8-181">Set the **Name** of the remote to **Azure-SampleApp** .</span></span>

1. <span data-ttu-id="579b8-182">Définissez la valeur de **Récupérer** sur **l’URL Git** précédemment copiée depuis Azure dans ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="579b8-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="579b8-183">Notez qu’il s’agit de l’URL qui se termine par **.git** .</span><span class="sxs-lookup"><span data-stu-id="579b8-183">Note that this is the URL that ends with **.git** .</span></span>

   ![Boîte de dialogue Modifier un élément distant](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="579b8-185">Vous pouvez aussi spécifier le dépôt distant à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande** , en accédant au répertoire du projet et en entrant la commande.</span><span class="sxs-lookup"><span data-stu-id="579b8-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window** , changing to the project directory, and entering the command.</span></span> <span data-ttu-id="579b8-186">Exemple :</span><span class="sxs-lookup"><span data-stu-id="579b8-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="579b8-187">Sélectionnez **Accueil** (icône de maison) > **Paramètres** > **Paramètres globaux** .</span><span class="sxs-lookup"><span data-stu-id="579b8-187">Select the **Home** (home icon) > **Settings** > **Global Settings** .</span></span> <span data-ttu-id="579b8-188">Vérifiez que le nom et l’adresse e-mail sont définis.</span><span class="sxs-lookup"><span data-stu-id="579b8-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="579b8-189">Sélectionnez **Mettre à jour** si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="579b8-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="579b8-190">Sélectionnez **Home**  >  **modifications** familiales pour revenir à l’affichage des **modifications** .</span><span class="sxs-lookup"><span data-stu-id="579b8-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="579b8-191">Entrez un message de validation, comme **Initial Push #1** , et sélectionnez **Valider** .</span><span class="sxs-lookup"><span data-stu-id="579b8-191">Enter a commit message, such as **Initial Push #1** and select **Commit** .</span></span> <span data-ttu-id="579b8-192">Cette action crée une *validation* localement.</span><span class="sxs-lookup"><span data-stu-id="579b8-192">This action creates a *commit* locally.</span></span>

   ![Onglet Connexion de Team Explorer](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="579b8-194">Vous pouvez aussi valider les modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande** , en accédant au répertoire du projet et en entrant les commandes Git.</span><span class="sxs-lookup"><span data-stu-id="579b8-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window** , changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="579b8-195">Exemple :</span><span class="sxs-lookup"><span data-stu-id="579b8-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="579b8-196">Sélectionnez **démarrage** de la  >  **synchronisation**  >  **actions**  >  **ouvrir l’invite de commandes** .</span><span class="sxs-lookup"><span data-stu-id="579b8-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt** .</span></span> <span data-ttu-id="579b8-197">L’invite de commandes ouvre le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="579b8-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="579b8-198">Entrez la commande suivante dans la fenêtre Commande :</span><span class="sxs-lookup"><span data-stu-id="579b8-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="579b8-199">Entrez le mot de passe des **informations d’identification de déploiement** Azure créé précédemment dans Azure.</span><span class="sxs-lookup"><span data-stu-id="579b8-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="579b8-200">Cette commande démarre le processus d’envoi (push) des fichiers de projet locaux vers Azure.</span><span class="sxs-lookup"><span data-stu-id="579b8-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="579b8-201">La sortie de la commande ci-dessus se termine par un message indiquant que le déploiement a réussi.</span><span class="sxs-lookup"><span data-stu-id="579b8-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="579b8-202">Si une collaboration sur le projet est requise, envisagez un envoi (push) vers [GitHub](https://github.com) avant un envoi (push) vers Azure.</span><span class="sxs-lookup"><span data-stu-id="579b8-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="579b8-203">Vérifier le déploiement actif</span><span class="sxs-lookup"><span data-stu-id="579b8-203">Verify the Active Deployment</span></span>

<span data-ttu-id="579b8-204">Vérifiez que le transfert de l’application web à partir de l’environnement local vers Azure a réussi.</span><span class="sxs-lookup"><span data-stu-id="579b8-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="579b8-205">Dans le [portail Azure](https://portal.azure.com), sélectionnez l’application web.</span><span class="sxs-lookup"><span data-stu-id="579b8-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="579b8-206">Sélectionnez **Deployment**  >  **options de déploiement** de déploiement.</span><span class="sxs-lookup"><span data-stu-id="579b8-206">Select **Deployment** > **Deployment options** .</span></span>

![Portail Azure : Panneau Paramètres : Panneau Déploiements montrant la réussite du déploiement](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="579b8-208">Exécuter l’application dans Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="579b8-208">Run the app in Azure</span></span>

<span data-ttu-id="579b8-209">L’application web étant déployée sur Azure, exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="579b8-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="579b8-210">Cette opération peut se faire de deux façons :</span><span class="sxs-lookup"><span data-stu-id="579b8-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="579b8-211">Dans le portail Azure, recherchez le panneau lié à l’application web.</span><span class="sxs-lookup"><span data-stu-id="579b8-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="579b8-212">Sélectionnez **Parcourir** pour afficher l’application dans le navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="579b8-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="579b8-213">Ouvrez un navigateur et entrez l’URL de l’application web.</span><span class="sxs-lookup"><span data-stu-id="579b8-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="579b8-214">Exemple : `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="579b8-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="579b8-215">Mettre à jour l’application web et la republier</span><span class="sxs-lookup"><span data-stu-id="579b8-215">Update the web app and republish</span></span>

<span data-ttu-id="579b8-216">Après avoir apporté des modifications au code local, effectuez une republication :</span><span class="sxs-lookup"><span data-stu-id="579b8-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="579b8-217">Dans **l’Explorateur de solutions** de Visual Studio, ouvrez le fichier *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="579b8-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="579b8-218">Dans la méthode `Configure`, modifiez la méthode `Response.WriteAsync` comme suit :</span><span class="sxs-lookup"><span data-stu-id="579b8-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="579b8-219">Enregistrez les modifications de *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="579b8-219">Save the changes to *Startup.cs* .</span></span>

1. <span data-ttu-id="579b8-220">Dans **Explorateur de solutions** , cliquez avec le bouton droit sur **solution’SampleWebAppDemo'** et sélectionnez **valider** .</span><span class="sxs-lookup"><span data-stu-id="579b8-220">In **Solution Explorer** , right-click **Solution 'SampleWebAppDemo'** and select **Commit** .</span></span> <span data-ttu-id="579b8-221">**Team Explorer** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="579b8-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="579b8-222">Entrez un message de validation, comme `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="579b8-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="579b8-223">Appuyez sur le bouton **Valider** pour valider les modifications du projet.</span><span class="sxs-lookup"><span data-stu-id="579b8-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="579b8-224">Sélectionnez **démarrage**  >  **Sync**  >  **actions** Sync  >  **Push** .</span><span class="sxs-lookup"><span data-stu-id="579b8-224">Select **Home** > **Sync** > **Actions** > **Push** .</span></span>

> [!NOTE]
> <span data-ttu-id="579b8-225">Vous pouvez aussi envoyer (push) les modifications à partir de la **Fenêtre Commande** en ouvrant la **Fenêtre Commande** , en accédant au répertoire du projet et en entrant une commande Git.</span><span class="sxs-lookup"><span data-stu-id="579b8-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window** , changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="579b8-226">Exemple :</span><span class="sxs-lookup"><span data-stu-id="579b8-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="579b8-227">Afficher l’application web mise à jour dans Azure</span><span class="sxs-lookup"><span data-stu-id="579b8-227">View the updated web app in Azure</span></span>

<span data-ttu-id="579b8-228">Affichez l’application web mise à jour en sélectionnant **Parcourir** dans le panneau Application web du portail Azure, ou en ouvrant un navigateur et en entrant l’URL de l’application web.</span><span class="sxs-lookup"><span data-stu-id="579b8-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="579b8-229">Exemple : `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="579b8-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="579b8-230">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="579b8-230">Additional resources</span></span>

* [<span data-ttu-id="579b8-231">Créer son premier pipeline avec Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="579b8-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="579b8-232">Project Kudu</span><span class="sxs-lookup"><span data-stu-id="579b8-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
