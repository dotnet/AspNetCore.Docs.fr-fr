---
title: Intégration et déploiement continus-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Intégration et déploiement continus dans DevOps avec ASP.NET Core et Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/cicd
ms.openlocfilehash: 2ac7a130d223b21330d0a797c1d460fc0cf467d7
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901208"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="f8f6d-103">Intégration et déploiement continus</span><span class="sxs-lookup"><span data-stu-id="f8f6d-103">Continuous integration and deployment</span></span>

<span data-ttu-id="f8f6d-104">Dans le chapitre précédent, vous avez créé un référentiel Git local pour l’application de lecture de flux simple.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="f8f6d-105">Dans ce chapitre, vous allez publier ce code dans un dépôt GitHub et construire un pipeline Azure DevOps Services à l’aide de Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="f8f6d-106">Le pipeline permet des builds et des déploiements continus de l’application.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="f8f6d-107">Toute validation dans le référentiel GitHub déclenche une build et un déploiement sur l’emplacement intermédiaire de l’application Web Azure.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="f8f6d-108">Dans cette section, vous allez effectuer les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="f8f6d-109">Publier le code de l’application sur GitHub</span><span class="sxs-lookup"><span data-stu-id="f8f6d-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="f8f6d-110">Déconnecter le déploiement Git local</span><span class="sxs-lookup"><span data-stu-id="f8f6d-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="f8f6d-111">Créer une organisation Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="f8f6d-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="f8f6d-112">Créer un projet d’équipe dans Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="f8f6d-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="f8f6d-113">Créer une définition de build</span><span class="sxs-lookup"><span data-stu-id="f8f6d-113">Create a build definition</span></span>
* <span data-ttu-id="f8f6d-114">Créer un pipeline de mise en production</span><span class="sxs-lookup"><span data-stu-id="f8f6d-114">Create a release pipeline</span></span>
* <span data-ttu-id="f8f6d-115">Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure</span><span class="sxs-lookup"><span data-stu-id="f8f6d-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="f8f6d-116">Examiner le pipeline Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="f8f6d-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="f8f6d-117">Publier le code de l’application sur GitHub</span><span class="sxs-lookup"><span data-stu-id="f8f6d-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="f8f6d-118">Ouvrez une fenêtre de navigateur et accédez à `https://github.com` .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="f8f6d-119">Cliquez sur la **+** liste déroulante dans l’en-tête, puis sélectionnez **nouveau référentiel**:</span><span class="sxs-lookup"><span data-stu-id="f8f6d-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub nouvelle option de référentiel](media/cicd/github-new-repo.png)

