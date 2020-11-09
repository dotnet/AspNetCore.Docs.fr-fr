---
title: Détecter les modifications avec des jetons de modification dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser des jetons de modification pour effectuer le suivi des modifications.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
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
uid: fundamentals/change-tokens
ms.openlocfilehash: f20d44c7767b284f727ce19a46224dae0cf6a5e1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053772"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="d513c-103">Détecter les modifications avec des jetons de modification dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d513c-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d513c-104">Un *jeton de modification* est un module à usage général de bas niveau, utilisé pour effectuer le suivi des modifications de l’état.</span><span class="sxs-lookup"><span data-stu-id="d513c-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="d513c-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d513c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="d513c-106">Interface d’IChangeToken</span><span class="sxs-lookup"><span data-stu-id="d513c-106">IChangeToken interface</span></span>

<span data-ttu-id="d513c-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propage des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="d513c-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="d513c-108">`IChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d513c-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="d513c-109">Le package NuGet [Microsoft. extensions. Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) est fourni implicitement aux applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="d513c-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="d513c-110">`IChangeToken` a deux propriétés :</span><span class="sxs-lookup"><span data-stu-id="d513c-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="d513c-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indique si le jeton déclenche des rappels de façon proactive.</span><span class="sxs-lookup"><span data-stu-id="d513c-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="d513c-112">Si `ActiveChangedCallbacks` est défini sur `false`, un rappel n’est jamais appelé, et l’application doit interroger `HasChanged` pour connaître les modifications.</span><span class="sxs-lookup"><span data-stu-id="d513c-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="d513c-113">Il est également possible qu’un jeton ne soit jamais annulé si aucune modification ne se produit, ou si l’écouteur de modifications sous-jacent est supprimé ou désactivé.</span><span class="sxs-lookup"><span data-stu-id="d513c-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="d513c-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> obtient une valeur qui indique si une modification a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="d513c-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="d513c-115">L' `IChangeToken` interface inclut la méthode [RegisterChangeCallback (action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , qui inscrit un rappel appelé lorsque le jeton a changé.</span><span class="sxs-lookup"><span data-stu-id="d513c-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="d513c-116">`HasChanged` doit être défini avant que le rappel soit appelé.</span><span class="sxs-lookup"><span data-stu-id="d513c-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="d513c-117">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="d513c-117">ChangeToken class</span></span>

<span data-ttu-id="d513c-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> est une classe statique utilisée pour propager des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="d513c-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="d513c-119">`ChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d513c-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="d513c-120">Le package NuGet [Microsoft. extensions. Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) est fourni implicitement aux applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="d513c-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="d513c-121">La méthode [ChangeToken. OnChange (Func \<IChangeToken> , action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) inscrit un `Action` à appeler chaque fois que le jeton change :</span><span class="sxs-lookup"><span data-stu-id="d513c-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="d513c-122">`Func<IChangeToken>` produit le jeton.</span><span class="sxs-lookup"><span data-stu-id="d513c-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="d513c-123">`Action` est appelée quand le jeton change.</span><span class="sxs-lookup"><span data-stu-id="d513c-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="d513c-124">La surcharge [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) prend un paramètre supplémentaire `TState` passé dans le consommateur de jetons `Action` .</span><span class="sxs-lookup"><span data-stu-id="d513c-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="d513c-125">`OnChange`Retourne un <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="d513c-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="d513c-126">L’appel de <xref:System.IDisposable.Dispose*> fait que le jeton cesse d’être à l’écoute des modifications suivantes et libère les ressources du jeton.</span><span class="sxs-lookup"><span data-stu-id="d513c-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="d513c-127">Exemples d’utilisation de jetons de modification dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d513c-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="d513c-128">Les jetons de modification sont utilisés dans des zones importantes d’ASP.NET Core pour surveiller les modifications apportées aux objets :</span><span class="sxs-lookup"><span data-stu-id="d513c-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="d513c-129">Pour surveiller les modifications apportées aux fichiers, la méthode <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crée un `IChangeToken` pour les fichiers ou le dossier à surveiller.</span><span class="sxs-lookup"><span data-stu-id="d513c-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="d513c-130">Les jetons `IChangeToken` peuvent être ajoutés aux entrées du cache pour déclencher des suppressions dans le cache en cas de modification.</span><span class="sxs-lookup"><span data-stu-id="d513c-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="d513c-131">Pour les modifications `TOptions`, l’implémentation <xref:Microsoft.Extensions.Options.OptionsMonitor`1> par défaut de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> a une surcharge qui accepte une ou plusieurs instances <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="d513c-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="d513c-132">Chaque instance retourne un `IChangeToken` pour inscrire un rappel de notification de modification pour les modifications des options de suivi.</span><span class="sxs-lookup"><span data-stu-id="d513c-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="d513c-133">Surveiller les modifications de configuration</span><span class="sxs-lookup"><span data-stu-id="d513c-133">Monitor for configuration changes</span></span>

<span data-ttu-id="d513c-134">Par défaut, les modèles de ASP.net Core utilisent des [fichiers de configuration JSON](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.jssous* et *appsettings.Production.js* ) pour charger les paramètres de configuration des applications.</span><span class="sxs-lookup"><span data-stu-id="d513c-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.json* , and *appsettings.Production.json* ) to load app configuration settings.</span></span>

<span data-ttu-id="d513c-135">Ces fichiers sont configurés avec la méthode d’extension [AddJsonFile(IConfigurationBuilder, chaîne, booléen, booléen)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) sur <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> qui accepte un paramètre `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="d513c-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="d513c-136">`reloadOnChange` indique si la configuration doit être rechargée en cas de modification d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="d513c-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="d513c-137">Ce paramètre s’affiche dans la méthode pratique <xref:Microsoft.Extensions.Hosting.Host><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="d513c-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="d513c-138">La configuration basée sur les fichiers est représentée par <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="d513c-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="d513c-139">`FileConfigurationSource` utilise <xref:Microsoft.Extensions.FileProviders.IFileProvider> pour surveiller les fichiers.</span><span class="sxs-lookup"><span data-stu-id="d513c-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="d513c-140">Par défaut, `IFileMonitor` est fourni par un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, qui utilise <xref:System.IO.FileSystemWatcher> pour surveiller les modifications des fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="d513c-141">L’exemple d’application montre les deux implémentations de la surveillance des modifications de configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="d513c-142">Si l’un des fichiers *appsettings* change, les deux implémentations d’analyse du fichier exécutent du code personnalisé&mdash;l’exemple d’application écrit un message dans la console.</span><span class="sxs-lookup"><span data-stu-id="d513c-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="d513c-143">Le `FileSystemWatcher` d’un fichier de configuration peut déclencher plusieurs rappels de jeton pour une même modification du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="d513c-144">Pour vous assurer que le code personnalisé n’est exécuté qu’une seule fois lors du déclenchement de plusieurs rappels de jeton, l’implémentation de l’exemple vérifie les hachages des fichiers.</span><span class="sxs-lookup"><span data-stu-id="d513c-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="d513c-145">L’exemple utilise le hachage de fichier SHA1.</span><span class="sxs-lookup"><span data-stu-id="d513c-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="d513c-146">Une nouvelle tentative est implémentée avec une interruption d’une durée exponentielle.</span><span class="sxs-lookup"><span data-stu-id="d513c-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="d513c-147">Une nouvelle tentative est effectuée, car il peut se produire un verrouillage du fichier qui empêche temporairement le calcul d’un nouveau hachage sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="d513c-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="d513c-148">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="d513c-148">*Utilities/Utilities.cs* :</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="d513c-149">Jeton de modification de démarrage simple</span><span class="sxs-lookup"><span data-stu-id="d513c-149">Simple startup change token</span></span>

