---
title: Migrer de Microsoft. extensions. Logging 2,1 vers 2,2 ou 3,0
author: pakrym
description: Découvrez comment migrer une application non-ASP.NET Core qui utilise Microsoft. extensions. Logging de 2,1 à 2,2 ou 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 1082ae494aae7a31b195b32dfedae81c60fe3eed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060376"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="9bb74-103">Migrer de Microsoft. extensions. Logging 2,1 vers 2,2 ou 3,0</span><span class="sxs-lookup"><span data-stu-id="9bb74-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="9bb74-104">Cet article décrit les étapes courantes de la migration d’une application non-ASP.NET Core qui utilise `Microsoft.Extensions.Logging` de 2,1 à 2,2 ou 3,0.</span><span class="sxs-lookup"><span data-stu-id="9bb74-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="9bb74-105">2.1 à 2.2</span><span class="sxs-lookup"><span data-stu-id="9bb74-105">2.1 to 2.2</span></span>

<span data-ttu-id="9bb74-106">Créez `ServiceCollection` et appelez manuellement `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="9bb74-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="9bb74-107">2,1 exemple :</span><span class="sxs-lookup"><span data-stu-id="9bb74-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="9bb74-108">2,2 exemple :</span><span class="sxs-lookup"><span data-stu-id="9bb74-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="9bb74-109">2,1 à 3,0</span><span class="sxs-lookup"><span data-stu-id="9bb74-109">2.1 to 3.0</span></span>

<span data-ttu-id="9bb74-110">Dans 3,0, utilisez `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="9bb74-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="9bb74-111">2,1 exemple :</span><span class="sxs-lookup"><span data-stu-id="9bb74-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="9bb74-112">3,0 exemple :</span><span class="sxs-lookup"><span data-stu-id="9bb74-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="9bb74-113">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9bb74-113">Additional resources</span></span>

* <span data-ttu-id="9bb74-114">[Package NuGet Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="9bb74-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
