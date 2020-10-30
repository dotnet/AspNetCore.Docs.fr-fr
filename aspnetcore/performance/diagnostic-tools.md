---
title: Outils de diagnostic des performances
author: mjrousos
description: Outils utiles pour diagnostiquer les problèmes de performances dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060857"
---
# <a name="performance-diagnostic-tools"></a>Outils de diagnostic des performances

Par [Mike Rousos](https://github.com/mjrousos)

Cet article répertorie les outils permettant de diagnostiquer les problèmes de performances dans ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>Outils de diagnostic Visual Studio

Les [outils de profilage et de diagnostic](/visualstudio/profiling) intégrés à Visual Studio sont un bon point de départ pour examiner les problèmes de performances. Ces outils sont puissants et pratiques à utiliser à partir de l’environnement de développement Visual Studio. Les outils permettent d’analyser l’utilisation de l’UC, l’utilisation de la mémoire et les événements de performances dans les applications ASP.NET Core. En cours de création, le profilage est facile au moment du développement.

Des informations supplémentaires sont disponibles dans [la documentation de Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) fournit des données de performances approfondies pour votre application. Application Insights collecte automatiquement les données sur les taux de réponse, les taux d’échec, les temps de réponse des dépendances, etc. Application Insights prend en charge la journalisation des événements et des métriques personnalisés spécifiques à votre application.

Azure Application Insights offre plusieurs moyens d’obtenir des Insights sur les applications analysées :

- [Plan d’application](/azure/application-insights/app-insights-app-map) : permet de repérer les goulots d’étranglement de performances ou d’échec dans tous les composants des applications distribuées.
- [Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) est un composant du portail Microsoft Azure qui permet de tracer des graphiques, de corréler visuellement des tendances et d’examiner des pics et des creux dans des valeurs de métriques.
- Panneau [performances dans application Insights portail](/azure/application-insights/app-insights-tutorial-performance):

  - Affiche les détails des performances pour les différentes opérations de l’application analysée.
  - Permet le perçage dans une opération unique pour vérifier toutes les parties/dépendances qui contribuent à une longue durée.
  - Le profileur peut être appelé à partir d’ici pour collecter les traces de performances à la demande.

- [Azure application Insights Profiler](/azure/azure-monitor/app/profiler) permet le profilage normal et à la demande des applications .net.  Portail Azure affiche les traces de performances capturées avec les piles d’appels et les chemins d’accès à chaud. Les fichiers de suivi peuvent également être téléchargés pour une analyse plus poussée à l’aide de PerfView.

Application Insights peut être utilisé dans différents environnements :

- Optimisé pour fonctionner dans Azure.
- Fonctionne en production, développement et intermédiaire.
- Fonctionne localement à partir de [Visual Studio](/azure/application-insights/app-insights-visual-studio) ou dans d’autres environnements d’hébergement.

Pour plus d’informations, consultez [Application Insights pour ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) est un outil d’analyse des performances créé par l’équipe .net spécifiquement pour diagnostiquer les problèmes de performances de .net. PerfView permet l’analyse de l’utilisation du processeur, de la mémoire et du comportement du GC, des événements de performances et de l’heure de la paroi.

Vous pouvez en savoir plus sur PerfView et la prise en main des [didacticiels vidéo PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) ou en lisant le Guide de l’utilisateur disponible dans l’outil ou [sur GitHub](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows performance Toolkit](/windows-hardware/test/wpt/) (WPT) est constitué de deux composants : l’enregistreur de performances Windows (WPR) et l’analyseur de performances Windows (WPA). Les outils produisent des profils de performances détaillés des applications et des systèmes d’exploitation Windows. WPT offre des méthodes plus riches de visualisation des données, mais la collecte des données est moins puissante que celle de PerfView.

## <a name="perfcollect"></a>PerfCollect

Bien que PerfView soit un outil d’analyse des performances utile pour les scénarios .NET, il s’exécute uniquement sur Windows. vous ne pouvez donc pas l’utiliser pour collecter des traces à partir de ASP.NET Core applications qui s’exécutent dans des environnements Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) est un script bash qui utilise les outils de profilage Linux natifs ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) et [LTTng](https://lttng.org/)) pour collecter les traces sur Linux qui peuvent être analysées par PerfView. PerfCollect est utile lorsque les problèmes de performances s’affichent dans les environnements Linux où les PerfView ne peuvent pas être utilisés directement. Au lieu de cela, PerfCollect peut collecter des traces à partir d’applications .NET Core qui sont ensuite analysées sur un ordinateur Windows à l’aide de PerfView.

Vous trouverez plus d’informations sur l’installation et la prise en main de PerfCollect [sur GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Autres outils de performances tiers

La liste suivante répertorie certains outils de performances tiers qui sont utiles pour l’investigation des performances des applications .NET Core.

- [Mini](https://miniprofiler.com/)
- [dotTrace](https://www.jetbrains.com/profiler/) et [dotMemory](https://www.jetbrains.com/dotmemory/) à partir de [jetbrains](https://www.jetbrains.com/)
- [VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) à partir d’Intel
