---
title: Gestion de la mémoire et modèles dans ASP.NET Core
author: rick-anderson
description: Découvrez comment fonctionne la gestion de la mémoire dans ASP.NET Core et comment le garbage collector (GC) fonctionne.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: 6d2a89ec7c64728bc585ad235293f2277f9a66f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061481"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="28ef9-103">Gestion de la mémoire et garbage collection (GC) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28ef9-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="28ef9-104">Par [Sébastien Ros](https://github.com/sebastienros) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="28ef9-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="28ef9-105">La gestion de la mémoire est complexe, même dans une infrastructure gérée telle que .NET.</span><span class="sxs-lookup"><span data-stu-id="28ef9-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="28ef9-106">L’analyse et la compréhension des problèmes de mémoire peuvent être difficiles.</span><span class="sxs-lookup"><span data-stu-id="28ef9-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="28ef9-107">Cet article :</span><span class="sxs-lookup"><span data-stu-id="28ef9-107">This article:</span></span>

* <span data-ttu-id="28ef9-108">A été motivée par de nombreuses *fuites de mémoire* et les problèmes de *GC ne fonctionnaient pas* .</span><span class="sxs-lookup"><span data-stu-id="28ef9-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="28ef9-109">La plupart de ces problèmes ont été provoqués par le fait de ne pas comprendre comment fonctionne la consommation de mémoire dans .NET Core, ou de ne pas comprendre comment elle est mesurée.</span><span class="sxs-lookup"><span data-stu-id="28ef9-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="28ef9-110">Illustre l’utilisation de la mémoire problématique et suggère d’autres approches.</span><span class="sxs-lookup"><span data-stu-id="28ef9-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="28ef9-111">Fonctionnement d’garbage collection (GC) dans .NET Core</span><span class="sxs-lookup"><span data-stu-id="28ef9-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="28ef9-112">Le garbage collector alloue des segments de tas où chaque segment est une plage contiguë de mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="28ef9-113">Les objets placés dans le tas sont classés en trois générations : 0, 1 ou 2.</span><span class="sxs-lookup"><span data-stu-id="28ef9-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="28ef9-114">La génération détermine la fréquence à laquelle le garbage collector tente de libérer de la mémoire sur les objets gérés qui ne sont plus référencés par l’application.</span><span class="sxs-lookup"><span data-stu-id="28ef9-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="28ef9-115">Les générations numérotées inférieures sont plus fréquentes.</span><span class="sxs-lookup"><span data-stu-id="28ef9-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="28ef9-116">Les objets sont déplacés d’une génération à l’autre en fonction de leur durée de vie.</span><span class="sxs-lookup"><span data-stu-id="28ef9-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="28ef9-117">À mesure que les objets vivent plus longtemps, ils sont déplacés vers une génération plus élevée.</span><span class="sxs-lookup"><span data-stu-id="28ef9-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="28ef9-118">Comme mentionné précédemment, les générations plus élevées sont moins souvent GC.</span><span class="sxs-lookup"><span data-stu-id="28ef9-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="28ef9-119">Les objets éphémères à courte durée restent toujours dans la génération 0.</span><span class="sxs-lookup"><span data-stu-id="28ef9-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="28ef9-120">Par exemple, les objets qui sont référencés pendant la durée de vie d’une requête Web sont à courte durée de vie.</span><span class="sxs-lookup"><span data-stu-id="28ef9-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="28ef9-121">Les [singletons](xref:fundamentals/dependency-injection#service-lifetimes) au niveau de l’application migrent généralement vers la génération 2.</span><span class="sxs-lookup"><span data-stu-id="28ef9-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="28ef9-122">Lors du démarrage d’une application ASP.NET Core, le garbage collector :</span><span class="sxs-lookup"><span data-stu-id="28ef9-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="28ef9-123">Réserve de la mémoire pour les segments de tas initiaux.</span><span class="sxs-lookup"><span data-stu-id="28ef9-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="28ef9-124">Valide une petite partie de la mémoire lors du chargement du Runtime.</span><span class="sxs-lookup"><span data-stu-id="28ef9-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="28ef9-125">Les allocations de mémoire précédentes sont effectuées pour des raisons de performances.</span><span class="sxs-lookup"><span data-stu-id="28ef9-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="28ef9-126">L’avantage en termes de performances provient des segments de segment de mémoire contigus.</span><span class="sxs-lookup"><span data-stu-id="28ef9-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="28ef9-127">Appelez GC. Rassembler</span><span class="sxs-lookup"><span data-stu-id="28ef9-127">Call GC.Collect</span></span>

<span data-ttu-id="28ef9-128">Appel de [gc. Collecter](xref:System.GC.Collect*) explicitement :</span><span class="sxs-lookup"><span data-stu-id="28ef9-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="28ef9-129">**Ne doivent pas** être effectuées par les applications de production ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="28ef9-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="28ef9-130">Est utile lors de l’examen des fuites de mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="28ef9-131">Lors de l’examen, vérifie que le GC a supprimé tous les objets en suspens de la mémoire afin que la mémoire puisse être mesurée.</span><span class="sxs-lookup"><span data-stu-id="28ef9-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="28ef9-132">Analyse de l’utilisation de la mémoire d’une application</span><span class="sxs-lookup"><span data-stu-id="28ef9-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="28ef9-133">Les outils dédiés peuvent vous aider à analyser l’utilisation de la mémoire :</span><span class="sxs-lookup"><span data-stu-id="28ef9-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="28ef9-134">Comptage des références d’objets</span><span class="sxs-lookup"><span data-stu-id="28ef9-134">Counting object references</span></span>
- <span data-ttu-id="28ef9-135">Mesure de l’impact du GC sur l’utilisation du processeur</span><span class="sxs-lookup"><span data-stu-id="28ef9-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="28ef9-136">Mesure de l’espace mémoire utilisé pour chaque génération</span><span class="sxs-lookup"><span data-stu-id="28ef9-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="28ef9-137">Utilisez les outils suivants pour analyser l’utilisation de la mémoire :</span><span class="sxs-lookup"><span data-stu-id="28ef9-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="28ef9-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): peut être utilisé sur des ordinateurs de production.</span><span class="sxs-lookup"><span data-stu-id="28ef9-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="28ef9-139">Analyser l’utilisation de la mémoire sans le débogueur Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28ef9-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="28ef9-140">Profiler l’utilisation de la mémoire dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28ef9-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="28ef9-141">Détection des problèmes de mémoire</span><span class="sxs-lookup"><span data-stu-id="28ef9-141">Detecting memory issues</span></span>