<span data-ttu-id="d513c-150">Inscrivez un rappel `Action` de consommateur de jeton pour les notifications de modification au jeton de rechargement de configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="d513c-151">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d513c-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="d513c-152">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="d513c-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="d513c-153">Le rappel est la méthode `InvokeChanged` :</span><span class="sxs-lookup"><span data-stu-id="d513c-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="d513c-154">Le `state` du rappel est utilisé pour transmettre le `IWebHostEnvironment`, ce qui est utile pour spécifier le bon fichier de configuration *appsettings* à surveiller (par exemple, *appsettings.Development.json* dans l’environnement de développement).</span><span class="sxs-lookup"><span data-stu-id="d513c-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="d513c-155">Les hachages de fichier sont utilisés pour empêcher l’instruction `WriteConsole` d’être exécutée plusieurs fois en raison de plusieurs rappels de jeton alors que le fichier de configuration n’a été modifié qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="d513c-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="d513c-156">Ce système s’exécute tant que l’application est en cours d’exécution et ne peut pas être désactivé par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d513c-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="d513c-157">Surveiller les modifications de configuration en tant que service</span><span class="sxs-lookup"><span data-stu-id="d513c-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="d513c-158">L’exemple implémente :</span><span class="sxs-lookup"><span data-stu-id="d513c-158">The sample implements:</span></span>

