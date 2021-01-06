---
title: Gérer les erreurs dans ASP.NET Core
author: rick-anderson
description: Découvrez comment gérer les erreurs dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: ad9920ccd830b93d083f3c5ede03702164842b6e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753112"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="363a0-103">Gérer les erreurs dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="363a0-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="363a0-104">Par [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="363a0-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="363a0-105">Cet article décrit les approches courantes de gestion des erreurs dans ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="363a0-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="363a0-106">Consultez <xref:web-api/handle-errors> pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="363a0-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="363a0-107">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="363a0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="363a0-108">([Comment télécharger](xref:index#how-to-download-a-sample).) L’onglet Réseau des outils de développement du navigateur F12 est utile lors du test de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="363a0-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="363a0-109">Page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="363a0-109">Developer Exception Page</span></span>

<span data-ttu-id="363a0-110">La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes.</span><span class="sxs-lookup"><span data-stu-id="363a0-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="363a0-111">Les modèles ASP.NET Core génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="363a0-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="363a0-112">Le code en surbrillance précédent active la page d’exception du développeur lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="363a0-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="363a0-113">Les modèles <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> sont placés tôt dans le pipeline de l’intergiciel (middleware) afin de pouvoir intercepter les exceptions levées dans l’intergiciel (middleware) qui suit.</span><span class="sxs-lookup"><span data-stu-id="363a0-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="363a0-114">Le code précédent permet à la page d’exception de développeur \***only** _ lorsque l’application s’exécute dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="363a0-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="363a0-115">Les informations détaillées sur les exceptions ne doivent pas être affichées publiquement lorsque l’application s’exécute dans l’environnement de production.</span><span class="sxs-lookup"><span data-stu-id="363a0-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="363a0-116">Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="363a0-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="363a0-117">La page exception du développeur contient les informations suivantes sur l’exception et la requête :</span><span class="sxs-lookup"><span data-stu-id="363a0-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="363a0-118">_ Trace de la pile</span><span class="sxs-lookup"><span data-stu-id="363a0-118">_ Stack trace</span></span>
* <span data-ttu-id="363a0-119">Paramètres de chaîne de requête, le cas échéant</span><span class="sxs-lookup"><span data-stu-id="363a0-119">Query string parameters if any</span></span>
* <span data-ttu-id="363a0-120">Cookiele cas échéant</span><span class="sxs-lookup"><span data-stu-id="363a0-120">Cookies if any</span></span>
* <span data-ttu-id="363a0-121">En-têtes</span><span class="sxs-lookup"><span data-stu-id="363a0-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="363a0-122">Page Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="363a0-122">Exception handler page</span></span>

<span data-ttu-id="363a0-123">Pour configurer une page de gestion des erreurs personnalisée pour l' [environnement de production](xref:fundamentals/environments), appelez <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="363a0-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="363a0-124">Cet intergiciel (middleware) de gestion des exceptions :</span><span class="sxs-lookup"><span data-stu-id="363a0-124">This exception handling middleware:</span></span>

* <span data-ttu-id="363a0-125">Intercepte et consigne les exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="363a0-126">Réexécute la requête dans un autre pipeline en utilisant le chemin d’accès indiqué.</span><span class="sxs-lookup"><span data-stu-id="363a0-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="363a0-127">La demande n’est pas réexécutée si la réponse a démarré.</span><span class="sxs-lookup"><span data-stu-id="363a0-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="363a0-128">Le code généré par le modèle ré-exécute la demande à l’aide du `/Error` chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="363a0-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="363a0-129">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ajoute l’intergiciel (middleware) de gestion des exceptions dans les environnements qui ne sont pas des environnements de développement :</span><span class="sxs-lookup"><span data-stu-id="363a0-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="363a0-130">Le Razor modèle d’application pages fournit une page d’erreur (*. cshtml*) et une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="363a0-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="363a0-131">Pour une application MVC, le modèle de projet comprend une `Error` méthode d’action et un affichage des erreurs pour le contrôleur d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="363a0-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="363a0-132">Ne Marquez pas la méthode d’action du gestionnaire d’erreurs avec des attributs de méthode HTTP, tels que `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="363a0-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="363a0-133">Les verbes explicites empêchent certaines demandes d’atteindre la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="363a0-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="363a0-134">Autorisez l’accès anonyme à la méthode si les utilisateurs non authentifiés doivent voir l’affichage des erreurs.</span><span class="sxs-lookup"><span data-stu-id="363a0-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="363a0-135">Accéder à l'exception</span><span class="sxs-lookup"><span data-stu-id="363a0-135">Access the exception</span></span>

<span data-ttu-id="363a0-136">Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin d’accès de la requête d’origine dans un gestionnaire d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="363a0-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="363a0-137">Le code suivant ajoute `ExceptionMessage` aux *pages/Error. cshtml. cs* par défaut générés par les modèles ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="363a0-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="363a0-138">Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients.</span><span class="sxs-lookup"><span data-stu-id="363a0-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="363a0-139">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="363a0-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="363a0-140">Pour tester l’exception dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="363a0-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="363a0-141">Définissez l’environnement sur production.</span><span class="sxs-lookup"><span data-stu-id="363a0-141">Set the environment to production.</span></span>
* <span data-ttu-id="363a0-142">Supprimez les commentaires de `webBuilder.UseStartup<Startup>();` dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="363a0-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="363a0-143">Sélectionnez **déclencher une exception** sur la page d’origine.</span><span class="sxs-lookup"><span data-stu-id="363a0-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="363a0-144">Expression lambda Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="363a0-144">Exception handler lambda</span></span>

<span data-ttu-id="363a0-145">En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>,</span><span class="sxs-lookup"><span data-stu-id="363a0-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="363a0-146">ce qui permet d’accéder à l’erreur avant de retourner la réponse.</span><span class="sxs-lookup"><span data-stu-id="363a0-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="363a0-147">Le code suivant utilise une expression lambda pour la gestion des exceptions :</span><span class="sxs-lookup"><span data-stu-id="363a0-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="363a0-148">Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients.</span><span class="sxs-lookup"><span data-stu-id="363a0-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="363a0-149">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="363a0-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="363a0-150">Pour tester le lambda de gestion des exceptions dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="363a0-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="363a0-151">Définissez l’environnement sur production.</span><span class="sxs-lookup"><span data-stu-id="363a0-151">Set the environment to production.</span></span>
* <span data-ttu-id="363a0-152">Supprimez les commentaires de `webBuilder.UseStartup<StartupLambda>();` dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="363a0-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="363a0-153">Sélectionnez **déclencher une exception** sur la page d’origine.</span><span class="sxs-lookup"><span data-stu-id="363a0-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="363a0-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="363a0-154">UseStatusCodePages</span></span>

<span data-ttu-id="363a0-155">Par défaut, une ASP.NET Core application ne fournit pas de page de codes d’État pour les codes d’état d’erreur HTTP, par exemple *404-introuvable*.</span><span class="sxs-lookup"><span data-stu-id="363a0-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="363a0-156">Lorsque l’application rencontre un code d’état d’erreur HTTP 400-599 qui n’a pas de corps, elle retourne le code d’État et un corps de réponse vide.</span><span class="sxs-lookup"><span data-stu-id="363a0-156">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="363a0-157">Pour fournir des pages de codes d’État, utilisez l’intergiciel (middleware) pages de codes d’État.</span><span class="sxs-lookup"><span data-stu-id="363a0-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="363a0-158">Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> dans la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="363a0-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="363a0-159">Appelez `UseStatusCodePages` avant l’intergiciel (middleware) de gestion des demandes.</span><span class="sxs-lookup"><span data-stu-id="363a0-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="363a0-160">Par exemple, appelez `UseStatusCodePages` avant l’intergiciel de fichiers statiques et l’intergiciel (middleware) des points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="363a0-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="363a0-161">Lorsque `UseStatusCodePages` n’est pas utilisé, la navigation vers une URL sans point de terminaison retourne un message d’erreur dépendant du navigateur qui indique que le point de terminaison est introuvable.</span><span class="sxs-lookup"><span data-stu-id="363a0-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="363a0-162">Par exemple, la navigation vers `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="363a0-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="363a0-163">Lorsque `UseStatusCodePages` est appelé, le navigateur retourne :</span><span class="sxs-lookup"><span data-stu-id="363a0-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="363a0-164">`UseStatusCodePages` n’est généralement pas utilisé en production, car il renvoie un message qui n’est pas utile aux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="363a0-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="363a0-165">Pour effectuer `UseStatusCodePages` un test dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="363a0-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="363a0-166">Définissez l’environnement sur production.</span><span class="sxs-lookup"><span data-stu-id="363a0-166">Set the environment to production.</span></span>
* <span data-ttu-id="363a0-167">Supprimez les commentaires de `webBuilder.UseStartup<StartupUseStatusCodePages>();` dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="363a0-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="363a0-168">Sélectionnez les liens sur la page d’hébergement sur la page d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="363a0-168">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="363a0-169">L’intergiciel (middleware) des pages de codes d’État n’intercepte **pas** les exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-169">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="363a0-170">Pour fournir une page de gestion des erreurs personnalisée, utilisez la [page Gestionnaire d’exceptions](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="363a0-170">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="363a0-171">UseStatusCodePages avec chaîne de format</span><span class="sxs-lookup"><span data-stu-id="363a0-171">UseStatusCodePages with format string</span></span>

<span data-ttu-id="363a0-172">Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend un type de contenu et une chaîne de format :</span><span class="sxs-lookup"><span data-stu-id="363a0-172">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="363a0-173">Dans le code précédent, `{0}` est un espace réservé pour le code d’erreur.</span><span class="sxs-lookup"><span data-stu-id="363a0-173">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="363a0-174">`UseStatusCodePages` avec une chaîne de format n’est généralement pas utilisée en production, car elle retourne un message qui n’est pas utile aux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="363a0-174">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="363a0-175">Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupFormat>();` dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="363a0-175">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="363a0-176">UseStatusCodePages avec expression lambda</span><span class="sxs-lookup"><span data-stu-id="363a0-176">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="363a0-177">Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend une expression lambda :</span><span class="sxs-lookup"><span data-stu-id="363a0-177">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="363a0-178">`UseStatusCodePages` avec une expression lambda n’est généralement pas utilisée en production, car elle retourne un message qui n’est pas utile aux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="363a0-178">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="363a0-179">Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupStatusLambda>();` dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="363a0-179">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="363a0-180">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="363a0-180">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="363a0-181">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> :</span><span class="sxs-lookup"><span data-stu-id="363a0-181">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="363a0-182">Envoie un code d’état [302 - Trouvé](https://developer.mozilla.org/docs/Web/HTTP/Status/302) au client.</span><span class="sxs-lookup"><span data-stu-id="363a0-182">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="363a0-183">Redirige le client vers le point de terminaison de gestion des erreurs fourni dans le modèle d’URL.</span><span class="sxs-lookup"><span data-stu-id="363a0-183">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="363a0-184">Le point de terminaison de gestion des erreurs affiche généralement les informations d’erreur et retourne HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="363a0-184">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="363a0-185">Le modèle d’URL peut inclure un `{0}` espace réservé pour le code d’État, comme indiqué dans le code précédent.</span><span class="sxs-lookup"><span data-stu-id="363a0-185">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="363a0-186">Si le modèle d’URL commence par `~` (tilde), le `~` est remplacé par le de l’application `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="363a0-186">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="363a0-187">Lorsque vous spécifiez un point de terminaison dans l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="363a0-187">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="363a0-188">Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="363a0-188">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="363a0-189">Cette méthode est généralement utilisée lorsque l’application :</span><span class="sxs-lookup"><span data-stu-id="363a0-189">This method is commonly used when the app:</span></span>

* <span data-ttu-id="363a0-190">Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur.</span><span class="sxs-lookup"><span data-stu-id="363a0-190">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="363a0-191">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.</span><span class="sxs-lookup"><span data-stu-id="363a0-191">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="363a0-192">Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.</span><span class="sxs-lookup"><span data-stu-id="363a0-192">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="363a0-193">Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupSCredirect>();` dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="363a0-193">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="363a0-194">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="363a0-194">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="363a0-195">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> :</span><span class="sxs-lookup"><span data-stu-id="363a0-195">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="363a0-196">Retourne le code d’état d’origine au client.</span><span class="sxs-lookup"><span data-stu-id="363a0-196">Returns the original status code to the client.</span></span>
* <span data-ttu-id="363a0-197">Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.</span><span class="sxs-lookup"><span data-stu-id="363a0-197">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="363a0-198">Si un point de terminaison est spécifié dans l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="363a0-198">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="363a0-199">Vérifiez que `UseStatusCodePagesWithReExecute` est placé avant `UseRouting` que la demande puisse être redirigée vers la page d’État.</span><span class="sxs-lookup"><span data-stu-id="363a0-199">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="363a0-200">Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="363a0-200">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="363a0-201">Cette méthode est généralement utilisée lorsque l’application doit :</span><span class="sxs-lookup"><span data-stu-id="363a0-201">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="363a0-202">Traiter la demande sans la rediriger vers un autre point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="363a0-202">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="363a0-203">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.</span><span class="sxs-lookup"><span data-stu-id="363a0-203">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="363a0-204">Conserver et retourner le code d’état d’origine avec la réponse.</span><span class="sxs-lookup"><span data-stu-id="363a0-204">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="363a0-205">Les modèles d’URL et de chaîne de requête peuvent inclure un espace réservé `{0}` pour le code d’État.</span><span class="sxs-lookup"><span data-stu-id="363a0-205">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="363a0-206">Le modèle d’URL doit commencer par `/` .</span><span class="sxs-lookup"><span data-stu-id="363a0-206">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="363a0-207">Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="363a0-207">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="363a0-208">Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="363a0-208">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="363a0-209">Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupSCreX>();` dans *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="363a0-209">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="363a0-210">Désactiver les pages de codes d’état</span><span class="sxs-lookup"><span data-stu-id="363a0-210">Disable status code pages</span></span>

<span data-ttu-id="363a0-211">Pour désactiver les pages de codes d’état d’un contrôleur MVC ou d’une méthode d’action, utilisez l’attribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="363a0-211">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="363a0-212">Pour désactiver les pages de codes d’État pour des demandes spécifiques dans une Razor méthode de gestionnaire de pages ou dans un contrôleur MVC, utilisez <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="363a0-212">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="363a0-213">Code de gestion des exceptions</span><span class="sxs-lookup"><span data-stu-id="363a0-213">Exception-handling code</span></span>

<span data-ttu-id="363a0-214">Le code dans les pages de gestion des exceptions peut également lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-214">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="363a0-215">Les pages d’erreurs de production doivent être testées minutieusement et prendre soin d’éviter de lever des exceptions propres.</span><span class="sxs-lookup"><span data-stu-id="363a0-215">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="363a0-216">En-têtes de réponse</span><span class="sxs-lookup"><span data-stu-id="363a0-216">Response headers</span></span>

<span data-ttu-id="363a0-217">Une fois les en-têtes d’une réponse envoyés :</span><span class="sxs-lookup"><span data-stu-id="363a0-217">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="363a0-218">L’application ne peut pas modifier le code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="363a0-218">The app can't change the response's status code.</span></span>
* <span data-ttu-id="363a0-219">Il est impossible d’exécuter les pages d’exception ou les gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="363a0-219">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="363a0-220">La réponse doit être accomplie ou la connexion abandonnée.</span><span class="sxs-lookup"><span data-stu-id="363a0-220">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="363a0-221">Gestion des exceptions de serveur</span><span class="sxs-lookup"><span data-stu-id="363a0-221">Server exception handling</span></span>

<span data-ttu-id="363a0-222">En plus de la logique de gestion des exceptions dans une application, l' [implémentation du serveur http](xref:fundamentals/servers/index) peut gérer certaines exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-222">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="363a0-223">Si le serveur intercepte une exception avant l’envoi des en-têtes de réponse, le serveur envoie une `500 - Internal Server Error` réponse sans corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="363a0-223">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="363a0-224">Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion.</span><span class="sxs-lookup"><span data-stu-id="363a0-224">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="363a0-225">Les demandes qui ne sont pas gérées par l’application sont gérées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="363a0-225">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="363a0-226">Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur.</span><span class="sxs-lookup"><span data-stu-id="363a0-226">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="363a0-227">Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.</span><span class="sxs-lookup"><span data-stu-id="363a0-227">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="363a0-228">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="363a0-228">Startup exception handling</span></span>

<span data-ttu-id="363a0-229">Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="363a0-229">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="363a0-230">L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="363a0-230">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="363a0-231">La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port.</span><span class="sxs-lookup"><span data-stu-id="363a0-231">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="363a0-232">Si la liaison échoue :</span><span class="sxs-lookup"><span data-stu-id="363a0-232">If binding fails:</span></span>

* <span data-ttu-id="363a0-233">La couche d’hébergement journalise une exception critique.</span><span class="sxs-lookup"><span data-stu-id="363a0-233">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="363a0-234">Le processus dotnet tombe en panne.</span><span class="sxs-lookup"><span data-stu-id="363a0-234">The dotnet process crashes.</span></span>
* <span data-ttu-id="363a0-235">Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="363a0-235">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="363a0-236">En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="363a0-236">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="363a0-237">Pour plus d’informations, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="363a0-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="363a0-238">Page d’erreur de base de données</span><span class="sxs-lookup"><span data-stu-id="363a0-238">Database error page</span></span>

<span data-ttu-id="363a0-239">Le filtre d’exception de la page développeur de bases de données `AddDatabaseDeveloperPageExceptionFilter` capture les exceptions liées aux bases de données qui peuvent être résolues à l’aide de Entity Framework Core migrations.</span><span class="sxs-lookup"><span data-stu-id="363a0-239">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="363a0-240">Lorsque ces exceptions se produisent, une réponse HTML est générée avec des détails sur les actions possibles pour résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="363a0-240">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="363a0-241">Cette page est activée uniquement dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="363a0-241">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="363a0-242">Le code suivant a été généré par les Razor modèles de Pages ASP.net core lorsque des comptes d’utilisateur individuels ont été spécifiés :</span><span class="sxs-lookup"><span data-stu-id="363a0-242">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="363a0-243">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="363a0-243">Exception filters</span></span>

<span data-ttu-id="363a0-244">Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action.</span><span class="sxs-lookup"><span data-stu-id="363a0-244">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="363a0-245">Dans Razor les applications pages, elles peuvent être configurées globalement ou par modèle de page.</span><span class="sxs-lookup"><span data-stu-id="363a0-245">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="363a0-246">Ces filtres gèrent les exceptions non gérées qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="363a0-246">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="363a0-247">Pour plus d’informations, consultez <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="363a0-247">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="363a0-248">Les filtres d’exception sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais elles ne sont pas aussi flexibles que l’intergiciel (middleware) de [gestion des exceptions](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)intégré `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="363a0-248">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="363a0-249">Nous vous recommandons `UseExceptionHandler` d’utiliser, sauf si vous devez effectuer la gestion des erreurs différemment en fonction de l’action MVC choisie.</span><span class="sxs-lookup"><span data-stu-id="363a0-249">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="363a0-250">Erreurs d’état de modèle</span><span class="sxs-lookup"><span data-stu-id="363a0-250">Model state errors</span></span>

<span data-ttu-id="363a0-251">Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="363a0-251">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="363a0-252">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="363a0-252">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="363a0-253">Par  [Tom Dykstra](https://github.com/tdykstra/)et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="363a0-253">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="363a0-254">Cet article décrit les approches courantes de gestion des erreurs dans ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="363a0-254">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="363a0-255">Consultez <xref:web-api/handle-errors> pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="363a0-255">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="363a0-256">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="363a0-256">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="363a0-257">([Guide pratique de téléchargement](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="363a0-257">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="363a0-258">Page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="363a0-258">Developer Exception Page</span></span>

<span data-ttu-id="363a0-259">La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes.</span><span class="sxs-lookup"><span data-stu-id="363a0-259">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="363a0-260">Les modèles ASP.NET Core génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="363a0-260">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="363a0-261">Le code précédent permet à la page d’exception de développeur lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="363a0-261">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="363a0-262">Les modèles <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> sont placés avant tout intergiciel, de sorte que les exceptions sont interceptées dans l’intergiciel (middleware) qui suit.</span><span class="sxs-lookup"><span data-stu-id="363a0-262">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="363a0-263">Le code précédent active la page d’exception du développeur **uniquement lorsque l’application s’exécute dans l’environnement de développement**.</span><span class="sxs-lookup"><span data-stu-id="363a0-263">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="363a0-264">Les informations détaillées sur les exceptions ne doivent pas être affichées publiquement lorsque l’application s’exécute en production.</span><span class="sxs-lookup"><span data-stu-id="363a0-264">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="363a0-265">Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="363a0-265">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="363a0-266">La page exception du développeur contient les informations suivantes sur l’exception et la requête :</span><span class="sxs-lookup"><span data-stu-id="363a0-266">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="363a0-267">Arborescence des appels de procédure</span><span class="sxs-lookup"><span data-stu-id="363a0-267">Stack trace</span></span>
* <span data-ttu-id="363a0-268">Paramètres de chaîne de requête, le cas échéant</span><span class="sxs-lookup"><span data-stu-id="363a0-268">Query string parameters if any</span></span>
* <span data-ttu-id="363a0-269">Cookiele cas échéant</span><span class="sxs-lookup"><span data-stu-id="363a0-269">Cookies if any</span></span>
* <span data-ttu-id="363a0-270">En-têtes</span><span class="sxs-lookup"><span data-stu-id="363a0-270">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="363a0-271">Page Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="363a0-271">Exception handler page</span></span>

<span data-ttu-id="363a0-272">Pour configurer une page de gestion des erreurs personnalisée en fonction de l’environnement de production, utilisez le middleware de gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-272">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="363a0-273">Le middleware :</span><span class="sxs-lookup"><span data-stu-id="363a0-273">The middleware:</span></span>

* <span data-ttu-id="363a0-274">Intercepte et consigne les exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-274">Catches and logs exceptions.</span></span>
* <span data-ttu-id="363a0-275">Réexécute la requête dans un autre pipeline pour la page ou le contrôleur indiqué(e).</span><span class="sxs-lookup"><span data-stu-id="363a0-275">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="363a0-276">La demande n’est pas réexécutée si la réponse a démarré.</span><span class="sxs-lookup"><span data-stu-id="363a0-276">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="363a0-277">Le code généré par le modèle ré-exécute la demande à `/Error` .</span><span class="sxs-lookup"><span data-stu-id="363a0-277">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="363a0-278">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ajoute le middleware de gestion des exceptions dans des environnements autres que les environnements de développement :</span><span class="sxs-lookup"><span data-stu-id="363a0-278">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="363a0-279">Le Razor modèle d’application pages fournit une page d’erreur (*. cshtml*) et une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="363a0-279">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="363a0-280">Pour une application MVC, le modèle de projet comprend une méthode d’action d’erreur et un affichage des erreurs dans le contrôleur d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="363a0-280">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="363a0-281">Ne Marquez pas la méthode d’action du gestionnaire d’erreurs avec des attributs de méthode HTTP, tels que `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="363a0-281">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="363a0-282">Les verbes explicites empêchent certaines demandes d’atteindre la méthode.</span><span class="sxs-lookup"><span data-stu-id="363a0-282">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="363a0-283">Autorisez l’accès anonyme à la méthode si les utilisateurs non authentifiés doivent voir l’affichage des erreurs.</span><span class="sxs-lookup"><span data-stu-id="363a0-283">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="363a0-284">Accéder à l'exception</span><span class="sxs-lookup"><span data-stu-id="363a0-284">Access the exception</span></span>

<span data-ttu-id="363a0-285">Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin de la demande d’origine dans une page ou un contrôleur de gestionnaire d’erreurs :</span><span class="sxs-lookup"><span data-stu-id="363a0-285">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="363a0-286">Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients.</span><span class="sxs-lookup"><span data-stu-id="363a0-286">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="363a0-287">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="363a0-287">Serving errors is a security risk.</span></span>

<span data-ttu-id="363a0-288">Pour déclencher la page de gestion des exceptions précédente, définissez l’environnement sur productions et forcez une exception.</span><span class="sxs-lookup"><span data-stu-id="363a0-288">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="363a0-289">Expression lambda Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="363a0-289">Exception handler lambda</span></span>

<span data-ttu-id="363a0-290">En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>,</span><span class="sxs-lookup"><span data-stu-id="363a0-290">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="363a0-291">ce qui permet d’accéder à l’erreur avant de retourner la réponse.</span><span class="sxs-lookup"><span data-stu-id="363a0-291">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="363a0-292">Voici un exemple d’utilisation d’une expression lambda pour la gestion des exceptions :</span><span class="sxs-lookup"><span data-stu-id="363a0-292">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="363a0-293">Dans le code précédent, `await context.Response.WriteAsync(new string(' ', 512));` est ajouté afin que le navigateur Internet Explorer affiche le message d’erreur plutôt qu’un message d’erreur IE.</span><span class="sxs-lookup"><span data-stu-id="363a0-293">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="363a0-294">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="363a0-294">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="363a0-295">Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients.</span><span class="sxs-lookup"><span data-stu-id="363a0-295">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="363a0-296">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="363a0-296">Serving errors is a security risk.</span></span>

<span data-ttu-id="363a0-297">Pour afficher le résultat de l’expression lambda de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerLambda` et sélectionnez **Déclencher une exception** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="363a0-297">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="363a0-298">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="363a0-298">UseStatusCodePages</span></span>

<span data-ttu-id="363a0-299">Par défaut, une application ASP.NET Core ne fournit pas une page de codes d’état pour les codes d’état HTTP, comme *404 - Introuvable*.</span><span class="sxs-lookup"><span data-stu-id="363a0-299">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="363a0-300">L’application retourne un code d’état et un corps de réponse vide.</span><span class="sxs-lookup"><span data-stu-id="363a0-300">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="363a0-301">Pour fournir des pages de codes d’état, utilisez le middleware Pages de codes d’état.</span><span class="sxs-lookup"><span data-stu-id="363a0-301">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="363a0-302">L’intergiciel est mis à disposition par le package [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .</span><span class="sxs-lookup"><span data-stu-id="363a0-302">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="363a0-303">Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> dans la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="363a0-303">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="363a0-304">Appelez `UseStatusCodePages` avant les middlewares de gestion des demandes (par exemple, le middleware de fichiers statiques et le middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="363a0-304">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="363a0-305">Lorsque `UseStatusCodePages` n’est pas utilisé, la navigation vers une URL sans point de terminaison retourne un message d’erreur dépendant du navigateur qui indique que le point de terminaison est introuvable.</span><span class="sxs-lookup"><span data-stu-id="363a0-305">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="363a0-306">Par exemple, la navigation vers `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="363a0-306">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="363a0-307">Lorsque `UseStatusCodePages` est appelé, le navigateur retourne :</span><span class="sxs-lookup"><span data-stu-id="363a0-307">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="363a0-308">UseStatusCodePages avec chaîne de format</span><span class="sxs-lookup"><span data-stu-id="363a0-308">UseStatusCodePages with format string</span></span>

<span data-ttu-id="363a0-309">Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend un type de contenu et une chaîne de format :</span><span class="sxs-lookup"><span data-stu-id="363a0-309">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="363a0-310">UseStatusCodePages avec expression lambda</span><span class="sxs-lookup"><span data-stu-id="363a0-310">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="363a0-311">Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend une expression lambda :</span><span class="sxs-lookup"><span data-stu-id="363a0-311">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="363a0-312">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="363a0-312">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="363a0-313">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> :</span><span class="sxs-lookup"><span data-stu-id="363a0-313">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="363a0-314">Envoie un code d’état *302 - Trouvé* au client.</span><span class="sxs-lookup"><span data-stu-id="363a0-314">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="363a0-315">Redirige le client à l’emplacement fourni dans le modèle d’URL.</span><span class="sxs-lookup"><span data-stu-id="363a0-315">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="363a0-316">Le modèle d’URL peut comporter un espace réservé `{0}` pour le code d’état, comme dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="363a0-316">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="363a0-317">Si le modèle d’URL commence par `~` (tilde), le `~` est remplacé par le de l’application `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="363a0-317">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="363a0-318">Si vous pointez vers un point de terminaison au sein de l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="363a0-318">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="363a0-319">Pour obtenir un Razor exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="363a0-319">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="363a0-320">Cette méthode est généralement utilisée lorsque l’application :</span><span class="sxs-lookup"><span data-stu-id="363a0-320">This method is commonly used when the app:</span></span>

* <span data-ttu-id="363a0-321">Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur.</span><span class="sxs-lookup"><span data-stu-id="363a0-321">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="363a0-322">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.</span><span class="sxs-lookup"><span data-stu-id="363a0-322">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="363a0-323">Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.</span><span class="sxs-lookup"><span data-stu-id="363a0-323">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="363a0-324">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="363a0-324">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="363a0-325">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> :</span><span class="sxs-lookup"><span data-stu-id="363a0-325">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="363a0-326">Retourne le code d’état d’origine au client.</span><span class="sxs-lookup"><span data-stu-id="363a0-326">Returns the original status code to the client.</span></span>
* <span data-ttu-id="363a0-327">Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.</span><span class="sxs-lookup"><span data-stu-id="363a0-327">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="363a0-328">Si vous pointez vers un point de terminaison au sein de l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="363a0-328">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="363a0-329">Vérifiez que `UseStatusCodePagesWithReExecute` est placé avant `UseRouting` que la demande puisse être redirigée vers la page d’État.</span><span class="sxs-lookup"><span data-stu-id="363a0-329">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="363a0-330">Pour obtenir un Razor exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="363a0-330">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="363a0-331">Cette méthode est généralement utilisée lorsque l’application doit :</span><span class="sxs-lookup"><span data-stu-id="363a0-331">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="363a0-332">Traiter la demande sans la rediriger vers un autre point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="363a0-332">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="363a0-333">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.</span><span class="sxs-lookup"><span data-stu-id="363a0-333">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="363a0-334">Conserver et retourner le code d’état d’origine avec la réponse.</span><span class="sxs-lookup"><span data-stu-id="363a0-334">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="363a0-335">Les modèles d’URL et de chaîne de requête peuvent comporter un espace réservé (`{0}`) pour le code d’état.</span><span class="sxs-lookup"><span data-stu-id="363a0-335">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="363a0-336">Le modèle d’URL doit commencer par une barre oblique (`/`).</span><span class="sxs-lookup"><span data-stu-id="363a0-336">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="363a0-337">Si vous utilisez un espace réservé dans le chemin, vérifiez que le point de terminaison (page ou contrôleur) peut traiter le segment de chemin.</span><span class="sxs-lookup"><span data-stu-id="363a0-337">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="363a0-338">Par exemple, une Razor page pour les erreurs doit accepter la valeur de segment de chemin d’accès facultative avec la `@page` directive :</span><span class="sxs-lookup"><span data-stu-id="363a0-338">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="363a0-339">Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="363a0-339">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="363a0-340">Désactiver les pages de codes d’état</span><span class="sxs-lookup"><span data-stu-id="363a0-340">Disable status code pages</span></span>

<span data-ttu-id="363a0-341">Pour désactiver les pages de codes d’État pour un contrôleur MVC ou une méthode d’action, utilisez l' [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="363a0-341">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="363a0-342">Pour désactiver les pages de codes d’État pour des demandes spécifiques dans une Razor méthode de gestionnaire de pages ou dans un contrôleur MVC, utilisez <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="363a0-342">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="363a0-343">Code de gestion des exceptions</span><span class="sxs-lookup"><span data-stu-id="363a0-343">Exception-handling code</span></span>

<span data-ttu-id="363a0-344">Le code dans les pages de gestion des exceptions peut lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-344">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="363a0-345">Il est souvent judicieux de mettre dans les pages d’erreur de production du contenu purement statique.</span><span class="sxs-lookup"><span data-stu-id="363a0-345">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="363a0-346">En-têtes de réponse</span><span class="sxs-lookup"><span data-stu-id="363a0-346">Response headers</span></span>

<span data-ttu-id="363a0-347">Une fois les en-têtes d’une réponse envoyés :</span><span class="sxs-lookup"><span data-stu-id="363a0-347">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="363a0-348">L’application ne peut pas modifier le code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="363a0-348">The app can't change the response's status code.</span></span>
* <span data-ttu-id="363a0-349">Il est impossible d’exécuter les pages d’exception ou les gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="363a0-349">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="363a0-350">La réponse doit être accomplie ou la connexion abandonnée.</span><span class="sxs-lookup"><span data-stu-id="363a0-350">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="363a0-351">Gestion des exceptions de serveur</span><span class="sxs-lookup"><span data-stu-id="363a0-351">Server exception handling</span></span>

<span data-ttu-id="363a0-352">En plus de la logique de gestion des exceptions de votre application, [l’implémentation de serveur HTTP](xref:fundamentals/servers/index) peut gérer certaines exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-352">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="363a0-353">Si le serveur intercepte une exception avant que les en-têtes de réponse ne soient envoyés, le serveur envoie une réponse *500 Erreur interne du serveur* sans corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="363a0-353">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="363a0-354">Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion.</span><span class="sxs-lookup"><span data-stu-id="363a0-354">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="363a0-355">Les demandes qui ne sont pas gérées par votre application sont gérées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="363a0-355">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="363a0-356">Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur.</span><span class="sxs-lookup"><span data-stu-id="363a0-356">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="363a0-357">Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.</span><span class="sxs-lookup"><span data-stu-id="363a0-357">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="363a0-358">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="363a0-358">Startup exception handling</span></span>

<span data-ttu-id="363a0-359">Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="363a0-359">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="363a0-360">L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="363a0-360">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="363a0-361">La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port.</span><span class="sxs-lookup"><span data-stu-id="363a0-361">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="363a0-362">Si la liaison échoue :</span><span class="sxs-lookup"><span data-stu-id="363a0-362">If binding fails:</span></span>

* <span data-ttu-id="363a0-363">La couche d’hébergement journalise une exception critique.</span><span class="sxs-lookup"><span data-stu-id="363a0-363">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="363a0-364">Le processus dotnet tombe en panne.</span><span class="sxs-lookup"><span data-stu-id="363a0-364">The dotnet process crashes.</span></span>
* <span data-ttu-id="363a0-365">Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="363a0-365">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="363a0-366">En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="363a0-366">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="363a0-367">Pour plus d’informations, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="363a0-367">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="363a0-368">Page d’erreur de base de données</span><span class="sxs-lookup"><span data-stu-id="363a0-368">Database error page</span></span>

<span data-ttu-id="363a0-369">L’intergiciel (middleware) de page d’erreur de base de données capture des exceptions liées à la base de données qui peuvent être résolues à l’aide de Entity Framework migrations.</span><span class="sxs-lookup"><span data-stu-id="363a0-369">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="363a0-370">Lorsque ces exceptions se produisent, une réponse HTML comportant le détail des actions possibles pour résoudre le problème est générée.</span><span class="sxs-lookup"><span data-stu-id="363a0-370">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="363a0-371">Cette page ne doit être activée que dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="363a0-371">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="363a0-372">Pour cela, ajoutez du code à `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="363a0-372">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="363a0-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requiert le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="363a0-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="363a0-374">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="363a0-374">Exception filters</span></span>

<span data-ttu-id="363a0-375">Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action.</span><span class="sxs-lookup"><span data-stu-id="363a0-375">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="363a0-376">Dans Razor les applications pages, elles peuvent être configurées globalement ou par modèle de page.</span><span class="sxs-lookup"><span data-stu-id="363a0-376">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="363a0-377">Ces filtres gèrent les exceptions non prises en charge qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="363a0-377">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="363a0-378">Pour plus d’informations, consultez <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="363a0-378">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="363a0-379">Les filtres d’exceptions sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais n’offrent pas la même souplesse que le middleware de gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="363a0-379">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="363a0-380">Nous vous recommandons d’utiliser le middleware.</span><span class="sxs-lookup"><span data-stu-id="363a0-380">We recommend using the middleware.</span></span> <span data-ttu-id="363a0-381">N’utilisez des filtres que si vous devez gérer les erreurs différemment en fonction de l’action MVC choisie.</span><span class="sxs-lookup"><span data-stu-id="363a0-381">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="363a0-382">Erreurs d’état de modèle</span><span class="sxs-lookup"><span data-stu-id="363a0-382">Model state errors</span></span>

<span data-ttu-id="363a0-383">Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="363a0-383">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="363a0-384">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="363a0-384">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