1. <span data-ttu-id="f8f6d-121">Sélectionnez votre compte dans la liste déroulante **propriétaire** , puis entrez *simple-Feed-Reader* dans la zone de texte **nom du dépôt** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="f8f6d-122">Cliquez sur le bouton **créer un référentiel** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="f8f6d-123">Ouvrez l’interface de commande de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-123">Open your local machine's command shell.</span></span> <span data-ttu-id="f8f6d-124">Accédez au répertoire dans lequel est stocké le référentiel git du *lecteur de flux simple* .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="f8f6d-125">Renommez l' *origine* existante à distance en *amont*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="f8f6d-126">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="f8f6d-127">Ajoutez une nouvelle *origine* à distance pointant vers votre copie du référentiel sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="f8f6d-128">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="f8f6d-129">Publiez votre référentiel Git local dans le référentiel GitHub nouvellement créé.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="f8f6d-130">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="f8f6d-131">Ouvrez une fenêtre de navigateur et accédez à `https://github.com/<GitHub_username>/simple-feed-reader/` .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="f8f6d-132">Vérifiez que votre code apparaît dans le référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="f8f6d-133">Déconnecter le déploiement Git local</span><span class="sxs-lookup"><span data-stu-id="f8f6d-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="f8f6d-134">Supprimez le déploiement Git local en suivant les étapes ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="f8f6d-135">Azure Pipelines (un service Azure DevOps) remplace et augmente cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="f8f6d-136">Ouvrez le [portail Azure](https://portal.azure.com/), puis accédez à l’application Web *intermédiaire (myWebApp \<unique_number\> /staging)* .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="f8f6d-137">L’application Web peut être rapidement localisée en entrant *intermédiaire* dans la zone de recherche du portail :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![terme de recherche de l’application Web intermédiaire](media/cicd/portal-search-box.png)

1. <span data-ttu-id="f8f6d-139">Cliquez sur **Centre de déploiement**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-139">Click **Deployment Center**.</span></span> <span data-ttu-id="f8f6d-140">Un nouveau panneau s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-140">A new panel appears.</span></span> <span data-ttu-id="f8f6d-141">Cliquez sur **déconnecter** pour supprimer la configuration du contrôle de code source git locale qui a été ajoutée dans le chapitre précédent.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="f8f6d-142">Confirmez l’opération de suppression en cliquant sur le bouton **Oui** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="f8f6d-143">Accédez au *<mywebapp unique_number>* App service.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="f8f6d-144">En guise de rappel, la zone de recherche du portail peut être utilisée pour localiser rapidement le App Service.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="f8f6d-145">Cliquez sur **Centre de déploiement**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-145">Click **Deployment Center**.</span></span> <span data-ttu-id="f8f6d-146">Un nouveau panneau s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-146">A new panel appears.</span></span> <span data-ttu-id="f8f6d-147">Cliquez sur **déconnecter** pour supprimer la configuration du contrôle de code source git locale qui a été ajoutée dans le chapitre précédent.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="f8f6d-148">Confirmez l’opération de suppression en cliquant sur le bouton **Oui** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="f8f6d-149">Créer une organisation Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="f8f6d-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="f8f6d-150">Ouvrez un navigateur et accédez à la page de création de l' [organisation Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="f8f6d-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="f8f6d-151">Tapez un nom unique dans la zone de texte **choisir un nom mémorable** pour former l’URL d’accès à votre organisation Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="f8f6d-152">Sélectionnez la case d’option **git** , car le code est hébergé dans un référentiel github.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="f8f6d-153">Cliquez sur le bouton **Continuer**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-153">Click the **Continue** button.</span></span> <span data-ttu-id="f8f6d-154">Après une brève attente, un compte et un projet d’équipe, nommés *MyFirstProject*, sont créés.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Page de création de l’organisation Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="f8f6d-156">Ouvrez le message électronique de confirmation indiquant que l’organisation et le projet Azure DevOps sont prêts à être utilisés.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="f8f6d-157">Cliquez sur le bouton **Démarrer votre projet** :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-157">Click the **Start your project** button:</span></span>

    ![Bouton Démarrer votre projet](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="f8f6d-159">Un navigateur s’ouvre sur *\<account_name\> . VisualStudio.com*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="f8f6d-160">Cliquez sur le lien *MyFirstProject* pour commencer à configurer le pipeline DevOps du projet.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="f8f6d-161">Configurer le pipeline de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="f8f6d-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="f8f6d-162">Il existe trois étapes distinctes à effectuer.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="f8f6d-163">La réalisation des étapes décrites dans les trois sections suivantes aboutit à un pipeline DevOps opérationnel.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="f8f6d-164">Accorder à Azure DevOps l’accès au référentiel GitHub</span><span class="sxs-lookup"><span data-stu-id="f8f6d-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="f8f6d-165">Développez le **Code de build ou à partir d’un accordéon de référentiel externe** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="f8f6d-166">Cliquez sur le bouton **configurer la build** :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-166">Click the **Setup Build** button:</span></span>

    ![Bouton Configurer la build](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="f8f6d-168">Sélectionnez l’option **GitHub** dans la section **Sélectionner une source** :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Sélectionner une source-GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="f8f6d-170">L’autorisation est requise avant qu’Azure DevOps puisse accéder à votre référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="f8f6d-171">Entrez *<GitHub_username> connexion GitHub* dans la zone de texte nom de la **connexion** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="f8f6d-172">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-172">For example:</span></span>

    ![Nom de la connexion GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="f8f6d-174">Si l’authentification à deux facteurs est activée sur votre compte GitHub, un jeton d’accès personnel est requis.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="f8f6d-175">Dans ce cas, cliquez sur le lien **autoriser avec un jeton d’accès personnel GitHub** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="f8f6d-176">Pour obtenir de l’aide, consultez les [instructions officielles de création d’un jeton d’accès personnel GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="f8f6d-177">Seule l’étendue *référentiel* des autorisations est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="f8f6d-178">Dans le cas contraire, cliquez sur le bouton **autoriser à l’aide d’OAuth** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="f8f6d-179">Lorsque vous y êtes invité, connectez-vous à votre compte GitHub.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="f8f6d-180">Sélectionnez ensuite autoriser pour accorder l’accès à votre organisation Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="f8f6d-181">En cas de réussite, un nouveau point de terminaison de service est créé.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="f8f6d-182">Cliquez sur le bouton de sélection en regard du bouton **référentiel** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="f8f6d-183">Sélectionnez le *<GitHub_username>référentiel/simple-Feed-Reader* dans la liste.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="f8f6d-184">Cliquez sur le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="f8f6d-185">Sélectionnez la branche par défaut (*Master*) à partir de la branche par défaut pour la liste déroulante **manuelle et planifiée des builds** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-185">Select the default branch (*master*) from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="f8f6d-186">Cliquez sur le bouton **Continuer**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-186">Click the **Continue** button.</span></span> <span data-ttu-id="f8f6d-187">La page sélection du modèle s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="f8f6d-188">Créer la définition de build</span><span class="sxs-lookup"><span data-stu-id="f8f6d-188">Create the build definition</span></span>

1. <span data-ttu-id="f8f6d-189">Dans la page sélection du modèle, entrez *ASP.net Core* dans la zone de recherche :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core la recherche sur la page de modèle](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="f8f6d-191">Les résultats de la recherche du modèle s’affichent.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-191">The template search results appear.</span></span> <span data-ttu-id="f8f6d-192">Pointez sur le modèle **ASP.net Core** , puis cliquez sur le bouton **appliquer** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="f8f6d-193">L’onglet **tâches** de la définition de build s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="f8f6d-194">Cliquez sur l’onglet **déclencheurs** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="f8f6d-195">Cochez la case **activer l’intégration continue** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="f8f6d-196">Dans la section **filtres de branche** , vérifiez que la liste déroulante **type** est définie sur *include*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="f8f6d-197">Définissez la liste déroulante **spécification de branche** sur *Master*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Activer les paramètres d’intégration continue](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="f8f6d-199">Ces paramètres provoquent le déclenchement d’une génération lorsqu’une modification est envoyée à la branche par défaut (*Master*) du dépôt github.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-199">These settings cause a build to trigger when any change is pushed to the default branch (*master*) of the GitHub repository.</span></span> <span data-ttu-id="f8f6d-200">L’intégration continue est testée dans la section [valider les modifications apportées à GitHub et déployer automatiquement sur Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="f8f6d-201">Cliquez sur le bouton **enregistrer la file d’attente &** , puis sélectionnez l’option **Enregistrer** :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Bouton Enregistrer](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="f8f6d-203">La boîte de dialogue modale suivante s’affiche :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-203">The following modal dialog appears:</span></span>

    ![Boîte de dialogue Enregistrer la définition de build-modal](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="f8f6d-205">Utilisez le dossier par défaut de *\\* , puis cliquez sur le bouton **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="f8f6d-206">Créer le pipeline de mise en production</span><span class="sxs-lookup"><span data-stu-id="f8f6d-206">Create the release pipeline</span></span>

1. <span data-ttu-id="f8f6d-207">Cliquez sur l’onglet **mises** en production de votre projet d’équipe.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="f8f6d-208">Cliquez sur le bouton **nouveau pipeline** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-208">Click the **New pipeline** button.</span></span>

    ![Onglet versions-bouton nouvelle définition](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="f8f6d-210">Le volet sélection du modèle s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="f8f6d-211">Dans la page sélection du modèle, entrez *app service* dans la zone de recherche :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Zone de recherche du modèle de pipeline de mise en version](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="f8f6d-213">Les résultats de la recherche du modèle s’affichent.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-213">The template search results appear.</span></span> <span data-ttu-id="f8f6d-214">Pointez sur le modèle **Azure App service déploiement avec emplacement** , puis cliquez sur le bouton **appliquer** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="f8f6d-215">L’onglet **pipeline** du pipeline de mise en sortie s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Onglet pipeline du pipeline de mise en version](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="f8f6d-217">Cliquez sur le bouton **Ajouter** dans la zone **artefacts** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="f8f6d-218">Le panneau **Ajouter un artefact** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-218">The **Add artifact** panel appears:</span></span>

    ![Pipeline de mise en version-ajouter un panneau d’artefact](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="f8f6d-220">Sélectionnez la vignette **Build** dans la section **type de source** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="f8f6d-221">Ce type autorise la liaison du pipeline de mise en version à la définition de Build.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="f8f6d-222">Sélectionnez *MyFirstProject* dans la liste déroulante **projet** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="f8f6d-223">Sélectionnez le nom de la définition de build, *MyFirstProject-ASP.net Core-ci*, dans la liste déroulante **source (définition de Build)** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="f8f6d-224">Sélectionnez *dernier* dans la liste déroulante **version par défaut** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="f8f6d-225">Cette option génère les artefacts produits par la dernière exécution de la définition de Build.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="f8f6d-226">Remplacez le texte de la zone de texte **alias source** par *Drop*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="f8f6d-227">Cliquez sur le bouton **Add** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-227">Click the **Add** button.</span></span> <span data-ttu-id="f8f6d-228">La section **artefacts** est mise à jour pour afficher les modifications.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="f8f6d-229">Cliquez sur l’icône représentant un éclair pour activer les déploiements continus :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Artefacts du pipeline de mise en sortie-icône éclair](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="f8f6d-231">Lorsque cette option est activée, un déploiement se produit chaque fois qu’une nouvelle build est disponible.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="f8f6d-232">Un panneau **déclencheur de déploiement continu** s’affiche à droite.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="f8f6d-233">Cliquez sur le bouton bascule pour activer la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="f8f6d-234">Il n’est pas nécessaire d’activer le **déclencheur de requête de tirage**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="f8f6d-235">Cliquez sur la liste déroulante **Ajouter** dans la section **créer des filtres de branche** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="f8f6d-236">Choisissez l’option **de branche par défaut de la définition de build** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="f8f6d-237">Ce filtre entraîne le déclenchement de la mise en sortie uniquement pour une build à partir de la branche par défaut du dépôt GitHub (*Master*).</span><span class="sxs-lookup"><span data-stu-id="f8f6d-237">This filter causes the release to trigger only for a build from the GitHub repository's default branch (*master*).</span></span>
1. <span data-ttu-id="f8f6d-238">Cliquez sur le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-238">Click the **Save** button.</span></span> <span data-ttu-id="f8f6d-239">Cliquez sur le bouton **OK** dans la boîte de dialogue d' **enregistrement** modal.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="f8f6d-240">Cliquez sur la zone **environnement 1** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="f8f6d-241">Un panneau d' **environnement** s’affiche à droite.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="f8f6d-242">Modifiez le texte de l' *environnement 1* dans la zone de texte nom de l' **environnement** en *production*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Zone de texte Release pipeline-nom de l’environnement](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="f8f6d-244">Cliquez sur le lien **1 phase, 2 tâches** dans la zone **production** :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Pipeline de mise en production-link.png de l’environnement de production](media/cicd/vsts-production-link.png)

    <span data-ttu-id="f8f6d-246">L’onglet **tâches** de l’environnement s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="f8f6d-247">Cliquez sur la tâche **déployer Azure App service à l’emplacement** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="f8f6d-248">Ses paramètres s’affichent dans un panneau à droite.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="f8f6d-249">Sélectionnez l’abonnement Azure associé à la App Service dans la liste déroulante **abonnement Azure** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="f8f6d-250">Une fois sélectionné, cliquez sur le bouton **autoriser** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="f8f6d-251">Sélectionnez *application Web* dans la liste déroulante **type d’application** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="f8f6d-252">Sélectionnez *myWebApp/<unique_number/>* dans la liste déroulante nom de l' **app service** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="f8f6d-253">Sélectionnez *AzureTutorial* dans la liste déroulante **groupe de ressources** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="f8f6d-254">Sélectionnez *intermédiaire* dans la liste déroulante **emplacement** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="f8f6d-255">Cliquez sur le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="f8f6d-256">Pointez sur le nom du pipeline de version par défaut.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="f8f6d-257">Cliquez sur l’icône de crayon pour la modifier.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="f8f6d-258">Utilisez *MyFirstProject-ASP.net Core-CD* comme nom.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nom du pipeline de version](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="f8f6d-260">Cliquez sur le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="f8f6d-261">Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure</span><span class="sxs-lookup"><span data-stu-id="f8f6d-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="f8f6d-262">Ouvrez *SimpleFeedReader. sln* dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="f8f6d-263">Dans Explorateur de solutions, ouvrez *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="f8f6d-264">Remplacez `<h2>Simple Feed Reader - V3</h2>` par `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="f8f6d-265">Appuyez sur **CTRL** + **MAJ** + **B** pour générer l’application.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="f8f6d-266">Validez le fichier dans le référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="f8f6d-267">Utilisez la page **modifications** dans l’onglet *Team Explorer* de Visual Studio, ou exécutez la commande suivante à l’aide de l’interface de commande de l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="f8f6d-268">Transmettent la modification dans la branche par défaut (*Master*) à l' *origine* distante de votre référentiel github.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-268">Push the change in the default branch (*master*) to the *origin* remote of your GitHub repository.</span></span> <span data-ttu-id="f8f6d-269">Dans la commande suivante, remplacez l’espace réservé `{BRANCH}` par la branche par défaut (use `master` ) :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-269">In the following command, replace the placeholder `{BRANCH}` with the default branch (use `master`):</span></span>

    ```console
    git push origin {BRANCH}
    ```

    <span data-ttu-id="f8f6d-270">La validation s’affiche dans la branche par défaut du dépôt GitHub (*Master*) :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-270">The commit appears in the GitHub repository's default branch (*master*):</span></span>

    ![Validation GitHub dans la branche par défaut (Master)](media/cicd/github-commit.png)

    <span data-ttu-id="f8f6d-272">La build est déclenchée, car l’intégration continue est activée dans l’onglet **déclencheurs** de la définition de build :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-272">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![activer l’intégration continue](media/cicd/enable-ci.png)

1. <span data-ttu-id="f8f6d-274">Accédez à l’onglet en **file d’attente** de la page **Azure pipelines**  >  **Builds** dans Azure DevOps services.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-274">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="f8f6d-275">La build en file d’attente affiche la branche et la validation qui ont déclenché la build :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-275">The queued build shows the branch and commit that triggered the build:</span></span>

    ![Build mise en file d’attente](media/cicd/build-queued.png)

1. <span data-ttu-id="f8f6d-277">Une fois la génération réussie, un déploiement sur Azure se produit.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-277">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="f8f6d-278">Accédez à l’application dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-278">Navigate to the app in the browser.</span></span> <span data-ttu-id="f8f6d-279">Notez que le texte « v4 » s’affiche dans l’en-tête :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-279">Notice that the "V4" text appears in the heading:</span></span>

    ![application mise à jour](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="f8f6d-281">Examiner le pipeline Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="f8f6d-281">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="f8f6d-282">Définition de build</span><span class="sxs-lookup"><span data-stu-id="f8f6d-282">Build definition</span></span>

<span data-ttu-id="f8f6d-283">Une définition de Build a été créée avec le nom *MyFirstProject-ASP.net Core-ci*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-283">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="f8f6d-284">Une fois l’opération terminée, la génération produit un fichier *. zip* incluant les ressources à publier.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-284">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="f8f6d-285">Le pipeline de mise en version déploie ces ressources dans Azure.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-285">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="f8f6d-286">L’onglet **tâches** de la définition de build répertorie les étapes individuelles en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-286">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="f8f6d-287">Il existe cinq tâches de génération.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-287">There are five build tasks.</span></span>

![tâches de définition de build](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="f8f6d-289">**Restaurer** &mdash; Exécute la `dotnet restore` commande pour restaurer les packages NuGet de l’application.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-289">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="f8f6d-290">Le flux de package par défaut utilisé est nuget.org.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-290">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="f8f6d-291">**Créer** &mdash; Exécute la `dotnet build --configuration release` commande pour compiler le code de l’application.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-291">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="f8f6d-292">Cette `--configuration` option est utilisée pour produire une version optimisée du code, qui convient au déploiement dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-292">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="f8f6d-293">Modifiez la variable *BuildConfiguration* sous l’onglet **variables** de la définition de build si, par exemple, une configuration Debug est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-293">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="f8f6d-294">**Test** &mdash; Exécute la `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` commande pour exécuter les tests unitaires de l’application.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-294">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="f8f6d-295">Les tests unitaires sont exécutés dans n’importe quel projet C# correspondant au `**/*Tests/*.csproj` modèle glob.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-295">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="f8f6d-296">Les résultats des tests sont enregistrés dans un fichier *. trx* à l’emplacement spécifié par l' `--results-directory` option.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-296">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="f8f6d-297">Si des tests échouent, la génération échoue et n’est pas déployée.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-297">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="f8f6d-298">Pour vérifier le fonctionnement des tests unitaires, modifiez *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* afin de rompre intentionnellement l’un des tests.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-298">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="f8f6d-299">Par exemple, remplacez `Assert.True(result.Count > 0);` par `Assert.False(result.Count > 0);` dans la `Returns_News_Stories_Given_Valid_Uri` méthode.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-299">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="f8f6d-300">Valider et transmettre la modification à GitHub.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-300">Commit and push the change to GitHub.</span></span> <span data-ttu-id="f8f6d-301">La génération est déclenchée et échoue.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-301">The build is triggered and fails.</span></span> <span data-ttu-id="f8f6d-302">L’état du pipeline de build passe à **échec**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-302">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="f8f6d-303">Rétablissez la modification, la validation et la transmission de type push.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-303">Revert the change, commit, and push again.</span></span> <span data-ttu-id="f8f6d-304">La génération a échoué.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-304">The build succeeds.</span></span>

1. <span data-ttu-id="f8f6d-305">**Publication** &mdash; Exécute la `dotnet publish --configuration release --output <local_path_on_build_agent>` commande pour générer un fichier *. zip* avec les artefacts à déployer.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-305">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="f8f6d-306">L' `--output` option spécifie l’emplacement de publication du fichier *. zip* .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-306">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="f8f6d-307">Cet emplacement est spécifié en passant une [variable prédéfinie](/azure/devops/pipelines/build/variables) nommée `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-307">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="f8f6d-308">Cette variable se développe en un chemin d’accès local, tel que *c:\Agent \_ work\1\a*, sur l’agent de Build.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-308">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="f8f6d-309">**Publier l’artefact** &mdash; Publie le fichier *. zip* produit par la tâche de **publication** .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-309">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="f8f6d-310">La tâche accepte l’emplacement du fichier *. zip* en tant que paramètre, qui est la variable prédéfinie `$(build.artifactstagingdirectory)` .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-310">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="f8f6d-311">Le fichier *. zip* est publié sous la forme d’un dossier nommé *Drop*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-311">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="f8f6d-312">Cliquez sur le lien **Résumé** de la définition de build pour afficher un historique des builds avec la définition :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-312">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Capture d’écran montrant l’historique de définition de build](media/cicd/build-definition-summary.png)

<span data-ttu-id="f8f6d-314">Sur la page résultante, cliquez sur le lien correspondant au numéro de build unique :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-314">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Capture d’écran montrant la page de résumé de la définition de build](media/cicd/build-definition-completed.png)

<span data-ttu-id="f8f6d-316">Un résumé de cette build spécifique s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-316">A summary of this specific build is displayed.</span></span> <span data-ttu-id="f8f6d-317">Cliquez sur l’onglet **artefacts** , et notez que le dossier de *dépôt* produit par la build est listé :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-317">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Capture d’écran montrant les artefacts de définition de build-dossier cible](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="f8f6d-319">Utilisez les liens **Télécharger** et **Explorer** pour inspecter les artefacts publiés.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-319">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="f8f6d-320">Pipeline de mise en production</span><span class="sxs-lookup"><span data-stu-id="f8f6d-320">Release pipeline</span></span>

<span data-ttu-id="f8f6d-321">Un pipeline de version a été créé avec le nom *MyFirstProject-ASP.net Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="f8f6d-321">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Capture d’écran montrant la présentation du pipeline de version](media/cicd/release-definition-overview.png)

<span data-ttu-id="f8f6d-323">Les deux principaux composants du pipeline de mise en version sont les **artefacts** et les **environnements**.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-323">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="f8f6d-324">Le fait de cliquer sur la zone dans la section **artefacts** affiche le panneau suivant :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-324">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Capture d’écran montrant les artefacts du pipeline de mise en version](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="f8f6d-326">La valeur **source (définition de Build)** représente la définition de build à laquelle ce pipeline de mise en version est lié.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-326">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="f8f6d-327">Le fichier *. zip* produit par une exécution réussie de la définition de build est fourni à l’environnement de *production* pour le déploiement sur Azure.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-327">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="f8f6d-328">Cliquez sur le lien *1 phase, 2 tâches* dans la zone environnement de production pour afficher les tâches de pipeline de mise en *production* :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-328">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Capture d’écran montrant les tâches de pipeline de version](media/cicd/release-definition-tasks.png)

<span data-ttu-id="f8f6d-330">Le pipeline de mise en version est constitué de deux tâches : *déployer des Azure App service sur l’emplacement* et gérer l' *échange d’emplacement Azure App service*.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-330">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="f8f6d-331">Le fait de cliquer sur la première tâche révèle la configuration de tâche suivante :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-331">Clicking the first task reveals the following task configuration:</span></span>

![Capture d’écran montrant la tâche de déploiement du pipeline de version](media/cicd/release-definition-task1.png)

<span data-ttu-id="f8f6d-333">L’abonnement Azure, le type de service, le nom de l’application Web, le groupe de ressources et l’emplacement de déploiement sont définis dans la tâche de déploiement.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-333">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="f8f6d-334">La zone de texte **package ou dossier** contient le chemin d’accès du fichier *. zip* à extraire et à déployer dans l’emplacement *intermédiaire* de l’application Web *myWebApp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="f8f6d-334">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="f8f6d-335">Le fait de cliquer sur la tâche d’échange d’emplacement révèle la configuration de tâche suivante :</span><span class="sxs-lookup"><span data-stu-id="f8f6d-335">Clicking the slot swap task reveals the following task configuration:</span></span>

![Capture d’écran montrant la tâche d’échange d’emplacement de pipeline de version](media/cicd/release-definition-task2.png)

<span data-ttu-id="f8f6d-337">Les détails de l’abonnement, du groupe de ressources, du type de service, du nom de l’application Web et de l’emplacement de déploiement sont fournis.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-337">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="f8f6d-338">La case à cocher **swap with production** est activée.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-338">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="f8f6d-339">Par conséquent, les bits déployés sur l’emplacement *intermédiaire* sont échangés dans l’environnement de production.</span><span class="sxs-lookup"><span data-stu-id="f8f6d-339">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="f8f6d-340">Documentation supplémentaire</span><span class="sxs-lookup"><span data-stu-id="f8f6d-340">Additional reading</span></span>

* [<span data-ttu-id="f8f6d-341">Créer son premier pipeline avec Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="f8f6d-341">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="f8f6d-342">Build et projet .NET Core</span><span class="sxs-lookup"><span data-stu-id="f8f6d-342">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="f8f6d-343">Déployer une application Web avec Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="f8f6d-343">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