<span data-ttu-id="28ef9-142">Le gestionnaire des tâches peut être utilisé pour avoir une idée de la quantité de mémoire utilisée par une application ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="28ef9-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="28ef9-143">Valeur de la mémoire du gestionnaire des tâches :</span><span class="sxs-lookup"><span data-stu-id="28ef9-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="28ef9-144">Représente la quantité de mémoire utilisée par le processus ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="28ef9-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="28ef9-145">Comprend les objets vivants et autres consommateurs de mémoire de l’application, tels que l’utilisation de la mémoire native.</span><span class="sxs-lookup"><span data-stu-id="28ef9-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="28ef9-146">Si la valeur de la mémoire du gestionnaire des tâches augmente indéfiniment et n’est jamais aplatie, l’application présente une fuite de mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="28ef9-147">Les sections suivantes montrent et expliquent plusieurs modèles d’utilisation de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="28ef9-148">Exemple d’application de l’utilisation de la mémoire</span><span class="sxs-lookup"><span data-stu-id="28ef9-148">Sample display memory usage app</span></span>

<span data-ttu-id="28ef9-149">L' [exemple d’application MemoryLeak](https://github.com/sebastienros/memoryleak) est disponible sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="28ef9-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="28ef9-150">L’application MemoryLeak :</span><span class="sxs-lookup"><span data-stu-id="28ef9-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="28ef9-151">Comprend un contrôleur de diagnostic qui collecte la mémoire en temps réel et les données de catalogue global pour l’application.</span><span class="sxs-lookup"><span data-stu-id="28ef9-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="28ef9-152">Contient une page d’index qui affiche la mémoire et les données de catalogue global.</span><span class="sxs-lookup"><span data-stu-id="28ef9-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="28ef9-153">La page d’index est actualisée chaque seconde.</span><span class="sxs-lookup"><span data-stu-id="28ef9-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="28ef9-154">Contient un contrôleur d’API qui fournit divers modèles de charge de mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="28ef9-155">N’est pas un outil pris en charge, mais il peut être utilisé pour afficher les modèles d’utilisation de la mémoire des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28ef9-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="28ef9-156">Exécutez MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="28ef9-156">Run MemoryLeak.</span></span> <span data-ttu-id="28ef9-157">La mémoire allouée augmente lentement jusqu’à ce qu’un GC se produise.</span><span class="sxs-lookup"><span data-stu-id="28ef9-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="28ef9-158">La mémoire augmente, car l’outil alloue un objet personnalisé pour capturer les données.</span><span class="sxs-lookup"><span data-stu-id="28ef9-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="28ef9-159">L’illustration suivante montre la page d’index MemoryLeak lorsqu’un GC de génération 0 se produit.</span><span class="sxs-lookup"><span data-stu-id="28ef9-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="28ef9-160">Le graphique affiche 0 RPS (demandes par seconde) car aucun point de terminaison d’API du contrôleur d’API n’a été appelé.</span><span class="sxs-lookup"><span data-stu-id="28ef9-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![graphique précédent](memory/_static/0RPS.png)

