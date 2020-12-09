---
title: Déployer une application sur App Service-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Déployez une application ASP.NET Core sur Azure App Service, la première étape pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: f1c7acba0b7fb7dc07da576b188e580328ff4b89
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901156"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="cd347-103">Déployer une application sur App Service</span><span class="sxs-lookup"><span data-stu-id="cd347-103">Deploy an app to App Service</span></span>

<span data-ttu-id="cd347-104">[Azure App service](/azure/app-service/) est la plateforme d’hébergement Web d’Azure.</span><span class="sxs-lookup"><span data-stu-id="cd347-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="cd347-105">Le déploiement d’une application Web sur Azure App Service peut être effectué manuellement ou à l’aide d’un processus automatisé.</span><span class="sxs-lookup"><span data-stu-id="cd347-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="cd347-106">Cette section du guide décrit les méthodes de déploiement qui peuvent être déclenchées manuellement ou par script à l’aide de la ligne de commande, ou déclenchées manuellement à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd347-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="cd347-107">Dans cette section, vous allez effectuer les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="cd347-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="cd347-108">Téléchargez et générez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="cd347-108">Download and build the sample app.</span></span>
* <span data-ttu-id="cd347-109">Créez une application Web Azure App Service à l’aide de l’Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="cd347-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="cd347-110">Déployez l’exemple d’application sur Azure à l’aide de git.</span><span class="sxs-lookup"><span data-stu-id="cd347-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="cd347-111">Déployer une modification apportée à l’application à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd347-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="cd347-112">Ajoutez un emplacement intermédiaire à l’application Web.</span><span class="sxs-lookup"><span data-stu-id="cd347-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="cd347-113">Déployez une mise à jour sur l’emplacement intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="cd347-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="cd347-114">Procédez à l’échange des emplacements de préproduction et de production.</span><span class="sxs-lookup"><span data-stu-id="cd347-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="cd347-115">Télécharger et tester l’application</span><span class="sxs-lookup"><span data-stu-id="cd347-115">Download and test the app</span></span>

