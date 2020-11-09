---
title: Modèle d’options dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le modèle d’options pour représenter des groupes de paramètres associés dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061091"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="71083-103">Modèle d’options dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71083-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71083-104">Par [Kirk Larkin](https://twitter.com/serpent5) et [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="71083-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="71083-105">Le modèle d’options utilise des classes pour fournir un accès fortement typé aux groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="71083-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="71083-106">Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :</span><span class="sxs-lookup"><span data-stu-id="71083-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="71083-107">Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.</span><span class="sxs-lookup"><span data-stu-id="71083-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="71083-108">[Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.</span><span class="sxs-lookup"><span data-stu-id="71083-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="71083-109">Ces options fournissent également un mécanisme de validation des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="71083-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="71083-110">Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="71083-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="71083-111">Cette rubrique fournit des informations sur le modèle d’options dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="71083-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="71083-112">Pour plus d’informations sur l’utilisation du modèle options dans les applications console, consultez [modèle d’options dans .net](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="71083-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="71083-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71083-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="71083-114">Liaison de la configuration hiérarchique</span><span class="sxs-lookup"><span data-stu-id="71083-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="71083-115">Interfaces d’options</span><span class="sxs-lookup"><span data-stu-id="71083-115">Options interfaces</span></span>

<span data-ttu-id="71083-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="71083-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="71083-117">Ne prend **pas** en charge : _ la lecture des données de configuration après le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="71083-117">Does \* **not** _ support: _ Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="71083-118">Options nommées</span><span class="sxs-lookup"><span data-stu-id="71083-118">Named options</span></span>](#named)
* <span data-ttu-id="71083-119">Est inscrit en tant que [Singleton](xref:fundamentals/dependency-injection#singleton) et peut être injecté dans n’importe quelle [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="71083-119">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="71083-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="71083-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="71083-121">Est utile dans les scénarios où les options doivent être recalculées à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="71083-121">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="71083-122">Pour plus d’informations, consultez [utiliser IOptionsSnapshot pour lire des données mises à jour](#ios).</span><span class="sxs-lookup"><span data-stu-id="71083-122">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="71083-123">Est inscrit en tant qu' [étendue](xref:fundamentals/dependency-injection#scoped) et ne peut donc pas être injecté dans un service Singleton.</span><span class="sxs-lookup"><span data-stu-id="71083-123">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="71083-124">Prend en charge les [options nommées](#named)</span><span class="sxs-lookup"><span data-stu-id="71083-124">Supports [named options](#named)</span></span>

<span data-ttu-id="71083-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="71083-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="71083-126">Est utilisé pour récupérer des options et gérer les notifications d’options pour les `TOptions` instances.</span><span class="sxs-lookup"><span data-stu-id="71083-126">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="71083-127">Est inscrit en tant que [Singleton](xref:fundamentals/dependency-injection#singleton) et peut être injecté dans n’importe quelle [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="71083-127">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="71083-128">Prend en charge :</span><span class="sxs-lookup"><span data-stu-id="71083-128">Supports:</span></span>
  * <span data-ttu-id="71083-129">Notifications de modifications</span><span class="sxs-lookup"><span data-stu-id="71083-129">Change notifications</span></span>
  * [<span data-ttu-id="71083-130">Options nommées</span><span class="sxs-lookup"><span data-stu-id="71083-130">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="71083-131">Configuration rechargeable</span><span class="sxs-lookup"><span data-stu-id="71083-131">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="71083-132">Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="71083-132">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="71083-133">Les scénarios [postérieurs à la configuration](#options-post-configuration) permettent de définir ou de modifier des options après l’exécution de toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configurations.</span><span class="sxs-lookup"><span data-stu-id="71083-133">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="71083-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options.</span><span class="sxs-lookup"><span data-stu-id="71083-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="71083-135">Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="71083-135">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="71083-136">L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations.</span><span class="sxs-lookup"><span data-stu-id="71083-136">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="71083-137">Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.</span><span class="sxs-lookup"><span data-stu-id="71083-137">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="71083-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="71083-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="71083-139">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="71083-140">Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="71083-140">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="71083-141">La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.</span><span class="sxs-lookup"><span data-stu-id="71083-141">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="71083-142">Utiliser IOptionsSnapshot pour lire les données mises à jour</span><span class="sxs-lookup"><span data-stu-id="71083-142">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="71083-143">À l’aide de <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> , les options sont calculées une fois par demande, en cas d’accès et de mise en cache pour la durée de vie de la demande.</span><span class="sxs-lookup"><span data-stu-id="71083-143">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="71083-144">Les modifications apportées à la configuration sont lues après le démarrage de l’application lors de l’utilisation de fournisseurs de configuration qui prennent en charge la lecture des valeurs de configuration mises</span><span class="sxs-lookup"><span data-stu-id="71083-144">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="71083-145">La différence entre `IOptionsMonitor` et `IOptionsSnapshot` est que :</span><span class="sxs-lookup"><span data-stu-id="71083-145">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="71083-146">`IOptionsMonitor` est un [service Singleton](xref:fundamentals/dependency-injection#singleton) qui récupère les valeurs d’option actuelles à tout moment, ce qui est particulièrement utile dans les dépendances Singleton.</span><span class="sxs-lookup"><span data-stu-id="71083-146">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="71083-147">`IOptionsSnapshot` est un [service étendu](xref:fundamentals/dependency-injection#scoped) et fournit un instantané des options au moment de la construction de l' `IOptionsSnapshot<T>` objet.</span><span class="sxs-lookup"><span data-stu-id="71083-147">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="71083-148">Les instantanés d’options sont conçus pour être utilisés avec des dépendances transitoires et délimitées.</span><span class="sxs-lookup"><span data-stu-id="71083-148">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="71083-149">Le code suivant utilise <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="71083-149">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="71083-150">Le code suivant inscrit une instance de configuration qui se `MyOptions` lie à :</span><span class="sxs-lookup"><span data-stu-id="71083-150">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="71083-151">Dans le code précédent, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.</span><span class="sxs-lookup"><span data-stu-id="71083-151">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="71083-152">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="71083-152">IOptionsMonitor</span></span>

<span data-ttu-id="71083-153">Le code suivant inscrit une instance de configuration qui est `MyOptions` liée à.</span><span class="sxs-lookup"><span data-stu-id="71083-153">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="71083-154">L'exemple suivant utilise <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="71083-154">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="71083-155">Dans le code précédent, par défaut, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.</span><span class="sxs-lookup"><span data-stu-id="71083-155">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="71083-156">Prise en charge des options nommées à l’aide de IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="71083-156">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="71083-157">Options nommées :</span><span class="sxs-lookup"><span data-stu-id="71083-157">Named options:</span></span>

* <span data-ttu-id="71083-158">Sont utiles lorsque plusieurs sections de configuration sont liées aux mêmes propriétés.</span><span class="sxs-lookup"><span data-stu-id="71083-158">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="71083-159">Respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="71083-159">Are case sensitive.</span></span>

<span data-ttu-id="71083-160">Prenons le *appsettings.json* fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="71083-160">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="71083-161">Au lieu de créer deux classes pour lier `TopItem:Month` et `TopItem:Year` , la classe suivante est utilisée pour chaque section :</span><span class="sxs-lookup"><span data-stu-id="71083-161">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="71083-162">Le code suivant configure les options nommées :</span><span class="sxs-lookup"><span data-stu-id="71083-162">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="71083-163">Le code suivant affiche les options nommées :</span><span class="sxs-lookup"><span data-stu-id="71083-163">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="71083-164">Toutes les options sont des instances nommées.</span><span class="sxs-lookup"><span data-stu-id="71083-164">All options are named instances.</span></span> <span data-ttu-id="71083-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> les instances sont traitées comme ciblant l' `Options.DefaultName` instance, qui est `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="71083-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="71083-166">En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="71083-167">L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="71083-167">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="71083-168">L' `null` option nommée est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique.</span><span class="sxs-lookup"><span data-stu-id="71083-168">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="71083-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette Convention.</span><span class="sxs-lookup"><span data-stu-id="71083-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="71083-170">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="71083-170">OptionsBuilder API</span></span>

<span data-ttu-id="71083-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="71083-172">`OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="71083-172">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="71083-173">La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71083-173">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="71083-174">`OptionsBuilder` est utilisé dans la section des [options de validation](#val) .</span><span class="sxs-lookup"><span data-stu-id="71083-174">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="71083-175">Utiliser les services d’injection de dépendances (DI) pour configurer des options</span><span class="sxs-lookup"><span data-stu-id="71083-175">Use DI services to configure options</span></span>

<span data-ttu-id="71083-176">Les services sont accessibles à partir de l’injection de dépendances lors de la configuration des options de deux manières :</span><span class="sxs-lookup"><span data-stu-id="71083-176">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="71083-177">Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="71083-177">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="71083-178">`OptionsBuilder<TOptions>` fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui autorisent l’utilisation de cinq services au maximum pour configurer des options :</span><span class="sxs-lookup"><span data-stu-id="71083-178">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="71083-179">Créez un type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.</span><span class="sxs-lookup"><span data-stu-id="71083-179">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="71083-180">Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service.</span><span class="sxs-lookup"><span data-stu-id="71083-180">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="71083-181">La création d’un type équivaut à ce que fait l’infrastructure lors de l’appel de [configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="71083-181">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="71083-182">L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés.</span><span class="sxs-lookup"><span data-stu-id="71083-182">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="71083-183">Validation des options</span><span class="sxs-lookup"><span data-stu-id="71083-183">Options validation</span></span>

<span data-ttu-id="71083-184">La validation des options permet de valider les valeurs d’option.</span><span class="sxs-lookup"><span data-stu-id="71083-184">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="71083-185">Prenons le *appsettings.json* fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="71083-185">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="71083-186">La classe suivante crée une liaison avec la `"MyConfig"` section de configuration et applique deux `DataAnnotations` règles :</span><span class="sxs-lookup"><span data-stu-id="71083-186">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="71083-187">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="71083-187">The following code:</span></span>

* <span data-ttu-id="71083-188">Appelle <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> pour recevoir un [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) qui crée une liaison avec la `MyConfigOptions` classe.</span><span class="sxs-lookup"><span data-stu-id="71083-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="71083-189">Appelle <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> pour activer la validation à l’aide de `DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="71083-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="71083-190">La `ValidateDataAnnotations` méthode d’extension est définie dans le package NuGet [Microsoft. extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="71083-190">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="71083-191">Pour les applications Web qui utilisent le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK), ce package est référencé implicitement à partir de l’infrastructure partagée.</span><span class="sxs-lookup"><span data-stu-id="71083-191">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="71083-192">Le code suivant affiche les valeurs de configuration ou les erreurs de validation :</span><span class="sxs-lookup"><span data-stu-id="71083-192">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="71083-193">Le code suivant applique une règle de validation plus complexe à l’aide d’un délégué :</span><span class="sxs-lookup"><span data-stu-id="71083-193">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="71083-194">IValidateOptions pour la validation complexe</span><span class="sxs-lookup"><span data-stu-id="71083-194">IValidateOptions for complex validation</span></span>

<span data-ttu-id="71083-195">La classe suivante implémente <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="71083-195">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="71083-196">`IValidateOptions` permet de déplacer le code de validation de `StartUp` et vers une classe.</span><span class="sxs-lookup"><span data-stu-id="71083-196">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="71083-197">À l’aide du code précédent, la validation est activée dans `Startup.ConfigureServices` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="71083-197">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="71083-198">Options de post-configuration</span><span class="sxs-lookup"><span data-stu-id="71083-198">Options post-configuration</span></span>

<span data-ttu-id="71083-199">Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-199">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="71083-200">La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="71083-200">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="71083-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :</span><span class="sxs-lookup"><span data-stu-id="71083-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="71083-202">Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :</span><span class="sxs-lookup"><span data-stu-id="71083-202">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="71083-203">Accès aux options au démarrage</span><span class="sxs-lookup"><span data-stu-id="71083-203">Accessing options during startup</span></span>

<span data-ttu-id="71083-204"><xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.</span><span class="sxs-lookup"><span data-stu-id="71083-204"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="71083-205">N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="71083-205">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71083-206">Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="71083-206">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="71083-207">Package NuGet Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="71083-207">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="71083-208">Le package [ urationExtensionsMicrosoft.Extensions.Options.Config](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) est implicitement référencé dans les applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="71083-208">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="71083-209">Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="71083-209">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="71083-210">Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :</span><span class="sxs-lookup"><span data-stu-id="71083-210">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="71083-211">Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.</span><span class="sxs-lookup"><span data-stu-id="71083-211">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="71083-212">[Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.</span><span class="sxs-lookup"><span data-stu-id="71083-212">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="71083-213">Ces options fournissent également un mécanisme de validation des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="71083-213">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="71083-214">Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="71083-214">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="71083-215">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71083-215">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="71083-216">Prérequis</span><span class="sxs-lookup"><span data-stu-id="71083-216">Prerequisites</span></span>

<span data-ttu-id="71083-217">Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="71083-217">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="71083-218">Interfaces d’options</span><span class="sxs-lookup"><span data-stu-id="71083-218">Options interfaces</span></span>

<span data-ttu-id="71083-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> permet de récupérer des options et de gérer les notifications d’options pour les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="71083-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="71083-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="71083-221">Notifications de modifications</span><span class="sxs-lookup"><span data-stu-id="71083-221">Change notifications</span></span>
* [<span data-ttu-id="71083-222">Options nommées</span><span class="sxs-lookup"><span data-stu-id="71083-222">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="71083-223">Configuration rechargeable</span><span class="sxs-lookup"><span data-stu-id="71083-223">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="71083-224">Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="71083-224">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="71083-225">Des scénarios [post-configuration](#options-post-configuration) vous permettent de définir ou de modifier les options après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-225">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="71083-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options.</span><span class="sxs-lookup"><span data-stu-id="71083-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="71083-227">Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="71083-227">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="71083-228">L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations.</span><span class="sxs-lookup"><span data-stu-id="71083-228">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="71083-229">Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.</span><span class="sxs-lookup"><span data-stu-id="71083-229">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="71083-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="71083-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="71083-231">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="71083-232">Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="71083-232">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="71083-233">La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.</span><span class="sxs-lookup"><span data-stu-id="71083-233">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="71083-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est utile dans les scénarios où des options doivent être recalculées à chaque requête.</span><span class="sxs-lookup"><span data-stu-id="71083-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="71083-235">Pour plus d’informations, consultez la section [Recharger les données de configuration avec IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="71083-235">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="71083-236"><xref:Microsoft.Extensions.Options.IOptions%601> peut être utilisée pour prendre en charge des options.</span><span class="sxs-lookup"><span data-stu-id="71083-236"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="71083-237">Toutefois, <xref:Microsoft.Extensions.Options.IOptions%601> ne prend pas en charge les scénarios <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> précédents.</span><span class="sxs-lookup"><span data-stu-id="71083-237">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="71083-238">Vous pouvez continuer à utiliser <xref:Microsoft.Extensions.Options.IOptions%601> dans des infrastructures et bibliothèques existantes qui utilisent déjà l’interface <xref:Microsoft.Extensions.Options.IOptions%601> mais ne nécessitent pas les scénarios fournis par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-238">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="71083-239">Configuration des options générales</span><span class="sxs-lookup"><span data-stu-id="71083-239">General options configuration</span></span>

<span data-ttu-id="71083-240">La configuration des options générales est illustrée dans l’exemple 1 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-240">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="71083-241">Une classe d’options doit être non abstraite avec un constructeur public sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="71083-241">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="71083-242">La classe suivante, `MyOptions`, a deux propriétés : `Option1` et `Option2`.</span><span class="sxs-lookup"><span data-stu-id="71083-242">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="71083-243">Définir des valeurs par défaut est facultatif, mais le constructeur de classe dans l’exemple suivant définit la valeur par défaut `Option1`.</span><span class="sxs-lookup"><span data-stu-id="71083-243">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="71083-244">`Option2` a une valeur par défaut définie par l’initialisation directe de la propriété ( *Models/MyOptions.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-244">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="71083-245">La classe `MyOptions` est ajoutée au conteneur de service avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et liée à la configuration :</span><span class="sxs-lookup"><span data-stu-id="71083-245">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="71083-246">Le modèle de page suivant utilise une [injection de dépendances de constructeur](xref:mvc/controllers/dependency-injection) avec <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour accéder aux paramètres ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-246">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="71083-247">Le fichier de l’exemple *appsettings.json* spécifie des valeurs pour `option1` et `option2` :</span><span class="sxs-lookup"><span data-stu-id="71083-247">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="71083-248">Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :</span><span class="sxs-lookup"><span data-stu-id="71083-248">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="71083-249">Quand vous utilisez un <xref:System.Configuration.ConfigurationBuilder> personnalisé pour charger une configuration d’options à partir d’un fichier de paramètres, vérifiez que le chemin de base est correctement défini :</span><span class="sxs-lookup"><span data-stu-id="71083-249">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="71083-250">Vous n’avez pas besoin de définir explicitement le chemin de base quand vous chargez une configuration d’options à partir du fichier de paramètres via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="71083-250">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="71083-251">Configurer des options simples avec un délégué</span><span class="sxs-lookup"><span data-stu-id="71083-251">Configure simple options with a delegate</span></span>

<span data-ttu-id="71083-252">La configuration d’options simples avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-252">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="71083-253">Utilisez un délégué pour définir les valeurs des options.</span><span class="sxs-lookup"><span data-stu-id="71083-253">Use a delegate to set options values.</span></span> <span data-ttu-id="71083-254">L’exemple d’application utilise la classe `MyOptionsWithDelegateConfig` ( *Models/MyOptionsWithDelegateConfig.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-254">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="71083-255">Dans le code suivant, un second service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="71083-255">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="71083-256">Il utilise un délégué pour configurer la liaison avec `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="71083-256">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="71083-257">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="71083-257">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="71083-258">Vous pouvez ajouter plusieurs fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="71083-258">You can add multiple configuration providers.</span></span> <span data-ttu-id="71083-259">Des fournisseurs de configuration sont disponibles à partir de packages NuGet et sont appliqués dans l’ordre de leur inscription.</span><span class="sxs-lookup"><span data-stu-id="71083-259">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="71083-260">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="71083-260">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="71083-261">Chaque appel à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> ajoute un service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="71083-261">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="71083-262">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont toutes deux spécifiées dans *appsettings.json* , mais les valeurs de `Option1` et `Option2` sont remplacées par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="71083-262">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="71083-263">Quand plusieurs services de configuration sont activés, la dernière source de configuration spécifiée *gagne* et définit la valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="71083-263">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="71083-264">Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :</span><span class="sxs-lookup"><span data-stu-id="71083-264">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="71083-265">Configuration des sous-options</span><span class="sxs-lookup"><span data-stu-id="71083-265">Suboptions configuration</span></span>

<span data-ttu-id="71083-266">La configuration des sous-options est illustrée dans l’exemple 3 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-266">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="71083-267">Les applications doivent créer des classes d’options qui appartiennent à des groupes de scénarios spécifiques (classes) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="71083-267">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="71083-268">Les parties de l’application qui requièrent des valeurs de configuration ne doivent avoir accès qu’aux valeurs de configuration qu’elles utilisent.</span><span class="sxs-lookup"><span data-stu-id="71083-268">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="71083-269">Au moment de la liaison des options à la configuration, chaque propriété du type options est liée à une clé de configuration sous la forme `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="71083-269">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="71083-270">Par exemple, la `MyOptions.Option1` propriété est liée à la clé `Option1` , qui est lue à partir de la `option1` propriété dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="71083-270">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json* .</span></span>

<span data-ttu-id="71083-271">Dans le code suivant, un troisième service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="71083-271">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="71083-272">Il crée une liaison `MySubOptions` avec la section `subsection` du *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="71083-272">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="71083-273">La `GetSection` méthode requiert l' <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> espace de noms.</span><span class="sxs-lookup"><span data-stu-id="71083-273">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="71083-274">Le fichier de l’exemple *appsettings.json* définit un `subsection` membre avec des clés pour `suboption1` et `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="71083-274">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="71083-275">La classe `MySubOptions` définit des propriétés, `SubOption1` et `SubOption2`, pour conserver les valeurs des options ( *Models/MySubOptions.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-275">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="71083-276">La méthode `OnGet` du modèle de page retourne une chaîne avec les valeurs des options ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-276">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="71083-277">Quand l’application est exécutée, la méthode `OnGet` retourne une chaîne indiquant les valeurs de la classe de sous-options :</span><span class="sxs-lookup"><span data-stu-id="71083-277">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="71083-278">Injection d’options</span><span class="sxs-lookup"><span data-stu-id="71083-278">Options injection</span></span>

<span data-ttu-id="71083-279">L’injection d’options est illustrée dans l’exemple 4 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-279">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="71083-280">Injecter <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans :</span><span class="sxs-lookup"><span data-stu-id="71083-280">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="71083-281">Une Razor page ou une vue MVC avec la [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span><span class="sxs-lookup"><span data-stu-id="71083-281">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="71083-282">Modèle de page ou de vue.</span><span class="sxs-lookup"><span data-stu-id="71083-282">A page or view model.</span></span>

<span data-ttu-id="71083-283">L’exemple suivant tiré de l’exemple d’application injecte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans un modèle de page ( *pages/index. cshtml. cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-283">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="71083-284">L’exemple d’application montre comment injecter `IOptionsMonitor<MyOptions>` avec une directive `@inject` :</span><span class="sxs-lookup"><span data-stu-id="71083-284">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="71083-285">Quand l’application est exécutée, les valeurs d’options sont affichées dans la page rendue :</span><span class="sxs-lookup"><span data-stu-id="71083-285">When the app is run, the options values are shown in the rendered page:</span></span>

![Les valeurs d’options Option1: value1_from_json et Option2: -1 sont chargées à partir du modèle et par injection dans la vue.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="71083-287">Recharger les données de configuration avec IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="71083-287">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="71083-288">Le rechargement des données de configuration avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est illustré dans l’exemple 5 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-288">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="71083-289">À l’aide de <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> , les options sont calculées une fois par demande, en cas d’accès et de mise en cache pour la durée de vie de la demande.</span><span class="sxs-lookup"><span data-stu-id="71083-289">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="71083-290">La différence entre `IOptionsMonitor` et `IOptionsSnapshot` est que :</span><span class="sxs-lookup"><span data-stu-id="71083-290">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="71083-291">`IOptionsMonitor` est un [service Singleton](xref:fundamentals/dependency-injection#singleton) qui récupère les valeurs d’option actuelles à tout moment, ce qui est particulièrement utile dans les dépendances Singleton.</span><span class="sxs-lookup"><span data-stu-id="71083-291">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="71083-292">`IOptionsSnapshot` est un [service étendu](xref:fundamentals/dependency-injection#scoped) et fournit un instantané des options au moment de la construction de l' `IOptionsSnapshot<T>` objet.</span><span class="sxs-lookup"><span data-stu-id="71083-292">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="71083-293">Les instantanés d’options sont conçus pour être utilisés avec des dépendances transitoires et délimitées.</span><span class="sxs-lookup"><span data-stu-id="71083-293">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="71083-294">L’exemple suivant montre comment un nouveau <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est créé après *appsettings.json* les modifications ( *pages/index. cshtml. cs* ).</span><span class="sxs-lookup"><span data-stu-id="71083-294">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="71083-295">Plusieurs demandes au serveur retournent des valeurs constantes fournies par le *appsettings.json* fichier jusqu’à ce que le fichier soit modifié et que la configuration recharge.</span><span class="sxs-lookup"><span data-stu-id="71083-295">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="71083-296">L’illustration suivante montre les `option1` valeurs initiales et `option2` chargées à partir du *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="71083-296">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="71083-297">Remplacez les valeurs du *appsettings.json* fichier par `value1_from_json UPDATED` et `200` .</span><span class="sxs-lookup"><span data-stu-id="71083-297">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="71083-298">Enregistrez le fichier *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="71083-298">Save the *appsettings.json* file.</span></span> <span data-ttu-id="71083-299">Actualisez le navigateur pour constater la mise à jour des valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="71083-299">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="71083-300">Prise en charge des options nommées avec IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="71083-300">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="71083-301">La prise en charge des options nommées avec <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> est illustrée dans l’exemple 6 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-301">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="71083-302">La prise en charge des options nommées permet à l’application de faire la distinction entre les configurations d’options nommées.</span><span class="sxs-lookup"><span data-stu-id="71083-302">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="71083-303">Dans l’exemple d’application, les options nommées sont déclarées avec [OptionsServiceCollectionExtensions.Configurer](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), qui appelle [ConfigureNamedOptions \<TOptions> . Configurez](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) la méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="71083-303">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="71083-304">Les options nommées respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="71083-304">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="71083-305">L’exemple d’application accède aux options nommées avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-305">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="71083-306">Quand l’exemple d’application est exécuté, les options nommées sont retournées :</span><span class="sxs-lookup"><span data-stu-id="71083-306">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="71083-307">`named_options_1` les valeurs sont fournies à partir de la configuration, qui sont chargées à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="71083-307">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="71083-308">Les valeurs `named_options_2` sont fournies par :</span><span class="sxs-lookup"><span data-stu-id="71083-308">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="71083-309">Le délégué `named_options_2` dans `ConfigureServices` pour `Option1`.</span><span class="sxs-lookup"><span data-stu-id="71083-309">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="71083-310">La valeur par défaut `Option2` fournie par la classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-310">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="71083-311">Configurer toutes les options avec la méthode ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="71083-311">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="71083-312">Configurez toutes les instances d’options avec la méthode <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="71083-312">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="71083-313">Le code suivant configure `Option1` pour toutes les instances de configuration ayant une valeur commune.</span><span class="sxs-lookup"><span data-stu-id="71083-313">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="71083-314">Ajoutez le code suivant manuellement à la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71083-314">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="71083-315">Exécuter l’exemple d’application après avoir ajouté le code produit le résultat suivant :</span><span class="sxs-lookup"><span data-stu-id="71083-315">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="71083-316">Toutes les options sont des instances nommées.</span><span class="sxs-lookup"><span data-stu-id="71083-316">All options are named instances.</span></span> <span data-ttu-id="71083-317">Les instances <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existantes sont traitées comme ciblant l’instance `Options.DefaultName`, qui est `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="71083-317">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="71083-318">En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="71083-319">L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="71083-319">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="71083-320">L’option nommée `null` est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette convention).</span><span class="sxs-lookup"><span data-stu-id="71083-320">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="71083-321">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="71083-321">OptionsBuilder API</span></span>

<span data-ttu-id="71083-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="71083-323">`OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="71083-323">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="71083-324">La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71083-324">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="71083-325">Utiliser les services d’injection de dépendances (DI) pour configurer des options</span><span class="sxs-lookup"><span data-stu-id="71083-325">Use DI services to configure options</span></span>

<span data-ttu-id="71083-326">Vous pouvez accéder à d’autres services à partir de l’injection de dépendances pendant que vous configurez des options de deux manières différentes :</span><span class="sxs-lookup"><span data-stu-id="71083-326">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="71083-327">Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="71083-327">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="71083-328">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui vous permettent d’utiliser jusqu’à cinq services pour configurer des options :</span><span class="sxs-lookup"><span data-stu-id="71083-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="71083-329">Créez votre propre type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.</span><span class="sxs-lookup"><span data-stu-id="71083-329">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="71083-330">Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service.</span><span class="sxs-lookup"><span data-stu-id="71083-330">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="71083-331">Créer votre propre type équivaut à ce que fait automatiquement le framework quand vous utilisez [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="71083-331">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="71083-332">L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés.</span><span class="sxs-lookup"><span data-stu-id="71083-332">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="71083-333">Validation des options</span><span class="sxs-lookup"><span data-stu-id="71083-333">Options validation</span></span>

<span data-ttu-id="71083-334">La validation des options vous permet de valider les options lorsque celles-ci sont configurées.</span><span class="sxs-lookup"><span data-stu-id="71083-334">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="71083-335">Appelez `Validate` avec une méthode de validation qui retourne `true` si les options sont valides et `false` si elles ne sont pas valides :</span><span class="sxs-lookup"><span data-stu-id="71083-335">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="71083-336">L’exemple précédent définit l’instance d’options nommée sur `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="71083-336">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="71083-337">L'instance d’options par défaut est `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="71083-337">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="71083-338">La validation s’exécute lorsque l’instance d’options est créée.</span><span class="sxs-lookup"><span data-stu-id="71083-338">Validation runs when the options instance is created.</span></span> <span data-ttu-id="71083-339">La validation d’une instance options est garantie lors de sa première accès.</span><span class="sxs-lookup"><span data-stu-id="71083-339">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71083-340">La validation des options ne protège pas contre les modifications apportées aux options une fois l’instance des options créée.</span><span class="sxs-lookup"><span data-stu-id="71083-340">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="71083-341">Par exemple, `IOptionsSnapshot` les options sont créées et validées une fois par demande lorsque les options y accèdent pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="71083-341">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="71083-342">Les `IOptionsSnapshot` options ne sont pas validées à nouveau lors des tentatives d’accès suivantes *pour la même requête* .</span><span class="sxs-lookup"><span data-stu-id="71083-342">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request* .</span></span>

<span data-ttu-id="71083-343">La méthode `Validate` accepte un `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="71083-343">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="71083-344">Pour personnaliser entièrement la validation, implémentez `IValidateOptions<TOptions>`, ce qui permet :</span><span class="sxs-lookup"><span data-stu-id="71083-344">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="71083-345">Validation de plusieurs types d’options : `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="71083-345">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="71083-346">Validation qui dépend d’un autre type d’option : `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="71083-346">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="71083-347">`IValidateOptions` valide :</span><span class="sxs-lookup"><span data-stu-id="71083-347">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="71083-348">Une instance d’options nommée spécifique.</span><span class="sxs-lookup"><span data-stu-id="71083-348">A specific named options instance.</span></span>
* <span data-ttu-id="71083-349">Toutes les options quand `name` est `null`.</span><span class="sxs-lookup"><span data-stu-id="71083-349">All options when `name` is `null`.</span></span>

<span data-ttu-id="71083-350">Retourne `ValidateOptionsResult` à partir de votre implémentation de l’interface :</span><span class="sxs-lookup"><span data-stu-id="71083-350">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="71083-351">La validation basée sur les annotations de données est disponible à partir du package [Microsoft. extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) en appelant la <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> méthode sur `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="71083-351">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="71083-352">`Microsoft.Extensions.Options.DataAnnotations` est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="71083-352">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="71083-353">La validation hâtive (échec rapide au démarrage) est à l’étude pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="71083-353">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="71083-354">Options de post-configuration</span><span class="sxs-lookup"><span data-stu-id="71083-354">Options post-configuration</span></span>

<span data-ttu-id="71083-355">Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-355">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="71083-356">La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="71083-356">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="71083-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :</span><span class="sxs-lookup"><span data-stu-id="71083-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="71083-358">Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :</span><span class="sxs-lookup"><span data-stu-id="71083-358">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="71083-359">Accès aux options au démarrage</span><span class="sxs-lookup"><span data-stu-id="71083-359">Accessing options during startup</span></span>

<span data-ttu-id="71083-360"><xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.</span><span class="sxs-lookup"><span data-stu-id="71083-360"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="71083-361">N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="71083-361">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71083-362">Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="71083-362">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="71083-363">Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="71083-363">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="71083-364">Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :</span><span class="sxs-lookup"><span data-stu-id="71083-364">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="71083-365">Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.</span><span class="sxs-lookup"><span data-stu-id="71083-365">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="71083-366">[Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.</span><span class="sxs-lookup"><span data-stu-id="71083-366">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="71083-367">Ces options fournissent également un mécanisme de validation des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="71083-367">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="71083-368">Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="71083-368">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="71083-369">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71083-369">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="71083-370">Prérequis</span><span class="sxs-lookup"><span data-stu-id="71083-370">Prerequisites</span></span>

<span data-ttu-id="71083-371">Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="71083-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="71083-372">Interfaces d’options</span><span class="sxs-lookup"><span data-stu-id="71083-372">Options interfaces</span></span>

<span data-ttu-id="71083-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> permet de récupérer des options et de gérer les notifications d’options pour les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="71083-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="71083-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="71083-375">Notifications de modifications</span><span class="sxs-lookup"><span data-stu-id="71083-375">Change notifications</span></span>
* [<span data-ttu-id="71083-376">Options nommées</span><span class="sxs-lookup"><span data-stu-id="71083-376">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="71083-377">Configuration rechargeable</span><span class="sxs-lookup"><span data-stu-id="71083-377">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="71083-378">Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="71083-378">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="71083-379">Des scénarios [post-configuration](#options-post-configuration) vous permettent de définir ou de modifier les options après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-379">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="71083-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options.</span><span class="sxs-lookup"><span data-stu-id="71083-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="71083-381">Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="71083-381">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="71083-382">L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations.</span><span class="sxs-lookup"><span data-stu-id="71083-382">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="71083-383">Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.</span><span class="sxs-lookup"><span data-stu-id="71083-383">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="71083-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="71083-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="71083-385">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="71083-386">Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="71083-386">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="71083-387">La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.</span><span class="sxs-lookup"><span data-stu-id="71083-387">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="71083-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est utile dans les scénarios où des options doivent être recalculées à chaque requête.</span><span class="sxs-lookup"><span data-stu-id="71083-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="71083-389">Pour plus d’informations, consultez la section [Recharger les données de configuration avec IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="71083-389">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="71083-390"><xref:Microsoft.Extensions.Options.IOptions%601> peut être utilisée pour prendre en charge des options.</span><span class="sxs-lookup"><span data-stu-id="71083-390"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="71083-391">Toutefois, <xref:Microsoft.Extensions.Options.IOptions%601> ne prend pas en charge les scénarios <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> précédents.</span><span class="sxs-lookup"><span data-stu-id="71083-391">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="71083-392">Vous pouvez continuer à utiliser <xref:Microsoft.Extensions.Options.IOptions%601> dans des infrastructures et bibliothèques existantes qui utilisent déjà l’interface <xref:Microsoft.Extensions.Options.IOptions%601> mais ne nécessitent pas les scénarios fournis par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-392">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="71083-393">Configuration des options générales</span><span class="sxs-lookup"><span data-stu-id="71083-393">General options configuration</span></span>

<span data-ttu-id="71083-394">La configuration des options générales est illustrée dans l’exemple 1 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-394">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="71083-395">Une classe d’options doit être non abstraite avec un constructeur public sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="71083-395">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="71083-396">La classe suivante, `MyOptions`, a deux propriétés : `Option1` et `Option2`.</span><span class="sxs-lookup"><span data-stu-id="71083-396">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="71083-397">Définir des valeurs par défaut est facultatif, mais le constructeur de classe dans l’exemple suivant définit la valeur par défaut `Option1`.</span><span class="sxs-lookup"><span data-stu-id="71083-397">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="71083-398">`Option2` a une valeur par défaut définie par l’initialisation directe de la propriété ( *Models/MyOptions.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-398">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="71083-399">La classe `MyOptions` est ajoutée au conteneur de service avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et liée à la configuration :</span><span class="sxs-lookup"><span data-stu-id="71083-399">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="71083-400">Le modèle de page suivant utilise une [injection de dépendances de constructeur](xref:mvc/controllers/dependency-injection) avec <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour accéder aux paramètres ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-400">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="71083-401">Le fichier de l’exemple *appsettings.json* spécifie des valeurs pour `option1` et `option2` :</span><span class="sxs-lookup"><span data-stu-id="71083-401">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="71083-402">Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :</span><span class="sxs-lookup"><span data-stu-id="71083-402">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="71083-403">Quand vous utilisez un <xref:System.Configuration.ConfigurationBuilder> personnalisé pour charger une configuration d’options à partir d’un fichier de paramètres, vérifiez que le chemin de base est correctement défini :</span><span class="sxs-lookup"><span data-stu-id="71083-403">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="71083-404">Vous n’avez pas besoin de définir explicitement le chemin de base quand vous chargez une configuration d’options à partir du fichier de paramètres via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="71083-404">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="71083-405">Configurer des options simples avec un délégué</span><span class="sxs-lookup"><span data-stu-id="71083-405">Configure simple options with a delegate</span></span>

<span data-ttu-id="71083-406">La configuration d’options simples avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-406">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="71083-407">Utilisez un délégué pour définir les valeurs des options.</span><span class="sxs-lookup"><span data-stu-id="71083-407">Use a delegate to set options values.</span></span> <span data-ttu-id="71083-408">L’exemple d’application utilise la classe `MyOptionsWithDelegateConfig` ( *Models/MyOptionsWithDelegateConfig.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-408">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="71083-409">Dans le code suivant, un second service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="71083-409">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="71083-410">Il utilise un délégué pour configurer la liaison avec `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="71083-410">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="71083-411">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="71083-411">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="71083-412">Vous pouvez ajouter plusieurs fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="71083-412">You can add multiple configuration providers.</span></span> <span data-ttu-id="71083-413">Des fournisseurs de configuration sont disponibles à partir de packages NuGet et sont appliqués dans l’ordre de leur inscription.</span><span class="sxs-lookup"><span data-stu-id="71083-413">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="71083-414">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="71083-414">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="71083-415">Chaque appel à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> ajoute un service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="71083-415">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="71083-416">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont toutes deux spécifiées dans *appsettings.json* , mais les valeurs de `Option1` et `Option2` sont remplacées par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="71083-416">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="71083-417">Quand plusieurs services de configuration sont activés, la dernière source de configuration spécifiée *gagne* et définit la valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="71083-417">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="71083-418">Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :</span><span class="sxs-lookup"><span data-stu-id="71083-418">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="71083-419">Configuration des sous-options</span><span class="sxs-lookup"><span data-stu-id="71083-419">Suboptions configuration</span></span>

<span data-ttu-id="71083-420">La configuration des sous-options est illustrée dans l’exemple 3 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-420">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="71083-421">Les applications doivent créer des classes d’options qui appartiennent à des groupes de scénarios spécifiques (classes) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="71083-421">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="71083-422">Les parties de l’application qui requièrent des valeurs de configuration ne doivent avoir accès qu’aux valeurs de configuration qu’elles utilisent.</span><span class="sxs-lookup"><span data-stu-id="71083-422">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="71083-423">Au moment de la liaison des options à la configuration, chaque propriété du type options est liée à une clé de configuration sous la forme `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="71083-423">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="71083-424">Par exemple, la `MyOptions.Option1` propriété est liée à la clé `Option1` , qui est lue à partir de la `option1` propriété dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="71083-424">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json* .</span></span>

<span data-ttu-id="71083-425">Dans le code suivant, un troisième service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="71083-425">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="71083-426">Il crée une liaison `MySubOptions` avec la section `subsection` du *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="71083-426">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="71083-427">La `GetSection` méthode requiert l' <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> espace de noms.</span><span class="sxs-lookup"><span data-stu-id="71083-427">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="71083-428">Le fichier de l’exemple *appsettings.json* définit un `subsection` membre avec des clés pour `suboption1` et `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="71083-428">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="71083-429">La classe `MySubOptions` définit des propriétés, `SubOption1` et `SubOption2`, pour conserver les valeurs des options ( *Models/MySubOptions.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-429">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="71083-430">La méthode `OnGet` du modèle de page retourne une chaîne avec les valeurs des options ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-430">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="71083-431">Quand l’application est exécutée, la méthode `OnGet` retourne une chaîne indiquant les valeurs de la classe de sous-options :</span><span class="sxs-lookup"><span data-stu-id="71083-431">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="71083-432">Options fournies par un modèle d’affichage ou avec une injection de vue directe</span><span class="sxs-lookup"><span data-stu-id="71083-432">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="71083-433">Les options fournies par un modèle d’affichage ou avec une injection de vue directe sont illustrées dans l’exemple 4 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-433">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="71083-434">Vous pouvez fournir les options dans un modèle d’affichage ou en injectant <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directement dans une vue ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-434">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="71083-435">L’exemple d’application montre comment injecter `IOptionsMonitor<MyOptions>` avec une directive `@inject` :</span><span class="sxs-lookup"><span data-stu-id="71083-435">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="71083-436">Quand l’application est exécutée, les valeurs d’options sont affichées dans la page rendue :</span><span class="sxs-lookup"><span data-stu-id="71083-436">When the app is run, the options values are shown in the rendered page:</span></span>

![Les valeurs d’options Option1: value1_from_json et Option2: -1 sont chargées à partir du modèle et par injection dans la vue.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="71083-438">Recharger les données de configuration avec IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="71083-438">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="71083-439">Le rechargement des données de configuration avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est illustré dans l’exemple 5 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-439">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="71083-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> prend en charge le rechargement des options avec une charge de traitement minimale.</span><span class="sxs-lookup"><span data-stu-id="71083-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="71083-441">Les options sont calculées une fois par requête quand le système y accède et sont mises en cache pour toute la durée de vie de la requête.</span><span class="sxs-lookup"><span data-stu-id="71083-441">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="71083-442">L’exemple suivant montre comment un nouveau <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est créé après *appsettings.json* les modifications ( *pages/index. cshtml. cs* ).</span><span class="sxs-lookup"><span data-stu-id="71083-442">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="71083-443">Plusieurs demandes au serveur retournent des valeurs constantes fournies par le *appsettings.json* fichier jusqu’à ce que le fichier soit modifié et que la configuration recharge.</span><span class="sxs-lookup"><span data-stu-id="71083-443">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="71083-444">L’illustration suivante montre les `option1` valeurs initiales et `option2` chargées à partir du *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="71083-444">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="71083-445">Remplacez les valeurs du *appsettings.json* fichier par `value1_from_json UPDATED` et `200` .</span><span class="sxs-lookup"><span data-stu-id="71083-445">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="71083-446">Enregistrez le fichier *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="71083-446">Save the *appsettings.json* file.</span></span> <span data-ttu-id="71083-447">Actualisez le navigateur pour constater la mise à jour des valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="71083-447">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="71083-448">Prise en charge des options nommées avec IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="71083-448">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="71083-449">La prise en charge des options nommées avec <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> est illustrée dans l’exemple 6 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="71083-449">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="71083-450">La prise en charge des options nommées permet à l’application de faire la distinction entre les configurations d’options nommées.</span><span class="sxs-lookup"><span data-stu-id="71083-450">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="71083-451">Dans l’exemple d’application, les options nommées sont déclarées avec [OptionsServiceCollectionExtensions.Configurer](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), qui appelle [ConfigureNamedOptions \<TOptions> . Configurez](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) la méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="71083-451">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="71083-452">Les options nommées respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="71083-452">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="71083-453">L’exemple d’application accède aux options nommées avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="71083-453">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="71083-454">Quand l’exemple d’application est exécuté, les options nommées sont retournées :</span><span class="sxs-lookup"><span data-stu-id="71083-454">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="71083-455">`named_options_1` les valeurs sont fournies à partir de la configuration, qui sont chargées à partir du *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="71083-455">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="71083-456">Les valeurs `named_options_2` sont fournies par :</span><span class="sxs-lookup"><span data-stu-id="71083-456">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="71083-457">Le délégué `named_options_2` dans `ConfigureServices` pour `Option1`.</span><span class="sxs-lookup"><span data-stu-id="71083-457">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="71083-458">La valeur par défaut `Option2` fournie par la classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-458">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="71083-459">Configurer toutes les options avec la méthode ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="71083-459">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="71083-460">Configurez toutes les instances d’options avec la méthode <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="71083-460">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="71083-461">Le code suivant configure `Option1` pour toutes les instances de configuration ayant une valeur commune.</span><span class="sxs-lookup"><span data-stu-id="71083-461">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="71083-462">Ajoutez le code suivant manuellement à la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71083-462">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="71083-463">Exécuter l’exemple d’application après avoir ajouté le code produit le résultat suivant :</span><span class="sxs-lookup"><span data-stu-id="71083-463">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="71083-464">Toutes les options sont des instances nommées.</span><span class="sxs-lookup"><span data-stu-id="71083-464">All options are named instances.</span></span> <span data-ttu-id="71083-465">Les instances <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existantes sont traitées comme ciblant l’instance `Options.DefaultName`, qui est `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="71083-465">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="71083-466">En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="71083-467">L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="71083-467">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="71083-468">L’option nommée `null` est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette convention).</span><span class="sxs-lookup"><span data-stu-id="71083-468">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="71083-469">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="71083-469">OptionsBuilder API</span></span>

<span data-ttu-id="71083-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="71083-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="71083-471">`OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="71083-471">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="71083-472">La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71083-472">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="71083-473">Utiliser les services d’injection de dépendances (DI) pour configurer des options</span><span class="sxs-lookup"><span data-stu-id="71083-473">Use DI services to configure options</span></span>

<span data-ttu-id="71083-474">Vous pouvez accéder à d’autres services à partir de l’injection de dépendances pendant que vous configurez des options de deux manières différentes :</span><span class="sxs-lookup"><span data-stu-id="71083-474">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="71083-475">Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="71083-475">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="71083-476">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui vous permettent d’utiliser jusqu’à cinq services pour configurer des options :</span><span class="sxs-lookup"><span data-stu-id="71083-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="71083-477">Créez votre propre type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.</span><span class="sxs-lookup"><span data-stu-id="71083-477">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="71083-478">Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service.</span><span class="sxs-lookup"><span data-stu-id="71083-478">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="71083-479">Créer votre propre type équivaut à ce que fait automatiquement le framework quand vous utilisez [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="71083-479">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="71083-480">L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés.</span><span class="sxs-lookup"><span data-stu-id="71083-480">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="71083-481">Options de post-configuration</span><span class="sxs-lookup"><span data-stu-id="71083-481">Options post-configuration</span></span>

<span data-ttu-id="71083-482">Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="71083-482">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="71083-483">La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="71083-483">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="71083-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :</span><span class="sxs-lookup"><span data-stu-id="71083-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="71083-485">Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :</span><span class="sxs-lookup"><span data-stu-id="71083-485">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="71083-486">Accès aux options au démarrage</span><span class="sxs-lookup"><span data-stu-id="71083-486">Accessing options during startup</span></span>

<span data-ttu-id="71083-487"><xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.</span><span class="sxs-lookup"><span data-stu-id="71083-487"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="71083-488">N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="71083-488">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71083-489">Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="71083-489">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="71083-490">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="71083-490">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
