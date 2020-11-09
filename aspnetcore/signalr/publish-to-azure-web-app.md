---
title: 'Publier une SignalR application ASP.net Core sur Azure App service'
author: bradygaster
description: 'Découvrez comment publier une application ASP.NET Core SignalR sur Azure App service.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234506"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="c4001-103">Publier une SignalR application ASP.net Core sur Azure App service</span><span class="sxs-lookup"><span data-stu-id="c4001-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="c4001-104">Par [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="c4001-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="c4001-105">[Azure App service](/azure/app-service/app-service-web-overview) est un service de plateforme [Microsoft Cloud Computing](https://azure.microsoft.com/) pour héberger des applications web, y compris ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c4001-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="c4001-106">Cet article fait référence à la publication d’une SignalR application ASP.net core à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c4001-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="c4001-107">Pour plus d’informations, consultez [ SignalR service pour Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="c4001-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="c4001-108">Publier l’application</span><span class="sxs-lookup"><span data-stu-id="c4001-108">Publish the app</span></span>

<span data-ttu-id="c4001-109">Cet article traite de la publication à l’aide des outils de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c4001-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="c4001-110">Visual Studio Code utilisateurs peuvent utiliser des commandes [Azure CLI](/cli/azure) pour publier des applications sur Azure.</span><span class="sxs-lookup"><span data-stu-id="c4001-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="c4001-111">Pour plus d’informations, consultez [publier une application ASP.net Core sur Azure à l’aide des outils en ligne de commande](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="c4001-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="c4001-112">Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions** , puis sélectionnez **Publier** .</span><span class="sxs-lookup"><span data-stu-id="c4001-112">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>