* <span data-ttu-id="d513c-159">Surveillance de jeton du démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="d513c-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="d513c-160">Surveillance en tant que service.</span><span class="sxs-lookup"><span data-stu-id="d513c-160">Monitoring as a service.</span></span>
* <span data-ttu-id="d513c-161">Un mécanisme pour activer et désactiver la surveillance.</span><span class="sxs-lookup"><span data-stu-id="d513c-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="d513c-162">L’exemple établit une interface `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="d513c-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="d513c-163">*Extensions/Configurationmonitor.cs*  :</span><span class="sxs-lookup"><span data-stu-id="d513c-163">*Extensions/ConfigurationMonitor.cs* :</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="d513c-164">Le constructeur de la classe implémentée, `ConfigurationMonitor`, inscrit un rappel pour les notifications de modification :</span><span class="sxs-lookup"><span data-stu-id="d513c-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="d513c-165">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="d513c-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="d513c-166">`InvokeChanged` est la méthode de rappel.</span><span class="sxs-lookup"><span data-stu-id="d513c-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="d513c-167">Le `state` dans cette instance est une référence à l’instance `IConfigurationMonitor` qui est utilisée pour accéder à l’état du monitoring.</span><span class="sxs-lookup"><span data-stu-id="d513c-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="d513c-168">Deux propriétés sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="d513c-168">Two properties are used:</span></span>

