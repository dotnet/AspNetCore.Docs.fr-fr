---
title: Publier une application ASP.NET Core sur IIS
author: rick-anderson
description: Découvrez comment héberger une application ASP.NET Core sur un serveur IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 0f70b5f12b9097f8710c9641404b3e085968fc3f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753151"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="902bf-103">Publier une application ASP.NET Core sur IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="902bf-104">Ce tutoriel explique comment héberger une application ASP.NET Core sur un serveur IIS.</span><span class="sxs-lookup"><span data-stu-id="902bf-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="902bf-105">Ce tutoriel couvre les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="902bf-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="902bf-106">Installer le bundle d’hébergement .NET Core sur Windows Server</span><span class="sxs-lookup"><span data-stu-id="902bf-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="902bf-107">Créer un site IIS dans le gestionnaire IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="902bf-108">Déployer une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="902bf-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="902bf-109">Conditions préalables requises</span><span class="sxs-lookup"><span data-stu-id="902bf-109">Prerequisites</span></span>

* <span data-ttu-id="902bf-110">Installation du [SDK .NET Core](/dotnet/core/sdk) sur l’ordinateur de développement</span><span class="sxs-lookup"><span data-stu-id="902bf-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="902bf-111">Configuration de Windows Server avec le rôle serveur **Serveur Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="902bf-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="902bf-112">Si votre serveur n’est pas configuré pour héberger des sites web avec IIS, suivez les instructions qui se trouvent dans la section *Configuration IIS* de l’article <xref:host-and-deploy/iis/index#iis-configuration>, puis revenez à ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="902bf-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="902bf-113">**La configuration d’IIS et la sécurité des sites web impliquent des concepts qui ne sont pas abordés dans ce tutoriel.**</span><span class="sxs-lookup"><span data-stu-id="902bf-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="902bf-114">Avant d’héberger des applications de production sur IIS, consultez la [documentation IIS de Microsoft](https://www.iis.net/) et cet [article ASP.NET Core concernant l’hébergement sur IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="902bf-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="902bf-115">Voici certains scénarios importants concernant l’hébergement sur IIS qui ne sont pas abordés dans ce tutoriel :</span><span class="sxs-lookup"><span data-stu-id="902bf-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="902bf-116">Création d’une ruche de Registre pour la protection des données ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="902bf-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/advanced#data-protection)
> * [<span data-ttu-id="902bf-117">Configuration de la liste de contrôle d’accès (ACL) du pool d’applications</span><span class="sxs-lookup"><span data-stu-id="902bf-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/advanced#application-pool-identity)
> * <span data-ttu-id="902bf-118">Pour aborder les concepts de déploiement IIS, ce tutoriel déploie une application pour laquelle aucune sécurité HTTPS n’a été configurée dans IIS.</span><span class="sxs-lookup"><span data-stu-id="902bf-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="902bf-119">Pour plus d’informations sur l’hébergement d’une application dans laquelle est activé le protocole HTTPS, consultez les rubriques relatives à la sécurité dans la section [Ressources supplémentaires](#additional-resources) de cet article.</span><span class="sxs-lookup"><span data-stu-id="902bf-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="902bf-120">Vous trouverez des informations supplémentaires sur l’hébergement d’applications ASP.NET Core dans l’article <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="902bf-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="902bf-121">Installer le bundle d’hébergement .NET Core</span><span class="sxs-lookup"><span data-stu-id="902bf-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="902bf-122">Installez le *bundle d’hébergement .NET Core* sur le serveur IIS.</span><span class="sxs-lookup"><span data-stu-id="902bf-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="902bf-123">L’offre groupée installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="902bf-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="902bf-124">Le module permet aux applications ASP.NET Core de s’exécuter derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="902bf-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="902bf-125">Téléchargez le programme d’installation à l’aide du lien suivant :</span><span class="sxs-lookup"><span data-stu-id="902bf-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="902bf-126">Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)</span><span class="sxs-lookup"><span data-stu-id="902bf-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="902bf-127">Exécutez le programme d’installation sur le serveur IIS.</span><span class="sxs-lookup"><span data-stu-id="902bf-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="902bf-128">Redémarrez le serveur ou exécutez `net stop was /y` suivi `net start w3svc` de dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="902bf-128">Restart the server or execute `net stop was /y` followed by `net start w3svc` in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="902bf-129">Créer le site IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-129">Create the IIS site</span></span>

