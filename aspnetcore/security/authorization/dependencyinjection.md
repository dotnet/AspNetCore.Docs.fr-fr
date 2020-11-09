---
title: Injection de dépendances dans les gestionnaires d’exigences dans ASP.NET Core
author: rick-anderson
description: Découvrez comment injecter des gestionnaires d’exigence d’autorisation dans une application ASP.NET Core à l’aide de l’injection de dépendances.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060259"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="7380f-103">Injection de dépendances dans les gestionnaires d’exigences dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7380f-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="7380f-104">Les [gestionnaires d’autorisations doivent être inscrits](xref:security/authorization/policies#handler-registration) dans la collection de services lors de la configuration à l’aide de l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7380f-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7380f-105">Supposons que vous disposiez d’un référentiel de règles que vous souhaitiez évaluer à l’intérieur d’un gestionnaire d’autorisations et que ce dépôt ait été enregistré dans la collection de services.</span><span class="sxs-lookup"><span data-stu-id="7380f-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="7380f-106">L’autorisation le résout et l’injecte dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="7380f-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="7380f-107">Par exemple, pour utiliser ASP. L’infrastructure de journalisation de .net, injecter `ILoggerFactory` dans le gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7380f-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="7380f-108">Un tel gestionnaire peut se présenter comme le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7380f-108">Such a handler might look like the following code:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="7380f-109">Le gestionnaire précédent peut être inscrit avec toute [durée de vie de service](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="7380f-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="7380f-110">Le code suivant utilise `AddSingleton` pour inscrire le gestionnaire précédent :</span><span class="sxs-lookup"><span data-stu-id="7380f-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="7380f-111">Une instance du gestionnaire est créée au démarrage de l’application, et DI injecte le inscrit `ILoggerFactory` dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="7380f-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="7380f-112">Les gestionnaires qui utilisent Entity Framework ne doivent pas être inscrits en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="7380f-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
