---
title: Publier une application ASP.NET Core sur Azure avec Visual Studio
author: rick-anderson
description: Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060220"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="822ba-103">Publier une application ASP.NET Core sur Azure avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="822ba-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="822ba-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="822ba-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="822ba-105">Consultez [publier une application Web sur Azure App service à l’aide de Visual Studio pour Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) si vous travaillez sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="822ba-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="822ba-106">Pour résoudre un problème de déploiement App Service, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="822ba-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="822ba-107">Configurer</span><span class="sxs-lookup"><span data-stu-id="822ba-107">Set up</span></span>

* <span data-ttu-id="822ba-108">Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas.</span><span class="sxs-lookup"><span data-stu-id="822ba-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="822ba-109">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="822ba-109">Create a web app</span></span>

<span data-ttu-id="822ba-110">Dans la page de démarrage de Visual Studio, sélectionnez **Fichier > Nouveau > Projet...**</span><span class="sxs-lookup"><span data-stu-id="822ba-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Fichier](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="822ba-112">Renseignez la boîte de dialogue **Nouveau projet** :</span><span class="sxs-lookup"><span data-stu-id="822ba-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="822ba-113">Sélectionnez **Application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="822ba-113">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="822ba-114">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="822ba-114">Select **Next** .</span></span>

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="822ba-116">Dans la boîte de dialogue **Nouvelle application web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="822ba-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="822ba-117">Sélectionnez **application Web** .</span><span class="sxs-lookup"><span data-stu-id="822ba-117">Select **Web Application** .</span></span>
* <span data-ttu-id="822ba-118">Sélectionnez **modifier** sous authentification.</span><span class="sxs-lookup"><span data-stu-id="822ba-118">Select **Change** under Authentication.</span></span>

