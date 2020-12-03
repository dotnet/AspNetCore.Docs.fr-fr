---
title: API JSON de base avec Route-to-code dans ASP.net Core
author: jamesnk
description: Découvrez comment utiliser Route-to-code les méthodes d’extension et JSON pour créer des API Web JSON légères.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556604"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="e7ce1-103">API JSON de base avec Route-to-code dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e7ce1-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="e7ce1-104">Par [James Newton-King](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e7ce1-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="e7ce1-105">ASP.NET Core prend en charge plusieurs façons de créer des API Web JSON :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="e7ce1-106">[ASP.net Core API Web](xref:web-api/index) fournit une infrastructure complète pour la création d’API.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="e7ce1-107">Un service est créé en héritant de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="e7ce1-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="e7ce1-108">Certaines fonctionnalités fournies par l’infrastructure incluent la liaison de modèle, la validation, la négociation de contenu, la mise en forme d’entrée et de sortie, et OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="e7ce1-109">Route-to-code n’est pas une alternative au Framework pour ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="e7ce1-110">Route-to-code connecte [ASP.net Core routage](xref:fundamentals/routing) directement à votre code.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="e7ce1-111">Votre code lit la demande et écrit la réponse.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="e7ce1-112">Route-to-code ne dispose pas de fonctionnalités avancées de l’API Web, mais il n’y a pas non plus de configuration requise pour l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="e7ce1-113">Route-to-code est une bonne approche lors de la création d’API Web JSON de petite et de base.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="e7ce1-114">Créer des API Web JSON</span><span class="sxs-lookup"><span data-stu-id="e7ce1-114">Create JSON web APIs</span></span>

<span data-ttu-id="e7ce1-115">ASP.NET Core fournit des méthodes d’assistance qui facilitent la création d’API Web JSON :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="e7ce1-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> vérifie l' `Content-Type` en-tête pour un type de contenu JSON.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="e7ce1-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> lit JSON à partir de la demande et le désérialise vers le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="e7ce1-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> écrit la valeur spécifiée au format JSON dans le corps de la réponse et définit le type de contenu de la réponse sur `application/json` .</span><span class="sxs-lookup"><span data-stu-id="e7ce1-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="e7ce1-119">Les API JSON basées sur un itinéraire sont spécifiées dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="e7ce1-120">L’itinéraire et la logique de l’API sont configurés dans dans le cadre <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> du pipeline de requête d’une application.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="e7ce1-121">Écrire une réponse JSON</span><span class="sxs-lookup"><span data-stu-id="e7ce1-121">Write JSON response</span></span>

<span data-ttu-id="e7ce1-122">Prenons le code suivant qui configure une API JSON pour une application :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="e7ce1-123">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-123">The preceding code:</span></span>

* <span data-ttu-id="e7ce1-124">Ajoute un point de terminaison d’API HTTP d’extraction avec `/hello/{name:alpha}` comme modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="e7ce1-125">Lorsque l’itinéraire est mis en correspondance, l’API lit la `name` valeur de route à partir de la demande.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="e7ce1-126">Écrit un type anonyme en tant que réponse JSON avec `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="e7ce1-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="e7ce1-127">Lire la requête JSON</span><span class="sxs-lookup"><span data-stu-id="e7ce1-127">Read JSON request</span></span>

<span data-ttu-id="e7ce1-128">`HasJsonContentType` et `ReadFromJsonAsync` peuvent être utilisés pour désérialiser une réponse JSON dans une API JSON basée sur l’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="e7ce1-129">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-129">The preceding code:</span></span>

* <span data-ttu-id="e7ce1-130">Ajoute un point de terminaison d’API HTTP après `/weather` comme modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="e7ce1-131">Lorsque l’itinéraire est mis en correspondance, `HasJsonContentType` valide le type de contenu de la demande.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="e7ce1-132">Un type de contenu non JSON retourne un code d’État 415.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="e7ce1-133">Si le type de contenu est JSON, le contenu de la demande est désérialisé par `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="e7ce1-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="e7ce1-134">Configurer la sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="e7ce1-134">Configure JSON serialization</span></span>

<span data-ttu-id="e7ce1-135">Il existe deux façons de personnaliser la sérialisation JSON :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="e7ce1-136">Les options de sérialisation par défaut peuvent être configurées avec <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-136">Default serialization options can be configured with <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="e7ce1-137">`WriteAsJsonAsync` et `ReadFromJsonAsync` ont des surcharges qui acceptent un <xref:System.Text.Json.JsonSerializerOptions> objet.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a <xref:System.Text.Json.JsonSerializerOptions> object.</span></span> <span data-ttu-id="e7ce1-138">Cet objet d’options remplace les options par défaut.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-138">This options object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="e7ce1-139">Authentification et autorisation</span><span class="sxs-lookup"><span data-stu-id="e7ce1-139">Authentication and authorization</span></span>

