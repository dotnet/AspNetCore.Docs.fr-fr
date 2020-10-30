---
title: BlazorJournalisation ASP.net Core
author: guardrex
description: En savoir plus sur la journalisation dans Blazor les applications, y compris la configuration du niveau de journalisation et comment écrire des messages de journal à partir de Razor composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055982"
---
# <a name="aspnet-core-no-locblazor-logging"></a>BlazorJournalisation ASP.net Core

## Blazor WebAssembly

Configurez la journalisation dans Blazor WebAssembly les applications avec la `WebAssemblyHostBuilder.Logging` propriété dans `Program.Main` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

La `Logging` propriété est de type <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , donc toutes les méthodes d’extension disponibles sur <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sont également disponibles sur `Logging` .

La configuration de la journalisation peut être chargée à partir des fichiers de paramètres d’application. Pour plus d'informations, consultez <xref:blazor/fundamentals/configuration#logging-configuration>.

## Blazor Server

Pour obtenir des instructions générales sur la journalisation des ASP.NET Core, consultez <xref:fundamentals/logging/index> .

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a>Blazor WebAssemblySignalRJournalisation du client .net

Injectez un <xref:Microsoft.Extensions.Logging.ILoggerProvider> pour ajouter une `WebAssemblyConsoleLogger` aux fournisseurs de journalisation passés à <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Contrairement à un traditionnel <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` est un wrapper autour des API de journalisation spécifiques aux navigateurs (par exemple, `console.log` ). L’utilisation de `WebAssemblyConsoleLogger` rend la journalisation possible dans mono dans un contexte de navigateur.

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a>Connecter les Razor composants

Les journaux respectent la configuration de démarrage de l’application.

La `using` directive pour <xref:Microsoft.Extensions.Logging> est requise pour prendre en charge les saisies semi-automatiques IntelliSense pour les API, telles que <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> et <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .

L’exemple suivant illustre la journalisation avec un <xref:Microsoft.Extensions.Logging.ILogger> dans des Razor composants :

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

L’exemple suivant illustre la journalisation avec un <xref:Microsoft.Extensions.Logging.ILoggerFactory> dans des Razor composants :

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/index>
