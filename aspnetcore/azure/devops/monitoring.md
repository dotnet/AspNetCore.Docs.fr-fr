---
title: Surveiller et déboguer-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Surveillance et débogage de votre code dans le cadre d’une solution DevOps avec ASP.NET Core et Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
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
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056749"
---
# <a name="monitor-and-debug"></a>Surveiller et déboguer

Après avoir déployé l’application et créé un pipeline DevOps, il est important de comprendre comment surveiller et dépanner l’application.

Dans cette section, vous allez effectuer les tâches suivantes :

* Recherchez des données de surveillance et de dépannage de base dans le Portail Azure
* Découvrez comment Azure Monitor fournit un examen plus approfondi des métriques dans tous les services Azure
* Connecter l’application Web avec Application Insights pour le profilage d’application
* Activer la journalisation et savoir où télécharger les journaux
* Effectuer le streaming des journaux en temps réel
* En savoir plus sur la configuration des alertes
* En savoir plus sur le débogage à distance Azure App Service Web Apps.

## <a name="basic-monitoring-and-troubleshooting"></a>Surveillance et dépannage de base

App Service applications Web sont facilement surveillées en temps réel. Le Portail Azure restitue des métriques dans des graphiques et des graphiques faciles à comprendre.

1. Ouvrez le [portail Azure](https://portal.azure.com), puis accédez au App service *myWebApp \<unique_number\>* .

1. L’onglet **vue d’ensemble** affiche des informations utiles « en un clin d’œil », y compris des graphiques qui affichent les métriques récentes.

    ![Capture d’écran montrant le panneau vue d’ensemble](./media/monitoring/overview.png)

    * **Http 5xx**: nombre d’erreurs côté serveur, généralement les exceptions dans ASP.net Core code.
    * **Données dans**: entrée de données entrant dans votre application Web.
    * **Données sortantes**: sortie de données de votre application Web vers les clients.
    * **Demandes**: nombre de requêtes http.
    * **Temps de réponse moyen**: temps moyen pour que l’application Web réponde aux requêtes http.

    Plusieurs outils libre-service pour le dépannage et l’optimisation sont également disponibles sur cette page.

    ![Capture d’écran montrant les outils libre-service](./media/monitoring/wizards.png)

    * **Diagnostiquer et résoudre les problèmes** est un utilitaire de résolution des problèmes en libre-service.
    * **Application Insights** concerne les performances de profilage et le comportement des applications, et est abordé plus loin dans cette section.
    * **App service Advisor** émet des recommandations pour optimiser l’expérience de votre application.

## <a name="advanced-monitoring"></a>Surveillance avancée

[Azure Monitor](/azure/monitoring-and-diagnostics/) est le service centralisé pour surveiller toutes les métriques et définir des alertes dans les services Azure. Dans Azure Monitor, les administrateurs peuvent effectuer un suivi granulaire des performances et identifier les tendances. Chaque service Azure offre son propre [ensemble de mesures](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) à Azure Monitor.

## <a name="profile-with-application-insights"></a>Profil avec Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) est un service Azure qui permet d’analyser les performances et la stabilité des applications Web, ainsi que la manière dont les utilisateurs les utilisent. Les données de Application Insights sont plus larges et plus profondes que celles de Azure Monitor. Les données peuvent fournir aux développeurs et aux administrateurs des informations clés pour améliorer les applications. Application Insights peuvent être ajoutés à une ressource Azure App Service sans modification du code.

1. Ouvrez le [portail Azure](https://portal.azure.com), puis accédez au App service *myWebApp \<unique_number\>* .
1. Dans l’onglet **vue d’ensemble** , cliquez sur la vignette **application Insights** .

    ![Vignette Application Insights](./media/monitoring/app-insights.png)

1. Sélectionnez la case d’option **créer une nouvelle ressource** . Utilisez le nom de ressource par défaut, puis sélectionnez l’emplacement de la ressource Application Insights. L’emplacement n’a pas besoin de correspondre à celui de votre application Web.

    ![Configuration de Application Insights](./media/monitoring/new-app-insights.png)

1. Pour **Runtime/Framework**, sélectionnez **ASP.net Core**. Acceptez les paramètres par défaut.
1. Sélectionnez **OK**. Si vous êtes invité à confirmer, sélectionnez **Continuer**.
1. Une fois la ressource créée, cliquez sur le nom de Application Insights ressource pour accéder directement à la page de Application Insights.

    ![La nouvelle ressource de Application Insights est prête](./media/monitoring/new-app-insights-done.png)

Lorsque l’application est utilisée, les données s’accumulent. Sélectionnez **Actualiser** pour recharger le panneau avec de nouvelles données.

![Onglet vue d’ensemble des Application Insights](./media/monitoring/app-insights-overview.png)

Application Insights fournit des informations utiles côté serveur, sans configuration supplémentaire. Pour tirer le meilleur parti de Application Insights, [instrumentez votre application avec le kit de développement logiciel (SDK) application Insights](/azure/application-insights/app-insights-asp-net-core). Lorsqu’il est correctement configuré, le service fournit une surveillance de bout en bout sur le serveur Web et le navigateur, y compris les performances côté client. Pour plus d’informations, consultez la [documentation application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Journalisation

Les journaux de serveur Web et d’application sont désactivés par défaut dans Azure App Service. Activez les journaux en procédant comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com), puis accédez au App service *myWebApp \<unique_number\>* .
1. Dans le menu de gauche, faites défiler jusqu’à la section **surveillance** . Sélectionnez **journaux de diagnostic**.

    ![Lien des journaux de diagnostic](./media/monitoring/logging.png)

1. Activez la **journalisation des applications (système de fichiers)**. Si vous y êtes invité, cliquez sur la case pour installer les extensions afin d’activer la journalisation de l’application dans l’application Web.
1. Définissez la **journalisation du serveur Web** dans le **système de fichiers**.
1. Entrez la **période de rétention** en jours. Par exemple, 30.
1. Cliquez sur **Enregistrer**.

Les journaux de ASP.NET Core et de serveur Web (App Service) sont générés pour l’application Web. Ils peuvent être téléchargés à l’aide des informations FTP/FTPS affichées. Le mot de passe est le même que celui des informations d’identification de déploiement créées précédemment dans ce guide. Les journaux peuvent être [transmis en continu directement sur votre machine locale avec PowerShell ou Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download). Vous pouvez également consulter les journaux [dans application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Diffusion de journaux

Les journaux des applications et des serveurs Web peuvent être diffusés en temps réel via le portail.

1. Ouvrez le [portail Azure](https://portal.azure.com), puis accédez au App service *myWebApp \<unique_number\>* .
1. Dans le menu de gauche, faites défiler jusqu’à la section **surveillance** , puis sélectionnez **flux de journal**.

    ![Capture d’écran montrant le lien du flux de journal](./media/monitoring/log-stream.png)

Les journaux peuvent également être [diffusés en continu via Azure CLI ou Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), y compris via le Cloud Shell.

## <a name="alerts"></a>Alertes

Azure Monitor fournit également des [alertes en temps réel](/azure/monitoring-and-diagnostics/insights-alerts-portal) en fonction des métriques, des événements administratifs et d’autres critères.

> *Remarque : actuellement, les alertes sur les métriques de l’application Web sont uniquement disponibles dans le service alertes (Classic).*

Le [service alertes (Classic)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) se trouve dans Azure Monitor ou sous la section **surveillance** des paramètres App service.

![Lien alertes (classiques)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Débogage en direct

Les Azure App Service peuvent être [débogués à distance avec Visual Studio quand les](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) journaux ne fournissent pas suffisamment d’informations. Toutefois, le débogage distant requiert que l’application soit compilée avec des symboles de débogage. Le débogage ne doit pas être effectué en production, sauf en dernier recours.

## <a name="conclusion"></a>Conclusion

Dans cette section, vous avez effectué les tâches suivantes :

* Recherchez des données de surveillance et de dépannage de base dans le Portail Azure
* Découvrez comment Azure Monitor fournit un examen plus approfondi des métriques dans tous les services Azure
* Connecter l’application Web avec Application Insights pour le profilage d’application
* Activer la journalisation et savoir où télécharger les journaux
* Effectuer le streaming des journaux en temps réel
* En savoir plus sur la configuration des alertes
* En savoir plus sur le débogage à distance Azure App Service Web Apps.

## <a name="additional-reading"></a>Documentation supplémentaire

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Surveiller les performances de l’application web Azure avec Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Créer des alertes de métriques classiques dans Azure Monitor pour les services Azure - Portail Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)