<span data-ttu-id="28ef9-162">Le graphique affiche deux valeurs pour l’utilisation de la mémoire :</span><span class="sxs-lookup"><span data-stu-id="28ef9-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="28ef9-163">Allouée : quantité de mémoire occupée par les objets managés</span><span class="sxs-lookup"><span data-stu-id="28ef9-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="28ef9-164">Plage de [travail](/windows/win32/memory/working-set): ensemble de pages dans l’espace d’adressage virtuel du processus qui résident actuellement dans la mémoire physique.</span><span class="sxs-lookup"><span data-stu-id="28ef9-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="28ef9-165">La plage de travail affichée correspond à la même valeur que celle affichée par le gestionnaire des tâches.</span><span class="sxs-lookup"><span data-stu-id="28ef9-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="28ef9-166">Objets temporaires</span><span class="sxs-lookup"><span data-stu-id="28ef9-166">Transient objects</span></span>

<span data-ttu-id="28ef9-167">L’API suivante crée une instance de chaîne de 10 Ko et la retourne au client.</span><span class="sxs-lookup"><span data-stu-id="28ef9-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="28ef9-168">À chaque demande, un nouvel objet est alloué en mémoire et écrit dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="28ef9-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="28ef9-169">Les chaînes sont stockées en tant que caractères UTF-16 dans .NET, de sorte que chaque caractère occupe 2 octets en mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="28ef9-170">Le graphique suivant est généré avec une charge relativement faible dans pour montrer comment les allocations de mémoire sont affectées par le GC.</span><span class="sxs-lookup"><span data-stu-id="28ef9-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![graphique précédent](memory/_static/bigstring.png)

<span data-ttu-id="28ef9-172">Le graphique précédent montre les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="28ef9-172">The preceding chart shows:</span></span>

* <span data-ttu-id="28ef9-173">4 Ko (requêtes par seconde).</span><span class="sxs-lookup"><span data-stu-id="28ef9-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="28ef9-174">Les collections GC de génération 0 se produisent toutes les deux secondes.</span><span class="sxs-lookup"><span data-stu-id="28ef9-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="28ef9-175">La plage de travail est constante à environ 500 Mo.</span><span class="sxs-lookup"><span data-stu-id="28ef9-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="28ef9-176">L’UC est de 12%.</span><span class="sxs-lookup"><span data-stu-id="28ef9-176">CPU is 12%.</span></span>
* <span data-ttu-id="28ef9-177">La consommation et la libération de la mémoire (via GC) sont stables.</span><span class="sxs-lookup"><span data-stu-id="28ef9-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="28ef9-178">Le graphique suivant est pris au débit maximal qui peut être géré par l’ordinateur.</span><span class="sxs-lookup"><span data-stu-id="28ef9-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![graphique précédent](memory/_static/bigstring2.png)

<span data-ttu-id="28ef9-180">Le graphique précédent montre les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="28ef9-180">The preceding chart shows:</span></span>

* <span data-ttu-id="28ef9-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="28ef9-181">22K RPS</span></span>
* <span data-ttu-id="28ef9-182">Les collections GC de génération 0 se produisent plusieurs fois par seconde.</span><span class="sxs-lookup"><span data-stu-id="28ef9-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="28ef9-183">Les collections de génération 1 sont déclenchées, car l’application a alloué beaucoup plus de mémoire par seconde.</span><span class="sxs-lookup"><span data-stu-id="28ef9-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="28ef9-184">La plage de travail est constante à environ 500 Mo.</span><span class="sxs-lookup"><span data-stu-id="28ef9-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="28ef9-185">L’UC est de 33%.</span><span class="sxs-lookup"><span data-stu-id="28ef9-185">CPU is 33%.</span></span>
* <span data-ttu-id="28ef9-186">La consommation et la libération de la mémoire (via GC) sont stables.</span><span class="sxs-lookup"><span data-stu-id="28ef9-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="28ef9-187">UC (33%) n’est pas surexploitée, par conséquent le garbage collection peut suivre un nombre élevé d’allocations.</span><span class="sxs-lookup"><span data-stu-id="28ef9-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="28ef9-188">GC de station de travail et GC de serveur</span><span class="sxs-lookup"><span data-stu-id="28ef9-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="28ef9-189">Le garbage collector .NET a deux modes différents :</span><span class="sxs-lookup"><span data-stu-id="28ef9-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="28ef9-190">**GC de station de travail** : optimisé pour le bureau.</span><span class="sxs-lookup"><span data-stu-id="28ef9-190">**Workstation GC** : Optimized for the desktop.</span></span>
* <span data-ttu-id="28ef9-191">**GC du serveur** .</span><span class="sxs-lookup"><span data-stu-id="28ef9-191">**Server GC** .</span></span> <span data-ttu-id="28ef9-192">GC par défaut pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28ef9-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="28ef9-193">Optimisé pour le serveur.</span><span class="sxs-lookup"><span data-stu-id="28ef9-193">Optimized for the server.</span></span>

