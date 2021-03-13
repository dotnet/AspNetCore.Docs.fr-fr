---
title: Activer les requêtes Cross-Origin (CORS) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment CORS comme norme pour autoriser ou rejeter des demandes Cross-Origin dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: b057e5e08b8a4d0f9bcd68f92102cad309655acc
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413507"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="07610-103">Activer les requêtes Cross-Origin (CORS) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07610-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07610-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="07610-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="07610-105">Cet article explique comment activer CORS dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07610-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="07610-106">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="07610-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="07610-107">Cette restriction est appelée *stratégie de même origine*.</span><span class="sxs-lookup"><span data-stu-id="07610-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="07610-108">La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="07610-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="07610-109">Parfois, vous souhaiterez peut-être autoriser d’autres sites à effectuer des demandes Cross-Origin à votre application.</span><span class="sxs-lookup"><span data-stu-id="07610-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="07610-110">Pour plus d’informations, consultez l' [article Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="07610-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="07610-111">[Partage des ressources Cross-Origin](https://www.w3.org/TR/cors/) (cors) :</span><span class="sxs-lookup"><span data-stu-id="07610-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="07610-112">Est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.</span><span class="sxs-lookup"><span data-stu-id="07610-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="07610-113">N’est **pas** une fonctionnalité de sécurité, cors assouplit la sécurité.</span><span class="sxs-lookup"><span data-stu-id="07610-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="07610-114">Une API n’est pas plus sûre en autorisant CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="07610-115">Pour plus d’informations, consultez fonctionnement de [cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="07610-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="07610-116">Permet à un serveur d’autoriser explicitement certaines demandes Cross-Origin tout en rejetant d’autres.</span><span class="sxs-lookup"><span data-stu-id="07610-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="07610-117">Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="07610-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="07610-118">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07610-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="07610-119">Même origine</span><span class="sxs-lookup"><span data-stu-id="07610-119">Same origin</span></span>

<span data-ttu-id="07610-120">Deux URL ont la même origine si elles ont des schémas, des hôtes et des ports identiques ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="07610-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="07610-121">Ces deux URL ont le même origine :</span><span class="sxs-lookup"><span data-stu-id="07610-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="07610-122">Ces URL ont des origines différentes des deux précédentes URL :</span><span class="sxs-lookup"><span data-stu-id="07610-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="07610-123">`https://example.net`: Domaine différent</span><span class="sxs-lookup"><span data-stu-id="07610-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="07610-124">`https://www.example.com/foo.html`: Autre sous-domaine</span><span class="sxs-lookup"><span data-stu-id="07610-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="07610-125">`http://example.com/foo.html`: Schéma différent</span><span class="sxs-lookup"><span data-stu-id="07610-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="07610-126">`https://example.com:9000/foo.html`: Autre port</span><span class="sxs-lookup"><span data-stu-id="07610-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="07610-127">Activez CORS</span><span class="sxs-lookup"><span data-stu-id="07610-127">Enable CORS</span></span>

