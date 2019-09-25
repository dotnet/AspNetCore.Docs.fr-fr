---
title: Publier une application ASP.NET Core sur IIS
author: guardrex
description: Découvrez comment héberger une application ASP.NET Core sur un serveur IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 4ac7b3a2f738e443263dd888f556e0aff7c8099b
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082382"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="a2e16-103">Publier une application ASP.NET Core sur IIS</span><span class="sxs-lookup"><span data-stu-id="a2e16-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="a2e16-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a2e16-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a2e16-105">Ce tutoriel explique comment héberger une application ASP.NET Core sur un serveur IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-105">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="a2e16-106">Ce tutoriel couvre les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a2e16-106">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a2e16-107">Installer le bundle d’hébergement .NET Core sur Windows Server</span><span class="sxs-lookup"><span data-stu-id="a2e16-107">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="a2e16-108">Créer un site IIS dans le gestionnaire IIS</span><span class="sxs-lookup"><span data-stu-id="a2e16-108">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="a2e16-109">Déployer une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e16-109">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a2e16-110">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a2e16-110">Prerequisites</span></span>

* <span data-ttu-id="a2e16-111">Installation du [SDK .NET Core](/dotnet/core/sdk) sur l’ordinateur de développement</span><span class="sxs-lookup"><span data-stu-id="a2e16-111">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="a2e16-112">Configuration de Windows Server avec le rôle serveur **Serveur Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="a2e16-112">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="a2e16-113">Si votre serveur n’est pas configuré pour héberger des sites web avec IIS, suivez les instructions qui se trouvent dans la section *Configuration IIS* de l’article <xref:host-and-deploy/iis/index#iis-configuration>, puis revenez à ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="a2e16-113">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="a2e16-114">**La configuration d’IIS et la sécurité des sites web impliquent des concepts qui ne sont pas abordés dans ce tutoriel.**</span><span class="sxs-lookup"><span data-stu-id="a2e16-114">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="a2e16-115">Avant d’héberger des applications de production sur IIS, consultez la [documentation IIS de Microsoft](https://www.iis.net/) et cet [article ASP.NET Core concernant l’hébergement sur IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="a2e16-115">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="a2e16-116">Voici certains scénarios importants concernant l’hébergement sur IIS qui ne sont pas abordés dans ce tutoriel :</span><span class="sxs-lookup"><span data-stu-id="a2e16-116">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="a2e16-117">Création d’une ruche de Registre pour la protection des données ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e16-117">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="a2e16-118">Configuration de la liste de contrôle d’accès (ACL) du pool d’applications</span><span class="sxs-lookup"><span data-stu-id="a2e16-118">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="a2e16-119">Pour aborder les concepts de déploiement IIS, ce tutoriel déploie une application pour laquelle aucune sécurité HTTPS n’a été configurée dans IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-119">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="a2e16-120">Pour plus d’informations sur l’hébergement d’une application dans laquelle est activé le protocole HTTPS, consultez les rubriques relatives à la sécurité dans la section [Ressources supplémentaires](#additional-resources) de cet article.</span><span class="sxs-lookup"><span data-stu-id="a2e16-120">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="a2e16-121">Vous trouverez des informations supplémentaires sur l’hébergement d’applications ASP.NET Core dans l’article <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="a2e16-121">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="a2e16-122">Installer le bundle d’hébergement .NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e16-122">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="a2e16-123">Installez le *bundle d’hébergement .NET Core* sur le serveur IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-123">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="a2e16-124">Le bundle installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="a2e16-124">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a2e16-125">Le module permet aux applications ASP.NET Core de s’exécuter derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-125">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="a2e16-126">Si le système n’a pas de connexion Internet, obtenez et installez [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) avant d’installer le bundle d’hébergement .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2e16-126">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

<span data-ttu-id="a2e16-127">Téléchargez le programme d’installation à l’aide du lien suivant :</span><span class="sxs-lookup"><span data-stu-id="a2e16-127">Download the installer using the following link:</span></span>

[<span data-ttu-id="a2e16-128">Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)</span><span class="sxs-lookup"><span data-stu-id="a2e16-128">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="a2e16-129">Exécutez le programme d’installation sur le serveur IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-129">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="a2e16-130">Redémarrez le système ou exécutez **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commande.</span><span class="sxs-lookup"><span data-stu-id="a2e16-130">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="a2e16-131">Créer le site IIS</span><span class="sxs-lookup"><span data-stu-id="a2e16-131">Create the IIS site</span></span>