* <span data-ttu-id="d513c-169">`MonitoringEnabled`: Indique si le rappel doit exécuter son code personnalisé.</span><span class="sxs-lookup"><span data-stu-id="d513c-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="d513c-170">`CurrentState`: Décrit l’état d’analyse actuel pour une utilisation dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d513c-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="d513c-171">La méthode `InvokeChanged` est similaire à l’approche précédente, excepté que :</span><span class="sxs-lookup"><span data-stu-id="d513c-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="d513c-172">Elle n’exécute pas son code, sauf si `MonitoringEnabled` est `true`.</span><span class="sxs-lookup"><span data-stu-id="d513c-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="d513c-173">Elle indique le `state` actuel dans sa sortie `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="d513c-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="d513c-174">Une instance `ConfigurationMonitor` est inscrite en tant que service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d513c-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="d513c-175">La page Index permet à l’utilisateur de contrôler la surveillance de la configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="d513c-176">L’instance de `IConfigurationMonitor` est injectée dans le `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="d513c-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="d513c-177">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="d513c-177">*Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d513c-178">Le moniteur de configuration (`_monitor`) est utilisé pour activer ou désactiver l’analyse et définir l’état actuel pour les commentaires sur l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="d513c-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="d513c-179">Quand `OnPostStartMonitoring` est déclenché, la surveillance est activée et l’état actuel est effacé.</span><span class="sxs-lookup"><span data-stu-id="d513c-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="d513c-180">Quand `OnPostStopMonitoring` est déclenché, la surveillance est désactivée et l’état est défini de façon à indiquer que la surveillance n’est pas effectuée.</span><span class="sxs-lookup"><span data-stu-id="d513c-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="d513c-181">Des boutons dans l’IU activent et désactivent la surveillance.</span><span class="sxs-lookup"><span data-stu-id="d513c-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="d513c-182">*Pages/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="d513c-182">*Pages/Index.cshtml* :</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="d513c-183">Surveiller les modifications de fichiers mis en cache</span><span class="sxs-lookup"><span data-stu-id="d513c-183">Monitor cached file changes</span></span>

<span data-ttu-id="d513c-184">Le contenu des fichiers peut être mis en cache en mémoire avec <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="d513c-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="d513c-185">La mise en cache en mémoire est décrite dans la rubrique [Mise en cache en mémoire](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="d513c-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="d513c-186">Sans actions supplémentaires, comme l’implémentation décrite ci-dessous, les données *périmées* (obsolètes) sont retournées depuis un cache si la source de données change.</span><span class="sxs-lookup"><span data-stu-id="d513c-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="d513c-187">Par exemple, le fait de ne pas prendre en compte l’état d’un fichier source en cache lors du renouvellement d’une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) rend obsolètes les données du fichier mis cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="d513c-188">Chaque demande de données renouvelle la période d’expiration décalée, mais le fichier n’est jamais rechargé dans le cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="d513c-189">Toutes les fonctionnalités d’une application qui utilisent le contenu mis en cache d’un fichier sont exposées au risque de recevoir du contenu obsolète.</span><span class="sxs-lookup"><span data-stu-id="d513c-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="d513c-190">L’utilisation de jetons de modification dans un scénario de mise en cache de fichier empêche la présence de contenu obsolète dans le cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="d513c-191">L’exemple d’application montre une implémentation de l’approche.</span><span class="sxs-lookup"><span data-stu-id="d513c-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="d513c-192">L’exemple utilise `GetFileContent` pour :</span><span class="sxs-lookup"><span data-stu-id="d513c-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="d513c-193">Retourner le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="d513c-193">Return file content.</span></span>
* <span data-ttu-id="d513c-194">Implémenter un algorithme de nouvelle tentative avec interruption exponentielle pour couvrir les cas où un verrou de fichier empêche temporairement la lecture d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="d513c-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="d513c-195">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="d513c-195">*Utilities/Utilities.cs* :</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="d513c-196">Un `FileService` est créé pour gérer les recherches des fichiers mis en cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="d513c-197">L’appel de la méthode `GetFileContent` du service tente d’obtenir le contenu du fichier à partir du cache en mémoire et le retourne à l’appelant ( *Services/FileService.cs* ).</span><span class="sxs-lookup"><span data-stu-id="d513c-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller ( *Services/FileService.cs* ).</span></span>

<span data-ttu-id="d513c-198">Si le contenu en cache n’est pas trouvé avec la clé du cache, les actions suivantes sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="d513c-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="d513c-199">Le contenu du fichier est obtenu à l’aide de `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="d513c-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="d513c-200">Un jeton de modification est obtenu auprès du fournisseur du fichier avec [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="d513c-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="d513c-201">Le rappel du jeton est déclenché quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="d513c-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="d513c-202">Le contenu du fichier est mis en cache avec une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="d513c-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="d513c-203">Le jeton de modification est attaché avec [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) pour supprimer l’entrée du cache si le fichier change alors qu’il est mis en cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="d513c-204">Dans l’exemple suivant, les fichiers sont stockés dans la [racine de contenu](xref:fundamentals/index#content-root)de l’application.</span><span class="sxs-lookup"><span data-stu-id="d513c-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="d513c-205">`IWebHostEnvironment.ContentRootFileProvider` est utilisé pour obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointage au niveau de l’application `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="d513c-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="d513c-206">`filePath` est obtenue avec [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="d513c-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="d513c-207">`FileService` est inscrit dans le conteneur de service, ainsi que le service de mise en cache en mémoire.</span><span class="sxs-lookup"><span data-stu-id="d513c-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="d513c-208">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d513c-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="d513c-209">Le modèle de page charge le contenu du fichier en utilisant le service.</span><span class="sxs-lookup"><span data-stu-id="d513c-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="d513c-210">Dans la méthode `OnGet` de la page Index ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="d513c-210">In the Index page's `OnGet` method ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="d513c-211">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="d513c-211">CompositeChangeToken class</span></span>

<span data-ttu-id="d513c-212">Pour représenter une ou plusieurs instances de `IChangeToken` dans un même objet, utilisez la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="d513c-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="d513c-213">`HasChanged` sur le jeton composite indique `true` si l’élément `HasChanged` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="d513c-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="d513c-214">`ActiveChangeCallbacks` sur le jeton composite indique `true` si l’élément `ActiveChangeCallbacks` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="d513c-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="d513c-215">Si plusieurs modifications simultanées se produisent, le rappel de modification composite n’est appelé qu’une fois.</span><span class="sxs-lookup"><span data-stu-id="d513c-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d513c-216">Un *jeton de modification* est un module à usage général de bas niveau, utilisé pour effectuer le suivi des modifications de l’état.</span><span class="sxs-lookup"><span data-stu-id="d513c-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="d513c-217">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d513c-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="d513c-218">Interface d’IChangeToken</span><span class="sxs-lookup"><span data-stu-id="d513c-218">IChangeToken interface</span></span>

<span data-ttu-id="d513c-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propage des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="d513c-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="d513c-220">`IChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d513c-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="d513c-221">Pour les applications qui n’utilisent pas le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), créez une référence au package NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="d513c-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="d513c-222">`IChangeToken` a deux propriétés :</span><span class="sxs-lookup"><span data-stu-id="d513c-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="d513c-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indique si le jeton déclenche des rappels de façon proactive.</span><span class="sxs-lookup"><span data-stu-id="d513c-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="d513c-224">Si `ActiveChangedCallbacks` est défini sur `false`, un rappel n’est jamais appelé, et l’application doit interroger `HasChanged` pour connaître les modifications.</span><span class="sxs-lookup"><span data-stu-id="d513c-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="d513c-225">Il est également possible qu’un jeton ne soit jamais annulé si aucune modification ne se produit, ou si l’écouteur de modifications sous-jacent est supprimé ou désactivé.</span><span class="sxs-lookup"><span data-stu-id="d513c-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="d513c-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> obtient une valeur qui indique si une modification a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="d513c-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="d513c-227">L' `IChangeToken` interface inclut la méthode [RegisterChangeCallback (action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , qui inscrit un rappel appelé lorsque le jeton a changé.</span><span class="sxs-lookup"><span data-stu-id="d513c-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="d513c-228">`HasChanged` doit être défini avant que le rappel soit appelé.</span><span class="sxs-lookup"><span data-stu-id="d513c-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="d513c-229">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="d513c-229">ChangeToken class</span></span>

<span data-ttu-id="d513c-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> est une classe statique utilisée pour propager des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="d513c-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="d513c-231">`ChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d513c-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="d513c-232">Pour les applications qui n’utilisent pas le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), créez une référence au package NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="d513c-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="d513c-233">La méthode [ChangeToken. OnChange (Func \<IChangeToken> , action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) inscrit un `Action` à appeler chaque fois que le jeton change :</span><span class="sxs-lookup"><span data-stu-id="d513c-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="d513c-234">`Func<IChangeToken>` produit le jeton.</span><span class="sxs-lookup"><span data-stu-id="d513c-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="d513c-235">`Action` est appelée quand le jeton change.</span><span class="sxs-lookup"><span data-stu-id="d513c-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="d513c-236">La surcharge [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) prend un paramètre supplémentaire `TState` passé dans le consommateur de jetons `Action` .</span><span class="sxs-lookup"><span data-stu-id="d513c-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="d513c-237">`OnChange`Retourne un <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="d513c-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="d513c-238">L’appel de <xref:System.IDisposable.Dispose*> fait que le jeton cesse d’être à l’écoute des modifications suivantes et libère les ressources du jeton.</span><span class="sxs-lookup"><span data-stu-id="d513c-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="d513c-239">Exemples d’utilisation de jetons de modification dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d513c-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="d513c-240">Les jetons de modification sont utilisés dans des zones importantes d’ASP.NET Core pour surveiller les modifications apportées aux objets :</span><span class="sxs-lookup"><span data-stu-id="d513c-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="d513c-241">Pour surveiller les modifications apportées aux fichiers, la méthode <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crée un `IChangeToken` pour les fichiers ou le dossier à surveiller.</span><span class="sxs-lookup"><span data-stu-id="d513c-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="d513c-242">Les jetons `IChangeToken` peuvent être ajoutés aux entrées du cache pour déclencher des suppressions dans le cache en cas de modification.</span><span class="sxs-lookup"><span data-stu-id="d513c-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="d513c-243">Pour les modifications `TOptions`, l’implémentation <xref:Microsoft.Extensions.Options.OptionsMonitor`1> par défaut de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> a une surcharge qui accepte une ou plusieurs instances <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="d513c-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="d513c-244">Chaque instance retourne un `IChangeToken` pour inscrire un rappel de notification de modification pour les modifications des options de suivi.</span><span class="sxs-lookup"><span data-stu-id="d513c-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="d513c-245">Surveiller les modifications de configuration</span><span class="sxs-lookup"><span data-stu-id="d513c-245">Monitor for configuration changes</span></span>

<span data-ttu-id="d513c-246">Par défaut, les modèles de ASP.net Core utilisent des [fichiers de configuration JSON](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.jssous* et *appsettings.Production.js* ) pour charger les paramètres de configuration des applications.</span><span class="sxs-lookup"><span data-stu-id="d513c-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.json* , and *appsettings.Production.json* ) to load app configuration settings.</span></span>

<span data-ttu-id="d513c-247">Ces fichiers sont configurés avec la méthode d’extension [AddJsonFile(IConfigurationBuilder, chaîne, booléen, booléen)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) sur <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> qui accepte un paramètre `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="d513c-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="d513c-248">`reloadOnChange` indique si la configuration doit être rechargée en cas de modification d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="d513c-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="d513c-249">Ce paramètre s’affiche dans la méthode pratique <xref:Microsoft.AspNetCore.WebHost><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="d513c-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="d513c-250">La configuration basée sur les fichiers est représentée par <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="d513c-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="d513c-251">`FileConfigurationSource` utilise <xref:Microsoft.Extensions.FileProviders.IFileProvider> pour surveiller les fichiers.</span><span class="sxs-lookup"><span data-stu-id="d513c-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="d513c-252">Par défaut, `IFileMonitor` est fourni par un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, qui utilise <xref:System.IO.FileSystemWatcher> pour surveiller les modifications des fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="d513c-253">L’exemple d’application montre les deux implémentations de la surveillance des modifications de configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="d513c-254">Si l’un des fichiers *appsettings* change, les deux implémentations d’analyse du fichier exécutent du code personnalisé&mdash;l’exemple d’application écrit un message dans la console.</span><span class="sxs-lookup"><span data-stu-id="d513c-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="d513c-255">Le `FileSystemWatcher` d’un fichier de configuration peut déclencher plusieurs rappels de jeton pour une même modification du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="d513c-256">Pour vous assurer que le code personnalisé n’est exécuté qu’une seule fois lors du déclenchement de plusieurs rappels de jeton, l’implémentation de l’exemple vérifie les hachages des fichiers.</span><span class="sxs-lookup"><span data-stu-id="d513c-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="d513c-257">L’exemple utilise le hachage de fichier SHA1.</span><span class="sxs-lookup"><span data-stu-id="d513c-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="d513c-258">Une nouvelle tentative est implémentée avec une interruption d’une durée exponentielle.</span><span class="sxs-lookup"><span data-stu-id="d513c-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="d513c-259">Une nouvelle tentative est effectuée, car il peut se produire un verrouillage du fichier qui empêche temporairement le calcul d’un nouveau hachage sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="d513c-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="d513c-260">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="d513c-260">*Utilities/Utilities.cs* :</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="d513c-261">Jeton de modification de démarrage simple</span><span class="sxs-lookup"><span data-stu-id="d513c-261">Simple startup change token</span></span>