<span data-ttu-id="28ef9-194">Le mode GC peut être défini explicitement dans le fichier projet ou dans le *runtimeconfig.js* fichier de l’application publiée.</span><span class="sxs-lookup"><span data-stu-id="28ef9-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="28ef9-195">Le balisage suivant illustre la définition `ServerGarbageCollection` dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="28ef9-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="28ef9-196">La modification du `ServerGarbageCollection` fichier projet nécessite que l’application soit reconstruite.</span><span class="sxs-lookup"><span data-stu-id="28ef9-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="28ef9-197">**Remarque :** Le garbage collection serveur n’est **pas** disponible sur les machines avec un seul cœur.</span><span class="sxs-lookup"><span data-stu-id="28ef9-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="28ef9-198">Pour plus d'informations, consultez <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="28ef9-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="28ef9-199">L’illustration suivante montre le profil de mémoire sous un RPS de 5 Ko à l’aide du GC de station de travail.</span><span class="sxs-lookup"><span data-stu-id="28ef9-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![graphique précédent](memory/_static/workstation.png)

<span data-ttu-id="28ef9-201">Les différences entre ce graphique et la version du serveur sont significatives :</span><span class="sxs-lookup"><span data-stu-id="28ef9-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="28ef9-202">La plage de travail passe de 500 Mo à 70 Mo.</span><span class="sxs-lookup"><span data-stu-id="28ef9-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="28ef9-203">Le GC effectue des collections de génération 0 plusieurs fois par seconde, plutôt que toutes les deux secondes.</span><span class="sxs-lookup"><span data-stu-id="28ef9-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="28ef9-204">GC passe de 300 Mo à 10 Mo.</span><span class="sxs-lookup"><span data-stu-id="28ef9-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="28ef9-205">Dans un environnement de serveur Web classique, l’utilisation du processeur est plus importante que la mémoire, par conséquent, le garbage collector du serveur est préférable.</span><span class="sxs-lookup"><span data-stu-id="28ef9-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="28ef9-206">Si l’utilisation de la mémoire est élevée et que l’utilisation du processeur est relativement faible, le garbage collector de la station de travail peut être plus performant.</span><span class="sxs-lookup"><span data-stu-id="28ef9-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="28ef9-207">Par exemple, une haute densité hébergeant plusieurs applications Web où la mémoire est rare.</span><span class="sxs-lookup"><span data-stu-id="28ef9-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="28ef9-208">GC avec l’ancrage et les petits conteneurs</span><span class="sxs-lookup"><span data-stu-id="28ef9-208">GC using Docker and small containers</span></span>

<span data-ttu-id="28ef9-209">Lorsque plusieurs applications en conteneur s’exécutent sur un ordinateur, le garbage collector de station de travail peut être plus conforme que le GC de serveur.</span><span class="sxs-lookup"><span data-stu-id="28ef9-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="28ef9-210">Pour plus d’informations, consultez [exécution avec le garbage collection de serveur dans un petit conteneur](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) et [exécution avec le garbage collection de serveur dans un petit scénario de conteneur, partie 1 : limite inconditionnelle pour le tas GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="28ef9-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="28ef9-211">Références d’objets persistants</span><span class="sxs-lookup"><span data-stu-id="28ef9-211">Persistent object references</span></span>

<span data-ttu-id="28ef9-212">Le GC ne peut pas libérer des objets référencés.</span><span class="sxs-lookup"><span data-stu-id="28ef9-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="28ef9-213">Les objets référencés, mais qui ne sont plus nécessaires, provoquent une fuite de mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="28ef9-214">Si l’application alloue fréquemment des objets et ne parvient pas à les libérer une fois qu’ils ne sont plus nécessaires, l’utilisation de la mémoire augmente au fil du temps.</span><span class="sxs-lookup"><span data-stu-id="28ef9-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="28ef9-215">L’API suivante crée une instance de chaîne de 10 Ko et la retourne au client.</span><span class="sxs-lookup"><span data-stu-id="28ef9-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="28ef9-216">La différence avec l’exemple précédent est que cette instance est référencée par un membre statique, ce qui signifie qu’elle n’est jamais disponible pour la collection.</span><span class="sxs-lookup"><span data-stu-id="28ef9-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="28ef9-217">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="28ef9-217">The preceding code:</span></span>

* <span data-ttu-id="28ef9-218">Est un exemple de fuite de mémoire classique.</span><span class="sxs-lookup"><span data-stu-id="28ef9-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="28ef9-219">Avec des appels fréquents, provoque l’augmentation de la mémoire de l’application jusqu’à ce que le processus se bloque avec une `OutOfMemory` exception.</span><span class="sxs-lookup"><span data-stu-id="28ef9-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![graphique précédent](memory/_static/eternal.png)