1. <span data-ttu-id="902bf-130">Sur le serveur IIS, créez un dossier pour contenir les fichiers et dossiers publiés de l’application.</span><span class="sxs-lookup"><span data-stu-id="902bf-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="902bf-131">À l’étape suivante, le chemin du dossier est fourni à IIS en tant que chemin d’accès physique à l’application.</span><span class="sxs-lookup"><span data-stu-id="902bf-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="902bf-132">Pour plus d’informations sur le dossier de déploiement et la disposition d’un fichier d’une application, consultez <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="902bf-132">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="902bf-133">Dans le gestionnaire des services Internet, ouvrez le nœud du serveur dans le panneau **connexions** .</span><span class="sxs-lookup"><span data-stu-id="902bf-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="902bf-134">Cliquez avec le bouton de droite sur le dossier **Sites**.</span><span class="sxs-lookup"><span data-stu-id="902bf-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="902bf-135">Sélectionnez **Ajouter un site Web** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="902bf-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="902bf-136">Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application que vous avez créé.</span><span class="sxs-lookup"><span data-stu-id="902bf-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="902bf-137">Spécifiez la configuration **Liaison** et créez le site web en sélectionnant **OK**.</span><span class="sxs-lookup"><span data-stu-id="902bf-137">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

   > [!WARNING]
   > <span data-ttu-id="902bf-138">Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées.</span><span class="sxs-lookup"><span data-stu-id="902bf-138">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="902bf-139">Les liaisons génériques de niveau supérieur peuvent exposer votre application à des failles de sécurité.</span><span class="sxs-lookup"><span data-stu-id="902bf-139">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="902bf-140">Cela s’applique aux caractères génériques forts et faibles.</span><span class="sxs-lookup"><span data-stu-id="902bf-140">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="902bf-141">Utilisez des noms d’hôte explicites plutôt que des caractères génériques.</span><span class="sxs-lookup"><span data-stu-id="902bf-141">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="902bf-142">Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable).</span><span class="sxs-lookup"><span data-stu-id="902bf-142">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="902bf-143">Consultez la [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="902bf-143">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="902bf-144">Vérifiez que l’identité de modèle de processus dispose des autorisations appropriées.</span><span class="sxs-lookup"><span data-stu-id="902bf-144">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="902bf-145">Si l’identité par défaut du pool d’applications (**modèle de processus**  >  **Identity** ) est remplacée par `ApplicationPoolIdentity` une autre identité, vérifiez que la nouvelle identité dispose des autorisations nécessaires pour accéder au dossier de l’application, à la base de données et à d’autres ressources requises.</span><span class="sxs-lookup"><span data-stu-id="902bf-145">If the default identity of the app pool (**Process Model** > **Identity**) is changed from `ApplicationPoolIdentity` to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="902bf-146">Par exemple, le pool d’applications nécessite l’accès en lecture et en écriture aux dossiers dans lesquels l’application lit et écrit des fichiers.</span><span class="sxs-lookup"><span data-stu-id="902bf-146">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="902bf-147">Créer une Razor application ASP.net Core pages</span><span class="sxs-lookup"><span data-stu-id="902bf-147">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="902bf-148">Suivez le <xref:getting-started> didacticiel pour créer une Razor application pages.</span><span class="sxs-lookup"><span data-stu-id="902bf-148">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="902bf-149">Publier et déployer l’application</span><span class="sxs-lookup"><span data-stu-id="902bf-149">Publish and deploy the app</span></span>

<span data-ttu-id="902bf-150">*Publier une application* signifie que vous créez une application compilée qui peut être hébergée par un serveur.</span><span class="sxs-lookup"><span data-stu-id="902bf-150">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="902bf-151">*Déployer une application* signifie que vous déplacez l’application publiée vers un système d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="902bf-151">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="902bf-152">L’étape de publication est gérée par le [SDK .NET Core](/dotnet/core/sdk), alors que l’étape de déploiement peut être gérée à l’aide de différentes méthodes.</span><span class="sxs-lookup"><span data-stu-id="902bf-152">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="902bf-153">Ce tutoriel utilise la méthode de déploiement par *dossier*, dans laquelle :</span><span class="sxs-lookup"><span data-stu-id="902bf-153">This tutorial adopts the *folder* deployment approach, where:</span></span>
 
* <span data-ttu-id="902bf-154">L’application est publiée dans un dossier.</span><span class="sxs-lookup"><span data-stu-id="902bf-154">The app is published to a folder.</span></span>
* <span data-ttu-id="902bf-155">Le contenu du dossier est déplacé vers le dossier du site IIS (le **chemin physique** du site dans le gestionnaire IIS).</span><span class="sxs-lookup"><span data-stu-id="902bf-155">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="902bf-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="902bf-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="902bf-157">Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Publier**.</span><span class="sxs-lookup"><span data-stu-id="902bf-157">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="902bf-158">Dans la boîte de dialogue **Choisir une cible de publication**, sélectionnez l’option de publication **Dossier**.</span><span class="sxs-lookup"><span data-stu-id="902bf-158">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="902bf-159">Configurez un chemin pour **Dossier ou partage de fichiers**.</span><span class="sxs-lookup"><span data-stu-id="902bf-159">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="902bf-160">Si vous avez créé un dossier pour le site IIS qui est disponible sur l’ordinateur de développement en tant que partage réseau, indiquez le chemin de ce partage.</span><span class="sxs-lookup"><span data-stu-id="902bf-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="902bf-161">L’utilisateur actuel doit disposer d’un accès en écriture pour publier des données sur le partage.</span><span class="sxs-lookup"><span data-stu-id="902bf-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="902bf-162">Si vous ne parvenez pas à effectuer le déploiement directement dans le dossier site IIS sur le serveur IIS, publiez-le dans un dossier sur un support amovible et déplacez physiquement l’application publiée dans le dossier du site IIS sur le serveur, qui est le **chemin d’accès physique** du site dans le gestionnaire des services Internet.</span><span class="sxs-lookup"><span data-stu-id="902bf-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="902bf-163">Déplacez le contenu du `bin/Release/{TARGET FRAMEWORK}/publish` dossier vers le dossier site IIS sur le serveur, qui est le **chemin d’accès physique** du site dans le gestionnaire des services Internet.</span><span class="sxs-lookup"><span data-stu-id="902bf-163">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>
1. <span data-ttu-id="902bf-164">Cliquez sur le bouton **Publier**.</span><span class="sxs-lookup"><span data-stu-id="902bf-164">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="902bf-165">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="902bf-165">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="902bf-166">Dans une invite de commande, publiez l’application en configuration Release avec la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="902bf-166">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="902bf-167">Déplacez le contenu du `bin/Release/{TARGET FRAMEWORK}/publish` dossier vers le dossier site IIS sur le serveur, qui est le **chemin d’accès physique** du site dans le gestionnaire des services Internet.</span><span class="sxs-lookup"><span data-stu-id="902bf-167">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="902bf-168">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="902bf-168">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="902bf-169">Cliquez avec le bouton droit sur le projet dans **Solution**, puis sélectionnez **Publier** > **Publier sur un dossier**.</span><span class="sxs-lookup"><span data-stu-id="902bf-169">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="902bf-170">Configurez le chemin **Choisir un dossier**.</span><span class="sxs-lookup"><span data-stu-id="902bf-170">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="902bf-171">Si vous avez créé un dossier pour le site IIS qui est disponible sur l’ordinateur de développement en tant que partage réseau, indiquez le chemin de ce partage.</span><span class="sxs-lookup"><span data-stu-id="902bf-171">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="902bf-172">L’utilisateur actuel doit disposer d’un accès en écriture pour publier des données sur le partage.</span><span class="sxs-lookup"><span data-stu-id="902bf-172">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="902bf-173">Si vous ne parvenez pas à effectuer le déploiement directement dans le dossier du site IIS sur le serveur IIS, publiez l’application dans un dossier situé sur un support amovible et déplacez physiquement l’application publiée vers le dossier du site IIS sur le serveur, qui correspond au **chemin physique** du site dans le gestionnaire IIS.</span><span class="sxs-lookup"><span data-stu-id="902bf-173">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="902bf-174">Déplacez le contenu du `bin/Release/{TARGET FRAMEWORK}/publish` dossier vers le dossier site IIS sur le serveur, qui est le **chemin d’accès physique** du site dans le gestionnaire des services Internet.</span><span class="sxs-lookup"><span data-stu-id="902bf-174">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>
1. <span data-ttu-id="902bf-175">Cliquez sur le bouton **Publier**.</span><span class="sxs-lookup"><span data-stu-id="902bf-175">Select the **Publish** button.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="902bf-176">Parcourir le site Web</span><span class="sxs-lookup"><span data-stu-id="902bf-176">Browse the website</span></span>

<span data-ttu-id="902bf-177">L’application est accessible dans un navigateur après avoir reçu la première requête.</span><span class="sxs-lookup"><span data-stu-id="902bf-177">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="902bf-178">Envoyez une requête à l’application au niveau de la liaison de point de terminaison que vous avez établie dans le gestionnaire IIS pour le site.</span><span class="sxs-lookup"><span data-stu-id="902bf-178">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="902bf-179">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="902bf-179">Next steps</span></span>

<span data-ttu-id="902bf-180">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="902bf-180">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="902bf-181">Installer le bundle d’hébergement .NET Core sur Windows Server</span><span class="sxs-lookup"><span data-stu-id="902bf-181">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="902bf-182">Créer un site IIS dans le gestionnaire IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-182">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="902bf-183">Déployer une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="902bf-183">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="902bf-184">Pour plus d’informations sur l’hébergement des applications ASP.NET Core sur IIS, consultez l’article de présentation d’IIS :</span><span class="sxs-lookup"><span data-stu-id="902bf-184">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="902bf-185">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="902bf-185">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="902bf-186">Articles de la documentation ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="902bf-186">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="902bf-187">Articles relatifs au déploiement d’applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="902bf-187">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="902bf-188">Publier une application web sur un dossier à l’aide de Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="902bf-188">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="902bf-189">Articles sur la configuration HTTPS IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-189">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="902bf-190">Configuration du protocole SSL dans le gestionnaire IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-190">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="902bf-191">Configuration du protocole SSL sur IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-191">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="902bf-192">Articles sur IIS et Windows Server</span><span class="sxs-lookup"><span data-stu-id="902bf-192">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="902bf-193">Site officiel de Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-193">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="902bf-194">Bibliothèque de contenu technique Windows Server</span><span class="sxs-lookup"><span data-stu-id="902bf-194">Windows Server technical content library</span></span>](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="902bf-195">Déploiement de ressources pour les administrateurs IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-195">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="902bf-196">Documentation IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-196">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="902bf-197">Bien démarrer avec le Gestionnaire IIS dans IIS</span><span class="sxs-lookup"><span data-stu-id="902bf-197">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="902bf-198">Déploiement d’applications .NET Core</span><span class="sxs-lookup"><span data-stu-id="902bf-198">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