<span data-ttu-id="d513c-262">Inscrivez un rappel `Action` de consommateur de jeton pour les notifications de modification au jeton de rechargement de configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="d513c-263">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d513c-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="d513c-264">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="d513c-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="d513c-265">Le rappel est la méthode `InvokeChanged` :</span><span class="sxs-lookup"><span data-stu-id="d513c-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="d513c-266">Le `state` du rappel est utilisé pour transmettre le `IHostingEnvironment`, ce qui est utile pour spécifier le bon fichier de configuration *appsettings* à surveiller (par exemple, *appsettings.Development.json* dans l’environnement de développement).</span><span class="sxs-lookup"><span data-stu-id="d513c-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="d513c-267">Les hachages de fichier sont utilisés pour empêcher l’instruction `WriteConsole` d’être exécutée plusieurs fois en raison de plusieurs rappels de jeton alors que le fichier de configuration n’a été modifié qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="d513c-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="d513c-268">Ce système s’exécute tant que l’application est en cours d’exécution et ne peut pas être désactivé par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d513c-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="d513c-269">Surveiller les modifications de configuration en tant que service</span><span class="sxs-lookup"><span data-stu-id="d513c-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="d513c-270">L’exemple implémente :</span><span class="sxs-lookup"><span data-stu-id="d513c-270">The sample implements:</span></span>