<span data-ttu-id="28ef9-221">Dans l’image précédente :</span><span class="sxs-lookup"><span data-stu-id="28ef9-221">In the preceding image:</span></span>

* <span data-ttu-id="28ef9-222">Le test de charge du `/api/staticstring` point de terminaison provoque une augmentation linéaire de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="28ef9-223">Le GC tente de libérer de la mémoire à mesure que la sollicitation de la mémoire augmente, en appelant une collection de génération 2.</span><span class="sxs-lookup"><span data-stu-id="28ef9-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="28ef9-224">Le GC ne peut pas libérer la mémoire perdue.</span><span class="sxs-lookup"><span data-stu-id="28ef9-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="28ef9-225">Allouée et plage de travail augmentent avec le temps.</span><span class="sxs-lookup"><span data-stu-id="28ef9-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="28ef9-226">Certains scénarios, tels que la mise en cache, requièrent que les références d’objets soient maintenues jusqu’à ce que la sollicitation de la mémoire les force à libérer.</span><span class="sxs-lookup"><span data-stu-id="28ef9-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="28ef9-227">La <xref:System.WeakReference> classe peut être utilisée pour ce type de code de mise en cache.</span><span class="sxs-lookup"><span data-stu-id="28ef9-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="28ef9-228">Un `WeakReference` objet est collecté en conditions de mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="28ef9-229">L’implémentation par défaut de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> utilise `WeakReference` .</span><span class="sxs-lookup"><span data-stu-id="28ef9-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="28ef9-230">Mémoire Native</span><span class="sxs-lookup"><span data-stu-id="28ef9-230">Native memory</span></span>

<span data-ttu-id="28ef9-231">Certains objets .NET Core s’appuient sur la mémoire native.</span><span class="sxs-lookup"><span data-stu-id="28ef9-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="28ef9-232">La mémoire native **ne peut pas** être collectée par le gc.</span><span class="sxs-lookup"><span data-stu-id="28ef9-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="28ef9-233">L’objet .NET utilisant la mémoire Native doit le libérer à l’aide du code natif.</span><span class="sxs-lookup"><span data-stu-id="28ef9-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="28ef9-234">.NET fournit l' <xref:System.IDisposable> interface pour permettre aux développeurs de libérer de la mémoire native.</span><span class="sxs-lookup"><span data-stu-id="28ef9-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="28ef9-235">Même si <xref:System.IDisposable.Dispose*> n’est pas appelé, les classes correctement implémentées appellent `Dispose` lorsque le [finaliseur](/dotnet/csharp/programming-guide/classes-and-structs/destructors) s’exécute.</span><span class="sxs-lookup"><span data-stu-id="28ef9-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="28ef9-236">Considérez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="28ef9-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="28ef9-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) étant une classe managée, toute instance est collectée à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="28ef9-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="28ef9-238">L’illustration suivante montre le profil de mémoire lors de l’appel de l' `fileprovider` API en continu.</span><span class="sxs-lookup"><span data-stu-id="28ef9-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![graphique précédent](memory/_static/fileprovider.png)

