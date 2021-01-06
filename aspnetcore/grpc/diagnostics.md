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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Journalisation et diagnostics dans gRPC sur .NET

Par [James Newton-King](https://twitter.com/jamesnk)

Cet article fournit des conseils pour la collecte des diagnostics à partir d’une application gRPC afin de vous aider à résoudre les problèmes. Sont abordés les sujets suivants :

* Journaux structurés en **Journal** écrits dans la [journalisation .net Core](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger> est utilisé par les infrastructures d’application pour écrire des journaux et par les utilisateurs pour leur propre journalisation dans une application.
* **Suivi** -événements liés à une opération écrite à l’aide `DiaganosticSource` de et de `Activity` . Les traces à partir de la source de diagnostic sont couramment utilisées pour collecter des données de télémétrie d’application par des bibliothèques telles que [application Insights](/azure/azure-monitor/app/asp-net-core) et [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Métriques** : représentation des mesures de données sur des intervalles de temps, par exemple, les demandes par seconde. Les métriques sont émises à l’aide `EventCounter` de et peuvent être observées à l’aide de l’outil en ligne de commande [dotnet-Counters](/dotnet/core/diagnostics/dotnet-counters) ou avec [application Insights](/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Journalisation

les services gRPC et le client gRPC écrivent des journaux à l’aide de la [journalisation .net Core](xref:fundamentals/logging/index). Les journaux sont un bon point de départ lorsque vous avez besoin de déboguer un comportement inattendu dans vos applications.

### <a name="grpc-services-logging"></a>journalisation des services gRPC

> [!WARNING]
> Les journaux côté serveur peuvent contenir des informations sensibles de votre application. **Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.

Étant donné que les services gRPC sont hébergés sur ASP.NET Core, ils utilisent le système de journalisation ASP.NET Core. Dans la configuration par défaut, gRPC enregistre très peu d’informations, mais cela peut être configuré. Pour plus d’informations sur la configuration de la journalisation des ASP.NET Core, consultez la documentation sur [ASP.net Core Logging](xref:fundamentals/logging/index#configuration) .

gRPC ajoute des journaux sous la `Grpc` catégorie. Pour activer les journaux détaillés à partir de gRPC, configurez les `Grpc` préfixes sur le `Debug` niveau de votre *appsettings.json* fichier en ajoutant les éléments suivants à la `LogLevel` sous-section dans `Logging` :

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Vous pouvez également configurer cette configuration dans *Startup.cs* avec `ConfigureLogging` :

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Si vous n’utilisez pas la configuration basée sur JSON, définissez la valeur de configuration suivante dans votre système de configuration :

* `Logging:LogLevel:Grpc` = `Debug`

Consultez la documentation de votre système de configuration pour déterminer comment spécifier les valeurs de configuration imbriquées. Par exemple, lors de l’utilisation de variables d’environnement, deux `_` caractères sont utilisés à la place de `:` (par exemple, `Logging__LogLevel__Grpc` ).

Nous vous recommandons d’utiliser le `Debug` niveau lorsque vous collectez des diagnostics plus détaillés pour votre application. Le `Trace` niveau produit des diagnostics de bas niveau et est rarement nécessaire pour diagnostiquer les problèmes dans votre application.

#### <a name="sample-logging-output"></a>Exemple de sortie de la journalisation

Voici un exemple de sortie de console au `Debug` niveau d’un service gRPC :

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

### <a name="access-server-side-logs"></a>Accéder aux journaux côté serveur

La façon dont vous accédez aux journaux côté serveur dépend de l’environnement dans lequel vous exécutez.

#### <a name="as-a-console-app"></a>En tant qu’application console

Si vous exécutez dans une application console, l’enregistreur d’événements de [console](xref:fundamentals/logging/index#console) doit être activé par défaut. les journaux gRPC s’affichent dans la console.

#### <a name="other-environments"></a>Autres environnements

Si l’application est déployée dans un autre environnement (par exemple, docker, Kubernetes ou service Windows), consultez <xref:fundamentals/logging/index> pour plus d’informations sur la configuration des fournisseurs de journalisation appropriés pour l’environnement.

### <a name="grpc-client-logging"></a>journalisation du client gRPC

> [!WARNING]
> Les journaux côté client peuvent contenir des informations sensibles de votre application. **Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.

Pour récupérer des journaux à partir du client .NET, vous pouvez définir la `GrpcChannelOptions.LoggerFactory` propriété lorsque le canal du client est créé. Si vous appelez un service gRPC à partir d’une application ASP.NET Core, la fabrique d’enregistreur peut être résolue à partir de l’injection de dépendances (DI) :

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Une autre façon d’activer la journalisation du client consiste à utiliser la [fabrique de clients gRPC](xref:grpc/clientfactory) pour créer le client. Un client gRPC inscrit auprès de la fabrique de clients et résolu à partir de DI utilisera automatiquement la journalisation configurée de l’application.

Si votre application n’utilise pas DI, vous pouvez créer une nouvelle `ILoggerFactory` instance avec [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Pour accéder à cette méthode, ajoutez le package [Microsoft. extensions. logginging](https://www.nuget.org/packages/microsoft.extensions.logging/) à votre application.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>étendues du journal du client gRPC

Le client gRPC ajoute une [étendue de journalisation](../fundamentals/logging/index.md#log-scopes) aux journaux effectués pendant un appel gRPC. L’étendue a des métadonnées liées à l’appel gRPC :

* **GrpcMethodType** : type de méthode gRPC. Les valeurs possibles sont les noms de l' `Grpc.Core.MethodType` enum, par exemple unaire
* **GrpcUri** : URI relatif de la méthode gRPC, par exemple/Greet. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Exemple de sortie de la journalisation

Voici un exemple de sortie de console au `Debug` niveau d’un client gRPC :

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

## <a name="tracing"></a>Traçage

les services gRPC et le client gRPC fournissent des informations sur les appels gRPC à l’aide de [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) et de l' [activité](/dotnet/api/system.diagnostics.activity).

* .NET gRPC utilise une activité pour représenter un appel gRPC.
* Les événements de suivi sont écrits dans la source de diagnostic au démarrage et à l’arrêt de l’activité d’appel gRPC.
* Le suivi ne capture pas d’informations sur le moment où les messages sont envoyés pendant la durée de vie des appels de diffusion en continu gRPC.

### <a name="grpc-service-tracing"></a>suivi du service gRPC

les services gRPC sont hébergés sur ASP.NET Core qui signale les événements relatifs aux requêtes HTTP entrantes. les métadonnées spécifiques à gRPC sont ajoutées aux diagnostics de requête HTTP existants fournis par ASP.NET Core.

* Le nom de la source de diagnostic est `Microsoft.AspNetCore` .
* Le nom de l’activité est `Microsoft.AspNetCore.Hosting.HttpRequestIn` .
  * Le nom de la méthode gRPC appelée par l’appel gRPC est ajouté sous la forme d’une balise portant le nom `grpc.method` .
  * Le code d’état de l’appel gRPC lorsqu’il est terminé est ajouté sous la forme d’une balise portant le nom `grpc.status_code` .

### <a name="grpc-client-tracing"></a>suivi du client gRPC

Le client .NET gRPC utilise `HttpClient` pour effectuer des appels gRPC. Bien que `HttpClient` écrit des événements de diagnostic, le client .net gRPC fournit une source de diagnostic personnalisée, une activité et des événements pour que des informations complètes sur un appel gRPC puissent être collectées.

* Le nom de la source de diagnostic est `Grpc.Net.Client` .
* Le nom de l’activité est `Grpc.Net.Client.GrpcOut` .
  * Le nom de la méthode gRPC appelée par l’appel gRPC est ajouté sous la forme d’une balise portant le nom `grpc.method` .
  * Le code d’état de l’appel gRPC lorsqu’il est terminé est ajouté sous la forme d’une balise portant le nom `grpc.status_code` .

### <a name="collecting-tracing"></a>Collecte du suivi

Le moyen le plus simple d’utiliser `DiagnosticSource` est de configurer une bibliothèque de télémétrie telle que [application Insights](/azure/azure-monitor/app/asp-net-core) ou [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) dans votre application. La bibliothèque traitera les informations sur les appels gRPC en parallèle d’autres données de télémétrie d’application.

Le suivi peut être affiché dans un service géré comme Application Insights, ou vous pouvez choisir d’exécuter votre propre système de traçage distribué. OpenTelemetry prend en charge l’exportation des données de suivi vers [Jaeger](https://www.jaegertracing.io/) et [Zipkin](https://zipkin.io/).

`DiagnosticSource` peut consommer des événements de suivi dans le code à l’aide de `DiagnosticListener` . Pour plus d’informations sur l’écoute d’une source de diagnostic avec du code, consultez le [Guide de l’utilisateur DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Les bibliothèques de télémétrie ne capturent pas actuellement les données de télémétrie spécifiques à gRPC `Grpc.Net.Client.GrpcOut` . Travailler pour améliorer les bibliothèques de télémétrie la capture de ce suivi est en cours.

## <a name="metrics"></a>Mesures

Les métriques sont une représentation des mesures de données sur des intervalles de temps, par exemple, les demandes par seconde. Les données de métriques permettent d’observer l’état d’une application à un niveau élevé. Les métriques .NET gRPC sont émises à l’aide de `EventCounter` .

### <a name="grpc-service-metrics"></a>métriques du service gRPC

les métriques du serveur gRPC sont signalées sur la source de l' `Grpc.AspNetCore.Server` événement.

| Nom                      | Description                   |
| --------------------------|-------------------------------|
| `total-calls`             | Nombre total d’appels                   |
| `current-calls`           | Appels en cours                 |
| `calls-failed`            | Nombre total d’appels ayant échoué            |
| `calls-deadline-exceeded` | Nombre total d’appels échus dépassé |
| `messages-sent`           | Nombre total de messages envoyés           |
| `messages-received`       | Nombre total de messages reçus       |
| `calls-unimplemented`     | Nombre total d’appels non implémentés     |

ASP.NET Core fournit également ses propres mesures sur la `Microsoft.AspNetCore.Hosting` source de l’événement.

### <a name="grpc-client-metrics"></a>métriques du client gRPC

les métriques du client gRPC sont signalées sur la source de l' `Grpc.Net.Client` événement.

| Nom                      | Description                   |
| --------------------------|-------------------------------|
| `total-calls`             | Nombre total d’appels                   |
| `current-calls`           | Appels en cours                 |
| `calls-failed`            | Nombre total d’appels ayant échoué            |
| `calls-deadline-exceeded` | Nombre total d’appels échus dépassé |
| `messages-sent`           | Nombre total de messages envoyés           |
| `messages-received`       | Nombre total de messages reçus       |

### <a name="observe-metrics"></a>Observer les métriques

[dotnet-Counters](/dotnet/core/diagnostics/dotnet-counters) est un outil d’analyse des performances pour la surveillance de l’intégrité ad hoc et l’enquête sur les performances de premier niveau. Surveiller une application .NET avec `Grpc.AspNetCore.Server` ou `Grpc.Net.Client` en tant que nom du fournisseur.

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

Une autre façon d’observer les métriques gRPC consiste à capturer les données de compteur à l’aide du [package Microsoft. ApplicationInsights. EventCounterCollector](/azure/azure-monitor/app/eventcounters)de application Insights. Une fois le programme d’installation de, Application Insights collecte les compteurs .NET courants au moment de l’exécution. les compteurs de gRPC ne sont pas collectés par défaut, mais application Insights peut être [personnalisé pour inclure des compteurs supplémentaires](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Spécifiez les compteurs gRPC à collecter par application Insight dans *Startup.cs*:

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

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>