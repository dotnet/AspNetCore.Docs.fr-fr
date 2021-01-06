---
title: Activation d’un intergiciel (middleware) basé sur une fabrique dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser un middleware fortement typé avec une implémentation de l’activation basée sur une fabrique dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 125ebbba9fadf0db357fe7c69af7ca0d8ddc8041
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057490"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="e37c6-103">Activation d’un intergiciel (middleware) basé sur une fabrique dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e37c6-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e37c6-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> est un point d’extensibilité pour l’activation d’un [middleware (intergiciel)](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="e37c6-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="e37c6-105">Les méthodes d’extension de <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> vérifient si le type inscrit d’un middleware implémente <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="e37c6-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="e37c6-106">Si c’est le cas, l’instance de <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> inscrite dans le conteneur est utilisée pour résoudre l’implémentation de <xref:Microsoft.AspNetCore.Http.IMiddleware>, au lieu de la logique d’activation de middleware basée sur une convention.</span><span class="sxs-lookup"><span data-stu-id="e37c6-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="e37c6-107">Le middleware est inscrit comme [service délimité ou temporaire](xref:fundamentals/dependency-injection#service-lifetimes) dans le conteneur de service de l’application.</span><span class="sxs-lookup"><span data-stu-id="e37c6-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="e37c6-108">Avantages :</span><span class="sxs-lookup"><span data-stu-id="e37c6-108">Benefits:</span></span>

* <span data-ttu-id="e37c6-109">Activation par requête de client (injection de services délimités)</span><span class="sxs-lookup"><span data-stu-id="e37c6-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="e37c6-110">Typage fort du middleware</span><span class="sxs-lookup"><span data-stu-id="e37c6-110">Strong typing of middleware</span></span>

<span data-ttu-id="e37c6-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> est activé par requête de client (connexion) : des services délimités peuvent ainsi être injectés dans le constructeur du middleware.</span><span class="sxs-lookup"><span data-stu-id="e37c6-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="e37c6-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e37c6-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="e37c6-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="e37c6-113">IMiddleware</span></span>

<span data-ttu-id="e37c6-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> définit le middleware pour le pipeline des requêtes de l’application.</span><span class="sxs-lookup"><span data-stu-id="e37c6-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="e37c6-115">La méthode [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) gère les requêtes et retourne un élément <xref:System.Threading.Tasks.Task> qui représente l’exécution du middleware.</span><span class="sxs-lookup"><span data-stu-id="e37c6-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="e37c6-116">Middleware activé par convention :</span><span class="sxs-lookup"><span data-stu-id="e37c6-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="e37c6-117">Middleware activé par <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :</span><span class="sxs-lookup"><span data-stu-id="e37c6-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="e37c6-118">Des extensions sont créées pour les middleware :</span><span class="sxs-lookup"><span data-stu-id="e37c6-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="e37c6-119">Il n’est pas possible de passer des objets au middleware activé par fabrique avec <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :</span><span class="sxs-lookup"><span data-stu-id="e37c6-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="e37c6-120">Le middleware activé par fabrique est ajouté au conteneur intégré dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e37c6-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="e37c6-121">Les deux middlewares sont inscrits dans le pipeline de traitement des requêtes dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e37c6-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="e37c6-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="e37c6-122">IMiddlewareFactory</span></span>

<span data-ttu-id="e37c6-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fournit des méthodes pour créer un middleware.</span><span class="sxs-lookup"><span data-stu-id="e37c6-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="e37c6-124">L’implémentation de la fabrique de middlewares est inscrite dans le conteneur comme service délimité.</span><span class="sxs-lookup"><span data-stu-id="e37c6-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="e37c6-125">L’implémentation par défaut de <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se trouve dans le package [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="e37c6-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e37c6-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> est un point d’extensibilité pour l’activation d’un [middleware (intergiciel)](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="e37c6-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="e37c6-127">Les méthodes d’extension de <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> vérifient si le type inscrit d’un middleware implémente <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="e37c6-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="e37c6-128">Si c’est le cas, l’instance de <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> inscrite dans le conteneur est utilisée pour résoudre l’implémentation de <xref:Microsoft.AspNetCore.Http.IMiddleware>, au lieu de la logique d’activation de middleware basée sur une convention.</span><span class="sxs-lookup"><span data-stu-id="e37c6-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="e37c6-129">Le middleware est inscrit comme [service délimité ou temporaire](xref:fundamentals/dependency-injection#service-lifetimes) dans le conteneur de service de l’application.</span><span class="sxs-lookup"><span data-stu-id="e37c6-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="e37c6-130">Avantages :</span><span class="sxs-lookup"><span data-stu-id="e37c6-130">Benefits:</span></span>

* <span data-ttu-id="e37c6-131">Activation par requête de client (injection de services délimités)</span><span class="sxs-lookup"><span data-stu-id="e37c6-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="e37c6-132">Typage fort du middleware</span><span class="sxs-lookup"><span data-stu-id="e37c6-132">Strong typing of middleware</span></span>

<span data-ttu-id="e37c6-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> est activé par requête de client (connexion) : des services délimités peuvent ainsi être injectés dans le constructeur du middleware.</span><span class="sxs-lookup"><span data-stu-id="e37c6-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="e37c6-134">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e37c6-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="e37c6-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="e37c6-135">IMiddleware</span></span>

<span data-ttu-id="e37c6-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> définit le middleware pour le pipeline des requêtes de l’application.</span><span class="sxs-lookup"><span data-stu-id="e37c6-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="e37c6-137">La méthode [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) gère les requêtes et retourne un élément <xref:System.Threading.Tasks.Task> qui représente l’exécution du middleware.</span><span class="sxs-lookup"><span data-stu-id="e37c6-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="e37c6-138">Middleware activé par convention :</span><span class="sxs-lookup"><span data-stu-id="e37c6-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="e37c6-139">Middleware activé par <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :</span><span class="sxs-lookup"><span data-stu-id="e37c6-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="e37c6-140">Des extensions sont créées pour les middleware :</span><span class="sxs-lookup"><span data-stu-id="e37c6-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="e37c6-141">Il n’est pas possible de passer des objets au middleware activé par fabrique avec <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :</span><span class="sxs-lookup"><span data-stu-id="e37c6-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="e37c6-142">Le middleware activé par fabrique est ajouté au conteneur intégré dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e37c6-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="e37c6-143">Les deux middlewares sont inscrits dans le pipeline de traitement des requêtes dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e37c6-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="e37c6-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="e37c6-144">IMiddlewareFactory</span></span>

<span data-ttu-id="e37c6-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fournit des méthodes pour créer un middleware.</span><span class="sxs-lookup"><span data-stu-id="e37c6-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="e37c6-146">L’implémentation de la fabrique de middlewares est inscrite dans le conteneur comme service délimité.</span><span class="sxs-lookup"><span data-stu-id="e37c6-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="e37c6-147">L’implémentation par défaut de <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se trouve dans le package [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="e37c6-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e37c6-148">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e37c6-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