<span data-ttu-id="28ef9-240">Le graphique précédent présente un problème évident avec l’implémentation de cette classe, car elle continue d’améliorer l’utilisation de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="28ef9-241">Il s’agit d’un problème connu qui fait l’objet d’un suivi dans [ce numéro](https://github.com/dotnet/aspnetcore/issues/3110).</span><span class="sxs-lookup"><span data-stu-id="28ef9-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="28ef9-242">La même fuite peut se produire dans le code utilisateur, par l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="28ef9-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="28ef9-243">Impossible de libérer la classe correctement.</span><span class="sxs-lookup"><span data-stu-id="28ef9-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="28ef9-244">Oublier d’appeler la `Dispose` méthode des objets dépendants qui doivent être supprimés.</span><span class="sxs-lookup"><span data-stu-id="28ef9-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="28ef9-245">Tas d’objets volumineux</span><span class="sxs-lookup"><span data-stu-id="28ef9-245">Large objects heap</span></span>

<span data-ttu-id="28ef9-246">Les cycles d’allocation de mémoire/libre fréquents peuvent fragmenter la mémoire, en particulier lors de l’allocation de gros blocs de mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="28ef9-247">Les objets sont alloués dans des blocs de mémoire contigus.</span><span class="sxs-lookup"><span data-stu-id="28ef9-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="28ef9-248">Pour limiter la fragmentation, lorsque le garbage collector libère de la mémoire, il tente de le défragmenter.</span><span class="sxs-lookup"><span data-stu-id="28ef9-248">To mitigate fragmentation, when the GC frees memory, it tries to defragment it.</span></span> <span data-ttu-id="28ef9-249">Ce processus est appelé **compactage** .</span><span class="sxs-lookup"><span data-stu-id="28ef9-249">This process is called **compaction** .</span></span> <span data-ttu-id="28ef9-250">Le compactage implique le déplacement d’objets.</span><span class="sxs-lookup"><span data-stu-id="28ef9-250">Compaction involves moving objects.</span></span> <span data-ttu-id="28ef9-251">Le déplacement d’objets volumineux impose une baisse des performances.</span><span class="sxs-lookup"><span data-stu-id="28ef9-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="28ef9-252">Pour cette raison, le GC crée une zone de mémoire spéciale pour les objets _volumineux_ , appelée tas d’objets [volumineux](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="28ef9-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="28ef9-253">Les objets dont la taille est supérieure à 85 000 octets (environ 83 Ko) sont :</span><span class="sxs-lookup"><span data-stu-id="28ef9-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="28ef9-254">Placé sur le LOH.</span><span class="sxs-lookup"><span data-stu-id="28ef9-254">Placed on the LOH.</span></span>
* <span data-ttu-id="28ef9-255">Non compacté.</span><span class="sxs-lookup"><span data-stu-id="28ef9-255">Not compacted.</span></span>
* <span data-ttu-id="28ef9-256">Collecté au cours de la génération 2 gc.</span><span class="sxs-lookup"><span data-stu-id="28ef9-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="28ef9-257">Lorsque le LOH est plein, le GC déclenche une collection de génération 2.</span><span class="sxs-lookup"><span data-stu-id="28ef9-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="28ef9-258">Collections de génération 2 :</span><span class="sxs-lookup"><span data-stu-id="28ef9-258">Generation 2 collections:</span></span>

* <span data-ttu-id="28ef9-259">Sont par nature lentes.</span><span class="sxs-lookup"><span data-stu-id="28ef9-259">Are inherently slow.</span></span>
* <span data-ttu-id="28ef9-260">Surchargez également le coût du déclenchement d’une collection sur toutes les autres générations.</span><span class="sxs-lookup"><span data-stu-id="28ef9-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="28ef9-261">Le code suivant compacte le LOH immédiatement :</span><span class="sxs-lookup"><span data-stu-id="28ef9-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="28ef9-262"><xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>Pour plus d’informations sur le compactage du Loh, consultez.</span><span class="sxs-lookup"><span data-stu-id="28ef9-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="28ef9-263">Dans les conteneurs à l’aide de .NET Core 3,0 et versions ultérieures, le LOH est automatiquement compacté.</span><span class="sxs-lookup"><span data-stu-id="28ef9-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="28ef9-264">L’API suivante illustre ce comportement :</span><span class="sxs-lookup"><span data-stu-id="28ef9-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="28ef9-265">Le graphique suivant montre le profil de mémoire de l’appel du `/api/loh/84975` point de terminaison, sous charge maximale :</span><span class="sxs-lookup"><span data-stu-id="28ef9-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![graphique précédent](memory/_static/loh1.png)

<span data-ttu-id="28ef9-267">Le graphique suivant montre le profil de mémoire de l’appel du `/api/loh/84976` point de terminaison, en allouant *juste un octet de plus* :</span><span class="sxs-lookup"><span data-stu-id="28ef9-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte* :</span></span>

![graphique précédent](memory/_static/loh2.png)

<span data-ttu-id="28ef9-269">Remarque : la `byte[]` structure contient des octets de surcharge.</span><span class="sxs-lookup"><span data-stu-id="28ef9-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="28ef9-270">C’est pourquoi 84 976 octets déclenche la limite de 85 000.</span><span class="sxs-lookup"><span data-stu-id="28ef9-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="28ef9-271">Comparaison des deux graphiques précédents :</span><span class="sxs-lookup"><span data-stu-id="28ef9-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="28ef9-272">La plage de travail est similaire pour les deux scénarios, environ 450 Mo.</span><span class="sxs-lookup"><span data-stu-id="28ef9-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="28ef9-273">Les requêtes sous LOH (84 975 octets) affichent principalement des collections de génération 0.</span><span class="sxs-lookup"><span data-stu-id="28ef9-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="28ef9-274">Les requêtes sur LOH génèrent des collections de génération constante 2.</span><span class="sxs-lookup"><span data-stu-id="28ef9-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="28ef9-275">Les collections de génération 2 sont coûteuses.</span><span class="sxs-lookup"><span data-stu-id="28ef9-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="28ef9-276">Une plus grande UC est requise et le débit chute presque 50%.</span><span class="sxs-lookup"><span data-stu-id="28ef9-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="28ef9-277">Les objets volumineux temporaires sont particulièrement problématiques, car ils génèrent des catalogues globaux Gen.</span><span class="sxs-lookup"><span data-stu-id="28ef9-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="28ef9-278">Pour des performances optimales, l’utilisation d’objets volumineux doit être réduite.</span><span class="sxs-lookup"><span data-stu-id="28ef9-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="28ef9-279">Si possible, fractionnez les objets volumineux.</span><span class="sxs-lookup"><span data-stu-id="28ef9-279">If possible, split up large objects.</span></span> <span data-ttu-id="28ef9-280">Par exemple, l’intergiciel (middleware) de [mise en cache des réponses](xref:performance/caching/response) dans ASP.net Core fractionner les entrées de cache en blocs d’une taille inférieure à 85 000 octets.</span><span class="sxs-lookup"><span data-stu-id="28ef9-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="28ef9-281">Les liens suivants présentent l’approche ASP.NET Core pour la conservation des objets dans le cadre de la limite LOH :</span><span class="sxs-lookup"><span data-stu-id="28ef9-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="28ef9-282">ResponseCaching/Streams/StreamUtilities. cs</span><span class="sxs-lookup"><span data-stu-id="28ef9-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="28ef9-283">ResponseCaching/MemoryResponseCache. cs</span><span class="sxs-lookup"><span data-stu-id="28ef9-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="28ef9-284">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="28ef9-284">For more information, see:</span></span>

* [<span data-ttu-id="28ef9-285">Segment de mémoire Large Object non couvert</span><span class="sxs-lookup"><span data-stu-id="28ef9-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="28ef9-286">Tas d’objets volumineux</span><span class="sxs-lookup"><span data-stu-id="28ef9-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="28ef9-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="28ef9-287">HttpClient</span></span>

<span data-ttu-id="28ef9-288">Une utilisation incorrecte <xref:System.Net.Http.HttpClient> de peut entraîner une fuite de ressources.</span><span class="sxs-lookup"><span data-stu-id="28ef9-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="28ef9-289">Les ressources système, telles que les connexions de base de données, les sockets, les handles de fichiers, etc. :</span><span class="sxs-lookup"><span data-stu-id="28ef9-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="28ef9-290">Sont plus rares que la mémoire.</span><span class="sxs-lookup"><span data-stu-id="28ef9-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="28ef9-291">Sont plus problématiques quand la mémoire est perdue.</span><span class="sxs-lookup"><span data-stu-id="28ef9-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="28ef9-292">Les développeurs .NET expérimentés savent appeler <xref:System.IDisposable.Dispose*> sur les objets qui implémentent <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="28ef9-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="28ef9-293">Ne pas supprimer des objets qui implémentent `IDisposable` en général entraîne une fuite de mémoire ou une fuite de ressources système.</span><span class="sxs-lookup"><span data-stu-id="28ef9-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="28ef9-294">`HttpClient` implémente `IDisposable` , mais ne doit **pas** être supprimé à chaque appel.</span><span class="sxs-lookup"><span data-stu-id="28ef9-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="28ef9-295">Au lieu de cela, `HttpClient` doit être réutilisé.</span><span class="sxs-lookup"><span data-stu-id="28ef9-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="28ef9-296">Le point de terminaison suivant crée et supprime une nouvelle  `HttpClient` instance à chaque demande :</span><span class="sxs-lookup"><span data-stu-id="28ef9-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="28ef9-297">En cours de chargement, les messages d’erreur suivants sont enregistrés :</span><span class="sxs-lookup"><span data-stu-id="28ef9-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="28ef9-298">Même si les `HttpClient` instances sont supprimées, la connexion réseau réelle prend un certain temps pour être libérée par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="28ef9-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="28ef9-299">En créant continuellement de nouvelles connexions, l' _épuisement des ports_ se produit.</span><span class="sxs-lookup"><span data-stu-id="28ef9-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="28ef9-300">Chaque connexion cliente requiert son propre port client.</span><span class="sxs-lookup"><span data-stu-id="28ef9-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="28ef9-301">Pour empêcher l’épuisement de port, il est possible de réutiliser la même `HttpClient` instance de :</span><span class="sxs-lookup"><span data-stu-id="28ef9-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="28ef9-302">L' `HttpClient` instance est libérée lorsque l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="28ef9-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="28ef9-303">Cet exemple montre que toutes les ressources jetables ne doivent pas être supprimées après chaque utilisation.</span><span class="sxs-lookup"><span data-stu-id="28ef9-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="28ef9-304">Pour plus d’informations sur la façon de gérer la durée de vie d’une instance, consultez les rubriques suivantes `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="28ef9-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="28ef9-305">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="28ef9-305">HttpClient and lifetime management</span></span>](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [<span data-ttu-id="28ef9-306">Blog de la fabrique HTTPClient</span><span class="sxs-lookup"><span data-stu-id="28ef9-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="28ef9-307">Mise en pool d’objets</span><span class="sxs-lookup"><span data-stu-id="28ef9-307">Object pooling</span></span>

<span data-ttu-id="28ef9-308">L’exemple précédent a montré comment l' `HttpClient` instance peut être rendue statique et réutilisée par toutes les demandes.</span><span class="sxs-lookup"><span data-stu-id="28ef9-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="28ef9-309">La réutilisation empêche les ressources de manquer de ressources.</span><span class="sxs-lookup"><span data-stu-id="28ef9-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="28ef9-310">Mise en pool d’objets :</span><span class="sxs-lookup"><span data-stu-id="28ef9-310">Object pooling:</span></span>

* <span data-ttu-id="28ef9-311">Utilise le modèle de réutilisation.</span><span class="sxs-lookup"><span data-stu-id="28ef9-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="28ef9-312">Est conçu pour les objets chers à créer.</span><span class="sxs-lookup"><span data-stu-id="28ef9-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="28ef9-313">Un pool est une collection d’objets préinitialisés qui peuvent être réservés et libérés entre les threads.</span><span class="sxs-lookup"><span data-stu-id="28ef9-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="28ef9-314">Les pools peuvent définir des règles d’allocation, telles que les limites, les tailles prédéfinies ou le taux de croissance.</span><span class="sxs-lookup"><span data-stu-id="28ef9-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="28ef9-315">Le package NuGet [Microsoft. extensions. ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contient des classes qui permettent de gérer de tels pools.</span><span class="sxs-lookup"><span data-stu-id="28ef9-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="28ef9-316">Le point de terminaison d’API suivant instancie une `byte` mémoire tampon remplie avec des nombres aléatoires pour chaque requête :</span><span class="sxs-lookup"><span data-stu-id="28ef9-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="28ef9-317">Le graphique suivant affiche l’appel de l’API précédente avec une charge modérée :</span><span class="sxs-lookup"><span data-stu-id="28ef9-317">The following chart display calling the preceding API with moderate load:</span></span>

![graphique précédent](memory/_static/array.png)

<span data-ttu-id="28ef9-319">Dans le graphique précédent, les collections de génération 0 se produisent environ une fois par seconde.</span><span class="sxs-lookup"><span data-stu-id="28ef9-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="28ef9-320">Le code précédent peut être optimisé en regroupant la `byte` mémoire tampon à l’aide de [ \<T> ArrayPool](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="28ef9-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="28ef9-321">Une instance statique est réutilisée entre les requêtes.</span><span class="sxs-lookup"><span data-stu-id="28ef9-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="28ef9-322">Ce qui diffère avec cette approche, c’est qu’un objet regroupé est retourné à partir de l’API.</span><span class="sxs-lookup"><span data-stu-id="28ef9-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="28ef9-323">Cela signifie que :</span><span class="sxs-lookup"><span data-stu-id="28ef9-323">That means:</span></span>

* <span data-ttu-id="28ef9-324">L’objet est hors de votre contrôle dès que vous retournez à partir de la méthode.</span><span class="sxs-lookup"><span data-stu-id="28ef9-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="28ef9-325">Vous ne pouvez pas libérer l’objet.</span><span class="sxs-lookup"><span data-stu-id="28ef9-325">You can't release the object.</span></span>

<span data-ttu-id="28ef9-326">Pour configurer la suppression de l’objet :</span><span class="sxs-lookup"><span data-stu-id="28ef9-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="28ef9-327">Encapsulez le tableau mis en pool dans un objet jetable.</span><span class="sxs-lookup"><span data-stu-id="28ef9-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="28ef9-328">Enregistrez l’objet regroupé avec [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="28ef9-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="28ef9-329">`RegisterForDispose` prend en charge l’appel de `Dispose` sur l’objet cible afin qu’il ne soit libéré qu’à la fin de la requête http.</span><span class="sxs-lookup"><span data-stu-id="28ef9-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="28ef9-330">L’application de la même charge que la version non regroupée produit le graphique suivant :</span><span class="sxs-lookup"><span data-stu-id="28ef9-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![graphique précédent](memory/_static/pooledarray.png)

<span data-ttu-id="28ef9-332">La principale différence est le nombre d’octets alloués et, par conséquent, beaucoup moins de collections de génération 0.</span><span class="sxs-lookup"><span data-stu-id="28ef9-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28ef9-333">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="28ef9-333">Additional resources</span></span>

* [<span data-ttu-id="28ef9-334">Garbage collection</span><span class="sxs-lookup"><span data-stu-id="28ef9-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="28ef9-335">Fonctionnement de différents modes GC avec le visualiseur concurrentiel</span><span class="sxs-lookup"><span data-stu-id="28ef9-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="28ef9-336">Segment de mémoire Large Object non couvert</span><span class="sxs-lookup"><span data-stu-id="28ef9-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="28ef9-337">Tas d’objets volumineux</span><span class="sxs-lookup"><span data-stu-id="28ef9-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)