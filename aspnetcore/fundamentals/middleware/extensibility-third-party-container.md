---
title: Activation d’un intergiciel (middleware) avec un conteneur tiers dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser un middleware fortement typé avec une activation basée sur une fabrique et un conteneur tiers dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057763"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="3e277-103">Activation d’un intergiciel (middleware) avec un conteneur tiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e277-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e277-104">Cet article montre comment utiliser <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> et <xref:Microsoft.AspNetCore.Http.IMiddleware> comme point d’extensibilité pour l’activation d’un [middleware](xref:fundamentals/middleware/index) avec un conteneur tiers.</span><span class="sxs-lookup"><span data-stu-id="3e277-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="3e277-105">Pour obtenir des informations sur `IMiddlewareFactory` et `IMiddleware`, consultez <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="3e277-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="3e277-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e277-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3e277-107">L’exemple d’application montre l’activation d’un middleware par une implémentation de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="3e277-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="3e277-108">L’exemple utilise le conteneur d’injection de dépendances [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="3e277-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="3e277-109">L’implémentation du middleware de l’exemple enregistre la valeur fournie par un paramètre de la chaîne de requête (`key`).</span><span class="sxs-lookup"><span data-stu-id="3e277-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="3e277-110">Le middleware utilise un contexte de base de données injecté (un service délimité) pour enregistrer la valeur de la chaîne de requête dans une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="3e277-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="3e277-111">L’exemple d’application utilise [Simple Injector](https://github.com/simpleinjector/SimpleInjector) seulement à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="3e277-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="3e277-112">Sa simple utilisation ne constitue pas une publicité pour ce produit.</span><span class="sxs-lookup"><span data-stu-id="3e277-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="3e277-113">Les approches de l’activation des middlewares décrites dans la documentation de Simple Injector et les problèmes GitHub sont recommandées par les développeurs de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e277-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="3e277-114">Pour plus d’informations, consultez la [documentation de Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) et le [dépôt GitHub de Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="3e277-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="3e277-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="3e277-115">IMiddlewareFactory</span></span>

<span data-ttu-id="3e277-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fournit des méthodes pour créer un middleware.</span><span class="sxs-lookup"><span data-stu-id="3e277-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="3e277-117">Dans l’exemple d’application, une fabrique de middleware est implémentée pour créer une instance de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="3e277-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="3e277-118">La fabrique de middleware utilise le conteneur Simple Injector pour résoudre le middleware :</span><span class="sxs-lookup"><span data-stu-id="3e277-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="3e277-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="3e277-119">IMiddleware</span></span>

<span data-ttu-id="3e277-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> définit le middleware pour le pipeline des requêtes de l’application.</span><span class="sxs-lookup"><span data-stu-id="3e277-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="3e277-121">Middleware activé par une implémentation de `IMiddlewareFactory` ( *Middleware/SimpleInjectorActivatedMiddleware.css* ) :</span><span class="sxs-lookup"><span data-stu-id="3e277-121">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="3e277-122">Une extension est créée pour le middleware ( *Middleware/MiddlewareExtensions.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="3e277-122">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="3e277-123">`Startup.ConfigureServices` doit effectuer plusieurs tâches :</span><span class="sxs-lookup"><span data-stu-id="3e277-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="3e277-124">Configurer le conteneur Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e277-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="3e277-125">Inscrire la fabrique et le middleware.</span><span class="sxs-lookup"><span data-stu-id="3e277-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="3e277-126">Rendez le contexte de base de données de l’application disponible depuis le conteneur Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e277-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3e277-127">Le middleware est inscrit dans le pipeline de traitement des requêtes, dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3e277-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e277-128">Cet article montre comment utiliser <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> et <xref:Microsoft.AspNetCore.Http.IMiddleware> comme point d’extensibilité pour l’activation d’un [middleware](xref:fundamentals/middleware/index) avec un conteneur tiers.</span><span class="sxs-lookup"><span data-stu-id="3e277-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="3e277-129">Pour obtenir des informations sur `IMiddlewareFactory` et `IMiddleware`, consultez <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="3e277-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="3e277-130">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e277-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3e277-131">L’exemple d’application montre l’activation d’un middleware par une implémentation de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="3e277-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="3e277-132">L’exemple utilise le conteneur d’injection de dépendances [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="3e277-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="3e277-133">L’implémentation du middleware de l’exemple enregistre la valeur fournie par un paramètre de la chaîne de requête (`key`).</span><span class="sxs-lookup"><span data-stu-id="3e277-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="3e277-134">Le middleware utilise un contexte de base de données injecté (un service délimité) pour enregistrer la valeur de la chaîne de requête dans une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="3e277-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="3e277-135">L’exemple d’application utilise [Simple Injector](https://github.com/simpleinjector/SimpleInjector) seulement à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="3e277-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="3e277-136">Sa simple utilisation ne constitue pas une publicité pour ce produit.</span><span class="sxs-lookup"><span data-stu-id="3e277-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="3e277-137">Les approches de l’activation des middlewares décrites dans la documentation de Simple Injector et les problèmes GitHub sont recommandées par les développeurs de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e277-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="3e277-138">Pour plus d’informations, consultez la [documentation de Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) et le [dépôt GitHub de Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="3e277-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="3e277-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="3e277-139">IMiddlewareFactory</span></span>

<span data-ttu-id="3e277-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fournit des méthodes pour créer un middleware.</span><span class="sxs-lookup"><span data-stu-id="3e277-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="3e277-141">Dans l’exemple d’application, une fabrique de middleware est implémentée pour créer une instance de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="3e277-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="3e277-142">La fabrique de middleware utilise le conteneur Simple Injector pour résoudre le middleware :</span><span class="sxs-lookup"><span data-stu-id="3e277-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="3e277-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="3e277-143">IMiddleware</span></span>

<span data-ttu-id="3e277-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> définit le middleware pour le pipeline des requêtes de l’application.</span><span class="sxs-lookup"><span data-stu-id="3e277-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="3e277-145">Middleware activé par une implémentation de `IMiddlewareFactory` ( *Middleware/SimpleInjectorActivatedMiddleware.css* ) :</span><span class="sxs-lookup"><span data-stu-id="3e277-145">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="3e277-146">Une extension est créée pour le middleware ( *Middleware/MiddlewareExtensions.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="3e277-146">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="3e277-147">`Startup.ConfigureServices` doit effectuer plusieurs tâches :</span><span class="sxs-lookup"><span data-stu-id="3e277-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="3e277-148">Configurer le conteneur Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e277-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="3e277-149">Inscrire la fabrique et le middleware.</span><span class="sxs-lookup"><span data-stu-id="3e277-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="3e277-150">Rendez le contexte de base de données de l’application disponible depuis le conteneur Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="3e277-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3e277-151">Le middleware est inscrit dans le pipeline de traitement des requêtes, dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3e277-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3e277-152">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3e277-152">Additional resources</span></span>

* [<span data-ttu-id="3e277-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="3e277-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="3e277-154">Activation d’intergiciel (middleware) basée sur une fabrique</span><span class="sxs-lookup"><span data-stu-id="3e277-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="3e277-155">Dépôt GitHub de Simple Injector</span><span class="sxs-lookup"><span data-stu-id="3e277-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="3e277-156">Documentation de Simple Injector</span><span class="sxs-lookup"><span data-stu-id="3e277-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