<span data-ttu-id="07610-128">Il existe trois façons d’activer CORS :</span><span class="sxs-lookup"><span data-stu-id="07610-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="07610-129">Dans l’intergiciel (middleware) à l’aide d’une [stratégie nommée](#np) ou d’une [stratégie par défaut](#dp).</span><span class="sxs-lookup"><span data-stu-id="07610-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="07610-130">Utilisation du [routage de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="07610-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="07610-131">Avec l’attribut [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="07610-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="07610-132">L’utilisation de l’attribut [[EnableCors]](#attr) avec une stratégie nommée offre un contrôle plus fin pour limiter les points de terminaison qui prennent en charge cors.</span><span class="sxs-lookup"><span data-stu-id="07610-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="07610-133"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> doit être appelé dans le bon ordre.</span><span class="sxs-lookup"><span data-stu-id="07610-133"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> must be called in the correct order.</span></span> <span data-ttu-id="07610-134">Pour plus d’informations, consultez [ordre des intergiciels (middleware](xref:fundamentals/middleware/index#middleware-order)).</span><span class="sxs-lookup"><span data-stu-id="07610-134">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span> <span data-ttu-id="07610-135">Par exemple, `UseCors` doit être appelé avant <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> lors de l’utilisation de `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="07610-135">For example, `UseCors` must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="07610-136">Chaque approche est décrite en détail dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="07610-136">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="07610-137">CORS avec la stratégie et l’intergiciel (middleware) nommés</span><span class="sxs-lookup"><span data-stu-id="07610-137">CORS with named policy and middleware</span></span>

<span data-ttu-id="07610-138">L’intergiciel (middleware) CORS gère les demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-138">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="07610-139">Le code suivant applique une stratégie CORS à tous les points de terminaison de l’application avec les origines spécifiées :</span><span class="sxs-lookup"><span data-stu-id="07610-139">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="07610-140">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="07610-140">The preceding code:</span></span>

* <span data-ttu-id="07610-141">Définit le nom de la stratégie sur `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="07610-141">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="07610-142">Le nom de la stratégie est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="07610-142">The policy name is arbitrary.</span></span>
* <span data-ttu-id="07610-143">Appelle la <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> méthode d’extension et spécifie la  `_myAllowSpecificOrigins` stratégie cors.</span><span class="sxs-lookup"><span data-stu-id="07610-143">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="07610-144">`UseCors` Ajoute l’intergiciel (middleware) CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-144">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="07610-145">L’appel à `UseCors` doit être placé après `UseRouting` , mais avant `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="07610-145">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="07610-146">Pour plus d’informations, consultez [ordre des intergiciels (middleware](xref:fundamentals/middleware/index#middleware-order)).</span><span class="sxs-lookup"><span data-stu-id="07610-146">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="07610-147">Appelle <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="07610-147">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="07610-148">L’expression lambda prend un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objet.</span><span class="sxs-lookup"><span data-stu-id="07610-148">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="07610-149">Les [options de configuration](#cors-policy-options), telles que `WithOrigins` , sont décrites plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="07610-149">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="07610-150">Active la `_myAllowSpecificOrigins` stratégie cors pour tous les points de terminaison de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="07610-150">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="07610-151">Consultez [routage des points de terminaison](#ecors) pour appliquer une stratégie cors à des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="07610-151">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="07610-152">Lors de l’utilisation de l’intergiciel (middleware) de [mise en cache des réponses](xref:performance/caching/middleware), appelez <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> avant <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .</span><span class="sxs-lookup"><span data-stu-id="07610-152">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="07610-153">Avec le routage de point de terminaison, l’intergiciel (middleware) CORS **doit** être configuré pour s’exécuter entre les appels à `UseRouting` et `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="07610-153">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="07610-154">Consultez la page [test cors](#testc) pour obtenir des instructions sur le test de code similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="07610-154">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="07610-155">L' <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> appel de méthode ajoute des services cors au conteneur de services de l’application :</span><span class="sxs-lookup"><span data-stu-id="07610-155">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="07610-156">Pour plus d’informations, consultez les [options de stratégie cors](#cpo) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="07610-156">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="07610-157">Les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthodes peuvent être chaînées, comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="07610-157">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="07610-158">Remarque : l’URL spécifiée **ne doit pas** contenir de barre oblique finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="07610-158">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="07610-159">Si l’URL se termine par `/` , la comparaison retourne `false` et aucun en-tête n’est retourné.</span><span class="sxs-lookup"><span data-stu-id="07610-159">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="07610-160">CORS avec la stratégie et l’intergiciel (middleware) par défaut</span><span class="sxs-lookup"><span data-stu-id="07610-160">CORS with default policy and middleware</span></span>

<span data-ttu-id="07610-161">Le code en surbrillance suivant active la stratégie CORS par défaut :</span><span class="sxs-lookup"><span data-stu-id="07610-161">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="07610-162">Le code précédent applique la stratégie CORS par défaut à tous les points de terminaison de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="07610-162">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="07610-163">Activer cors avec routage du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="07610-163">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="07610-164">L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les [demandes de prévols automatiques](#apf).</span><span class="sxs-lookup"><span data-stu-id="07610-164">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="07610-165">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/20709) et [test cors avec routage des points de terminaison et [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="07610-165">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="07610-166">Avec le routage de point de terminaison, CORS peut être activé sur une base par point de terminaison à l’aide de l' <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ensemble de méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="07610-166">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="07610-167">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="07610-167">In the preceding code:</span></span>

* <span data-ttu-id="07610-168">`app.UseCors` active l’intergiciel (middleware) CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-168">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="07610-169">Étant donné qu’une stratégie par défaut n’a pas été configurée, `app.UseCors()` seuls n’activent cors.</span><span class="sxs-lookup"><span data-stu-id="07610-169">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="07610-170">Les `/echo` points de terminaison et des contrôleurs autorisent les demandes Cross-Origin à l’aide de la stratégie spécifiée.</span><span class="sxs-lookup"><span data-stu-id="07610-170">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="07610-171">Les `/echo2` Razor points de terminaison et pages n’autorisent **pas** les demandes Cross-Origin, car aucune stratégie par défaut n’a été spécifiée.</span><span class="sxs-lookup"><span data-stu-id="07610-171">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="07610-172">L’attribut [[DisableCors]](#dc) ne désactive **pas**  cors qui a été activé par le routage de point de terminaison avec `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="07610-172">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="07610-173">Consultez [test cors avec routage des points de terminaison et [HttpOptions]](#tcer) pour obtenir des instructions sur le test de code similaire au précédent.</span><span class="sxs-lookup"><span data-stu-id="07610-173">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="07610-174">Activer CORS avec des attributs</span><span class="sxs-lookup"><span data-stu-id="07610-174">Enable CORS with attributes</span></span>

<span data-ttu-id="07610-175">L’activation de CORS avec l’attribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) et l’application d’une stratégie nommée uniquement aux points de terminaison qui requièrent cors fournissent le contrôle le plus fin.</span><span class="sxs-lookup"><span data-stu-id="07610-175">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="07610-176">L’attribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de cors globalement.</span><span class="sxs-lookup"><span data-stu-id="07610-176">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="07610-177">L' `[EnableCors]` attribut Active cors pour les points de terminaison sélectionnés, plutôt que tous les points de terminaison :</span><span class="sxs-lookup"><span data-stu-id="07610-177">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="07610-178">`[EnableCors]` spécifie la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="07610-178">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="07610-179">`[EnableCors("{Policy String}")]` spécifie une stratégie nommée.</span><span class="sxs-lookup"><span data-stu-id="07610-179">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="07610-180">L' `[EnableCors]` attribut peut être appliqué aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="07610-180">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="07610-181">Razor Pagination `PageModel`</span><span class="sxs-lookup"><span data-stu-id="07610-181">Razor Page `PageModel`</span></span>
* <span data-ttu-id="07610-182">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="07610-182">Controller</span></span>
* <span data-ttu-id="07610-183">Méthode d’action du contrôleur</span><span class="sxs-lookup"><span data-stu-id="07610-183">Controller action method</span></span>

<span data-ttu-id="07610-184">Différentes stratégies peuvent être appliquées à des contrôleurs, des modèles de page ou des méthodes d’action avec l' `[EnableCors]` attribut.</span><span class="sxs-lookup"><span data-stu-id="07610-184">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="07610-185">Lorsque l' `[EnableCors]` attribut est appliqué à un contrôleur, à un modèle de page ou à une méthode d’action, et que cors est activé dans l’intergiciel (middleware), **les deux** stratégies sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="07610-185">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="07610-186">**Nous vous recommandons de combiner les stratégies. Utilisez l'** `[EnableCors]` **attribut ou l’intergiciel (middleware), pas les deux dans la même application.**</span><span class="sxs-lookup"><span data-stu-id="07610-186">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="07610-187">Le code suivant applique une stratégie différente à chaque méthode :</span><span class="sxs-lookup"><span data-stu-id="07610-187">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="07610-188">Le code suivant crée deux stratégies CORS :</span><span class="sxs-lookup"><span data-stu-id="07610-188">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="07610-189">Pour un meilleur contrôle de la limitation des demandes CORS :</span><span class="sxs-lookup"><span data-stu-id="07610-189">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="07610-190">Utilisez `[EnableCors("MyPolicy")]` avec une stratégie nommée.</span><span class="sxs-lookup"><span data-stu-id="07610-190">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="07610-191">Ne définissez pas de stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="07610-191">Don't define a default policy.</span></span>
* <span data-ttu-id="07610-192">N’utilisez pas le [routage de point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="07610-192">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="07610-193">Le code de la section suivante répond à la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="07610-193">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="07610-194">Consultez la page [test cors](#testc) pour obtenir des instructions sur le test de code similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="07610-194">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="07610-195">Désactiver CORS</span><span class="sxs-lookup"><span data-stu-id="07610-195">Disable CORS</span></span>

<span data-ttu-id="07610-196">L’attribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ne désactive **pas**  cors qui a été activé par le routage des [points de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="07610-196">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="07610-197">Le code suivant définit la stratégie CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="07610-197">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="07610-198">Le code suivant désactive CORS pour l' `GetValues2` action :</span><span class="sxs-lookup"><span data-stu-id="07610-198">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="07610-199">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="07610-199">The preceding code:</span></span>

* <span data-ttu-id="07610-200">N’active pas CORS avec [routage du point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="07610-200">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="07610-201">Ne définit pas une [stratégie cors par défaut](#dp).</span><span class="sxs-lookup"><span data-stu-id="07610-201">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="07610-202">Utilise [[EnableCors ("MyPolicy")]](#attr) pour activer la `"MyPolicy"` stratégie cors pour le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="07610-202">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="07610-203">Désactive CORS pour la `GetValues2` méthode.</span><span class="sxs-lookup"><span data-stu-id="07610-203">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="07610-204">Consultez la page [test cors](#testc) pour obtenir des instructions sur le test du code précédent.</span><span class="sxs-lookup"><span data-stu-id="07610-204">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="07610-205">Options de stratégie CORS</span><span class="sxs-lookup"><span data-stu-id="07610-205">CORS policy options</span></span>

<span data-ttu-id="07610-206">Cette section décrit les différentes options qui peuvent être définies dans une stratégie CORS :</span><span class="sxs-lookup"><span data-stu-id="07610-206">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="07610-207">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="07610-207">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="07610-208">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="07610-208">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="07610-209">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="07610-209">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="07610-210">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="07610-210">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="07610-211">Informations d’identification dans les demandes Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="07610-211">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="07610-212">Définir le délai d’expiration du prévols</span><span class="sxs-lookup"><span data-stu-id="07610-212">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="07610-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> est appelé dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="07610-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="07610-214">Pour certaines options, il peut être utile de lire d’abord la section [How cors Works](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="07610-214">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="07610-215">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="07610-215">Set the allowed origins</span></span>

<span data-ttu-id="07610-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Autorise les requêtes CORS de toutes les origines avec n’importe quel schéma ( `http` ou `https` ).</span><span class="sxs-lookup"><span data-stu-id="07610-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="07610-217">`AllowAnyOrigin` n’est pas sécurisé, car *un site Web* peut effectuer des demandes Cross-Origin à l’application.</span><span class="sxs-lookup"><span data-stu-id="07610-217">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="07610-218">La spécification de `AllowAnyOrigin` et `AllowCredentials` de est une configuration non sécurisée et peut entraîner une falsification de requête intersites.</span><span class="sxs-lookup"><span data-stu-id="07610-218">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="07610-219">Le service CORS retourne une réponse CORS non valide lorsqu’une application est configurée avec les deux méthodes.</span><span class="sxs-lookup"><span data-stu-id="07610-219">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="07610-220">`AllowAnyOrigin` affecte les demandes préliminaires et l' `Access-Control-Allow-Origin` en-tête.</span><span class="sxs-lookup"><span data-stu-id="07610-220">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="07610-221">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="07610-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="07610-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Définit la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriété de la stratégie pour qu’elle soit une fonction qui permet aux origines de correspondre à un domaine générique configuré lors de l’évaluation si l’origine est autorisée.</span><span class="sxs-lookup"><span data-stu-id="07610-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="07610-223">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="07610-223">Set the allowed HTTP methods</span></span>

<span data-ttu-id="07610-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="07610-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="07610-225">Autorise toute méthode HTTP :</span><span class="sxs-lookup"><span data-stu-id="07610-225">Allows any HTTP method:</span></span>
* <span data-ttu-id="07610-226">Affecte les demandes préliminaires et l' `Access-Control-Allow-Methods` en-tête.</span><span class="sxs-lookup"><span data-stu-id="07610-226">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="07610-227">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="07610-227">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="07610-228">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="07610-228">Set the allowed request headers</span></span>

<span data-ttu-id="07610-229">Pour autoriser l’envoi d’en-têtes spécifiques dans une demande CORS, appelée [créer des en-têtes de demande](https://xhr.spec.whatwg.org/#request), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> et spécifiez les en-têtes autorisés :</span><span class="sxs-lookup"><span data-stu-id="07610-229">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="07610-230">Pour autoriser tous les [en-têtes de demande d’auteur](https://www.w3.org/TR/cors/#author-request-headers), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="07610-230">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="07610-231">`AllowAnyHeader` affecte les demandes préliminaires et l’en-tête [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="07610-231">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="07610-232">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="07610-232">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="07610-233">Une stratégie d’intergiciel (middleware) CORS correspondant à des en-têtes spécifiques spécifiés par `WithHeaders` n’est possible que lorsque les en-têtes envoyés dans `Access-Control-Request-Headers` correspondent exactement aux en-têtes indiqués dans `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="07610-233">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="07610-234">Par exemple, considérez une application configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="07610-234">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="07610-235">L’intergiciel (middleware) CORS refuse une demande préliminaire avec l’en-tête de demande suivant, car `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) n’est pas listé dans `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="07610-235">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="07610-236">L’application renvoie une réponse *200 OK* , mais n’envoie pas les en-têtes cors.</span><span class="sxs-lookup"><span data-stu-id="07610-236">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="07610-237">Par conséquent, le navigateur n’essaie pas la demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-237">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="07610-238">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="07610-238">Set the exposed response headers</span></span>

<span data-ttu-id="07610-239">Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application.</span><span class="sxs-lookup"><span data-stu-id="07610-239">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="07610-240">Pour plus d’informations, consultez la page [relative au partage des ressources Cross-Origin W3C (terminologie) : en-tête de réponse simple](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="07610-240">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="07610-241">Les en-têtes de réponse qui sont disponibles par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="07610-241">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="07610-242">La spécification CORS appelle ces en *-têtes de réponse simples* en-têtes.</span><span class="sxs-lookup"><span data-stu-id="07610-242">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="07610-243">Pour mettre d’autres en-têtes à la disposition de l’application, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="07610-243">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="07610-244">Informations d’identification dans les demandes Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="07610-244">Credentials in cross-origin requests</span></span>

<span data-ttu-id="07610-245">Les informations d’identification nécessitent un traitement spécial dans une demande CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-245">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="07610-246">Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-246">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="07610-247">Les informations d’identification incluent cookie les schémas d’authentification s et http.</span><span class="sxs-lookup"><span data-stu-id="07610-247">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="07610-248">Pour envoyer des informations d’identification avec une demande Cross-Origin, le client doit affecter à la valeur `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="07610-248">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="07610-249">Utilisation `XMLHttpRequest` directe :</span><span class="sxs-lookup"><span data-stu-id="07610-249">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="07610-250">Utilisation de jQuery :</span><span class="sxs-lookup"><span data-stu-id="07610-250">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="07610-251">Utilisation de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="07610-251">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="07610-252">Le serveur doit autoriser les informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="07610-252">The server must allow the credentials.</span></span> <span data-ttu-id="07610-253">Pour autoriser les informations d’identification Cross-Origin, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="07610-253">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="07610-254">La réponse HTTP comprend un `Access-Control-Allow-Credentials` en-tête qui indique au navigateur que le serveur autorise les informations d’identification pour une demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-254">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="07610-255">Si le navigateur envoie des informations d’identification mais que la réponse n’inclut pas d' `Access-Control-Allow-Credentials` en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande Cross-Origin échoue.</span><span class="sxs-lookup"><span data-stu-id="07610-255">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="07610-256">L’autorisation des informations d’identification Cross-Origin est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="07610-256">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="07610-257">Un site Web dans un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application pour le compte de l’utilisateur sans la connaissance de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="07610-257">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="07610-258">La spécification CORS indique également que le paramètre Origins to `"*"` (All Origins) n’est pas valide si l' `Access-Control-Allow-Credentials` en-tête est présent.</span><span class="sxs-lookup"><span data-stu-id="07610-258">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="07610-259">Demandes préliminaires</span><span class="sxs-lookup"><span data-stu-id="07610-259">Preflight requests</span></span>

<span data-ttu-id="07610-260">Pour certaines demandes CORS, le navigateur envoie une demande d' [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) supplémentaire avant d’effectuer la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="07610-260">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="07610-261">Cette demande porte le nom de [demande préliminaire](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="07610-261">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="07610-262">Le navigateur peut ignorer la demande préliminaire si **toutes** les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="07610-262">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="07610-263">La méthode de demande est : obtenir, début ou publication.</span><span class="sxs-lookup"><span data-stu-id="07610-263">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="07610-264">L’application ne définit pas les en-têtes de requête autres que `Accept` , `Accept-Language` ,, `Content-Language` `Content-Type` ou `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="07610-264">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="07610-265">L' `Content-Type` en-tête, s’il est défini, a l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="07610-265">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="07610-266">La règle sur les en-têtes de demande définie pour la demande du client s’applique aux en-têtes définis par l’application en appelant `setRequestHeader` sur l' `XMLHttpRequest` objet.</span><span class="sxs-lookup"><span data-stu-id="07610-266">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="07610-267">La spécification CORS appelle ces en-têtes [créer des en-](https://www.w3.org/TR/cors/#author-request-headers)têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="07610-267">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="07610-268">La règle ne s’applique pas aux en-têtes que le navigateur peut définir, tels que `User-Agent` , `Host` ou `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="07610-268">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="07610-269">Voici un exemple de réponse similaire à la demande préliminaire effectuée à partir du bouton **[placer un test]** dans la section [cors de test](#testc) de ce document.</span><span class="sxs-lookup"><span data-stu-id="07610-269">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="07610-270">La demande préliminaire utilise la méthode [http options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="07610-270">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="07610-271">Il peut inclure les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="07610-271">It may include the following headers:</span></span>

* <span data-ttu-id="07610-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): méthode http qui sera utilisée pour la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="07610-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="07610-273">[Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): liste des en-têtes de requête que l’application définit sur la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="07610-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="07610-274">Comme indiqué précédemment, cela n’inclut pas les en-têtes définis par le navigateur, tels que `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="07610-274">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="07610-275">Access-Control-allow-Methods</span><span class="sxs-lookup"><span data-stu-id="07610-275">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="07610-276">Si la demande préliminaire est refusée, l’application retourne une `200 OK` réponse, mais ne définit pas les en-têtes cors.</span><span class="sxs-lookup"><span data-stu-id="07610-276">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="07610-277">Par conséquent, le navigateur n’essaie pas la demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-277">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="07610-278">Pour obtenir un exemple de demande préliminaire refusée, consultez la section « [test cors](#testc) » de ce document.</span><span class="sxs-lookup"><span data-stu-id="07610-278">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="07610-279">À l’aide des outils F12, l’application console affiche une erreur semblable à l’une des options suivantes, selon le navigateur :</span><span class="sxs-lookup"><span data-stu-id="07610-279">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="07610-280">Firefox : requête Cross-Origin bloquée : la même stratégie d’origine interdit la lecture de la ressource distante à l’adresse `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="07610-280">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="07610-281">(Raison : la demande CORS n’a pas pu être effectuée).</span><span class="sxs-lookup"><span data-stu-id="07610-281">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="07610-282">En savoir plus</span><span class="sxs-lookup"><span data-stu-id="07610-282">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="07610-283">Basé sur le chrome : l’accès à la récupération sur « https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 » à partir de l’origine « https://cors3.azurewebsites.net » a été bloqué par la stratégie cors : la réponse à la demande préliminaire ne réussit pas la vérification du contrôle d’accès : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="07610-283">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="07610-284">Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.</span><span class="sxs-lookup"><span data-stu-id="07610-284">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="07610-285">Pour autoriser des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="07610-285">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="07610-286">Pour autoriser tous les [en-têtes de demande d’auteur](https://www.w3.org/TR/cors/#author-request-headers), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="07610-286">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="07610-287">Les navigateurs ne sont pas cohérents dans la façon dont ils sont définis `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="07610-287">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="07610-288">Si l’une des deux :</span><span class="sxs-lookup"><span data-stu-id="07610-288">If either:</span></span>

* <span data-ttu-id="07610-289">Les en-têtes sont définis sur une valeur autre que `"*"`</span><span class="sxs-lookup"><span data-stu-id="07610-289">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="07610-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> est appelé : inclure au moins `Accept` , `Content-Type` , et `Origin` , ainsi que tous les en-têtes personnalisés que vous souhaitez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="07610-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="07610-291">Code de demande préliminaire automatique</span><span class="sxs-lookup"><span data-stu-id="07610-291">Automatic preflight request code</span></span>

<span data-ttu-id="07610-292">Lorsque la stratégie CORS est appliquée :</span><span class="sxs-lookup"><span data-stu-id="07610-292">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="07610-293">Globalement en appelant `app.UseCors` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="07610-293">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="07610-294">À l’aide de l' `[EnableCors]` attribut.</span><span class="sxs-lookup"><span data-stu-id="07610-294">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="07610-295">ASP.NET Core répond à la demande d’OPTIONS préliminaires.</span><span class="sxs-lookup"><span data-stu-id="07610-295">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="07610-296">L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les demandes de prévols automatiques.</span><span class="sxs-lookup"><span data-stu-id="07610-296">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="07610-297">La section « [cors de test](#testc) » de ce document illustre ce comportement.</span><span class="sxs-lookup"><span data-stu-id="07610-297">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="07610-298">Attribut [HttpOptions] pour les demandes préliminaires</span><span class="sxs-lookup"><span data-stu-id="07610-298">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="07610-299">Lorsque CORS est activé avec la stratégie appropriée, ASP.NET Core répond généralement aux demandes préliminaires CORS automatiquement.</span><span class="sxs-lookup"><span data-stu-id="07610-299">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="07610-300">Dans certains scénarios, cela peut ne pas être le cas.</span><span class="sxs-lookup"><span data-stu-id="07610-300">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="07610-301">Par exemple, à l’aide de [cors avec routage du point de terminaison](#ecors).</span><span class="sxs-lookup"><span data-stu-id="07610-301">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="07610-302">Le code suivant utilise l’attribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) pour créer des points de terminaison pour les demandes d’options :</span><span class="sxs-lookup"><span data-stu-id="07610-302">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="07610-303">Consultez [test cors avec routage des points de terminaison et [HttpOptions]](#tcer) pour obtenir des instructions sur le test du code précédent.</span><span class="sxs-lookup"><span data-stu-id="07610-303">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="07610-304">Définir le délai d’expiration du prévols</span><span class="sxs-lookup"><span data-stu-id="07610-304">Set the preflight expiration time</span></span>

<span data-ttu-id="07610-305">L' `Access-Control-Max-Age` en-tête spécifie la durée pendant laquelle la réponse à la demande préliminaire peut être mise en cache.</span><span class="sxs-lookup"><span data-stu-id="07610-305">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="07610-306">Pour définir cet en-tête, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="07610-306">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="07610-307">Fonctionnement de CORS</span><span class="sxs-lookup"><span data-stu-id="07610-307">How CORS works</span></span>

<span data-ttu-id="07610-308">Cette section décrit ce qui se produit dans une demande [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages http.</span><span class="sxs-lookup"><span data-stu-id="07610-308">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="07610-309">CORS n’est **pas** une fonctionnalité de sécurité.</span><span class="sxs-lookup"><span data-stu-id="07610-309">CORS is **not** a security feature.</span></span> <span data-ttu-id="07610-310">CORS est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.</span><span class="sxs-lookup"><span data-stu-id="07610-310">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="07610-311">Par exemple, un acteur malveillant peut utiliser des [scripts inter-sites (XSS)](xref:security/cross-site-scripting) sur votre site et exécuter une requête intersite vers son site Active cors pour voler des informations.</span><span class="sxs-lookup"><span data-stu-id="07610-311">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="07610-312">Une API n’est pas plus sûre en autorisant CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-312">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="07610-313">C’est au client (navigateur) d’appliquer CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-313">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="07610-314">Le serveur exécute la requête et retourne la réponse, c’est le client qui retourne une erreur et bloque la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-314">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="07610-315">Par exemple, l’un des outils suivants affiche la réponse du serveur :</span><span class="sxs-lookup"><span data-stu-id="07610-315">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="07610-316">Fiddler</span><span class="sxs-lookup"><span data-stu-id="07610-316">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="07610-317">Postman</span><span class="sxs-lookup"><span data-stu-id="07610-317">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="07610-318">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="07610-318">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="07610-319">Un navigateur Web en entrant l’URL dans la barre d’adresses.</span><span class="sxs-lookup"><span data-stu-id="07610-319">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="07610-320">C’est un moyen pour un serveur d’autoriser les navigateurs à exécuter une requête d’API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) Cross-Origin qui serait sinon interdite.</span><span class="sxs-lookup"><span data-stu-id="07610-320">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="07610-321">Les navigateurs sans CORS ne peuvent pas effectuer de requêtes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-321">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="07610-322">Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) était utilisé pour contourner cette restriction.</span><span class="sxs-lookup"><span data-stu-id="07610-322">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="07610-323">JSONP n’utilise pas XHR, elle utilise la `<script>` balise pour recevoir la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-323">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="07610-324">Les scripts sont autorisés à être chargés sur plusieurs origines.</span><span class="sxs-lookup"><span data-stu-id="07610-324">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="07610-325">La [spécification cors](https://www.w3.org/TR/cors/) a introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-325">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="07610-326">Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-326">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="07610-327">Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-327">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="07610-328">[Bouton put test](https://cors3.azurewebsites.net/test) sur l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) déployé</span><span class="sxs-lookup"><span data-stu-id="07610-328">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="07610-329">Voici un exemple de demande Cross-Origin à partir du bouton de test des [valeurs](https://cors3.azurewebsites.net/) vers `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="07610-329">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="07610-330">L' `Origin` en-tête :</span><span class="sxs-lookup"><span data-stu-id="07610-330">The `Origin` header:</span></span>

* <span data-ttu-id="07610-331">Fournit le domaine du site qui effectue la demande.</span><span class="sxs-lookup"><span data-stu-id="07610-331">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="07610-332">Est obligatoire et doit être différent de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="07610-332">Is required and must be different from the host.</span></span>

<span data-ttu-id="07610-333">**En-têtes généraux**</span><span class="sxs-lookup"><span data-stu-id="07610-333">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="07610-334">**En-têtes de réponse**</span><span class="sxs-lookup"><span data-stu-id="07610-334">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="07610-335">**En-têtes de demande**</span><span class="sxs-lookup"><span data-stu-id="07610-335">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="07610-336">Dans `OPTIONS` les demandes, le serveur définit l’en-tête des **en-têtes de réponse** `Access-Control-Allow-Origin: {allowed origin}` dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-336">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="07610-337">Par exemple, l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)de requête de bouton [delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) déployé `OPTIONS` contient les en-têtes suivants :</span><span class="sxs-lookup"><span data-stu-id="07610-337">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="07610-338">**En-têtes généraux**</span><span class="sxs-lookup"><span data-stu-id="07610-338">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="07610-339">**En-têtes de réponse**</span><span class="sxs-lookup"><span data-stu-id="07610-339">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="07610-340">**En-têtes de demande**</span><span class="sxs-lookup"><span data-stu-id="07610-340">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="07610-341">Dans les **en-têtes de réponse** précédents, le serveur définit l’en-tête [Access-Control-allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-341">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="07610-342">La `https://cors1.azurewebsites.net` valeur de cet en-tête correspond à l' `Origin` en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="07610-342">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="07610-343">Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> est appelé, la `Access-Control-Allow-Origin: *` valeur de caractère générique est retournée.</span><span class="sxs-lookup"><span data-stu-id="07610-343">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="07610-344">`AllowAnyOrigin` autorise toute origine.</span><span class="sxs-lookup"><span data-stu-id="07610-344">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="07610-345">Si la réponse n’inclut pas l' `Access-Control-Allow-Origin` en-tête, la demande Cross-Origin échoue.</span><span class="sxs-lookup"><span data-stu-id="07610-345">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="07610-346">Plus précisément, le navigateur n’autorise pas la demande.</span><span class="sxs-lookup"><span data-stu-id="07610-346">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="07610-347">Même si le serveur retourne une réponse correcte, le navigateur ne rend pas la réponse disponible pour l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="07610-347">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="07610-348">Afficher les demandes OPTIONS</span><span class="sxs-lookup"><span data-stu-id="07610-348">Display OPTIONS requests</span></span>

<span data-ttu-id="07610-349">Par défaut, les navigateurs Chrome et Edge n’affichent pas les demandes OPTIONS sous l’onglet Réseau des outils F12.</span><span class="sxs-lookup"><span data-stu-id="07610-349">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="07610-350">Pour afficher les demandes OPTIONS dans ces navigateurs :</span><span class="sxs-lookup"><span data-stu-id="07610-350">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="07610-351">`chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="07610-351">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="07610-352">Désactivez l’indicateur.</span><span class="sxs-lookup"><span data-stu-id="07610-352">disable the flag.</span></span>
* <span data-ttu-id="07610-353">redémarrer.</span><span class="sxs-lookup"><span data-stu-id="07610-353">restart.</span></span>

<span data-ttu-id="07610-354">Firefox affiche les demandes d’OPTIONS par défaut.</span><span class="sxs-lookup"><span data-stu-id="07610-354">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="07610-355">CORS dans IIS</span><span class="sxs-lookup"><span data-stu-id="07610-355">CORS in IIS</span></span>

<span data-ttu-id="07610-356">Lors du déploiement sur IIS, CORS doit s’exécuter avant l’authentification Windows si le serveur n’est pas configuré pour autoriser l’accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="07610-356">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="07610-357">Pour prendre en charge ce scénario, le [module cors IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.</span><span class="sxs-lookup"><span data-stu-id="07610-357">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="07610-358">Tester CORS</span><span class="sxs-lookup"><span data-stu-id="07610-358">Test CORS</span></span>

<span data-ttu-id="07610-359">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) contient du code pour tester cors.</span><span class="sxs-lookup"><span data-stu-id="07610-359">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="07610-360">Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="07610-360">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="07610-361">L’exemple est un projet d’API avec des Razor pages ajoutées :</span><span class="sxs-lookup"><span data-stu-id="07610-361">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="07610-362">`WithOrigins("https://localhost:<port>");` doit uniquement être utilisé pour tester un exemple d’application semblable à l' [exemple de code de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="07610-362">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="07610-363">Les éléments suivants `ValuesController` fournissent les points de terminaison pour le test :</span><span class="sxs-lookup"><span data-stu-id="07610-363">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="07610-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) est fourni par le package NuGet [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) et affiche des informations sur l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="07610-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="07610-365">Testez l’exemple de code précédent à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="07610-365">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="07610-366">Utilisez l’exemple d’application déployée à l’adresse [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="07610-366">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="07610-367">Il n’est pas nécessaire de télécharger l’exemple.</span><span class="sxs-lookup"><span data-stu-id="07610-367">There is no need to download the sample.</span></span>
* <span data-ttu-id="07610-368">Exécutez l’exemple avec l' `dotnet run` URL par défaut de `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="07610-368">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="07610-369">Exécutez l’exemple à partir de Visual Studio avec le port défini sur 44398 pour une URL de `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="07610-369">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="07610-370">À l’aide d’un navigateur avec les outils F12 :</span><span class="sxs-lookup"><span data-stu-id="07610-370">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="07610-371">Sélectionnez le bouton **valeurs** et passez en revue les en-têtes sous l’onglet **réseau** .</span><span class="sxs-lookup"><span data-stu-id="07610-371">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="07610-372">Sélectionnez le bouton **put test** .</span><span class="sxs-lookup"><span data-stu-id="07610-372">Select the **PUT test** button.</span></span> <span data-ttu-id="07610-373">Consultez [afficher les demandes d’options](#options) pour obtenir des instructions sur l’affichage de la demande d’options.</span><span class="sxs-lookup"><span data-stu-id="07610-373">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="07610-374">Le **test put** crée deux demandes, une demande préliminaire options et la demande put.</span><span class="sxs-lookup"><span data-stu-id="07610-374">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="07610-375">Sélectionnez le **`GetValues2 [DisableCors]`** bouton pour déclencher une demande cors ayant échoué.</span><span class="sxs-lookup"><span data-stu-id="07610-375">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="07610-376">Comme mentionné dans le document, la réponse retourne 200 réussite, mais la demande CORS n’est pas effectuée.</span><span class="sxs-lookup"><span data-stu-id="07610-376">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="07610-377">Sélectionnez l’onglet **console** pour afficher l’erreur cors.</span><span class="sxs-lookup"><span data-stu-id="07610-377">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="07610-378">En fonction du navigateur, une erreur semblable à la suivante s’affiche :</span><span class="sxs-lookup"><span data-stu-id="07610-378">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="07610-379">L’accès à l’extraction à `'https://cors1.azurewebsites.net/api/values/GetValues2'` partir de l’origine `'https://cors3.azurewebsites.net'` a été bloqué par la stratégie cors : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="07610-379">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="07610-380">Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.</span><span class="sxs-lookup"><span data-stu-id="07610-380">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="07610-381">Les points de terminaison compatibles CORS peuvent être testés à l’aide d’un outil, tel que le terme « [bouclé](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)» ou « [postal](https://www.getpostman.com/)».</span><span class="sxs-lookup"><span data-stu-id="07610-381">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="07610-382">Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l' `Origin` en-tête doit différer de l’hôte recevant la demande.</span><span class="sxs-lookup"><span data-stu-id="07610-382">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="07610-383">Si la requête n’est pas *une origine croisée* en fonction de la valeur de l' `Origin` en-tête :</span><span class="sxs-lookup"><span data-stu-id="07610-383">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="07610-384">Il n’est pas nécessaire d’utiliser un intergiciel (middleware) CORS pour traiter la requête.</span><span class="sxs-lookup"><span data-stu-id="07610-384">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="07610-385">Les en-têtes CORS ne sont pas retournés dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-385">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="07610-386">La commande suivante utilise `curl` pour émettre une demande d’options avec des informations :</span><span class="sxs-lookup"><span data-stu-id="07610-386">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="07610-387">De test CORS avec routage du point de terminaison et [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="07610-387">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="07610-388">L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les [demandes de prévols automatiques](#apf).</span><span class="sxs-lookup"><span data-stu-id="07610-388">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="07610-389">Prenons le code suivant qui utilise le [routage de point de terminaison pour activer cors](#ecors):</span><span class="sxs-lookup"><span data-stu-id="07610-389">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="07610-390">Les éléments suivants `TodoItems1Controller` fournissent des points de terminaison pour le test :</span><span class="sxs-lookup"><span data-stu-id="07610-390">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="07610-391">Testez le code précédent à partir de la [page de test](https://cors1.azurewebsites.net/test?number=1) de l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé.</span><span class="sxs-lookup"><span data-stu-id="07610-391">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="07610-392">Les boutons **supprimer [EnableCors]** et **obtenir [EnableCors]** fonctionnent correctement, car les points de terminaison ont `[EnableCors]` et répondent aux demandes préliminaires.</span><span class="sxs-lookup"><span data-stu-id="07610-392">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="07610-393">Les autres points de terminaison échouent.</span><span class="sxs-lookup"><span data-stu-id="07610-393">The other endpoints fails.</span></span> <span data-ttu-id="07610-394">Le bouton d' **extraction** échoue, car le [code JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envoie :</span><span class="sxs-lookup"><span data-stu-id="07610-394">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="07610-395">Les éléments suivants `TodoItems2Controller` fournissent des points de terminaison similaires, mais incluent du code explicite pour répondre aux demandes d’options :</span><span class="sxs-lookup"><span data-stu-id="07610-395">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="07610-396">Testez le code précédent à partir de la [page de test](https://cors1.azurewebsites.net/test?number=2) de l’exemple déployé.</span><span class="sxs-lookup"><span data-stu-id="07610-396">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="07610-397">Dans la liste déroulante **contrôleur** , sélectionnez **prévols** , puis **définir le contrôleur**.</span><span class="sxs-lookup"><span data-stu-id="07610-397">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="07610-398">Tous les appels CORS aux points de terminaison ont été `TodoItems2Controller` correctement effectués.</span><span class="sxs-lookup"><span data-stu-id="07610-398">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07610-399">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="07610-399">Additional resources</span></span>

* [<span data-ttu-id="07610-400">Partage des ressources Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="07610-400">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="07610-401">Prise en main du module IIS CORS</span><span class="sxs-lookup"><span data-stu-id="07610-401">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07610-402">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="07610-402">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="07610-403">Cet article explique comment activer CORS dans une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07610-403">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="07610-404">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="07610-404">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="07610-405">Cette restriction est appelée *stratégie de même origine*.</span><span class="sxs-lookup"><span data-stu-id="07610-405">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="07610-406">La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="07610-406">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="07610-407">Parfois, vous souhaiterez peut-être autoriser d’autres sites à effectuer des demandes Cross-Origin à votre application.</span><span class="sxs-lookup"><span data-stu-id="07610-407">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="07610-408">Pour plus d’informations, consultez l' [article Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="07610-408">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="07610-409">[Partage des ressources Cross-Origin](https://www.w3.org/TR/cors/) (cors) :</span><span class="sxs-lookup"><span data-stu-id="07610-409">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="07610-410">Est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.</span><span class="sxs-lookup"><span data-stu-id="07610-410">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="07610-411">N’est **pas** une fonctionnalité de sécurité, cors assouplit la sécurité.</span><span class="sxs-lookup"><span data-stu-id="07610-411">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="07610-412">Une API n’est pas plus sûre en autorisant CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-412">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="07610-413">Pour plus d’informations, consultez fonctionnement de [cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="07610-413">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="07610-414">Permet à un serveur d’autoriser explicitement certaines demandes Cross-Origin tout en rejetant d’autres.</span><span class="sxs-lookup"><span data-stu-id="07610-414">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="07610-415">Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="07610-415">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="07610-416">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07610-416">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="07610-417">Même origine</span><span class="sxs-lookup"><span data-stu-id="07610-417">Same origin</span></span>

<span data-ttu-id="07610-418">Deux URL ont la même origine si elles ont des schémas, des hôtes et des ports identiques ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="07610-418">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="07610-419">Ces deux URL ont le même origine :</span><span class="sxs-lookup"><span data-stu-id="07610-419">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="07610-420">Ces URL ont des origines différentes des deux précédentes URL :</span><span class="sxs-lookup"><span data-stu-id="07610-420">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="07610-421">`https://example.net`: Domaine différent</span><span class="sxs-lookup"><span data-stu-id="07610-421">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="07610-422">`https://www.example.com/foo.html`: Autre sous-domaine</span><span class="sxs-lookup"><span data-stu-id="07610-422">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="07610-423">`http://example.com/foo.html`: Schéma différent</span><span class="sxs-lookup"><span data-stu-id="07610-423">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="07610-424">`https://example.com:9000/foo.html`: Autre port</span><span class="sxs-lookup"><span data-stu-id="07610-424">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="07610-425">Internet Explorer ne prend pas en compte le port lors de la comparaison des origines.</span><span class="sxs-lookup"><span data-stu-id="07610-425">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="07610-426">CORS avec la stratégie et l’intergiciel (middleware) nommés</span><span class="sxs-lookup"><span data-stu-id="07610-426">CORS with named policy and middleware</span></span>

<span data-ttu-id="07610-427">L’intergiciel (middleware) CORS gère les demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-427">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="07610-428">Le code suivant active CORS pour l’application entière avec l’origine spécifiée :</span><span class="sxs-lookup"><span data-stu-id="07610-428">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="07610-429">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="07610-429">The preceding code:</span></span>

* <span data-ttu-id="07610-430">Définit le nom de la stratégie sur « \_ myAllowSpecificOrigins ».</span><span class="sxs-lookup"><span data-stu-id="07610-430">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="07610-431">Le nom de la stratégie est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="07610-431">The policy name is arbitrary.</span></span>
* <span data-ttu-id="07610-432">Appelle la <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> méthode d’extension, qui active cors.</span><span class="sxs-lookup"><span data-stu-id="07610-432">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="07610-433">Appelle <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="07610-433">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="07610-434">L’expression lambda prend un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objet.</span><span class="sxs-lookup"><span data-stu-id="07610-434">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="07610-435">Les [options de configuration](#cors-policy-options), telles que `WithOrigins` , sont décrites plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="07610-435">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="07610-436">L' <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> appel de méthode ajoute des services cors au conteneur de services de l’application :</span><span class="sxs-lookup"><span data-stu-id="07610-436">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="07610-437">Pour plus d’informations, consultez les [options de stratégie cors](#cpo) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="07610-437">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="07610-438">La <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthode peut chaîner des méthodes, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="07610-438">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="07610-439">Remarque : l’URL **ne doit pas** contenir de barre oblique finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="07610-439">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="07610-440">Si l’URL se termine par `/` , la comparaison retourne `false` et aucun en-tête n’est retourné.</span><span class="sxs-lookup"><span data-stu-id="07610-440">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="07610-441">Le code suivant applique des stratégies CORS à tous les points de terminaison des applications par le biais de l’intergiciel (middleware) CORS :</span><span class="sxs-lookup"><span data-stu-id="07610-441">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="07610-442">Remarque : `UseCors` doit être appelé avant `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="07610-442">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="07610-443">Consultez [activer cors dans les Razor pages, les contrôleurs et les méthodes d’action](#ecors) pour appliquer la stratégie cors au niveau de la page/du contrôleur/action.</span><span class="sxs-lookup"><span data-stu-id="07610-443">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="07610-444">Consultez la page [test cors](#test) pour obtenir des instructions sur le test de code similaire au code précédent.</span><span class="sxs-lookup"><span data-stu-id="07610-444">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="07610-445">Activer CORS avec des attributs</span><span class="sxs-lookup"><span data-stu-id="07610-445">Enable CORS with attributes</span></span>

<span data-ttu-id="07610-446">L’attribut [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fournit une alternative à l’application de cors globalement.</span><span class="sxs-lookup"><span data-stu-id="07610-446">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="07610-447">L' `[EnableCors]` attribut Active cors pour les points de terminaison sélectionnés, plutôt que tous les points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="07610-447">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="07610-448">Utilisez `[EnableCors]` pour spécifier la stratégie par défaut et `[EnableCors("{Policy String}")]` spécifier une stratégie.</span><span class="sxs-lookup"><span data-stu-id="07610-448">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="07610-449">L' `[EnableCors]` attribut peut être appliqué aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="07610-449">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="07610-450">Razor Pagination `PageModel`</span><span class="sxs-lookup"><span data-stu-id="07610-450">Razor Page `PageModel`</span></span>
* <span data-ttu-id="07610-451">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="07610-451">Controller</span></span>
* <span data-ttu-id="07610-452">Méthode d’action du contrôleur</span><span class="sxs-lookup"><span data-stu-id="07610-452">Controller action method</span></span>

<span data-ttu-id="07610-453">Vous pouvez appliquer différentes stratégies à Controller/page-Model/action avec l'  `[EnableCors]` attribut.</span><span class="sxs-lookup"><span data-stu-id="07610-453">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="07610-454">Lorsque l' `[EnableCors]` attribut est appliqué à un modèle de contrôleurs/page/méthode d’action et que cors est activé dans l’intergiciel (middleware), **les deux** stratégies sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="07610-454">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="07610-455">Nous vous recommandons de **ne pas** combiner les stratégies.</span><span class="sxs-lookup"><span data-stu-id="07610-455">We recommend **not** combining policies.</span></span> <span data-ttu-id="07610-456">Utilisez l' `[EnableCors]` attribut ou l’intergiciel (middleware), **pas les deux**.</span><span class="sxs-lookup"><span data-stu-id="07610-456">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="07610-457">Lorsque vous utilisez `[EnableCors]` , ne définissez **pas** de stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="07610-457">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="07610-458">Le code suivant applique une stratégie différente à chaque méthode :</span><span class="sxs-lookup"><span data-stu-id="07610-458">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="07610-459">Le code suivant crée une stratégie CORS par défaut et une stratégie nommée `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="07610-459">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="07610-460">Désactiver CORS</span><span class="sxs-lookup"><span data-stu-id="07610-460">Disable CORS</span></span>

<span data-ttu-id="07610-461">L’attribut [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) désactive cors pour le contrôleur/page-Model/action.</span><span class="sxs-lookup"><span data-stu-id="07610-461">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="07610-462">Options de stratégie CORS</span><span class="sxs-lookup"><span data-stu-id="07610-462">CORS policy options</span></span>

<span data-ttu-id="07610-463">Cette section décrit les différentes options qui peuvent être définies dans une stratégie CORS :</span><span class="sxs-lookup"><span data-stu-id="07610-463">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="07610-464">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="07610-464">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="07610-465">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="07610-465">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="07610-466">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="07610-466">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="07610-467">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="07610-467">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="07610-468">Informations d’identification dans les demandes Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="07610-468">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="07610-469">Définir le délai d’expiration du prévols</span><span class="sxs-lookup"><span data-stu-id="07610-469">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="07610-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> est appelé dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="07610-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="07610-471">Pour certaines options, il peut être utile de lire d’abord la section [How cors Works](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="07610-471">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="07610-472">Définir les origines autorisées</span><span class="sxs-lookup"><span data-stu-id="07610-472">Set the allowed origins</span></span>

<span data-ttu-id="07610-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Autorise les requêtes CORS de toutes les origines avec n’importe quel schéma ( `http` ou `https` ).</span><span class="sxs-lookup"><span data-stu-id="07610-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="07610-474">`AllowAnyOrigin` n’est pas sécurisé, car *un site Web* peut effectuer des demandes Cross-Origin à l’application.</span><span class="sxs-lookup"><span data-stu-id="07610-474">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="07610-475">La spécification de `AllowAnyOrigin` et `AllowCredentials` de est une configuration non sécurisée et peut entraîner une falsification de requête intersites.</span><span class="sxs-lookup"><span data-stu-id="07610-475">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="07610-476">Pour une application sécurisée, spécifiez une liste exacte d’origines si le client doit s’autoriser à accéder aux ressources du serveur.</span><span class="sxs-lookup"><span data-stu-id="07610-476">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="07610-477">`AllowAnyOrigin` affecte les demandes préliminaires et l' `Access-Control-Allow-Origin` en-tête.</span><span class="sxs-lookup"><span data-stu-id="07610-477">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="07610-478">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="07610-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="07610-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Définit la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriété de la stratégie pour qu’elle soit une fonction qui permet aux origines de correspondre à un domaine générique configuré lors de l’évaluation si l’origine est autorisée.</span><span class="sxs-lookup"><span data-stu-id="07610-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="07610-480">Définir les méthodes HTTP autorisées</span><span class="sxs-lookup"><span data-stu-id="07610-480">Set the allowed HTTP methods</span></span>

<span data-ttu-id="07610-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="07610-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="07610-482">Autorise toute méthode HTTP :</span><span class="sxs-lookup"><span data-stu-id="07610-482">Allows any HTTP method:</span></span>
* <span data-ttu-id="07610-483">Affecte les demandes préliminaires et l' `Access-Control-Allow-Methods` en-tête.</span><span class="sxs-lookup"><span data-stu-id="07610-483">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="07610-484">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="07610-484">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="07610-485">Définir les en-têtes de demande autorisés</span><span class="sxs-lookup"><span data-stu-id="07610-485">Set the allowed request headers</span></span>

<span data-ttu-id="07610-486">Pour autoriser l’envoi d’en-têtes spécifiques dans une demande CORS, appelée *créer des en-têtes de demande*, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> et spécifiez les en-têtes autorisés :</span><span class="sxs-lookup"><span data-stu-id="07610-486">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="07610-487">Pour autoriser tous les en-têtes de demande d’auteur, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="07610-487">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="07610-488">Ce paramètre affecte les demandes préliminaires et l' `Access-Control-Request-Headers` en-tête.</span><span class="sxs-lookup"><span data-stu-id="07610-488">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="07610-489">Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="07610-489">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="07610-490">L’intergiciel (middleware) CORS autorise toujours l’envoi de quatre en-têtes dans le `Access-Control-Request-Headers` , quelles que soient les valeurs configurées dans les en-têtes CorsPolicy.</span><span class="sxs-lookup"><span data-stu-id="07610-490">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="07610-491">Cette liste d’en-têtes comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="07610-491">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="07610-492">Par exemple, considérez une application configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="07610-492">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="07610-493">L’intergiciel (middleware) CORS répond correctement à une demande préliminaire avec l’en-tête de demande suivant, car `Content-Language` est toujours autorisé :</span><span class="sxs-lookup"><span data-stu-id="07610-493">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="07610-494">Définir les en-têtes de réponse exposés</span><span class="sxs-lookup"><span data-stu-id="07610-494">Set the exposed response headers</span></span>

<span data-ttu-id="07610-495">Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application.</span><span class="sxs-lookup"><span data-stu-id="07610-495">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="07610-496">Pour plus d’informations, consultez la page [relative au partage des ressources Cross-Origin W3C (terminologie) : en-tête de réponse simple](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="07610-496">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="07610-497">Les en-têtes de réponse qui sont disponibles par défaut sont :</span><span class="sxs-lookup"><span data-stu-id="07610-497">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="07610-498">La spécification CORS appelle ces en *-têtes de réponse simples* en-têtes.</span><span class="sxs-lookup"><span data-stu-id="07610-498">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="07610-499">Pour mettre d’autres en-têtes à la disposition de l’application, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="07610-499">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="07610-500">Informations d’identification dans les demandes Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="07610-500">Credentials in cross-origin requests</span></span>

<span data-ttu-id="07610-501">Les informations d’identification nécessitent un traitement spécial dans une demande CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-501">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="07610-502">Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-502">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="07610-503">Les informations d’identification incluent cookie les schémas d’authentification s et http.</span><span class="sxs-lookup"><span data-stu-id="07610-503">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="07610-504">Pour envoyer des informations d’identification avec une demande Cross-Origin, le client doit affecter à la valeur `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="07610-504">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="07610-505">Utilisation `XMLHttpRequest` directe :</span><span class="sxs-lookup"><span data-stu-id="07610-505">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="07610-506">Utilisation de jQuery :</span><span class="sxs-lookup"><span data-stu-id="07610-506">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="07610-507">Utilisation de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="07610-507">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="07610-508">Le serveur doit autoriser les informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="07610-508">The server must allow the credentials.</span></span> <span data-ttu-id="07610-509">Pour autoriser les informations d’identification Cross-Origin, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="07610-509">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="07610-510">La réponse HTTP comprend un `Access-Control-Allow-Credentials` en-tête qui indique au navigateur que le serveur autorise les informations d’identification pour une demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-510">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="07610-511">Si le navigateur envoie des informations d’identification mais que la réponse n’inclut pas d' `Access-Control-Allow-Credentials` en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande Cross-Origin échoue.</span><span class="sxs-lookup"><span data-stu-id="07610-511">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="07610-512">L’autorisation des informations d’identification Cross-Origin est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="07610-512">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="07610-513">Un site Web dans un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application pour le compte de l’utilisateur sans la connaissance de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="07610-513">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="07610-514">La spécification CORS indique également que le paramètre Origins to `"*"` (All Origins) n’est pas valide si l' `Access-Control-Allow-Credentials` en-tête est présent.</span><span class="sxs-lookup"><span data-stu-id="07610-514">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="07610-515">Demandes préliminaires</span><span class="sxs-lookup"><span data-stu-id="07610-515">Preflight requests</span></span>

<span data-ttu-id="07610-516">Pour certaines demandes CORS, le navigateur envoie une demande supplémentaire avant d’effectuer la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="07610-516">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="07610-517">Cette demande porte le nom de *demande préliminaire*.</span><span class="sxs-lookup"><span data-stu-id="07610-517">This request is called a *preflight request*.</span></span> <span data-ttu-id="07610-518">Le navigateur peut ignorer la demande préliminaire si les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="07610-518">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="07610-519">La méthode de demande est : obtenir, début ou publication.</span><span class="sxs-lookup"><span data-stu-id="07610-519">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="07610-520">L’application ne définit pas les en-têtes de requête autres que `Accept` , `Accept-Language` ,, `Content-Language` `Content-Type` ou `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="07610-520">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="07610-521">L' `Content-Type` en-tête, s’il est défini, a l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="07610-521">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="07610-522">La règle sur les en-têtes de demande définie pour la demande du client s’applique aux en-têtes définis par l’application en appelant `setRequestHeader` sur l' `XMLHttpRequest` objet.</span><span class="sxs-lookup"><span data-stu-id="07610-522">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="07610-523">La spécification CORS appelle ces en-têtes *créer des en-* têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="07610-523">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="07610-524">La règle ne s’applique pas aux en-têtes que le navigateur peut définir, tels que `User-Agent` , `Host` ou `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="07610-524">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="07610-525">Voici un exemple de demande préliminaire :</span><span class="sxs-lookup"><span data-stu-id="07610-525">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="07610-526">La requête de pré-vol utilise la méthode HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="07610-526">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="07610-527">Il comprend deux en-têtes spéciaux :</span><span class="sxs-lookup"><span data-stu-id="07610-527">It includes two special headers:</span></span>

* <span data-ttu-id="07610-528">`Access-Control-Request-Method`: Méthode HTTP qui sera utilisée pour la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="07610-528">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="07610-529">`Access-Control-Request-Headers`: Liste des en-têtes de requête que l’application définit sur la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="07610-529">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="07610-530">Comme indiqué précédemment, cela n’inclut pas les en-têtes définis par le navigateur, tels que `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="07610-530">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="07610-531">Lorsque CORS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes préliminaires CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-531">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="07610-532">Consultez l' [attribut [HttpOptions] pour les demandes préliminaires](#pro).</span><span class="sxs-lookup"><span data-stu-id="07610-532">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="07610-533">Une demande préliminaire CORS peut inclure un `Access-Control-Request-Headers` en-tête, qui indique au serveur les en-têtes envoyés avec la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="07610-533">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="07610-534">Pour autoriser des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="07610-534">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="07610-535">Pour autoriser tous les en-têtes de demande d’auteur, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="07610-535">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="07610-536">Les navigateurs ne sont pas entièrement cohérents dans la façon dont ils sont définis `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="07610-536">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="07610-537">Si vous définissez des en-têtes autres que `"*"` (ou utilisez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), vous devez inclure au moins `Accept` , `Content-Type` , et `Origin` , ainsi que tous les en-têtes personnalisés que vous souhaitez prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="07610-537">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="07610-538">Voici un exemple de réponse à la demande préliminaire (en supposant que le serveur autorise la demande) :</span><span class="sxs-lookup"><span data-stu-id="07610-538">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="07610-539">La réponse comprend un `Access-Control-Allow-Methods` en-tête qui répertorie les méthodes autorisées et éventuellement un `Access-Control-Allow-Headers` en-tête, qui répertorie les en-têtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="07610-539">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="07610-540">Si la demande préliminaire est réussie, le navigateur envoie la demande réelle.</span><span class="sxs-lookup"><span data-stu-id="07610-540">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="07610-541">Si la demande préliminaire est refusée, l’application renvoie une réponse *200 OK* , mais n’envoie pas les en-têtes cors.</span><span class="sxs-lookup"><span data-stu-id="07610-541">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="07610-542">Par conséquent, le navigateur n’essaie pas la demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-542">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="07610-543">Définir le délai d’expiration du prévols</span><span class="sxs-lookup"><span data-stu-id="07610-543">Set the preflight expiration time</span></span>

<span data-ttu-id="07610-544">L' `Access-Control-Max-Age` en-tête spécifie la durée pendant laquelle la réponse à la demande préliminaire peut être mise en cache.</span><span class="sxs-lookup"><span data-stu-id="07610-544">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="07610-545">Pour définir cet en-tête, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="07610-545">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="07610-546">Fonctionnement de CORS</span><span class="sxs-lookup"><span data-stu-id="07610-546">How CORS works</span></span>

<span data-ttu-id="07610-547">Cette section décrit ce qui se produit dans une demande [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages http.</span><span class="sxs-lookup"><span data-stu-id="07610-547">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="07610-548">CORS n’est **pas** une fonctionnalité de sécurité.</span><span class="sxs-lookup"><span data-stu-id="07610-548">CORS is **not** a security feature.</span></span> <span data-ttu-id="07610-549">CORS est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.</span><span class="sxs-lookup"><span data-stu-id="07610-549">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="07610-550">Par exemple, un acteur malveillant peut utiliser l’option [empêcher les scripts inter-sites (XSS)](xref:security/cross-site-scripting) sur votre site et exécuter une requête intersite vers son site Active cors pour voler des informations.</span><span class="sxs-lookup"><span data-stu-id="07610-550">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="07610-551">Votre API n’est pas plus sûre en autorisant CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-551">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="07610-552">C’est au client (navigateur) d’appliquer CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-552">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="07610-553">Le serveur exécute la requête et retourne la réponse, c’est le client qui retourne une erreur et bloque la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-553">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="07610-554">Par exemple, l’un des outils suivants affiche la réponse du serveur :</span><span class="sxs-lookup"><span data-stu-id="07610-554">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="07610-555">Fiddler</span><span class="sxs-lookup"><span data-stu-id="07610-555">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="07610-556">Postman</span><span class="sxs-lookup"><span data-stu-id="07610-556">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="07610-557">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="07610-557">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="07610-558">Un navigateur Web en entrant l’URL dans la barre d’adresses.</span><span class="sxs-lookup"><span data-stu-id="07610-558">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="07610-559">C’est un moyen pour un serveur d’autoriser les navigateurs à exécuter une requête d’API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) Cross-Origin qui serait sinon interdite.</span><span class="sxs-lookup"><span data-stu-id="07610-559">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="07610-560">Les navigateurs (sans CORS) ne peuvent pas effectuer de demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-560">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="07610-561">Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) était utilisé pour contourner cette restriction.</span><span class="sxs-lookup"><span data-stu-id="07610-561">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="07610-562">JSONP n’utilise pas XHR, elle utilise la `<script>` balise pour recevoir la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-562">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="07610-563">Les scripts sont autorisés à être chargés sur plusieurs origines.</span><span class="sxs-lookup"><span data-stu-id="07610-563">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="07610-564">La [spécification cors](https://www.w3.org/TR/cors/) a introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-564">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="07610-565">Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-565">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="07610-566">Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.</span><span class="sxs-lookup"><span data-stu-id="07610-566">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="07610-567">Voici un exemple de demande Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="07610-567">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="07610-568">L' `Origin` en-tête fournit le domaine du site qui effectue la demande.</span><span class="sxs-lookup"><span data-stu-id="07610-568">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="07610-569">L' `Origin` en-tête est obligatoire et doit être différent de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="07610-569">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="07610-570">Si le serveur autorise la demande, il définit l' `Access-Control-Allow-Origin` en-tête dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-570">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="07610-571">La valeur de cet en-tête correspond à l' `Origin` en-tête de la demande ou à la valeur du caractère générique `"*"` , ce qui signifie que toute origine est autorisée :</span><span class="sxs-lookup"><span data-stu-id="07610-571">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="07610-572">Si la réponse n’inclut pas l' `Access-Control-Allow-Origin` en-tête, la demande Cross-Origin échoue.</span><span class="sxs-lookup"><span data-stu-id="07610-572">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="07610-573">Plus précisément, le navigateur n’autorise pas la demande.</span><span class="sxs-lookup"><span data-stu-id="07610-573">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="07610-574">Même si le serveur retourne une réponse correcte, le navigateur ne rend pas la réponse disponible pour l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="07610-574">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="07610-575">Tester CORS</span><span class="sxs-lookup"><span data-stu-id="07610-575">Test CORS</span></span>

<span data-ttu-id="07610-576">Pour tester CORS :</span><span class="sxs-lookup"><span data-stu-id="07610-576">To test CORS:</span></span>

1. <span data-ttu-id="07610-577">[Créez un projet d’API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="07610-577">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="07610-578">Vous pouvez également [Télécharger l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="07610-578">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="07610-579">Activez CORS à l’aide de l’une des approches décrites dans ce document.</span><span class="sxs-lookup"><span data-stu-id="07610-579">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="07610-580">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="07610-580">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="07610-581">`WithOrigins("https://localhost:<port>");` doit uniquement être utilisé pour tester un exemple d’application semblable à l' [exemple de code de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="07610-581">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="07610-582">Créez un projet d’application Web ( Razor pages ou Mvc).</span><span class="sxs-lookup"><span data-stu-id="07610-582">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="07610-583">L’exemple utilise des Razor pages.</span><span class="sxs-lookup"><span data-stu-id="07610-583">The sample uses Razor Pages.</span></span> <span data-ttu-id="07610-584">Vous pouvez créer l’application Web dans la même solution que le projet d’API.</span><span class="sxs-lookup"><span data-stu-id="07610-584">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="07610-585">Ajoutez le code en surbrillance suivant au fichier *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="07610-585">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="07610-586">Dans le code précédent, remplacez `url: 'https://<web app>.azurewebsites.net/api/values/1',` par l’URL de l’application déployée.</span><span class="sxs-lookup"><span data-stu-id="07610-586">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="07610-587">Déployez le projet API.</span><span class="sxs-lookup"><span data-stu-id="07610-587">Deploy the API project.</span></span> <span data-ttu-id="07610-588">Par exemple, [déployez sur Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="07610-588">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="07610-589">Exécutez les Razor pages ou l’application MVC à partir du bureau, puis cliquez sur le bouton **test** .</span><span class="sxs-lookup"><span data-stu-id="07610-589">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="07610-590">Utilisez les outils F12 pour passer en revue les messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="07610-590">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="07610-591">Supprimez l’origine localhost de `WithOrigins` et déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="07610-591">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="07610-592">Vous pouvez également exécuter l’application cliente avec un autre port.</span><span class="sxs-lookup"><span data-stu-id="07610-592">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="07610-593">Par exemple, exécutez à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="07610-593">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="07610-594">Testez avec l’application cliente.</span><span class="sxs-lookup"><span data-stu-id="07610-594">Test with the client app.</span></span> <span data-ttu-id="07610-595">Les échecs CORS retournent une erreur, mais le message d’erreur n’est pas disponible pour JavaScript.</span><span class="sxs-lookup"><span data-stu-id="07610-595">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="07610-596">Utilisez l’onglet Console des outils F12 pour afficher l’erreur.</span><span class="sxs-lookup"><span data-stu-id="07610-596">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="07610-597">En fonction du navigateur, vous recevez une erreur (dans la console outils F12) semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="07610-597">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="07610-598">Utilisation de Microsoft Edge :</span><span class="sxs-lookup"><span data-stu-id="07610-598">Using Microsoft Edge:</span></span>

     <span data-ttu-id="07610-599">**SEC7120 : [CORS] l’origine `https://localhost:44375` n’a pas été trouvée `https://localhost:44375` dans l’en-tête de réponse Access-Control-allow-Origin pour la ressource Cross-Origin à l’adresse `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="07610-599">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="07610-600">Utilisation de chrome :</span><span class="sxs-lookup"><span data-stu-id="07610-600">Using Chrome:</span></span>

     <span data-ttu-id="07610-601">**L’accès à XMLHttpRequest à `https://webapi.azurewebsites.net/api/values/1` partir de l’origine `https://localhost:44375` a été bloqué par la stratégie cors : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée.**</span><span class="sxs-lookup"><span data-stu-id="07610-601">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="07610-602">Les points de terminaison compatibles CORS peuvent être testés à l’aide d’un outil tel que [Fiddler](https://www.telerik.com/fiddler) ou [postal](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="07610-602">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="07610-603">Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l' `Origin` en-tête doit différer de l’hôte recevant la demande.</span><span class="sxs-lookup"><span data-stu-id="07610-603">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="07610-604">Si la requête n’est pas *une origine croisée* en fonction de la valeur de l' `Origin` en-tête :</span><span class="sxs-lookup"><span data-stu-id="07610-604">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="07610-605">Il n’est pas nécessaire d’utiliser un intergiciel (middleware) CORS pour traiter la requête.</span><span class="sxs-lookup"><span data-stu-id="07610-605">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="07610-606">Les en-têtes CORS ne sont pas retournés dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="07610-606">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="07610-607">CORS dans IIS</span><span class="sxs-lookup"><span data-stu-id="07610-607">CORS in IIS</span></span>

<span data-ttu-id="07610-608">Lors du déploiement sur IIS, CORS doit s’exécuter avant l’authentification Windows si le serveur n’est pas configuré pour autoriser l’accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="07610-608">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="07610-609">Pour prendre en charge ce scénario, le [module cors IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.</span><span class="sxs-lookup"><span data-stu-id="07610-609">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07610-610">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="07610-610">Additional resources</span></span>

* [<span data-ttu-id="07610-611">Partage des ressources Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="07610-611">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="07610-612">Prise en main du module IIS CORS</span><span class="sxs-lookup"><span data-stu-id="07610-612">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