![Boîte de dialogue Nouveau ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="822ba-120">La boîte de dialogue **Modifier l’authentification** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="822ba-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="822ba-121">Sélectionnez **Comptes d’utilisateur individuels** .</span><span class="sxs-lookup"><span data-stu-id="822ba-121">Select **Individual User Accounts** .</span></span>
* <span data-ttu-id="822ba-122">Sélectionnez **OK** pour revenir à la **nouvelle application Web ASP.net Core** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="822ba-122">Select **OK** to return to the **New ASP.NET Core Web Application** , then select **Create** .</span></span>

![Boîte de dialogue Nouvelle application web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="822ba-124">Visual Studio crée la solution.</span><span class="sxs-lookup"><span data-stu-id="822ba-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="822ba-125">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="822ba-125">Run the app</span></span>

* <span data-ttu-id="822ba-126">Appuyez sur CTRL+F5 pour exécuter le projet.</span><span class="sxs-lookup"><span data-stu-id="822ba-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="822ba-127">Testez le lien de **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="822ba-127">Test the **Privacy** link.</span></span>

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="822ba-129">Inscrire un utilisateur</span><span class="sxs-lookup"><span data-stu-id="822ba-129">Register a user</span></span>

* <span data-ttu-id="822ba-130">Sélectionnez **S’inscrire** , puis inscrivez un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="822ba-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="822ba-131">Vous pouvez utiliser une adresse e-mail fictive.</span><span class="sxs-lookup"><span data-stu-id="822ba-131">You can use a fictitious email address.</span></span> <span data-ttu-id="822ba-132">Quand vous effectuez l’envoi, la page affiche l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="822ba-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="822ba-133">*«Une opération de base de données a échoué lors du traitement de la demande. L’application de migrations existantes pour le contexte de la base de la base de la base de de l’application peut résoudre ce problème*</span><span class="sxs-lookup"><span data-stu-id="822ba-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="822ba-134">Sélectionnez **Appliquer les migrations** , puis, une fois la page mise à jour, actualisez-la.</span><span class="sxs-lookup"><span data-stu-id="822ba-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Une opération de base de données a échoué lors du traitement de la requête.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="822ba-137">L’application affiche l’e-mail utilisé pour inscrire le nouvel utilisateur et un lien de **déconnexion** .</span><span class="sxs-lookup"><span data-stu-id="822ba-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Application web ouverte dans Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="822ba-140">Déploiement de l’application dans Azure</span><span class="sxs-lookup"><span data-stu-id="822ba-140">Deploy the app to Azure</span></span>

<span data-ttu-id="822ba-141">Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **publier...** .</span><span class="sxs-lookup"><span data-stu-id="822ba-141">Right-click on the project in Solution Explorer and select **Publish...** .</span></span>

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="822ba-143">Dans la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="822ba-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="822ba-144">Sélectionnez **Azure** .</span><span class="sxs-lookup"><span data-stu-id="822ba-144">Select **Azure** .</span></span>
* <span data-ttu-id="822ba-145">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="822ba-145">Select **Next** .</span></span>

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="822ba-147">Dans la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="822ba-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="822ba-148">Sélectionnez **Azure App service (Linux)** .</span><span class="sxs-lookup"><span data-stu-id="822ba-148">Select **Azure App Service (Linux)** .</span></span>
* <span data-ttu-id="822ba-149">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="822ba-149">Select **Next** .</span></span>

![Boîte de dialogue publier : sélectionner un service Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="822ba-151">Dans la boîte de dialogue **publier** , sélectionnez **créer une Azure App service...**</span><span class="sxs-lookup"><span data-stu-id="822ba-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Boîte de dialogue publier : sélectionner une instance de service Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="822ba-153">La boîte de dialogue **créer un app service** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="822ba-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="822ba-154">Les champs d’entrée **Nom de l’application** , **Groupe de ressources** et **Plan App Service** sont renseignés.</span><span class="sxs-lookup"><span data-stu-id="822ba-154">The **App Name** , **Resource Group** , and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="822ba-155">Vous pouvez conserver ces noms ou les changer.</span><span class="sxs-lookup"><span data-stu-id="822ba-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="822ba-156">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="822ba-156">Select **Create** .</span></span>

![Boîte de dialogue Créer App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="822ba-158">Une fois la création terminée, la boîte de dialogue est fermée automatiquement et la boîte de dialogue **publier** se met à nouveau en place :</span><span class="sxs-lookup"><span data-stu-id="822ba-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="822ba-159">La nouvelle instance qui vient d’être créée est automatiquement sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="822ba-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="822ba-160">Sélectionnez **Terminer** .</span><span class="sxs-lookup"><span data-stu-id="822ba-160">Select **Finish** .</span></span>

![Boîte de dialogue publier : sélectionner une instance de App Service](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="822ba-162">Vous voyez ensuite la page **Résumé du profil de publication** .</span><span class="sxs-lookup"><span data-stu-id="822ba-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="822ba-163">Visual Studio a détecté que cette application nécessite une base de données SQL Server et qu’elle vous demande de la configurer.</span><span class="sxs-lookup"><span data-stu-id="822ba-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="822ba-164">Sélectionnez **Configurer** .</span><span class="sxs-lookup"><span data-stu-id="822ba-164">Select **Configure** .</span></span>

![Page Résumé du profil de publication : configurer la dépendance SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="822ba-166">La boîte de dialogue **configurer la dépendance** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="822ba-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="822ba-167">Sélectionnez **Azure SQL Database** .</span><span class="sxs-lookup"><span data-stu-id="822ba-167">Select **Azure SQL Database** .</span></span>
* <span data-ttu-id="822ba-168">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="822ba-168">Select **Next** .</span></span>

![Boîte de dialogue configurer SQL Server dépendance](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="822ba-170">Dans la boîte **de dialogue Configurer la base de données SQL Azure** , sélectionnez **créer un SQL Database**</span><span class="sxs-lookup"><span data-stu-id="822ba-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Boîte de dialogue Configurer Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="822ba-172">L' **Azure SQL Database créer** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="822ba-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="822ba-173">Les champs **nom de la base de données** , **groupe de ressources** , **serveur de base de données** et entrée du **plan de App service** sont remplis.</span><span class="sxs-lookup"><span data-stu-id="822ba-173">The **Database name** , **Resource Group** , **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="822ba-174">Vous pouvez conserver ces valeurs ou les modifier.</span><span class="sxs-lookup"><span data-stu-id="822ba-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="822ba-175">Entrez le **nom d’utilisateur** et le **mot de passe** de l’administrateur de base de données pour le **serveur de base de données** sélectionné (Notez que le compte que vous utilisez doit disposer des autorisations nécessaires pour créer la nouvelle base de données SQL Azure)</span><span class="sxs-lookup"><span data-stu-id="822ba-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="822ba-176">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="822ba-176">Select **Create** .</span></span>

![Boîte de dialogue Nouveau Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="822ba-178">Une fois la création terminée, la boîte de dialogue est fermée automatiquement et la boîte de dialogue **configurer Azure SQL Database** est à nouveau activée :</span><span class="sxs-lookup"><span data-stu-id="822ba-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="822ba-179">La nouvelle instance qui vient d’être créée est automatiquement sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="822ba-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="822ba-180">Sélectionnez **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="822ba-180">Select **Next** .</span></span>

![Boîte de dialogue Configurer Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="822ba-182">À l’étape suivante de la boîte de dialogue **configurer Azure SQL Database** :</span><span class="sxs-lookup"><span data-stu-id="822ba-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="822ba-183">Entrez les champs **nom d’utilisateur** et **mot de passe de** connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="822ba-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="822ba-184">Il s’agit des détails que votre application utilisera pour se connecter à la base de données au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="822ba-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="822ba-185">La meilleure pratique consiste à éviter d’utiliser les mêmes détails que le nom d’utilisateur administrateur & mot de passe utilisé à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="822ba-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="822ba-186">Sélectionnez **Terminer** .</span><span class="sxs-lookup"><span data-stu-id="822ba-186">Select **Finish** .</span></span>

![Boîte de dialogue Configurer Azure SQL Database, détails de la chaîne de connexion](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="822ba-188">Dans la page **Résumé du profil de publication** , sélectionnez **paramètres** :</span><span class="sxs-lookup"><span data-stu-id="822ba-188">In the **Publish Profile summary** page select **Settings** :</span></span>

![Page Résumé du profil de publication : modifier les paramètres](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="822ba-190">Dans la page **Paramètres** de la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="822ba-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="822ba-191">Développez **bases de données** et cochez **la case utiliser cette chaîne de connexion au moment de l’exécution** .</span><span class="sxs-lookup"><span data-stu-id="822ba-191">Expand **Databases** and check **Use this connection string at runtime** .</span></span>
* <span data-ttu-id="822ba-192">Développez **Entity Framework migrations** et cochez **appliquer cette migration lors de la publication** .</span><span class="sxs-lookup"><span data-stu-id="822ba-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish** .</span></span>

* <span data-ttu-id="822ba-193">Sélectionnez **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="822ba-193">Select **Save** .</span></span> <span data-ttu-id="822ba-194">Visual Studio retourne à la boîte de dialogue **Publier** .</span><span class="sxs-lookup"><span data-stu-id="822ba-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="822ba-196">Cliquez sur **Publier** .</span><span class="sxs-lookup"><span data-stu-id="822ba-196">Click **Publish** .</span></span> <span data-ttu-id="822ba-197">Visual Studio publie votre application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="822ba-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="822ba-198">Quand le déploiement est terminé, l’application est ouverte dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="822ba-198">When the deployment completes, the app is opened in a browser.</span></span>

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="822ba-200">Mettre à jour l’application</span><span class="sxs-lookup"><span data-stu-id="822ba-200">Update the app</span></span>

* <span data-ttu-id="822ba-201">Modifiez la page *pages/index. cshtml* :::no-loc(Razor)::: et modifiez son contenu.</span><span class="sxs-lookup"><span data-stu-id="822ba-201">Edit the *Pages/Index.cshtml* :::no-loc(Razor)::: page and change its contents.</span></span> <span data-ttu-id="822ba-202">Par exemple, vous pouvez modifier le paragraphe pour indiquer « Hello ASP.NET Core! » :</span><span class="sxs-lookup"><span data-stu-id="822ba-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="822ba-203">Sélectionnez à nouveau **publier** dans la page **Résumé du profil de publication** .</span><span class="sxs-lookup"><span data-stu-id="822ba-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Page Résumé du profil de publication](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="822ba-205">Une fois l’application publiée, vérifiez que les changements apportés sont disponibles sur Azure.</span><span class="sxs-lookup"><span data-stu-id="822ba-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Vérifiez que la tâche est terminée](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="822ba-207">Nettoyage</span><span class="sxs-lookup"><span data-stu-id="822ba-207">Clean up</span></span>

<span data-ttu-id="822ba-208">Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.</span><span class="sxs-lookup"><span data-stu-id="822ba-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="822ba-209">Sélectionnez **Groupes de ressources** , puis sélectionnez le groupe de ressources que vous avez créé.</span><span class="sxs-lookup"><span data-stu-id="822ba-209">Select **Resource groups** , then select the resource group you created.</span></span>

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="822ba-211">Dans la page **Groupes de ressources** , sélectionnez **Supprimer** .</span><span class="sxs-lookup"><span data-stu-id="822ba-211">In the **Resource groups** page, select **Delete** .</span></span>

![Portail Azure : page Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="822ba-213">Entrez le nom du groupe de ressources et sélectionnez **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="822ba-213">Enter the name of the resource group and select **Delete** .</span></span> <span data-ttu-id="822ba-214">Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.</span><span class="sxs-lookup"><span data-stu-id="822ba-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="822ba-215">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="822ba-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="822ba-216">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="822ba-216">Additional resources</span></span>

* <span data-ttu-id="822ba-217">Pour Visual Studio Code, consultez [Profils de publication](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="822ba-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="822ba-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="822ba-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="822ba-219">Groupes de ressources Azure</span><span class="sxs-lookup"><span data-stu-id="822ba-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="822ba-220">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="822ba-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>