1. <span data-ttu-id="a2e16-132">Sur le serveur IIS, créez un dossier pour contenir les fichiers et dossiers publiés de l’application.</span><span class="sxs-lookup"><span data-stu-id="a2e16-132">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="a2e16-133">À l’étape suivante, le chemin du dossier est fourni à IIS en tant que chemin d’accès physique à l’application.</span><span class="sxs-lookup"><span data-stu-id="a2e16-133">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="a2e16-134">Dans le Gestionnaire IIS, ouvrez le nœud du serveur dans le panneau **Connexions**.</span><span class="sxs-lookup"><span data-stu-id="a2e16-134">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="a2e16-135">Cliquez avec le bouton de droite sur le dossier **Sites**.</span><span class="sxs-lookup"><span data-stu-id="a2e16-135">Right-click the **Sites** folder.</span></span> <span data-ttu-id="a2e16-136">Sélectionnez **Ajouter un site Web** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="a2e16-136">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="a2e16-137">Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application que vous avez créé.</span><span class="sxs-lookup"><span data-stu-id="a2e16-137">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="a2e16-138">Spécifiez la configuration **Liaison** et créez le site web en sélectionnant **OK**.</span><span class="sxs-lookup"><span data-stu-id="a2e16-138">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="a2e16-139">Créer une application Razor Pages ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e16-139">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="a2e16-140">Suivez le tutoriel <xref:getting-started> pour créer une application Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a2e16-140">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="a2e16-141">Publier et déployer l’application</span><span class="sxs-lookup"><span data-stu-id="a2e16-141">Publish and deploy the app</span></span>

<span data-ttu-id="a2e16-142">*Publier une application* signifie que vous créez une application compilée qui peut être hébergée par un serveur.</span><span class="sxs-lookup"><span data-stu-id="a2e16-142">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="a2e16-143">*Déployer une application* signifie que vous déplacez l’application publiée vers un système d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="a2e16-143">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="a2e16-144">L’étape de publication est gérée par le [SDK .NET Core](/dotnet/core/sdk), alors que l’étape de déploiement peut être gérée à l’aide de différentes méthodes.</span><span class="sxs-lookup"><span data-stu-id="a2e16-144">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="a2e16-145">Ce tutoriel utilise la méthode de déploiement par *dossier*, dans laquelle :</span><span class="sxs-lookup"><span data-stu-id="a2e16-145">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="a2e16-146">L’application est publiée dans un dossier.</span><span class="sxs-lookup"><span data-stu-id="a2e16-146">The app is published to a folder.</span></span>
* <span data-ttu-id="a2e16-147">Le contenu du dossier est déplacé vers le dossier du site IIS (le **chemin physique** du site dans le gestionnaire IIS).</span><span class="sxs-lookup"><span data-stu-id="a2e16-147">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a2e16-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2e16-148">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a2e16-149">Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Publier**.</span><span class="sxs-lookup"><span data-stu-id="a2e16-149">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="a2e16-150">Dans la boîte de dialogue **Choisir une cible de publication**, sélectionnez l’option de publication **Dossier**.</span><span class="sxs-lookup"><span data-stu-id="a2e16-150">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="a2e16-151">Configurez un chemin pour **Dossier ou partage de fichiers**.</span><span class="sxs-lookup"><span data-stu-id="a2e16-151">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="a2e16-152">Si vous avez créé un dossier pour le site IIS qui est disponible sur l’ordinateur de développement en tant que partage réseau, indiquez le chemin de ce partage.</span><span class="sxs-lookup"><span data-stu-id="a2e16-152">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="a2e16-153">L’utilisateur actuel doit disposer d’un accès en écriture pour publier des données sur le partage.</span><span class="sxs-lookup"><span data-stu-id="a2e16-153">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="a2e16-154">Si vous ne parvenez pas à effectuer le déploiement directement dans le dossier du site IIS sur le serveur IIS, publiez l’application dans un dossier situé sur un support amovible et déplacez physiquement l’application publiée vers le dossier du site IIS sur le serveur, qui correspond au **chemin physique** du site dans le gestionnaire IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-154">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="a2e16-155">Déplacez le contenu du dossier *bin/Release/{FRAMEWORK CIBLE}/publish* vers le dossier du site IIS qui se trouve sur le serveur et qui correspond au **chemin physique** du site dans le gestionnaire IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-155">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a2e16-156">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e16-156">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="a2e16-157">Dans une invite de commande, publiez l’application en configuration Release avec la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="a2e16-157">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="a2e16-158">Déplacez le contenu du dossier *bin/Release/{FRAMEWORK CIBLE}/publish* vers le dossier du site IIS qui se trouve sur le serveur et qui correspond au **chemin physique** du site dans le gestionnaire IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-158">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a2e16-159">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a2e16-159">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a2e16-160">Cliquez avec le bouton droit sur le projet dans **Solution**, puis sélectionnez **Publier** > **Publier sur un dossier**.</span><span class="sxs-lookup"><span data-stu-id="a2e16-160">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="a2e16-161">Configurez le chemin **Choisir un dossier**.</span><span class="sxs-lookup"><span data-stu-id="a2e16-161">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="a2e16-162">Si vous avez créé un dossier pour le site IIS qui est disponible sur l’ordinateur de développement en tant que partage réseau, indiquez le chemin de ce partage.</span><span class="sxs-lookup"><span data-stu-id="a2e16-162">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="a2e16-163">L’utilisateur actuel doit disposer d’un accès en écriture pour publier des données sur le partage.</span><span class="sxs-lookup"><span data-stu-id="a2e16-163">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="a2e16-164">Si vous ne parvenez pas à effectuer le déploiement directement dans le dossier du site IIS sur le serveur IIS, publiez l’application dans un dossier situé sur un support amovible et déplacez physiquement l’application publiée vers le dossier du site IIS sur le serveur, qui correspond au **chemin physique** du site dans le gestionnaire IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-164">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="a2e16-165">Déplacez le contenu du dossier *bin/Release/{FRAMEWORK CIBLE}/publish* vers le dossier du site IIS qui se trouve sur le serveur et qui correspond au **chemin physique** du site dans le gestionnaire IIS.</span><span class="sxs-lookup"><span data-stu-id="a2e16-165">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="a2e16-166">Parcourir le site web</span><span class="sxs-lookup"><span data-stu-id="a2e16-166">Browse the website</span></span>