1. <span data-ttu-id="c4001-113">Confirmez que **app service** et **créer nouveau** sont sélectionnés dans la boîte de dialogue **choisir une cible de publication** .</span><span class="sxs-lookup"><span data-stu-id="c4001-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="c4001-114">Sélectionnez **créer un profil** dans la liste déroulante du bouton **publier** .</span><span class="sxs-lookup"><span data-stu-id="c4001-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="c4001-115">Entrez les informations décrites dans le tableau suivant de la boîte de dialogue **créer un app service** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="c4001-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create** .</span></span>

   | <span data-ttu-id="c4001-116">Élément</span><span class="sxs-lookup"><span data-stu-id="c4001-116">Item</span></span>               | <span data-ttu-id="c4001-117">Description</span><span class="sxs-lookup"><span data-stu-id="c4001-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="c4001-118">**Nom**</span><span class="sxs-lookup"><span data-stu-id="c4001-118">**Name**</span></span>           | <span data-ttu-id="c4001-119">Nom unique de l’application.</span><span class="sxs-lookup"><span data-stu-id="c4001-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="c4001-120">**Abonnement**</span><span class="sxs-lookup"><span data-stu-id="c4001-120">**Subscription**</span></span>   | <span data-ttu-id="c4001-121">Abonnement Azure utilisé par l’application.</span><span class="sxs-lookup"><span data-stu-id="c4001-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="c4001-122">**Groupe de ressources**</span><span class="sxs-lookup"><span data-stu-id="c4001-122">**Resource Group**</span></span> | <span data-ttu-id="c4001-123">Groupe de ressources associées auxquelles l’application appartient.</span><span class="sxs-lookup"><span data-stu-id="c4001-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="c4001-124">**Plan d’hébergement**</span><span class="sxs-lookup"><span data-stu-id="c4001-124">**Hosting Plan**</span></span>   | <span data-ttu-id="c4001-125">Plan de tarification pour l’application Web.</span><span class="sxs-lookup"><span data-stu-id="c4001-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="c4001-126">Sélectionnez **SignalR service Azure** dans la section **dépendances du service** .</span><span class="sxs-lookup"><span data-stu-id="c4001-126">Select **Azure SignalR Service** in the **Service Dependencies** section.</span></span> <span data-ttu-id="c4001-127">Sélectionnez le **+** bouton :</span><span class="sxs-lookup"><span data-stu-id="c4001-127">Select the **+** button:</span></span>

   ![Zone dépendances indiquant la sélection d’Azure ::: No-Loc (Signalr) ::: service dans la liste déroulante Ajouter](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="c4001-129">Dans la boîte de dialogue **SignalR service Azure** , sélectionnez **créer une nouvelle SignalR instance de service Azure** .</span><span class="sxs-lookup"><span data-stu-id="c4001-129">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance** .</span></span>

1. <span data-ttu-id="c4001-130">Fournissez un **nom** , un **groupe de ressources** et un **emplacement** .</span><span class="sxs-lookup"><span data-stu-id="c4001-130">Provide a **Name** , **Resource Group** , and **Location** .</span></span> <span data-ttu-id="c4001-131">Revenez à la boîte de dialogue **SignalR service Azure** , puis sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="c4001-131">Return to the **Azure SignalR Service** dialog and select **Add** .</span></span>

<span data-ttu-id="c4001-132">Visual Studio effectue les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c4001-132">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="c4001-133">Crée un profil de publication contenant les paramètres de publication.</span><span class="sxs-lookup"><span data-stu-id="c4001-133">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="c4001-134">Crée une *application Web Azure* avec les détails fournis.</span><span class="sxs-lookup"><span data-stu-id="c4001-134">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="c4001-135">Publie l’application.</span><span class="sxs-lookup"><span data-stu-id="c4001-135">Publishes the app.</span></span>
* <span data-ttu-id="c4001-136">Lance un navigateur, qui charge l’application Web.</span><span class="sxs-lookup"><span data-stu-id="c4001-136">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="c4001-137">Le format de l’URL de l’application est `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="c4001-137">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="c4001-138">Par exemple, une application nommée `SignalRChatApp` a une URL de `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="c4001-138">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="c4001-139">Si une erreur de *passerelle HTTP 502,2-incorrecte* se produit lors du déploiement d’une application qui cible une version préliminaire de .net Core, consultez [déployer ASP.net core version préliminaire dans Azure App service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) pour la résoudre.</span><span class="sxs-lookup"><span data-stu-id="c4001-139">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="c4001-140">Configurer l’application dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c4001-140">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="c4001-141">*Cette section s’applique uniquement aux applications qui n’utilisent pas le SignalR service Azure.*</span><span class="sxs-lookup"><span data-stu-id="c4001-141">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="c4001-142">Si l’application utilise le SignalR service Azure, le App service ne nécessite pas la configuration de l’affinité application Request Routing (arr) et des sockets Web décrits dans cette section.</span><span class="sxs-lookup"><span data-stu-id="c4001-142">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="c4001-143">Les clients connectent leurs Sockets Web au SignalR service Azure, et non directement à l’application.</span><span class="sxs-lookup"><span data-stu-id="c4001-143">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="c4001-144">Pour les applications hébergées sans le SignalR service Azure, activez :</span><span class="sxs-lookup"><span data-stu-id="c4001-144">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="c4001-145">[ARR Affinity] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(Arr- cookie ) -for-Azure-web-apps.html) pour acheminer les demandes d’un utilisateur vers la même instance de App service.</span><span class="sxs-lookup"><span data-stu-id="c4001-145">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="c4001-146">La valeur par défaut est **on** .</span><span class="sxs-lookup"><span data-stu-id="c4001-146">The default setting is **On** .</span></span>
* <span data-ttu-id="c4001-147">[WebSockets](xref:fundamentals/websockets) pour permettre au transport Web Sockets de fonctionner.</span><span class="sxs-lookup"><span data-stu-id="c4001-147">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="c4001-148">La valeur par défaut est **off** .</span><span class="sxs-lookup"><span data-stu-id="c4001-148">The default setting is **Off** .</span></span>

1. <span data-ttu-id="c4001-149">Dans le Portail Azure, accédez à l’application Web dans **app services** .</span><span class="sxs-lookup"><span data-stu-id="c4001-149">In the Azure portal, navigate to the web app in **App Services** .</span></span>
1. <span data-ttu-id="c4001-150">Ouvrez **Configuration**  >  **paramètres généraux** de configuration.</span><span class="sxs-lookup"><span data-stu-id="c4001-150">Open **Configuration** > **General settings** .</span></span>
1. <span data-ttu-id="c4001-151">Affectez la valeur **on** à **Web Sockets** .</span><span class="sxs-lookup"><span data-stu-id="c4001-151">Set **Web sockets** to **On** .</span></span>
1. <span data-ttu-id="c4001-152">Vérifiez que l' **affinité arr** est définie sur **on** .</span><span class="sxs-lookup"><span data-stu-id="c4001-152">Verify that **ARR affinity** is set to **On** .</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="c4001-153">Limites du plan de App Service</span><span class="sxs-lookup"><span data-stu-id="c4001-153">App Service Plan limits</span></span>

<span data-ttu-id="c4001-154">Les sockets Web et les autres transports sont limités en fonction du plan de App Service sélectionné.</span><span class="sxs-lookup"><span data-stu-id="c4001-154">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="c4001-155">Pour plus d’informations, consultez les sections *limites de services Cloud Azure* et *limites de App service* de l’article [abonnement Azure et limites, quotas et contraintes du service](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="c4001-155">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4001-156">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c4001-156">Additional resources</span></span>

* [<span data-ttu-id="c4001-157">Qu’est-ce que le SignalR service Azure ?</span><span class="sxs-lookup"><span data-stu-id="c4001-157">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="c4001-158">Publier une application ASP.NET Core sur Azure avec des outils en ligne de commande</span><span class="sxs-lookup"><span data-stu-id="c4001-158">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="c4001-159">Héberger et déployer ASP.NET Core des applications en version préliminaire sur Azure</span><span class="sxs-lookup"><span data-stu-id="c4001-159">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
