---
title: Migrer de Microsoft. extensions. Logging 2,1 vers 2,2 ou 3,0
author: pakrym
description: Découvrez comment migrer une application non-ASP.NET Core qui utilise Microsoft. extensions. Logging de 2,1 à 2,2 ou 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 1082ae494aae7a31b195b32dfedae81c60fe3eed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060376"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrer de Microsoft. extensions. Logging 2,1 vers 2,2 ou 3,0

Cet article décrit les étapes courantes de la migration d’une application non-ASP.NET Core qui utilise `Microsoft.Extensions.Logging` de 2,1 à 2,2 ou 3,0.

## <a name="21-to-22"></a>2.1 à 2.2

Créez `ServiceCollection` et appelez manuellement `AddLogging` .

2,1 exemple :

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2,2 exemple :

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2,1 à 3,0

Dans 3,0, utilisez `LoggingFactory.Create` .

2,1 exemple :

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3,0 exemple :

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Package NuGet Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>