<span data-ttu-id="a2e16-167">L’application est accessible dans un navigateur après avoir reçu la première requête.</span><span class="sxs-lookup"><span data-stu-id="a2e16-167">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="a2e16-168">Envoyez une requête à l’application au niveau de la liaison de point de terminaison que vous avez établie dans le gestionnaire IIS pour le site.</span><span class="sxs-lookup"><span data-stu-id="a2e16-168">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a2e16-169">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="a2e16-169">Next steps</span></span>

<span data-ttu-id="a2e16-170">Dans ce tutoriel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="a2e16-170">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a2e16-171">Installer le bundle d’hébergement .NET Core sur Windows Server</span><span class="sxs-lookup"><span data-stu-id="a2e16-171">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="a2e16-172">Créer un site IIS dans le gestionnaire IIS</span><span class="sxs-lookup"><span data-stu-id="a2e16-172">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="a2e16-173">Déployer une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e16-173">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="a2e16-174">Pour plus d’informations sur l’hébergement des applications ASP.NET Core sur IIS, consultez l’article de présentation d’IIS :</span><span class="sxs-lookup"><span data-stu-id="a2e16-174">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="a2e16-175">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a2e16-175">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="a2e16-176">Articles de la documentation ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e16-176">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="a2e16-177">Articles relatifs au déploiement d’applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2e16-177">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="a2e16-178">Publier une application web sur un dossier à l’aide de Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="a2e16-178">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="a2e16-179">Articles sur la configuration HTTPS IIS</span><span class="sxs-lookup"><span data-stu-id="a2e16-179">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="a2e16-180">Configuration du protocole SSL dans le gestionnaire IIS</span><span class="sxs-lookup"><span data-stu-id="a2e16-180">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="a2e16-181">Configuration du protocole SSL sur IIS</span><span class="sxs-lookup"><span data-stu-id="a2e16-181">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="a2e16-182">Articles sur IIS et Windows Server</span><span class="sxs-lookup"><span data-stu-id="a2e16-182">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="a2e16-183">Site officiel de Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="a2e16-183">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="a2e16-184">Bibliothèque de contenu technique Windows Server</span><span class="sxs-lookup"><span data-stu-id="a2e16-184">Windows Server technical content library</span></span>](/windows-server/windows-server)