<span data-ttu-id="e7ce1-140">Route-to-code prend en charge l’authentification et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="e7ce1-141">Les attributs, tels que `[Authorize]` et `[AllowAnonymous]` , ne peuvent pas être placés sur des points de terminaison qui mappent à un délégué de requête.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="e7ce1-142">Au lieu de cela, les métadonnées d’autorisation sont ajoutées à l’aide des <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> méthodes d’extension et.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-142">Instead, authorization metadata is added using the <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="e7ce1-143">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="e7ce1-143">Dependency injection</span></span>

<span data-ttu-id="e7ce1-144">L' [injection de dépendance (di)](xref:fundamentals/dependency-injection) à l’aide d’un constructeur n’est pas possible avec Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="e7ce1-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="e7ce1-145">L’API Web crée un contrôleur pour vous avec les services injectés dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="e7ce1-146">Un type n’est pas créé lors de l’exécution d’un point de terminaison, les services doivent donc être résolus manuellement.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="e7ce1-147">Les API basées sur l’itinéraire peuvent utiliser <xref:System.IServiceProvider> pour résoudre les services :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="e7ce1-148">Les services de durée de vie temporaires et délimitées, tels que `DbContext` , doivent être résolus à partir de [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) dans le délégué de demande d’un point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="e7ce1-149">Les services de durée de vie Singleton, tels que `ILogger` , peuvent être résolus à partir de [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span><span class="sxs-lookup"><span data-stu-id="e7ce1-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="e7ce1-150">Les services peuvent être résolus en dehors des délégués de demande et partagés entre les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="e7ce1-151">Les API qui utilisent l’injection de compte doivent largement envisager d’utiliser un type d’application ASP.NET Core qui prend en charge DI.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="e7ce1-152">Par exemple, ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="e7ce1-153">L’injection de service à l’aide du constructeur d’un contrôleur est plus facile que de résoudre manuellement les services.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="e7ce1-154">Structure de projet d’API</span><span class="sxs-lookup"><span data-stu-id="e7ce1-154">API project structure</span></span>

<span data-ttu-id="e7ce1-155">Les API basées sur l’itinéraire n’ont pas besoin d’être situées dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="e7ce1-156">Les API peuvent être placées dans d’autres fichiers et mappées au démarrage avec `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="e7ce1-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="e7ce1-157">Cette approche réduit la taille du fichier de configuration de démarrage.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="e7ce1-158">Considérons la classe statique suivante `UserApi` qui définit une `Map` méthode.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="e7ce1-159">La méthode mappe les API basées sur l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="e7ce1-160">Dans la `Startup.Configure` méthode, la `Map` méthode et les méthodes statiques de la classe sont appelées dans `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="e7ce1-161">Fonctionnalités notables manquantes par rapport à l’API Web</span><span class="sxs-lookup"><span data-stu-id="e7ce1-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="e7ce1-162">Route-to-code est conçu pour les API JSON de base.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="e7ce1-163">Il ne prend pas en charge la plupart des fonctionnalités avancées fournies par ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="e7ce1-164">Les fonctionnalités non fournies par Route-to-code sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7ce1-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="e7ce1-165">Liaison de données</span><span class="sxs-lookup"><span data-stu-id="e7ce1-165">Model binding</span></span>
* <span data-ttu-id="e7ce1-166">Validation du modèle</span><span class="sxs-lookup"><span data-stu-id="e7ce1-166">Model validation</span></span>
* <span data-ttu-id="e7ce1-167">OpenAPI/Swagger</span><span class="sxs-lookup"><span data-stu-id="e7ce1-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="e7ce1-168">Négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="e7ce1-168">Content negotiation</span></span>
* <span data-ttu-id="e7ce1-169">Injection de dépendances de constructeur</span><span class="sxs-lookup"><span data-stu-id="e7ce1-169">Constructor dependency injection</span></span>
* <span data-ttu-id="e7ce1-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span><span class="sxs-lookup"><span data-stu-id="e7ce1-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span></span>

<span data-ttu-id="e7ce1-171">Envisagez d’utiliser [ASP.net Core API Web](xref:web-api/index) pour créer une API si elle requiert certaines des fonctionnalités de la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="e7ce1-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7ce1-172">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e7ce1-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
