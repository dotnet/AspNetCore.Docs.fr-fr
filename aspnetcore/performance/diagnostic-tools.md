---
title: Outils de diagnostic des performances
author: mjrousos
description: Outils utiles pour diagnostiquer les problèmes de performances dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060857"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="3a541-103">Outils de diagnostic des performances</span><span class="sxs-lookup"><span data-stu-id="3a541-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="3a541-104">Par [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="3a541-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="3a541-105">Cet article répertorie les outils permettant de diagnostiquer les problèmes de performances dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a541-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="3a541-106">Outils de diagnostic Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a541-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="3a541-107">Les [outils de profilage et de diagnostic](/visualstudio/profiling) intégrés à Visual Studio sont un bon point de départ pour examiner les problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="3a541-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="3a541-108">Ces outils sont puissants et pratiques à utiliser à partir de l’environnement de développement Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a541-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="3a541-109">Les outils permettent d’analyser l’utilisation de l’UC, l’utilisation de la mémoire et les événements de performances dans les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a541-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="3a541-110">En cours de création, le profilage est facile au moment du développement.</span><span class="sxs-lookup"><span data-stu-id="3a541-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="3a541-111">Des informations supplémentaires sont disponibles dans [la documentation de Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="3a541-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="3a541-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="3a541-112">Application Insights</span></span>

<span data-ttu-id="3a541-113">[Application Insights](/azure/application-insights/app-insights-overview) fournit des données de performances approfondies pour votre application.</span><span class="sxs-lookup"><span data-stu-id="3a541-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="3a541-114">Application Insights collecte automatiquement les données sur les taux de réponse, les taux d’échec, les temps de réponse des dépendances, etc.</span><span class="sxs-lookup"><span data-stu-id="3a541-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="3a541-115">Application Insights prend en charge la journalisation des événements et des métriques personnalisés spécifiques à votre application.</span><span class="sxs-lookup"><span data-stu-id="3a541-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="3a541-116">Azure Application Insights offre plusieurs moyens d’obtenir des Insights sur les applications analysées :</span><span class="sxs-lookup"><span data-stu-id="3a541-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="3a541-117">[Plan d’application](/azure/application-insights/app-insights-app-map) : permet de repérer les goulots d’étranglement de performances ou d’échec dans tous les composants des applications distribuées.</span><span class="sxs-lookup"><span data-stu-id="3a541-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="3a541-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) est un composant du portail Microsoft Azure qui permet de tracer des graphiques, de corréler visuellement des tendances et d’examiner des pics et des creux dans des valeurs de métriques.</span><span class="sxs-lookup"><span data-stu-id="3a541-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="3a541-119">Panneau [performances dans application Insights portail](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="3a541-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="3a541-120">Affiche les détails des performances pour les différentes opérations de l’application analysée.</span><span class="sxs-lookup"><span data-stu-id="3a541-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="3a541-121">Permet le perçage dans une opération unique pour vérifier toutes les parties/dépendances qui contribuent à une longue durée.</span><span class="sxs-lookup"><span data-stu-id="3a541-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="3a541-122">Le profileur peut être appelé à partir d’ici pour collecter les traces de performances à la demande.</span><span class="sxs-lookup"><span data-stu-id="3a541-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="3a541-123">[Azure application Insights Profiler](/azure/azure-monitor/app/profiler) permet le profilage normal et à la demande des applications .net.</span><span class="sxs-lookup"><span data-stu-id="3a541-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="3a541-124">Portail Azure affiche les traces de performances capturées avec les piles d’appels et les chemins d’accès à chaud.</span><span class="sxs-lookup"><span data-stu-id="3a541-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="3a541-125">Les fichiers de suivi peuvent également être téléchargés pour une analyse plus poussée à l’aide de PerfView.</span><span class="sxs-lookup"><span data-stu-id="3a541-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="3a541-126">Application Insights peut être utilisé dans différents environnements :</span><span class="sxs-lookup"><span data-stu-id="3a541-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="3a541-127">Optimisé pour fonctionner dans Azure.</span><span class="sxs-lookup"><span data-stu-id="3a541-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="3a541-128">Fonctionne en production, développement et intermédiaire.</span><span class="sxs-lookup"><span data-stu-id="3a541-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="3a541-129">Fonctionne localement à partir de [Visual Studio](/azure/application-insights/app-insights-visual-studio) ou dans d’autres environnements d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="3a541-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="3a541-130">Pour plus d’informations, consultez [Application Insights pour ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="3a541-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="3a541-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="3a541-131">PerfView</span></span>

<span data-ttu-id="3a541-132">[PerfView](https://github.com/Microsoft/perfview) est un outil d’analyse des performances créé par l’équipe .net spécifiquement pour diagnostiquer les problèmes de performances de .net.</span><span class="sxs-lookup"><span data-stu-id="3a541-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="3a541-133">PerfView permet l’analyse de l’utilisation du processeur, de la mémoire et du comportement du GC, des événements de performances et de l’heure de la paroi.</span><span class="sxs-lookup"><span data-stu-id="3a541-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="3a541-134">Vous pouvez en savoir plus sur PerfView et la prise en main des [didacticiels vidéo PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) ou en lisant le Guide de l’utilisateur disponible dans l’outil ou [sur GitHub](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="3a541-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="3a541-135">Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="3a541-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="3a541-136">[Windows performance Toolkit](/windows-hardware/test/wpt/) (WPT) est constitué de deux composants : l’enregistreur de performances Windows (WPR) et l’analyseur de performances Windows (WPA).</span><span class="sxs-lookup"><span data-stu-id="3a541-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="3a541-137">Les outils produisent des profils de performances détaillés des applications et des systèmes d’exploitation Windows.</span><span class="sxs-lookup"><span data-stu-id="3a541-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="3a541-138">WPT offre des méthodes plus riches de visualisation des données, mais la collecte des données est moins puissante que celle de PerfView.</span><span class="sxs-lookup"><span data-stu-id="3a541-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="3a541-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="3a541-139">PerfCollect</span></span>

<span data-ttu-id="3a541-140">Bien que PerfView soit un outil d’analyse des performances utile pour les scénarios .NET, il s’exécute uniquement sur Windows. vous ne pouvez donc pas l’utiliser pour collecter des traces à partir de ASP.NET Core applications qui s’exécutent dans des environnements Linux.</span><span class="sxs-lookup"><span data-stu-id="3a541-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="3a541-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) est un script bash qui utilise les outils de profilage Linux natifs ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) et [LTTng](https://lttng.org/)) pour collecter les traces sur Linux qui peuvent être analysées par PerfView.</span><span class="sxs-lookup"><span data-stu-id="3a541-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="3a541-142">PerfCollect est utile lorsque les problèmes de performances s’affichent dans les environnements Linux où les PerfView ne peuvent pas être utilisés directement.</span><span class="sxs-lookup"><span data-stu-id="3a541-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="3a541-143">Au lieu de cela, PerfCollect peut collecter des traces à partir d’applications .NET Core qui sont ensuite analysées sur un ordinateur Windows à l’aide de PerfView.</span><span class="sxs-lookup"><span data-stu-id="3a541-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="3a541-144">Vous trouverez plus d’informations sur l’installation et la prise en main de PerfCollect [sur GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="3a541-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="3a541-145">Autres outils de performances tiers</span><span class="sxs-lookup"><span data-stu-id="3a541-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="3a541-146">La liste suivante répertorie certains outils de performances tiers qui sont utiles pour l’investigation des performances des applications .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a541-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="3a541-147">Mini</span><span class="sxs-lookup"><span data-stu-id="3a541-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="3a541-148">[dotTrace](https://www.jetbrains.com/profiler/) et [dotMemory](https://www.jetbrains.com/dotmemory/) à partir de [jetbrains](https://www.jetbrains.com/)</span><span class="sxs-lookup"><span data-stu-id="3a541-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="3a541-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) à partir d’Intel</span><span class="sxs-lookup"><span data-stu-id="3a541-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