<span data-ttu-id="cd347-116">L’application utilisée dans ce guide est une application ASP.NET Core pré-générée, un [lecteur de flux simple](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="cd347-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="cd347-117">Il s’agit d’une Razor application pages qui utilise l' `Microsoft.SyndicationFeed.ReaderWriter` API pour récupérer un flux RSS/Atom et afficher les éléments d’actualité dans une liste.</span><span class="sxs-lookup"><span data-stu-id="cd347-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="cd347-118">N’hésitez pas à examiner le code, mais il est important de comprendre qu’il n’y a rien de spécial sur cette application.</span><span class="sxs-lookup"><span data-stu-id="cd347-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="cd347-119">Il s’agit simplement d’une simple ASP.NET Core application à des fins d’illustration.</span><span class="sxs-lookup"><span data-stu-id="cd347-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="cd347-120">À partir d’une interface de commande, téléchargez le code, générez le projet et exécutez-le comme suit.</span><span class="sxs-lookup"><span data-stu-id="cd347-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="cd347-121">*Remarque : les utilisateurs Linux/macOS doivent apporter les modifications appropriées pour les chemins d’accès, par exemple, à l’aide de la barre oblique ( `/` ) au lieu de la barre oblique inverse ( `\` ).*</span><span class="sxs-lookup"><span data-stu-id="cd347-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="cd347-122">Clonez le code dans un dossier sur votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="cd347-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="cd347-123">Remplacez votre dossier de travail par le dossier *simple-Feed-Reader* qui a été créé.</span><span class="sxs-lookup"><span data-stu-id="cd347-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="cd347-124">Restaurez les packages, puis générez la solution.</span><span class="sxs-lookup"><span data-stu-id="cd347-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="cd347-125">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="cd347-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![La commande dotnet Run est réussie](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="cd347-127">Ouvrez un navigateur et accédez à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="cd347-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="cd347-128">L’application vous permet de taper ou de coller une URL de flux de syndication et d’afficher une liste d’éléments d’actualité.</span><span class="sxs-lookup"><span data-stu-id="cd347-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Application affichant le contenu d’un flux RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="cd347-130">Une fois que vous êtes satisfait du bon fonctionnement de l’application, arrêtez-la en appuyant sur **CTRL** + **C** dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="cd347-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="cd347-131">Créer l’application Web Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cd347-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="cd347-132">Pour déployer l’application, vous devez créer une [application Web](/azure/app-service/app-service-web-overview)App service.</span><span class="sxs-lookup"><span data-stu-id="cd347-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="cd347-133">Après la création de l’application Web, vous allez le déployer à partir de votre ordinateur local à l’aide de git.</span><span class="sxs-lookup"><span data-stu-id="cd347-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="cd347-134">Connectez-vous au [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="cd347-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="cd347-135">Remarque : quand vous vous connectez pour la première fois, Cloud Shell vous invite à créer un compte de stockage pour les fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="cd347-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="cd347-136">Acceptez les valeurs par défaut ou fournissez un nom unique.</span><span class="sxs-lookup"><span data-stu-id="cd347-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="cd347-137">Utilisez l’Cloud Shell pour les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="cd347-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="cd347-138">a.</span><span class="sxs-lookup"><span data-stu-id="cd347-138">a.</span></span> <span data-ttu-id="cd347-139">Déclarez une variable pour stocker le nom de votre application Web.</span><span class="sxs-lookup"><span data-stu-id="cd347-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="cd347-140">Le nom doit être unique pour être utilisé dans l’URL par défaut.</span><span class="sxs-lookup"><span data-stu-id="cd347-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="cd347-141">L’utilisation de la `$RANDOM` fonction bash pour construire le nom garantit l’unicité et les résultats au format `webappname99999` .</span><span class="sxs-lookup"><span data-stu-id="cd347-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="cd347-142">b.</span><span class="sxs-lookup"><span data-stu-id="cd347-142">b.</span></span> <span data-ttu-id="cd347-143">Créez un groupe de ressources.</span><span class="sxs-lookup"><span data-stu-id="cd347-143">Create a resource group.</span></span> <span data-ttu-id="cd347-144">Les groupes de ressources fournissent un moyen d’agréger des ressources Azure à gérer en tant que groupe.</span><span class="sxs-lookup"><span data-stu-id="cd347-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="cd347-145">La `az` commande appelle la [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="cd347-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="cd347-146">L’interface CLI peut être exécutée localement, mais son utilisation dans le Cloud Shell permet de gagner du temps et de la configuration.</span><span class="sxs-lookup"><span data-stu-id="cd347-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="cd347-147">c.</span><span class="sxs-lookup"><span data-stu-id="cd347-147">c.</span></span> <span data-ttu-id="cd347-148">Créez un plan de App Service dans le niveau S1.</span><span class="sxs-lookup"><span data-stu-id="cd347-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="cd347-149">Un plan de App Service est un regroupement d’applications Web qui partagent le même niveau tarifaire.</span><span class="sxs-lookup"><span data-stu-id="cd347-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="cd347-150">Le niveau S1 n’est pas gratuit, mais il est requis pour la fonctionnalité d’emplacements intermédiaires.</span><span class="sxs-lookup"><span data-stu-id="cd347-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="cd347-151">d.</span><span class="sxs-lookup"><span data-stu-id="cd347-151">d.</span></span> <span data-ttu-id="cd347-152">Créez la ressource d’application Web à l’aide du plan de App Service dans le même groupe de ressources.</span><span class="sxs-lookup"><span data-stu-id="cd347-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="cd347-153">e.</span><span class="sxs-lookup"><span data-stu-id="cd347-153">e.</span></span> <span data-ttu-id="cd347-154">Définissez les informations d’identification de déploiement.</span><span class="sxs-lookup"><span data-stu-id="cd347-154">Set the deployment credentials.</span></span> <span data-ttu-id="cd347-155">Ces informations d’identification de déploiement s’appliquent à toutes les applications Web de votre abonnement.</span><span class="sxs-lookup"><span data-stu-id="cd347-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="cd347-156">N’utilisez pas de caractères spéciaux dans le nom d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cd347-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="cd347-157">f.</span><span class="sxs-lookup"><span data-stu-id="cd347-157">f.</span></span> <span data-ttu-id="cd347-158">Configurez l’application Web pour accepter les déploiements à partir du Git local et afficher l' *URL de déploiement git*.</span><span class="sxs-lookup"><span data-stu-id="cd347-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="cd347-159">**Notez cette URL pour référence ultérieure**.</span><span class="sxs-lookup"><span data-stu-id="cd347-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="cd347-160">g.</span><span class="sxs-lookup"><span data-stu-id="cd347-160">g.</span></span> <span data-ttu-id="cd347-161">Affichez l’URL de l' *application Web*.</span><span class="sxs-lookup"><span data-stu-id="cd347-161">Display the *web app URL*.</span></span> <span data-ttu-id="cd347-162">Accédez à cette URL pour voir l’application Web vide.</span><span class="sxs-lookup"><span data-stu-id="cd347-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="cd347-163">**Notez cette URL pour référence ultérieure**.</span><span class="sxs-lookup"><span data-stu-id="cd347-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="cd347-164">À l’aide d’un interpréteur de commandes sur votre ordinateur local, accédez au dossier du projet de l’application Web (par exemple, `.\simple-feed-reader\SimpleFeedReader` ).</span><span class="sxs-lookup"><span data-stu-id="cd347-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="cd347-165">Exécutez les commandes suivantes pour configurer Git pour l’envoi vers l’URL de déploiement :</span><span class="sxs-lookup"><span data-stu-id="cd347-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="cd347-166">a.</span><span class="sxs-lookup"><span data-stu-id="cd347-166">a.</span></span> <span data-ttu-id="cd347-167">Ajoutez l’URL distante au référentiel local.</span><span class="sxs-lookup"><span data-stu-id="cd347-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="cd347-168">b.</span><span class="sxs-lookup"><span data-stu-id="cd347-168">b.</span></span> <span data-ttu-id="cd347-169">Poussez la branche par défaut locale (*Master*) vers la branche par défaut (*Master*) d' *Azure-prod* Remote.</span><span class="sxs-lookup"><span data-stu-id="cd347-169">Push the local default branch (*master*) to the *azure-prod* remote's default branch (*master*).</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="cd347-170">Vous serez invité à entrer les informations d’identification de déploiement que vous avez créées précédemment.</span><span class="sxs-lookup"><span data-stu-id="cd347-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="cd347-171">Observez la sortie dans l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="cd347-171">Observe the output in the command shell.</span></span> <span data-ttu-id="cd347-172">Azure génère l’application ASP.NET Core à distance.</span><span class="sxs-lookup"><span data-stu-id="cd347-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="cd347-173">Dans un navigateur, accédez à l' *URL de l’application Web* et notez que l’application a été générée et déployée.</span><span class="sxs-lookup"><span data-stu-id="cd347-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="cd347-174">Des modifications supplémentaires peuvent être validées dans le référentiel Git local avec `git commit` .</span><span class="sxs-lookup"><span data-stu-id="cd347-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="cd347-175">Ces modifications sont transmises à Azure avec la `git push` commande précédente.</span><span class="sxs-lookup"><span data-stu-id="cd347-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="cd347-176">Déploiement avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd347-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="cd347-177">*Remarque : cette section s’applique uniquement à Windows. Les utilisateurs de Linux et macOS doivent apporter la modification décrite à l’étape 2 ci-dessous. Enregistrez le fichier et validez la modification dans le référentiel local avec `git commit` . Enfin, envoyez la modification avec `git push` , comme dans la première section.*</span><span class="sxs-lookup"><span data-stu-id="cd347-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="cd347-178">L’application a déjà été déployée à partir de l’interface de commande.</span><span class="sxs-lookup"><span data-stu-id="cd347-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="cd347-179">Nous allons utiliser les outils intégrés de Visual Studio pour déployer une mise à jour de l’application.</span><span class="sxs-lookup"><span data-stu-id="cd347-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="cd347-180">En arrière-plan, Visual Studio remplit la même fonction que les outils de ligne de commande, mais dans l’interface utilisateur familière de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd347-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="cd347-181">Ouvrez *SimpleFeedReader. sln* dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd347-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="cd347-182">Dans Explorateur de solutions, ouvrez *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="cd347-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="cd347-183">Remplacez `<h2>Simple Feed Reader</h2>` par `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="cd347-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="cd347-184">Appuyez sur **CTRL** + **MAJ** + **B** pour générer l’application.</span><span class="sxs-lookup"><span data-stu-id="cd347-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="cd347-185">Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet et cliquez sur **publier**.</span><span class="sxs-lookup"><span data-stu-id="cd347-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Capture d’écran montrant un clic droit, publier](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="cd347-187">Visual Studio peut créer une ressource de App Service, mais cette mise à jour sera publiée sur le déploiement existant.</span><span class="sxs-lookup"><span data-stu-id="cd347-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="cd347-188">Dans la boîte de dialogue **choisir une cible de publication** , sélectionnez **app service** dans la liste à gauche, puis sélectionnez **Sélectionner existant**.</span><span class="sxs-lookup"><span data-stu-id="cd347-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="cd347-189">Cliquez sur **Publier**.</span><span class="sxs-lookup"><span data-stu-id="cd347-189">Click **Publish**.</span></span>
6. <span data-ttu-id="cd347-190">Dans la boîte de dialogue **app service** , vérifiez que le compte Microsoft ou professionnel utilisé pour créer votre abonnement Azure s’affiche dans l’angle supérieur droit.</span><span class="sxs-lookup"><span data-stu-id="cd347-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="cd347-191">Si ce n’est pas le cas, cliquez sur la liste déroulante et ajoutez-la.</span><span class="sxs-lookup"><span data-stu-id="cd347-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="cd347-192">Confirmez que l' **abonnement** Azure correct est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="cd347-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="cd347-193">Pour **Afficher**, sélectionnez **groupe de ressources**.</span><span class="sxs-lookup"><span data-stu-id="cd347-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="cd347-194">Développez le groupe de ressources **AzureTutorial** , puis sélectionnez l’application Web existante.</span><span class="sxs-lookup"><span data-stu-id="cd347-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="cd347-195">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="cd347-195">Click **OK**.</span></span>

    ![Capture d’écran montrant la boîte de dialogue publier App Service](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="cd347-197">Visual Studio génère et déploie l’application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="cd347-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="cd347-198">Accédez à l’URL de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="cd347-198">Browse to the web app URL.</span></span> <span data-ttu-id="cd347-199">Vérifiez que la `<h2>` modification de l’élément est active.</span><span class="sxs-lookup"><span data-stu-id="cd347-199">Validate that the `<h2>` element modification is live.</span></span>

![Application avec le titre modifié](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="cd347-201">Emplacements de déploiement</span><span class="sxs-lookup"><span data-stu-id="cd347-201">Deployment slots</span></span>

<span data-ttu-id="cd347-202">Les emplacements de déploiement prennent en charge la mise en lots des modifications sans affecter l’exécution de l’application en production.</span><span class="sxs-lookup"><span data-stu-id="cd347-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="cd347-203">Une fois que la version intermédiaire de l’application est validée par une équipe d’assurance qualité, les emplacements de production et intermédiaires peuvent être permutés.</span><span class="sxs-lookup"><span data-stu-id="cd347-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="cd347-204">L’application dans l’environnement intermédiaire est promue en production de cette manière.</span><span class="sxs-lookup"><span data-stu-id="cd347-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="cd347-205">Les étapes suivantes créent un emplacement intermédiaire, y déploient des modifications et échangent l’emplacement intermédiaire avec la production après vérification.</span><span class="sxs-lookup"><span data-stu-id="cd347-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="cd347-206">Connectez-vous au [Azure Cloud Shell](https://shell.azure.com/bash), s’il n’est pas déjà connecté.</span><span class="sxs-lookup"><span data-stu-id="cd347-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="cd347-207">Créez l’emplacement intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="cd347-207">Create the staging slot.</span></span>

    <span data-ttu-id="cd347-208">a.</span><span class="sxs-lookup"><span data-stu-id="cd347-208">a.</span></span> <span data-ttu-id="cd347-209">Créez un emplacement de déploiement avec le nom *intermédiaire*.</span><span class="sxs-lookup"><span data-stu-id="cd347-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="cd347-210">b.</span><span class="sxs-lookup"><span data-stu-id="cd347-210">b.</span></span> <span data-ttu-id="cd347-211">Configurez l’emplacement intermédiaire pour utiliser le déploiement à partir de Git local et récupérez l’URL de déploiement **intermédiaire** .</span><span class="sxs-lookup"><span data-stu-id="cd347-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="cd347-212">**Notez cette URL pour référence ultérieure**.</span><span class="sxs-lookup"><span data-stu-id="cd347-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="cd347-213">c.</span><span class="sxs-lookup"><span data-stu-id="cd347-213">c.</span></span> <span data-ttu-id="cd347-214">Affichez l’URL de l’emplacement intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="cd347-214">Display the staging slot's URL.</span></span> <span data-ttu-id="cd347-215">Accédez à l’URL pour voir l’emplacement de mise en lots vide.</span><span class="sxs-lookup"><span data-stu-id="cd347-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="cd347-216">**Notez cette URL pour référence ultérieure**.</span><span class="sxs-lookup"><span data-stu-id="cd347-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="cd347-217">Dans un éditeur de texte ou Visual Studio, modifiez *pages/index. cshtml* à nouveau afin que l' `<h2>` élément Lise `<h2>Simple Feed Reader - V3</h2>` et enregistre le fichier.</span><span class="sxs-lookup"><span data-stu-id="cd347-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="cd347-218">Validez le fichier dans le référentiel Git local, à l’aide de la page **modifications** dans l’onglet *Team Explorer* de Visual Studio, ou en entrant la commande suivante à l’aide de l’interface de commande de l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="cd347-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="cd347-219">À l’aide de l’interface de commande de l’ordinateur local, ajoutez l’URL de déploiement intermédiaire en tant que git distant et poussez les modifications validées :</span><span class="sxs-lookup"><span data-stu-id="cd347-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="cd347-220">a.</span><span class="sxs-lookup"><span data-stu-id="cd347-220">a.</span></span> <span data-ttu-id="cd347-221">Ajoutez l’URL distante pour la mise en lots dans le référentiel Git local.</span><span class="sxs-lookup"><span data-stu-id="cd347-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="cd347-222">b.</span><span class="sxs-lookup"><span data-stu-id="cd347-222">b.</span></span> <span data-ttu-id="cd347-223">Pousser la branche locale par défaut (*Master*) vers la branche par défaut (*Master*) de la succursale *Azure intermédiaire* .</span><span class="sxs-lookup"><span data-stu-id="cd347-223">Push the local default branch (*master*) to the *azure-staging* remote's default branch (*master*).</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="cd347-224">Patientez pendant que Azure génère et déploie l’application.</span><span class="sxs-lookup"><span data-stu-id="cd347-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="cd347-225">Pour vérifier que v3 a été déployé dans l’emplacement intermédiaire, ouvrez deux fenêtres de navigateur.</span><span class="sxs-lookup"><span data-stu-id="cd347-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="cd347-226">Dans une fenêtre, accédez à l’URL de l’application Web d’origine.</span><span class="sxs-lookup"><span data-stu-id="cd347-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="cd347-227">Dans l’autre fenêtre, accédez à l’URL de l’application Web intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="cd347-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="cd347-228">L’URL de production dessert v2 de l’application.</span><span class="sxs-lookup"><span data-stu-id="cd347-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="cd347-229">L’URL intermédiaire sert v3 à l’application.</span><span class="sxs-lookup"><span data-stu-id="cd347-229">The staging URL serves V3 of the app.</span></span>

    ![Capture d’écran comparaison des fenêtres de navigateur](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="cd347-231">Dans le Cloud Shell, échangez l’emplacement intermédiaire vérifié/chaud en production.</span><span class="sxs-lookup"><span data-stu-id="cd347-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="cd347-232">Vérifiez que l’échange s’est produit en actualisant les deux fenêtres du navigateur.</span><span class="sxs-lookup"><span data-stu-id="cd347-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Comparaison des fenêtres de navigateur après l’échange](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="cd347-234">Résumé</span><span class="sxs-lookup"><span data-stu-id="cd347-234">Summary</span></span>

<span data-ttu-id="cd347-235">Dans cette section, les tâches suivantes ont été effectuées :</span><span class="sxs-lookup"><span data-stu-id="cd347-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="cd347-236">Téléchargez et construisez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="cd347-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="cd347-237">Création d’une application Web Azure App Service à l’aide du Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="cd347-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="cd347-238">Déploiement de l’exemple d’application sur Azure à l’aide de git.</span><span class="sxs-lookup"><span data-stu-id="cd347-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="cd347-239">Déploiement d’une modification apportée à l’application à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd347-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="cd347-240">Ajout d’un emplacement intermédiaire à l’application Web.</span><span class="sxs-lookup"><span data-stu-id="cd347-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="cd347-241">Une mise à jour a été déployée sur l’emplacement intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="cd347-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="cd347-242">Échange des emplacements intermédiaires et de production.</span><span class="sxs-lookup"><span data-stu-id="cd347-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="cd347-243">Dans la section suivante, vous apprendrez à créer un pipeline DevOps avec Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="cd347-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="cd347-244">Documentation supplémentaire</span><span class="sxs-lookup"><span data-stu-id="cd347-244">Additional reading</span></span>

* [<span data-ttu-id="cd347-245">Vue d’ensemble des applications web</span><span class="sxs-lookup"><span data-stu-id="cd347-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="cd347-246">Créer une application Web .NET Core et SQL Database dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cd347-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="cd347-247">Configurer les informations d’identification de déploiement pour Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cd347-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="cd347-248">Configurer des environnements intermédiaires dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cd347-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
