---
title: Surveiller et déboguer-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Surveillance et débogage de votre code dans le cadre d’une solution DevOps avec ASP.NET Core et Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056749"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="ae2ad-103">Surveiller et déboguer</span><span class="sxs-lookup"><span data-stu-id="ae2ad-103">Monitor and debug</span></span>

<span data-ttu-id="ae2ad-104">Après avoir déployé l’application et créé un pipeline DevOps, il est important de comprendre comment surveiller et dépanner l’application.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="ae2ad-105">Dans cette section, vous allez effectuer les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="ae2ad-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="ae2ad-106">Recherchez des données de surveillance et de dépannage de base dans le Portail Azure</span><span class="sxs-lookup"><span data-stu-id="ae2ad-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="ae2ad-107">Découvrez comment Azure Monitor fournit un examen plus approfondi des métriques dans tous les services Azure</span><span class="sxs-lookup"><span data-stu-id="ae2ad-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="ae2ad-108">Connecter l’application Web avec Application Insights pour le profilage d’application</span><span class="sxs-lookup"><span data-stu-id="ae2ad-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="ae2ad-109">Activer la journalisation et savoir où télécharger les journaux</span><span class="sxs-lookup"><span data-stu-id="ae2ad-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="ae2ad-110">Effectuer le streaming des journaux en temps réel</span><span class="sxs-lookup"><span data-stu-id="ae2ad-110">Stream logs in real time</span></span>
* <span data-ttu-id="ae2ad-111">En savoir plus sur la configuration des alertes</span><span class="sxs-lookup"><span data-stu-id="ae2ad-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="ae2ad-112">En savoir plus sur le débogage à distance Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="ae2ad-113">Surveillance et dépannage de base</span><span class="sxs-lookup"><span data-stu-id="ae2ad-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="ae2ad-114">App Service applications Web sont facilement surveillées en temps réel.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="ae2ad-115">Le Portail Azure restitue des métriques dans des graphiques et des graphiques faciles à comprendre.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="ae2ad-116">Ouvrez le [portail Azure](https://portal.azure.com), puis accédez au App service *myWebApp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="ae2ad-117">L’onglet **vue d’ensemble** affiche des informations utiles « en un clin d’œil », y compris des graphiques qui affichent les métriques récentes.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Capture d’écran montrant le panneau vue d’ensemble](./media/monitoring/overview.png)

    * <span data-ttu-id="ae2ad-119">**Http 5xx** : nombre d’erreurs côté serveur, généralement les exceptions dans ASP.net Core code.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-119">**Http 5xx** : Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="ae2ad-120">**Données dans** : entrée de données entrant dans votre application Web.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-120">**Data In** : Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="ae2ad-121">**Données sortantes** : sortie de données de votre application Web vers les clients.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-121">**Data Out** : Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="ae2ad-122">**Demandes** : nombre de requêtes http.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-122">**Requests** : Count of HTTP requests.</span></span>
    * <span data-ttu-id="ae2ad-123">**Temps de réponse moyen** : temps moyen pour que l’application Web réponde aux requêtes http.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-123">**Average Response Time** : Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="ae2ad-124">Plusieurs outils libre-service pour le dépannage et l’optimisation sont également disponibles sur cette page.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Capture d’écran montrant les outils libre-service](./media/monitoring/wizards.png)

    * <span data-ttu-id="ae2ad-126">**Diagnostiquer et résoudre les problèmes** est un utilitaire de résolution des problèmes en libre-service.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="ae2ad-127">**Application Insights** concerne les performances de profilage et le comportement des applications, et est abordé plus loin dans cette section.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="ae2ad-128">**App service Advisor** émet des recommandations pour optimiser l’expérience de votre application.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="ae2ad-129">Surveillance avancée</span><span class="sxs-lookup"><span data-stu-id="ae2ad-129">Advanced monitoring</span></span>

<span data-ttu-id="ae2ad-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) est le service centralisé pour surveiller toutes les métriques et définir des alertes dans les services Azure.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="ae2ad-131">Dans Azure Monitor, les administrateurs peuvent effectuer un suivi granulaire des performances et identifier les tendances.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="ae2ad-132">Chaque service Azure offre son propre [ensemble de mesures](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) à Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="ae2ad-133">Profil avec Application Insights</span><span class="sxs-lookup"><span data-stu-id="ae2ad-133">Profile with Application Insights</span></span>

<span data-ttu-id="ae2ad-134">[Application Insights](/azure/application-insights/app-insights-overview) est un service Azure qui permet d’analyser les performances et la stabilité des applications Web, ainsi que la manière dont les utilisateurs les utilisent.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="ae2ad-135">Les données de Application Insights sont plus larges et plus profondes que celles de Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="ae2ad-136">Les données peuvent fournir aux développeurs et aux administrateurs des informations clés pour améliorer les applications.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="ae2ad-137">Application Insights peuvent être ajoutés à une ressource Azure App Service sans modification du code.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="ae2ad-138">Ouvrez le [portail Azure](https://portal.azure.com), puis accédez au App service *myWebApp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ae2ad-139">Dans l’onglet **vue d’ensemble** , cliquez sur la vignette **application Insights** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Vignette Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="ae2ad-141">Sélectionnez la case d’option **créer une nouvelle ressource** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="ae2ad-142">Utilisez le nom de ressource par défaut, puis sélectionnez l’emplacement de la ressource Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="ae2ad-143">L’emplacement n’a pas besoin de correspondre à celui de votre application Web.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-143">The location doesn't need to match that of your web app.</span></span>

    ![Configuration de Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="ae2ad-145">Pour **Runtime/Framework** , sélectionnez **ASP.net Core** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-145">For **Runtime/Framework** , select **ASP.NET Core** .</span></span> <span data-ttu-id="ae2ad-146">Acceptez les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-146">Accept the default settings.</span></span>
1. <span data-ttu-id="ae2ad-147">Sélectionnez **OK** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-147">Select **OK** .</span></span> <span data-ttu-id="ae2ad-148">Si vous êtes invité à confirmer, sélectionnez **Continuer** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-148">If prompted to confirm, select **Continue** .</span></span>
1. <span data-ttu-id="ae2ad-149">Une fois la ressource créée, cliquez sur le nom de Application Insights ressource pour accéder directement à la page de Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![La nouvelle ressource de Application Insights est prête](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="ae2ad-151">Lorsque l’application est utilisée, les données s’accumulent.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="ae2ad-152">Sélectionnez **Actualiser** pour recharger le panneau avec de nouvelles données.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-152">Select **Refresh** to reload the blade with new data.</span></span>

![Onglet vue d’ensemble des Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="ae2ad-154">Application Insights fournit des informations utiles côté serveur, sans configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="ae2ad-155">Pour tirer le meilleur parti de Application Insights, [instrumentez votre application avec le kit de développement logiciel (SDK) application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="ae2ad-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="ae2ad-156">Lorsqu’il est correctement configuré, le service fournit une surveillance de bout en bout sur le serveur Web et le navigateur, y compris les performances côté client.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="ae2ad-157">Pour plus d’informations, consultez la [documentation application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="ae2ad-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="ae2ad-158">Journalisation</span><span class="sxs-lookup"><span data-stu-id="ae2ad-158">Logging</span></span>

<span data-ttu-id="ae2ad-159">Les journaux de serveur Web et d’application sont désactivés par défaut dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="ae2ad-160">Activez les journaux en procédant comme suit :</span><span class="sxs-lookup"><span data-stu-id="ae2ad-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="ae2ad-161">Ouvrez le [portail Azure](https://portal.azure.com), puis accédez au App service *myWebApp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ae2ad-162">Dans le menu de gauche, faites défiler jusqu’à la section **surveillance** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="ae2ad-163">Sélectionnez **journaux de diagnostic** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-163">Select **Diagnostics logs** .</span></span>

    ![Lien des journaux de diagnostic](./media/monitoring/logging.png)

1. <span data-ttu-id="ae2ad-165">Activez la **journalisation des applications (système de fichiers)** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-165">Turn on **Application Logging (Filesystem)** .</span></span> <span data-ttu-id="ae2ad-166">Si vous y êtes invité, cliquez sur la case pour installer les extensions afin d’activer la journalisation de l’application dans l’application Web.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="ae2ad-167">Définissez la **journalisation du serveur Web** dans le **système de fichiers** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-167">Set **Web server logging** to **File System** .</span></span>
1. <span data-ttu-id="ae2ad-168">Entrez la **période de rétention** en jours.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="ae2ad-169">Par exemple, 30.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-169">For example, 30.</span></span>
1. <span data-ttu-id="ae2ad-170">Cliquez sur **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-170">Click **Save** .</span></span>

<span data-ttu-id="ae2ad-171">Les journaux de ASP.NET Core et de serveur Web (App Service) sont générés pour l’application Web.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="ae2ad-172">Ils peuvent être téléchargés à l’aide des informations FTP/FTPS affichées.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="ae2ad-173">Le mot de passe est le même que celui des informations d’identification de déploiement créées précédemment dans ce guide.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="ae2ad-174">Les journaux peuvent être [transmis en continu directement sur votre machine locale avec PowerShell ou Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="ae2ad-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="ae2ad-175">Vous pouvez également consulter les journaux [dans application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="ae2ad-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="ae2ad-176">Diffusion de journaux</span><span class="sxs-lookup"><span data-stu-id="ae2ad-176">Log streaming</span></span>

<span data-ttu-id="ae2ad-177">Les journaux des applications et des serveurs Web peuvent être diffusés en temps réel via le portail.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="ae2ad-178">Ouvrez le [portail Azure](https://portal.azure.com), puis accédez au App service *myWebApp \<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ae2ad-179">Dans le menu de gauche, faites défiler jusqu’à la section **surveillance** , puis sélectionnez **flux de journal** .</span><span class="sxs-lookup"><span data-stu-id="ae2ad-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream** .</span></span>

    ![Capture d’écran montrant le lien du flux de journal](./media/monitoring/log-stream.png)

<span data-ttu-id="ae2ad-181">Les journaux peuvent également être [diffusés en continu via Azure CLI ou Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), y compris via le Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="ae2ad-182">Alertes</span><span class="sxs-lookup"><span data-stu-id="ae2ad-182">Alerts</span></span>

<span data-ttu-id="ae2ad-183">Azure Monitor fournit également des [alertes en temps réel](/azure/monitoring-and-diagnostics/insights-alerts-portal) en fonction des métriques, des événements administratifs et d’autres critères.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="ae2ad-184">*Remarque : actuellement, les alertes sur les métriques de l’application Web sont uniquement disponibles dans le service alertes (Classic).*</span><span class="sxs-lookup"><span data-stu-id="ae2ad-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="ae2ad-185">Le [service alertes (Classic)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) se trouve dans Azure Monitor ou sous la section **surveillance** des paramètres App service.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Lien alertes (classiques)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="ae2ad-187">Débogage en direct</span><span class="sxs-lookup"><span data-stu-id="ae2ad-187">Live debugging</span></span>

<span data-ttu-id="ae2ad-188">Les Azure App Service peuvent être [débogués à distance avec Visual Studio quand les](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) journaux ne fournissent pas suffisamment d’informations.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="ae2ad-189">Toutefois, le débogage distant requiert que l’application soit compilée avec des symboles de débogage.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="ae2ad-190">Le débogage ne doit pas être effectué en production, sauf en dernier recours.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="ae2ad-191">Conclusion</span><span class="sxs-lookup"><span data-stu-id="ae2ad-191">Conclusion</span></span>

<span data-ttu-id="ae2ad-192">Dans cette section, vous avez effectué les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="ae2ad-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="ae2ad-193">Recherchez des données de surveillance et de dépannage de base dans le Portail Azure</span><span class="sxs-lookup"><span data-stu-id="ae2ad-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="ae2ad-194">Découvrez comment Azure Monitor fournit un examen plus approfondi des métriques dans tous les services Azure</span><span class="sxs-lookup"><span data-stu-id="ae2ad-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="ae2ad-195">Connecter l’application Web avec Application Insights pour le profilage d’application</span><span class="sxs-lookup"><span data-stu-id="ae2ad-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="ae2ad-196">Activer la journalisation et savoir où télécharger les journaux</span><span class="sxs-lookup"><span data-stu-id="ae2ad-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="ae2ad-197">Effectuer le streaming des journaux en temps réel</span><span class="sxs-lookup"><span data-stu-id="ae2ad-197">Stream logs in real time</span></span>
* <span data-ttu-id="ae2ad-198">En savoir plus sur la configuration des alertes</span><span class="sxs-lookup"><span data-stu-id="ae2ad-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="ae2ad-199">En savoir plus sur le débogage à distance Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="ae2ad-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="ae2ad-200">Documentation supplémentaire</span><span class="sxs-lookup"><span data-stu-id="ae2ad-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="ae2ad-201">Surveiller les performances de l’application web Azure avec Application Insights</span><span class="sxs-lookup"><span data-stu-id="ae2ad-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="ae2ad-202">Activer la journalisation des diagnostics pour les applications web dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ae2ad-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="ae2ad-203">Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae2ad-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="ae2ad-204">Créer des alertes de métriques classiques dans Azure Monitor pour les services Azure - Portail Azure</span><span class="sxs-lookup"><span data-stu-id="ae2ad-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
