---
title: BlazorJournalisation ASP.net Core
author: guardrex
description: En savoir plus sur la journalisation dans Blazor les applications, y compris la configuration du niveau de journalisation et comment écrire des messages de journal à partir de Razor composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 10c96bd2d0cc64f3bd035e7079b0996eb5768595
ms.sourcegitcommit: e9b8835a02f75b6378b766edb8bab23b14a4192b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97666831"
---
# <a name="aspnet-core-no-locblazor-logging"></a>BlazorJournalisation ASP.net Core

::: zone pivot="webassembly"

Configurez la journalisation personnalisée dans Blazor WebAssembly les applications avec la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> propriété.

Ajoutez l’espace de noms pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> à `Program.cs` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

Dans `Program.Main` de `Program.cs` , définissez le niveau de journalisation minimale avec <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> et ajoutez le fournisseur de journalisation personnalisé :

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

La `Logging` propriété est de type <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , donc toutes les méthodes d’extension disponibles sur <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sont également disponibles sur `Logging` .

La configuration de la journalisation peut être chargée à partir des fichiers de paramètres d’application. Pour plus d’informations, consultez <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="no-locsignalr-net-client-logging"></a>SignalR Journalisation du client .NET

Injectez un <xref:Microsoft.Extensions.Logging.ILoggerProvider> pour ajouter une `WebAssemblyConsoleLogger` aux fournisseurs de journalisation passés à <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Contrairement à un traditionnel <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` est un wrapper autour des API de journalisation spécifiques aux navigateurs (par exemple, `console.log` ). L’utilisation de `WebAssemblyConsoleLogger` rend la journalisation possible dans mono dans un contexte de navigateur.

> [!NOTE]
> `WebAssemblyConsoleLogger` est [interne](/dotnet/csharp/language-reference/keywords/internal) et n’est pas disponible pour une utilisation directe dans le code du développeur.

Ajoutez l’espace de noms pour <xref:Microsoft.Extensions.Logging?displayProperty=fullName> et injectez <xref:Microsoft.Extensions.Logging.ILoggerProvider> dans le composant :

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

Dans la [ `OnInitializedAsync` méthode](xref:blazor/components/lifecycle#component-initialization-methods)du composant, utilisez <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

Pour obtenir des instructions générales sur la journalisation des ASP.NET Core qui concernent Blazor Server , consultez <xref:fundamentals/logging/index> .

::: zone-end

## <a name="log-in-no-locrazor-components"></a>Connecter les Razor composants

Les journaux respectent la configuration de démarrage de l’application.

La `using` directive pour <xref:Microsoft.Extensions.Logging> est requise pour prendre en charge les saisies semi-automatiques [IntelliSense](/visualstudio/ide/using-intellisense) pour les API, telles que <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> et <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .

L’exemple suivant illustre la journalisation avec un <xref:Microsoft.Extensions.Logging.ILogger> dans des composants.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

L’exemple suivant illustre la journalisation avec un <xref:Microsoft.Extensions.Logging.ILoggerFactory> dans des composants.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/index>
