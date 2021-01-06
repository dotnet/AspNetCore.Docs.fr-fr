---
title: Journalisation et diagnostics dans gRPC sur .NET
author: jamesnk
description: Découvrez Comment collecter des diagnostics à partir de votre application gRPC sur .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
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
uid: grpc/diagnostics
ms.openlocfilehash: 1f25ae76e5a480e5e6f247e4ac78d06dd4e778e9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060441"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="b060c-103">Journalisation et diagnostics dans gRPC sur .NET</span><span class="sxs-lookup"><span data-stu-id="b060c-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="b060c-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b060c-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b060c-105">Cet article fournit des conseils pour la collecte des diagnostics à partir d’une application gRPC afin de vous aider à résoudre les problèmes.</span><span class="sxs-lookup"><span data-stu-id="b060c-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="b060c-106">Sont abordés les sujets suivants :</span><span class="sxs-lookup"><span data-stu-id="b060c-106">Topics covered include:</span></span>

* <span data-ttu-id="b060c-107">Journaux structurés en **Journal** écrits dans la [journalisation .net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b060c-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b060c-108"><xref:Microsoft.Extensions.Logging.ILogger> est utilisé par les infrastructures d’application pour écrire des journaux et par les utilisateurs pour leur propre journalisation dans une application.</span><span class="sxs-lookup"><span data-stu-id="b060c-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="b060c-109">**Suivi** -événements liés à une opération écrite à l’aide `DiaganosticSource` de et de `Activity` .</span><span class="sxs-lookup"><span data-stu-id="b060c-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="b060c-110">Les traces à partir de la source de diagnostic sont couramment utilisées pour collecter des données de télémétrie d’application par des bibliothèques telles que [application Insights](/azure/azure-monitor/app/asp-net-core) et [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="b060c-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="b060c-111">**Métriques** : représentation des mesures de données sur des intervalles de temps, par exemple, les demandes par seconde.</span><span class="sxs-lookup"><span data-stu-id="b060c-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="b060c-112">Les métriques sont émises à l’aide `EventCounter` de et peuvent être observées à l’aide de l’outil en ligne de commande [dotnet-Counters](/dotnet/core/diagnostics/dotnet-counters) ou avec [application Insights](/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="b060c-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="b060c-113">Journalisation</span><span class="sxs-lookup"><span data-stu-id="b060c-113">Logging</span></span>

<span data-ttu-id="b060c-114">les services gRPC et le client gRPC écrivent des journaux à l’aide de la [journalisation .net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b060c-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b060c-115">Les journaux sont un bon point de départ lorsque vous avez besoin de déboguer un comportement inattendu dans vos applications.</span><span class="sxs-lookup"><span data-stu-id="b060c-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="b060c-116">journalisation des services gRPC</span><span class="sxs-lookup"><span data-stu-id="b060c-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="b060c-117">Les journaux côté serveur peuvent contenir des informations sensibles de votre application.</span><span class="sxs-lookup"><span data-stu-id="b060c-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b060c-118">**Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="b060c-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b060c-119">Étant donné que les services gRPC sont hébergés sur ASP.NET Core, ils utilisent le système de journalisation ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b060c-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="b060c-120">Dans la configuration par défaut, gRPC enregistre très peu d’informations, mais cela peut être configuré.</span><span class="sxs-lookup"><span data-stu-id="b060c-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="b060c-121">Pour plus d’informations sur la configuration de la journalisation des ASP.NET Core, consultez la documentation sur [ASP.net Core Logging](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="b060c-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="b060c-122">gRPC ajoute des journaux sous la `Grpc` catégorie.</span><span class="sxs-lookup"><span data-stu-id="b060c-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="b060c-123">Pour activer les journaux détaillés à partir de gRPC, configurez les `Grpc` préfixes sur le `Debug` niveau de votre *appsettings.json* fichier en ajoutant les éléments suivants à la `LogLevel` sous-section dans `Logging` :</span><span class="sxs-lookup"><span data-stu-id="b060c-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="b060c-124">Vous pouvez également configurer cette configuration dans *Startup.cs* avec `ConfigureLogging` :</span><span class="sxs-lookup"><span data-stu-id="b060c-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="b060c-125">Si vous n’utilisez pas la configuration basée sur JSON, définissez la valeur de configuration suivante dans votre système de configuration :</span><span class="sxs-lookup"><span data-stu-id="b060c-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="b060c-126">Consultez la documentation de votre système de configuration pour déterminer comment spécifier les valeurs de configuration imbriquées.</span><span class="sxs-lookup"><span data-stu-id="b060c-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="b060c-127">Par exemple, lors de l’utilisation de variables d’environnement, deux `_` caractères sont utilisés à la place de `:` (par exemple, `Logging__LogLevel__Grpc` ).</span><span class="sxs-lookup"><span data-stu-id="b060c-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="b060c-128">Nous vous recommandons d’utiliser le `Debug` niveau lorsque vous collectez des diagnostics plus détaillés pour votre application.</span><span class="sxs-lookup"><span data-stu-id="b060c-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="b060c-129">Le `Trace` niveau produit des diagnostics de bas niveau et est rarement nécessaire pour diagnostiquer les problèmes dans votre application.</span><span class="sxs-lookup"><span data-stu-id="b060c-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="b060c-130">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="b060c-130">Sample logging output</span></span>

<span data-ttu-id="b060c-131">Voici un exemple de sortie de console au `Debug` niveau d’un service gRPC :</span><span class="sxs-lookup"><span data-stu-id="b060c-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="b060c-132">Accéder aux journaux côté serveur</span><span class="sxs-lookup"><span data-stu-id="b060c-132">Access server-side logs</span></span>

<span data-ttu-id="b060c-133">La façon dont vous accédez aux journaux côté serveur dépend de l’environnement dans lequel vous exécutez.</span><span class="sxs-lookup"><span data-stu-id="b060c-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="b060c-134">En tant qu’application console</span><span class="sxs-lookup"><span data-stu-id="b060c-134">As a console app</span></span>

<span data-ttu-id="b060c-135">Si vous exécutez dans une application console, l’enregistreur d’événements de [console](xref:fundamentals/logging/index#console) doit être activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="b060c-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="b060c-136">les journaux gRPC s’affichent dans la console.</span><span class="sxs-lookup"><span data-stu-id="b060c-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="b060c-137">Autres environnements</span><span class="sxs-lookup"><span data-stu-id="b060c-137">Other environments</span></span>

<span data-ttu-id="b060c-138">Si l’application est déployée dans un autre environnement (par exemple, docker, Kubernetes ou service Windows), consultez <xref:fundamentals/logging/index> pour plus d’informations sur la configuration des fournisseurs de journalisation appropriés pour l’environnement.</span><span class="sxs-lookup"><span data-stu-id="b060c-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="b060c-139">journalisation du client gRPC</span><span class="sxs-lookup"><span data-stu-id="b060c-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b060c-140">Les journaux côté client peuvent contenir des informations sensibles de votre application.</span><span class="sxs-lookup"><span data-stu-id="b060c-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b060c-141">**Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="b060c-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b060c-142">Pour récupérer des journaux à partir du client .NET, vous pouvez définir la `GrpcChannelOptions.LoggerFactory` propriété lorsque le canal du client est créé.</span><span class="sxs-lookup"><span data-stu-id="b060c-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="b060c-143">Si vous appelez un service gRPC à partir d’une application ASP.NET Core, la fabrique d’enregistreur peut être résolue à partir de l’injection de dépendances (DI) :</span><span class="sxs-lookup"><span data-stu-id="b060c-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="b060c-144">Une autre façon d’activer la journalisation du client consiste à utiliser la [fabrique de clients gRPC](xref:grpc/clientfactory) pour créer le client.</span><span class="sxs-lookup"><span data-stu-id="b060c-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="b060c-145">Un client gRPC inscrit auprès de la fabrique de clients et résolu à partir de DI utilisera automatiquement la journalisation configurée de l’application.</span><span class="sxs-lookup"><span data-stu-id="b060c-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="b060c-146">Si votre application n’utilise pas DI, vous pouvez créer une nouvelle `ILoggerFactory` instance avec [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="b060c-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="b060c-147">Pour accéder à cette méthode, ajoutez le package [Microsoft. extensions. logginging](https://www.nuget.org/packages/microsoft.extensions.logging/) à votre application.</span><span class="sxs-lookup"><span data-stu-id="b060c-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="b060c-148">étendues du journal du client gRPC</span><span class="sxs-lookup"><span data-stu-id="b060c-148">gRPC client log scopes</span></span>

<span data-ttu-id="b060c-149">Le client gRPC ajoute une [étendue de journalisation](../fundamentals/logging/index.md#log-scopes) aux journaux effectués pendant un appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="b060c-149">The gRPC client adds a [logging scope](../fundamentals/logging/index.md#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="b060c-150">L’étendue a des métadonnées liées à l’appel gRPC :</span><span class="sxs-lookup"><span data-stu-id="b060c-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="b060c-151">**GrpcMethodType** : type de méthode gRPC.</span><span class="sxs-lookup"><span data-stu-id="b060c-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="b060c-152">Les valeurs possibles sont les noms de l' `Grpc.Core.MethodType` enum, par exemple unaire</span><span class="sxs-lookup"><span data-stu-id="b060c-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="b060c-153">**GrpcUri** : URI relatif de la méthode gRPC, par exemple/Greet. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="b060c-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="b060c-154">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="b060c-154">Sample logging output</span></span>

<span data-ttu-id="b060c-155">Voici un exemple de sortie de console au `Debug` niveau d’un client gRPC :</span><span class="sxs-lookup"><span data-stu-id="b060c-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="b060c-156">Traçage</span><span class="sxs-lookup"><span data-stu-id="b060c-156">Tracing</span></span>

<span data-ttu-id="b060c-157">les services gRPC et le client gRPC fournissent des informations sur les appels gRPC à l’aide de [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) et de l' [activité](/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="b060c-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="b060c-158">.NET gRPC utilise une activité pour représenter un appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="b060c-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="b060c-159">Les événements de suivi sont écrits dans la source de diagnostic au démarrage et à l’arrêt de l’activité d’appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="b060c-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="b060c-160">Le suivi ne capture pas d’informations sur le moment où les messages sont envoyés pendant la durée de vie des appels de diffusion en continu gRPC.</span><span class="sxs-lookup"><span data-stu-id="b060c-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="b060c-161">suivi du service gRPC</span><span class="sxs-lookup"><span data-stu-id="b060c-161">gRPC service tracing</span></span>

<span data-ttu-id="b060c-162">les services gRPC sont hébergés sur ASP.NET Core qui signale les événements relatifs aux requêtes HTTP entrantes.</span><span class="sxs-lookup"><span data-stu-id="b060c-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="b060c-163">les métadonnées spécifiques à gRPC sont ajoutées aux diagnostics de requête HTTP existants fournis par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b060c-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="b060c-164">Le nom de la source de diagnostic est `Microsoft.AspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="b060c-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="b060c-165">Le nom de l’activité est `Microsoft.AspNetCore.Hosting.HttpRequestIn` .</span><span class="sxs-lookup"><span data-stu-id="b060c-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="b060c-166">Le nom de la méthode gRPC appelée par l’appel gRPC est ajouté sous la forme d’une balise portant le nom `grpc.method` .</span><span class="sxs-lookup"><span data-stu-id="b060c-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="b060c-167">Le code d’état de l’appel gRPC lorsqu’il est terminé est ajouté sous la forme d’une balise portant le nom `grpc.status_code` .</span><span class="sxs-lookup"><span data-stu-id="b060c-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="b060c-168">suivi du client gRPC</span><span class="sxs-lookup"><span data-stu-id="b060c-168">gRPC client tracing</span></span>

<span data-ttu-id="b060c-169">Le client .NET gRPC utilise `HttpClient` pour effectuer des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="b060c-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="b060c-170">Bien que `HttpClient` écrit des événements de diagnostic, le client .net gRPC fournit une source de diagnostic personnalisée, une activité et des événements pour que des informations complètes sur un appel gRPC puissent être collectées.</span><span class="sxs-lookup"><span data-stu-id="b060c-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="b060c-171">Le nom de la source de diagnostic est `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="b060c-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="b060c-172">Le nom de l’activité est `Grpc.Net.Client.GrpcOut` .</span><span class="sxs-lookup"><span data-stu-id="b060c-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="b060c-173">Le nom de la méthode gRPC appelée par l’appel gRPC est ajouté sous la forme d’une balise portant le nom `grpc.method` .</span><span class="sxs-lookup"><span data-stu-id="b060c-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="b060c-174">Le code d’état de l’appel gRPC lorsqu’il est terminé est ajouté sous la forme d’une balise portant le nom `grpc.status_code` .</span><span class="sxs-lookup"><span data-stu-id="b060c-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="b060c-175">Collecte du suivi</span><span class="sxs-lookup"><span data-stu-id="b060c-175">Collecting tracing</span></span>

<span data-ttu-id="b060c-176">Le moyen le plus simple d’utiliser `DiagnosticSource` est de configurer une bibliothèque de télémétrie telle que [application Insights](/azure/azure-monitor/app/asp-net-core) ou [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) dans votre application.</span><span class="sxs-lookup"><span data-stu-id="b060c-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="b060c-177">La bibliothèque traitera les informations sur les appels gRPC en parallèle d’autres données de télémétrie d’application.</span><span class="sxs-lookup"><span data-stu-id="b060c-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="b060c-178">Le suivi peut être affiché dans un service géré comme Application Insights, ou vous pouvez choisir d’exécuter votre propre système de traçage distribué.</span><span class="sxs-lookup"><span data-stu-id="b060c-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="b060c-179">OpenTelemetry prend en charge l’exportation des données de suivi vers [Jaeger](https://www.jaegertracing.io/) et [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="b060c-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="b060c-180">`DiagnosticSource` peut consommer des événements de suivi dans le code à l’aide de `DiagnosticListener` .</span><span class="sxs-lookup"><span data-stu-id="b060c-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="b060c-181">Pour plus d’informations sur l’écoute d’une source de diagnostic avec du code, consultez le [Guide de l’utilisateur DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="b060c-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="b060c-182">Les bibliothèques de télémétrie ne capturent pas actuellement les données de télémétrie spécifiques à gRPC `Grpc.Net.Client.GrpcOut` .</span><span class="sxs-lookup"><span data-stu-id="b060c-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="b060c-183">Travailler pour améliorer les bibliothèques de télémétrie la capture de ce suivi est en cours.</span><span class="sxs-lookup"><span data-stu-id="b060c-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="b060c-184">Mesures</span><span class="sxs-lookup"><span data-stu-id="b060c-184">Metrics</span></span>

<span data-ttu-id="b060c-185">Les métriques sont une représentation des mesures de données sur des intervalles de temps, par exemple, les demandes par seconde.</span><span class="sxs-lookup"><span data-stu-id="b060c-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="b060c-186">Les données de métriques permettent d’observer l’état d’une application à un niveau élevé.</span><span class="sxs-lookup"><span data-stu-id="b060c-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="b060c-187">Les métriques .NET gRPC sont émises à l’aide de `EventCounter` .</span><span class="sxs-lookup"><span data-stu-id="b060c-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="b060c-188">métriques du service gRPC</span><span class="sxs-lookup"><span data-stu-id="b060c-188">gRPC service metrics</span></span>

<span data-ttu-id="b060c-189">les métriques du serveur gRPC sont signalées sur la source de l' `Grpc.AspNetCore.Server` événement.</span><span class="sxs-lookup"><span data-stu-id="b060c-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="b060c-190">Nom</span><span class="sxs-lookup"><span data-stu-id="b060c-190">Name</span></span>                      | <span data-ttu-id="b060c-191">Description</span><span class="sxs-lookup"><span data-stu-id="b060c-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="b060c-192">Nombre total d’appels</span><span class="sxs-lookup"><span data-stu-id="b060c-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="b060c-193">Appels en cours</span><span class="sxs-lookup"><span data-stu-id="b060c-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="b060c-194">Nombre total d’appels ayant échoué</span><span class="sxs-lookup"><span data-stu-id="b060c-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="b060c-195">Nombre total d’appels échus dépassé</span><span class="sxs-lookup"><span data-stu-id="b060c-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="b060c-196">Nombre total de messages envoyés</span><span class="sxs-lookup"><span data-stu-id="b060c-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="b060c-197">Nombre total de messages reçus</span><span class="sxs-lookup"><span data-stu-id="b060c-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="b060c-198">Nombre total d’appels non implémentés</span><span class="sxs-lookup"><span data-stu-id="b060c-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="b060c-199">ASP.NET Core fournit également ses propres mesures sur la `Microsoft.AspNetCore.Hosting` source de l’événement.</span><span class="sxs-lookup"><span data-stu-id="b060c-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="b060c-200">métriques du client gRPC</span><span class="sxs-lookup"><span data-stu-id="b060c-200">gRPC client metrics</span></span>

<span data-ttu-id="b060c-201">les métriques du client gRPC sont signalées sur la source de l' `Grpc.Net.Client` événement.</span><span class="sxs-lookup"><span data-stu-id="b060c-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="b060c-202">Nom</span><span class="sxs-lookup"><span data-stu-id="b060c-202">Name</span></span>                      | <span data-ttu-id="b060c-203">Description</span><span class="sxs-lookup"><span data-stu-id="b060c-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="b060c-204">Nombre total d’appels</span><span class="sxs-lookup"><span data-stu-id="b060c-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="b060c-205">Appels en cours</span><span class="sxs-lookup"><span data-stu-id="b060c-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="b060c-206">Nombre total d’appels ayant échoué</span><span class="sxs-lookup"><span data-stu-id="b060c-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="b060c-207">Nombre total d’appels échus dépassé</span><span class="sxs-lookup"><span data-stu-id="b060c-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="b060c-208">Nombre total de messages envoyés</span><span class="sxs-lookup"><span data-stu-id="b060c-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="b060c-209">Nombre total de messages reçus</span><span class="sxs-lookup"><span data-stu-id="b060c-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="b060c-210">Observer les métriques</span><span class="sxs-lookup"><span data-stu-id="b060c-210">Observe metrics</span></span>

<span data-ttu-id="b060c-211">[dotnet-Counters](/dotnet/core/diagnostics/dotnet-counters) est un outil d’analyse des performances pour la surveillance de l’intégrité ad hoc et l’enquête sur les performances de premier niveau.</span><span class="sxs-lookup"><span data-stu-id="b060c-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="b060c-212">Surveiller une application .NET avec `Grpc.AspNetCore.Server` ou `Grpc.Net.Client` en tant que nom du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="b060c-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="b060c-213">Une autre façon d’observer les métriques gRPC consiste à capturer les données de compteur à l’aide du [package Microsoft. ApplicationInsights. EventCounterCollector](/azure/azure-monitor/app/eventcounters)de application Insights.</span><span class="sxs-lookup"><span data-stu-id="b060c-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="b060c-214">Une fois le programme d’installation de, Application Insights collecte les compteurs .NET courants au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b060c-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="b060c-215">les compteurs de gRPC ne sont pas collectés par défaut, mais application Insights peut être [personnalisé pour inclure des compteurs supplémentaires](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="b060c-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="b060c-216">Spécifiez les compteurs gRPC à collecter par application Insight dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b060c-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="b060c-217">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b060c-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>