* <span data-ttu-id="d513c-271">Surveillance de jeton du démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="d513c-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="d513c-272">Surveillance en tant que service.</span><span class="sxs-lookup"><span data-stu-id="d513c-272">Monitoring as a service.</span></span>
* <span data-ttu-id="d513c-273">Un mécanisme pour activer et désactiver la surveillance.</span><span class="sxs-lookup"><span data-stu-id="d513c-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="d513c-274">L’exemple établit une interface `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="d513c-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="d513c-275">*Extensions/Configurationmonitor.cs*  :</span><span class="sxs-lookup"><span data-stu-id="d513c-275">*Extensions/ConfigurationMonitor.cs* :</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="d513c-276">Le constructeur de la classe implémentée, `ConfigurationMonitor`, inscrit un rappel pour les notifications de modification :</span><span class="sxs-lookup"><span data-stu-id="d513c-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="d513c-277">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="d513c-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="d513c-278">`InvokeChanged` est la méthode de rappel.</span><span class="sxs-lookup"><span data-stu-id="d513c-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="d513c-279">Le `state` dans cette instance est une référence à l’instance `IConfigurationMonitor` qui est utilisée pour accéder à l’état du monitoring.</span><span class="sxs-lookup"><span data-stu-id="d513c-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="d513c-280">Deux propriétés sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="d513c-280">Two properties are used:</span></span>

