---
title: Création et redirection des journaux avec le module ASP.NET Core
author: rick-anderson
description: Configurez IIS et le module ASP.NET Core pour capturer des journaux et des informations de diagnostic.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057438"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="cf6c4-103">Création et redirection de journal</span><span class="sxs-lookup"><span data-stu-id="cf6c4-103">Log creation and redirection</span></span>

<span data-ttu-id="cf6c4-104">Le module ASP.NET Core redirige la sortie de console stdout et stderr vers le disque si les attributs `stdoutLogEnabled` et `stdoutLogFile` de l’élément `aspNetCore` sont définis.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="cf6c4-105">Tous les dossiers du chemin `stdoutLogFile` sont créés par le module au moment de la création du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="cf6c4-106">Le pool d’applications doit avoir un accès en écriture à l’emplacement où les journaux sont écrits (utilisez `IIS AppPool\{APP POOL NAME}` pour fournir une autorisation d’écriture, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications).</span><span class="sxs-lookup"><span data-stu-id="cf6c4-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="cf6c4-107">Aucune rotation n’est appliquée aux journaux, sauf en cas de recyclage/redémarrage du processus.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="cf6c4-108">Il incombe à l’hébergeur de limiter l’espace disque utilisé par les journaux.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="cf6c4-109">L’utilisation du journal stdout est recommandée uniquement pour résoudre les problèmes de démarrage d’application lors de l’hébergement sur IIS ou lors de l’utilisation [de la prise en charge au moment du développement pour IIS avec Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), et non pendant le débogage local et l’exécution de l’application avec IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="cf6c4-110">N’utilisez pas le journal stdout à des fins de journalisation d’application générale.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="cf6c4-111">Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="cf6c4-112">Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="cf6c4-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="cf6c4-113">Un horodatage et une extension de fichier sont ajoutés automatiquement quand le fichier journal est créé.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="cf6c4-114">Le nom du fichier journal est composé en ajoutant l’horodateur, l’ID de processus et l’extension de fichier ( `.log` ) au dernier segment du `stdoutLogFile` chemin (généralement `stdout` ) délimité par des traits de soulignement.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="cf6c4-115">Si le `stdoutLogFile` chemin d’accès se termine par `stdout` , un journal pour une application avec un PID de 1934 créé le 2/5/2018 à 19:42:32 a le nom de fichier `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="cf6c4-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="cf6c4-116">Si `stdoutLogEnabled` a la valeur false, les erreurs qui se produisent au moment du démarrage de l’application sont capturées et émises dans le journal des événements (30 Ko maximum).</span><span class="sxs-lookup"><span data-stu-id="cf6c4-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="cf6c4-117">Après le démarrage, tous les journaux supplémentaires sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="cf6c4-118">L’exemple `aspNetCore` d’élément suivant configure la journalisation stdout sur le chemin d’accès relatif `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="cf6c4-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="cf6c4-119">Vérifiez que l’identité de l’utilisateur AppPool à l’autorisation d’écrire dans le chemin d’accès fourni.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="cf6c4-120">Lors de la publication d’une application pour le déploiement Azure App Service, le kit de développement logiciel (SDK) Web définit la `stdoutLogFile` valeur sur `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="cf6c4-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cf6c4-121">La `%home` variable d’environnement est prédéfinie pour les applications hébergées par Azure App service.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="cf6c4-122">Pour créer des règles de filtre de journalisation, consultez les sections [configuration](xref:fundamentals/logging/index#log-filtering) et [filtrage des journaux](xref:fundamentals/logging/index#log-filtering) de la documentation ASP.net Core Logging.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="cf6c4-123">Pour plus d’informations sur les formats de chemin d’accès, consultez [formats de chemin d’accès de fichier sur les systèmes Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="cf6c4-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="cf6c4-124">Journaux de diagnostic améliorés</span><span class="sxs-lookup"><span data-stu-id="cf6c4-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="cf6c4-125">Le module ASP.NET Core est configurable pour proposer des journaux de diagnostic améliorés.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="cf6c4-126">Ajoutez l' `<handlerSettings>` élément à l' `<aspNetCore>` élément dans `web.config` .</span><span class="sxs-lookup"><span data-stu-id="cf6c4-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="cf6c4-127">L’affectation de la valeur `TRACE` à `debugLevel` expose une fidélité plus élevée des informations de diagnostic :</span><span class="sxs-lookup"><span data-stu-id="cf6c4-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="cf6c4-128">Tous les dossiers dans le chemin d’accès ( `logs` dans l’exemple précédent) sont créés par le module lors de la création du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="cf6c4-129">Le pool d’applications doit avoir un accès en écriture à l’emplacement où les journaux sont écrits (utilisez `IIS AppPool\{APP POOL NAME}` pour fournir une autorisation d’écriture, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications).</span><span class="sxs-lookup"><span data-stu-id="cf6c4-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="cf6c4-130">Les valeurs du niveau de débogage (`debugLevel`) peuvent inclure à la fois le niveau et l’emplacement.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="cf6c4-131">Niveaux (dans l’ordre du moins au plus détaillé) :</span><span class="sxs-lookup"><span data-stu-id="cf6c4-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="cf6c4-132">ERROR</span><span class="sxs-lookup"><span data-stu-id="cf6c4-132">ERROR</span></span>
* <span data-ttu-id="cf6c4-133">WARNING</span><span class="sxs-lookup"><span data-stu-id="cf6c4-133">WARNING</span></span>
* <span data-ttu-id="cf6c4-134">INFO</span><span class="sxs-lookup"><span data-stu-id="cf6c4-134">INFO</span></span>
* <span data-ttu-id="cf6c4-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="cf6c4-135">TRACE</span></span>

<span data-ttu-id="cf6c4-136">Emplacements (plusieurs emplacements sont autorisés) :</span><span class="sxs-lookup"><span data-stu-id="cf6c4-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="cf6c4-137">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="cf6c4-137">CONSOLE</span></span>
* <span data-ttu-id="cf6c4-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="cf6c4-138">EVENTLOG</span></span>
* <span data-ttu-id="cf6c4-139">FILE</span><span class="sxs-lookup"><span data-stu-id="cf6c4-139">FILE</span></span>

<span data-ttu-id="cf6c4-140">Les paramètres de gestionnaire peuvent également être fournis par le biais de variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="cf6c4-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="cf6c4-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Chemin d’accès au fichier journal de débogage.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="cf6c4-142">(Par défaut : `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="cf6c4-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="cf6c4-143">`ASPNETCORE_MODULE_DEBUG`: Paramètre de niveau de débogage.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="cf6c4-144">Ne laissez **pas** la journalisation du débogage activée dans le déploiement plus longtemps que nécessaire pour résoudre un problème.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="cf6c4-145">La taille du journal n’est pas limitée.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-145">The size of the log isn't limited.</span></span> <span data-ttu-id="cf6c4-146">Si vous laissez la journalisation du débogage activée, vous risquez d’épuiser l’espace disque disponible et de bloquer le serveur ou le service d’application.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="cf6c4-147">Consultez [configuration de ASP.net Core Module avec `web.config` ](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) pour obtenir un exemple de l' `aspNetCore` élément dans le `web.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="cf6c4-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