* <span data-ttu-id="d513c-281">`MonitoringEnabled`: Indique si le rappel doit exécuter son code personnalisé.</span><span class="sxs-lookup"><span data-stu-id="d513c-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="d513c-282">`CurrentState`: Décrit l’état d’analyse actuel pour une utilisation dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d513c-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="d513c-283">La méthode `InvokeChanged` est similaire à l’approche précédente, excepté que :</span><span class="sxs-lookup"><span data-stu-id="d513c-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="d513c-284">Elle n’exécute pas son code, sauf si `MonitoringEnabled` est `true`.</span><span class="sxs-lookup"><span data-stu-id="d513c-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="d513c-285">Elle indique le `state` actuel dans sa sortie `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="d513c-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="d513c-286">Une instance `ConfigurationMonitor` est inscrite en tant que service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d513c-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="d513c-287">La page Index permet à l’utilisateur de contrôler la surveillance de la configuration.</span><span class="sxs-lookup"><span data-stu-id="d513c-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="d513c-288">L’instance de `IConfigurationMonitor` est injectée dans le `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="d513c-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="d513c-289">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="d513c-289">*Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d513c-290">Le moniteur de configuration (`_monitor`) est utilisé pour activer ou désactiver l’analyse et définir l’état actuel pour les commentaires sur l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="d513c-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="d513c-291">Quand `OnPostStartMonitoring` est déclenché, la surveillance est activée et l’état actuel est effacé.</span><span class="sxs-lookup"><span data-stu-id="d513c-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="d513c-292">Quand `OnPostStopMonitoring` est déclenché, la surveillance est désactivée et l’état est défini de façon à indiquer que la surveillance n’est pas effectuée.</span><span class="sxs-lookup"><span data-stu-id="d513c-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="d513c-293">Des boutons dans l’IU activent et désactivent la surveillance.</span><span class="sxs-lookup"><span data-stu-id="d513c-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="d513c-294">*Pages/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="d513c-294">*Pages/Index.cshtml* :</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="d513c-295">Surveiller les modifications de fichiers mis en cache</span><span class="sxs-lookup"><span data-stu-id="d513c-295">Monitor cached file changes</span></span>

<span data-ttu-id="d513c-296">Le contenu des fichiers peut être mis en cache en mémoire avec <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="d513c-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="d513c-297">La mise en cache en mémoire est décrite dans la rubrique [Mise en cache en mémoire](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="d513c-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="d513c-298">Sans actions supplémentaires, comme l’implémentation décrite ci-dessous, les données *périmées* (obsolètes) sont retournées depuis un cache si la source de données change.</span><span class="sxs-lookup"><span data-stu-id="d513c-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="d513c-299">Par exemple, le fait de ne pas prendre en compte l’état d’un fichier source en cache lors du renouvellement d’une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) rend obsolètes les données du fichier mis cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="d513c-300">Chaque demande de données renouvelle la période d’expiration décalée, mais le fichier n’est jamais rechargé dans le cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="d513c-301">Toutes les fonctionnalités d’une application qui utilisent le contenu mis en cache d’un fichier sont exposées au risque de recevoir du contenu obsolète.</span><span class="sxs-lookup"><span data-stu-id="d513c-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="d513c-302">L’utilisation de jetons de modification dans un scénario de mise en cache de fichier empêche la présence de contenu obsolète dans le cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="d513c-303">L’exemple d’application montre une implémentation de l’approche.</span><span class="sxs-lookup"><span data-stu-id="d513c-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="d513c-304">L’exemple utilise `GetFileContent` pour :</span><span class="sxs-lookup"><span data-stu-id="d513c-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="d513c-305">Retourner le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="d513c-305">Return file content.</span></span>
* <span data-ttu-id="d513c-306">Implémenter un algorithme de nouvelle tentative avec interruption exponentielle pour couvrir les cas où un verrou de fichier empêche temporairement la lecture d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="d513c-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="d513c-307">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="d513c-307">*Utilities/Utilities.cs* :</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="d513c-308">Un `FileService` est créé pour gérer les recherches des fichiers mis en cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="d513c-309">L’appel de la méthode `GetFileContent` du service tente d’obtenir le contenu du fichier à partir du cache en mémoire et le retourne à l’appelant ( *Services/FileService.cs* ).</span><span class="sxs-lookup"><span data-stu-id="d513c-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller ( *Services/FileService.cs* ).</span></span>

<span data-ttu-id="d513c-310">Si le contenu en cache n’est pas trouvé avec la clé du cache, les actions suivantes sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="d513c-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="d513c-311">Le contenu du fichier est obtenu à l’aide de `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="d513c-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="d513c-312">Un jeton de modification est obtenu auprès du fournisseur du fichier avec [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="d513c-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="d513c-313">Le rappel du jeton est déclenché quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="d513c-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="d513c-314">Le contenu du fichier est mis en cache avec une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="d513c-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="d513c-315">Le jeton de modification est attaché avec [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) pour supprimer l’entrée du cache si le fichier change alors qu’il est mis en cache.</span><span class="sxs-lookup"><span data-stu-id="d513c-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="d513c-316">Dans l’exemple suivant, les fichiers sont stockés dans la [racine de contenu](xref:fundamentals/index#content-root)de l’application.</span><span class="sxs-lookup"><span data-stu-id="d513c-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="d513c-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) est utilisé pour obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointant vers <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> de l’application.</span><span class="sxs-lookup"><span data-stu-id="d513c-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="d513c-318">`filePath` est obtenue avec [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="d513c-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="d513c-319">`FileService` est inscrit dans le conteneur de service, ainsi que le service de mise en cache en mémoire.</span><span class="sxs-lookup"><span data-stu-id="d513c-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="d513c-320">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d513c-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="d513c-321">Le modèle de page charge le contenu du fichier en utilisant le service.</span><span class="sxs-lookup"><span data-stu-id="d513c-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="d513c-322">Dans la méthode `OnGet` de la page Index ( *Pages/Index.cshtml.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="d513c-322">In the Index page's `OnGet` method ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="d513c-323">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="d513c-323">CompositeChangeToken class</span></span>

<span data-ttu-id="d513c-324">Pour représenter une ou plusieurs instances de `IChangeToken` dans un même objet, utilisez la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="d513c-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="d513c-325">`HasChanged` sur le jeton composite indique `true` si l’élément `HasChanged` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="d513c-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="d513c-326">`ActiveChangeCallbacks` sur le jeton composite indique `true` si l’élément `ActiveChangeCallbacks` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="d513c-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="d513c-327">Si plusieurs modifications simultanées se produisent, le rappel de modification composite n’est appelé qu’une fois.</span><span class="sxs-lookup"><span data-stu-id="d513c-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d513c-328">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d513c-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
