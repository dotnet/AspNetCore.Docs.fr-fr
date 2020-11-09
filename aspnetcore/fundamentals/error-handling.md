---
title: Gérer les erreurs dans ASP.NET Core
author: rick-anderson
description: Découvrez comment gérer les erreurs dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060467"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="7d26d-103">Gérer les erreurs dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d26d-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7d26d-104">Par [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7d26d-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7d26d-105">Cet article décrit les approches courantes de gestion des erreurs dans ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="7d26d-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="7d26d-106">Consultez <xref:web-api/handle-errors> pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="7d26d-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="7d26d-107">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="7d26d-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="7d26d-108">([Comment télécharger](xref:index#how-to-download-a-sample).) L’onglet Réseau des outils de développement du navigateur F12 est utile lors du test de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="7d26d-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="7d26d-109">Page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="7d26d-109">Developer Exception Page</span></span>

<span data-ttu-id="7d26d-110">La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes.</span><span class="sxs-lookup"><span data-stu-id="7d26d-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="7d26d-111">Les modèles ASP.NET Core génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7d26d-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="7d26d-112">Le code en surbrillance précédent active la page d’exception du développeur lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="7d26d-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="7d26d-113">Les modèles <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> sont placés tôt dans le pipeline de l’intergiciel (middleware) afin de pouvoir intercepter les exceptions levées dans l’intergiciel (middleware) qui suit.</span><span class="sxs-lookup"><span data-stu-id="7d26d-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="7d26d-114">Le code précédent permet à la page d’exception de développeur \* **only** _ lorsque l’application s’exécute dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="7d26d-114">The preceding code enables the Developer Exception Page \* **only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="7d26d-115">Les informations détaillées sur les exceptions ne doivent pas être affichées publiquement lorsque l’application s’exécute dans l’environnement de production.</span><span class="sxs-lookup"><span data-stu-id="7d26d-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="7d26d-116">Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7d26d-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="7d26d-117">La page exception du développeur contient les informations suivantes sur l’exception et la requête :</span><span class="sxs-lookup"><span data-stu-id="7d26d-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="7d26d-118">_ Trace de la pile</span><span class="sxs-lookup"><span data-stu-id="7d26d-118">_ Stack trace</span></span>
* <span data-ttu-id="7d26d-119">Paramètres de chaîne de requête, le cas échéant</span><span class="sxs-lookup"><span data-stu-id="7d26d-119">Query string parameters if any</span></span>
* <span data-ttu-id="7d26d-120">Cookiele cas échéant</span><span class="sxs-lookup"><span data-stu-id="7d26d-120">Cookies if any</span></span>
* <span data-ttu-id="7d26d-121">En-têtes</span><span class="sxs-lookup"><span data-stu-id="7d26d-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="7d26d-122">Page Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="7d26d-122">Exception handler page</span></span>

<span data-ttu-id="7d26d-123">Pour configurer une page de gestion des erreurs personnalisée pour l' [environnement de production](xref:fundamentals/environments), appelez <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="7d26d-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="7d26d-124">Cet intergiciel (middleware) de gestion des exceptions :</span><span class="sxs-lookup"><span data-stu-id="7d26d-124">This exception handling middleware:</span></span>

* <span data-ttu-id="7d26d-125">Intercepte et consigne les exceptions.</span><span class="sxs-lookup"><span data-stu-id="7d26d-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="7d26d-126">Réexécute la requête dans un autre pipeline en utilisant le chemin d’accès indiqué.</span><span class="sxs-lookup"><span data-stu-id="7d26d-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="7d26d-127">La demande n’est pas réexécutée si la réponse a démarré.</span><span class="sxs-lookup"><span data-stu-id="7d26d-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="7d26d-128">Le code généré par le modèle ré-exécute la demande à l’aide du `/Error` chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="7d26d-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="7d26d-129">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ajoute l’intergiciel (middleware) de gestion des exceptions dans les environnements qui ne sont pas des environnements de développement :</span><span class="sxs-lookup"><span data-stu-id="7d26d-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="7d26d-130">Le Razor modèle d’application pages fournit une page d’erreur ( *. cshtml* ) et une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-130">The Razor Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="7d26d-131">Pour une application MVC, le modèle de projet comprend une `Error` méthode d’action et un affichage des erreurs pour le contrôleur d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="7d26d-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="7d26d-132">Ne Marquez pas la méthode d’action du gestionnaire d’erreurs avec des attributs de méthode HTTP, tels que `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="7d26d-133">Les verbes explicites empêchent certaines demandes d’atteindre la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="7d26d-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="7d26d-134">Autorisez l’accès anonyme à la méthode si les utilisateurs non authentifiés doivent voir l’affichage des erreurs.</span><span class="sxs-lookup"><span data-stu-id="7d26d-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="7d26d-135">Accéder à l'exception</span><span class="sxs-lookup"><span data-stu-id="7d26d-135">Access the exception</span></span>

<span data-ttu-id="7d26d-136">Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin d’accès de la requête d’origine dans un gestionnaire d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="7d26d-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="7d26d-137">Le code suivant ajoute `ExceptionMessage` aux *pages/Error. cshtml. cs* par défaut générés par les modèles ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="7d26d-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="7d26d-138">Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients.</span><span class="sxs-lookup"><span data-stu-id="7d26d-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="7d26d-139">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="7d26d-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="7d26d-140">Pour tester l’exception dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="7d26d-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="7d26d-141">Définissez l’environnement sur production.</span><span class="sxs-lookup"><span data-stu-id="7d26d-141">Set the environment to production.</span></span>
* <span data-ttu-id="7d26d-142">Supprimez les commentaires de `webBuilder.UseStartup<Startup>();` dans *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs* .</span></span>
* <span data-ttu-id="7d26d-143">Sélectionnez **déclencher une exception** sur la page d’origine.</span><span class="sxs-lookup"><span data-stu-id="7d26d-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="7d26d-144">Expression lambda Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="7d26d-144">Exception handler lambda</span></span>

<span data-ttu-id="7d26d-145">En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>,</span><span class="sxs-lookup"><span data-stu-id="7d26d-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="7d26d-146">ce qui permet d’accéder à l’erreur avant de retourner la réponse.</span><span class="sxs-lookup"><span data-stu-id="7d26d-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="7d26d-147">Le code suivant utilise une expression lambda pour la gestion des exceptions :</span><span class="sxs-lookup"><span data-stu-id="7d26d-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="7d26d-148">Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients.</span><span class="sxs-lookup"><span data-stu-id="7d26d-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="7d26d-149">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="7d26d-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="7d26d-150">Pour tester le lambda de gestion des exceptions dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="7d26d-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="7d26d-151">Définissez l’environnement sur production.</span><span class="sxs-lookup"><span data-stu-id="7d26d-151">Set the environment to production.</span></span>
* <span data-ttu-id="7d26d-152">Supprimez les commentaires de `webBuilder.UseStartup<StartupLambda>();` dans *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs* .</span></span>
* <span data-ttu-id="7d26d-153">Sélectionnez **déclencher une exception** sur la page d’origine.</span><span class="sxs-lookup"><span data-stu-id="7d26d-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="7d26d-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="7d26d-154">UseStatusCodePages</span></span>

<span data-ttu-id="7d26d-155">Par défaut, une ASP.NET Core application ne fournit pas de page de codes d’État pour les codes d’état d’erreur HTTP, par exemple *404-introuvable* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found* .</span></span> <span data-ttu-id="7d26d-156">Lorsque l’application rencontre une condition d’erreur HTTP 400-499 qui n’a pas de corps, elle retourne le code d’État et un corps de réponse vide.</span><span class="sxs-lookup"><span data-stu-id="7d26d-156">When the app encounters an HTTP 400-499 error condition that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="7d26d-157">Pour fournir des pages de codes d’État, utilisez l’intergiciel (middleware) pages de codes d’État.</span><span class="sxs-lookup"><span data-stu-id="7d26d-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="7d26d-158">Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> dans la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7d26d-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

<span data-ttu-id="7d26d-159">Appelez `UseStatusCodePages` avant l’intergiciel (middleware) de gestion des demandes.</span><span class="sxs-lookup"><span data-stu-id="7d26d-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="7d26d-160">Par exemple, appelez `UseStatusCodePages` avant l’intergiciel de fichiers statiques et l’intergiciel (middleware) des points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7d26d-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="7d26d-161">Lorsque `UseStatusCodePages` n’est pas utilisé, la navigation vers une URL sans point de terminaison retourne un message d’erreur dépendant du navigateur qui indique que le point de terminaison est introuvable.</span><span class="sxs-lookup"><span data-stu-id="7d26d-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="7d26d-162">Par exemple, la navigation vers `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="7d26d-163">Lorsque `UseStatusCodePages` est appelé, le navigateur retourne :</span><span class="sxs-lookup"><span data-stu-id="7d26d-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="7d26d-164">`UseStatusCodePages` n’est généralement pas utilisé en production, car il renvoie un message qui n’est pas utile aux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="7d26d-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="7d26d-165">Pour effectuer `UseStatusCodePages` un test dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="7d26d-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="7d26d-166">Définissez l’environnement sur production.</span><span class="sxs-lookup"><span data-stu-id="7d26d-166">Set the environment to production.</span></span>
* <span data-ttu-id="7d26d-167">Supprimez les commentaires de `webBuilder.UseStartup<StartupUseStatusCodePages>();` dans *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs* .</span></span>
* <span data-ttu-id="7d26d-168">Sélectionnez les liens sur la page d’hébergement sur la page d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="7d26d-168">Select the links on the home page on the home page.</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="7d26d-169">UseStatusCodePages avec chaîne de format</span><span class="sxs-lookup"><span data-stu-id="7d26d-169">UseStatusCodePages with format string</span></span>

<span data-ttu-id="7d26d-170">Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend un type de contenu et une chaîne de format :</span><span class="sxs-lookup"><span data-stu-id="7d26d-170">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="7d26d-171">Dans le code précédent, `{0}` est un espace réservé pour le code d’erreur.</span><span class="sxs-lookup"><span data-stu-id="7d26d-171">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="7d26d-172">`UseStatusCodePages` avec une chaîne de format n’est généralement pas utilisée en production, car elle retourne un message qui n’est pas utile aux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="7d26d-172">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="7d26d-173">Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupFormat>();` dans *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-173">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs* .</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="7d26d-174">UseStatusCodePages avec expression lambda</span><span class="sxs-lookup"><span data-stu-id="7d26d-174">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="7d26d-175">Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend une expression lambda :</span><span class="sxs-lookup"><span data-stu-id="7d26d-175">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="7d26d-176">`UseStatusCodePages` avec une expression lambda n’est généralement pas utilisée en production, car elle retourne un message qui n’est pas utile aux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="7d26d-176">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="7d26d-177">Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupStatusLambda>();` dans *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-177">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs* .</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="7d26d-178">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="7d26d-178">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="7d26d-179">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> :</span><span class="sxs-lookup"><span data-stu-id="7d26d-179">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="7d26d-180">Envoie un code d’état [302 - Trouvé](https://developer.mozilla.org/docs/Web/HTTP/Status/302) au client.</span><span class="sxs-lookup"><span data-stu-id="7d26d-180">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="7d26d-181">Redirige le client vers le point de terminaison de gestion des erreurs fourni dans le modèle d’URL.</span><span class="sxs-lookup"><span data-stu-id="7d26d-181">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="7d26d-182">Le point de terminaison de gestion des erreurs affiche généralement les informations d’erreur et retourne HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="7d26d-182">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="7d26d-183">Le modèle d’URL peut inclure un `{0}` espace réservé pour le code d’État, comme indiqué dans le code précédent.</span><span class="sxs-lookup"><span data-stu-id="7d26d-183">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="7d26d-184">Si le modèle d’URL commence par `~` (tilde), le `~` est remplacé par le de l’application `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-184">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="7d26d-185">Lorsque vous spécifiez un point de terminaison dans l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7d26d-185">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="7d26d-186">Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="7d26d-186">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="7d26d-187">Cette méthode est généralement utilisée lorsque l’application :</span><span class="sxs-lookup"><span data-stu-id="7d26d-187">This method is commonly used when the app:</span></span>

* <span data-ttu-id="7d26d-188">Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur.</span><span class="sxs-lookup"><span data-stu-id="7d26d-188">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="7d26d-189">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.</span><span class="sxs-lookup"><span data-stu-id="7d26d-189">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="7d26d-190">Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.</span><span class="sxs-lookup"><span data-stu-id="7d26d-190">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="7d26d-191">Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupSCredirect>();` dans *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-191">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs* .</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="7d26d-192">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="7d26d-192">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="7d26d-193">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> :</span><span class="sxs-lookup"><span data-stu-id="7d26d-193">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="7d26d-194">Retourne le code d’état d’origine au client.</span><span class="sxs-lookup"><span data-stu-id="7d26d-194">Returns the original status code to the client.</span></span>
* <span data-ttu-id="7d26d-195">Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.</span><span class="sxs-lookup"><span data-stu-id="7d26d-195">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="7d26d-196">Si un point de terminaison est spécifié dans l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7d26d-196">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="7d26d-197">Vérifiez que `UseStatusCodePagesWithReExecute` est placé avant `UseRouting` que la demande puisse être redirigée vers la page d’État.</span><span class="sxs-lookup"><span data-stu-id="7d26d-197">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="7d26d-198">Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="7d26d-198">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="7d26d-199">Cette méthode est généralement utilisée lorsque l’application doit :</span><span class="sxs-lookup"><span data-stu-id="7d26d-199">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="7d26d-200">Traiter la demande sans la rediriger vers un autre point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7d26d-200">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="7d26d-201">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.</span><span class="sxs-lookup"><span data-stu-id="7d26d-201">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="7d26d-202">Conserver et retourner le code d’état d’origine avec la réponse.</span><span class="sxs-lookup"><span data-stu-id="7d26d-202">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="7d26d-203">Les modèles d’URL et de chaîne de requête peuvent inclure un espace réservé `{0}` pour le code d’État.</span><span class="sxs-lookup"><span data-stu-id="7d26d-203">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="7d26d-204">Le modèle d’URL doit commencer par `/` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-204">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="7d26d-205">Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7d26d-205">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="7d26d-206">Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="7d26d-206">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="7d26d-207">Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupSCreX>();` dans *Program.cs* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-207">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs* .</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="7d26d-208">Désactiver les pages de codes d’état</span><span class="sxs-lookup"><span data-stu-id="7d26d-208">Disable status code pages</span></span>

<span data-ttu-id="7d26d-209">Pour désactiver les pages de codes d’état d’un contrôleur MVC ou d’une méthode d’action, utilisez l’attribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="7d26d-209">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="7d26d-210">Pour désactiver les pages de codes d’État pour des demandes spécifiques dans une Razor méthode de gestionnaire de pages ou dans un contrôleur MVC, utilisez <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="7d26d-210">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="7d26d-211">Code de gestion des exceptions</span><span class="sxs-lookup"><span data-stu-id="7d26d-211">Exception-handling code</span></span>

<span data-ttu-id="7d26d-212">Le code dans les pages de gestion des exceptions peut également lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="7d26d-212">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="7d26d-213">Les pages d’erreurs de production doivent être testées minutieusement et prendre soin d’éviter de lever des exceptions propres.</span><span class="sxs-lookup"><span data-stu-id="7d26d-213">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="7d26d-214">En-têtes de réponse</span><span class="sxs-lookup"><span data-stu-id="7d26d-214">Response headers</span></span>

<span data-ttu-id="7d26d-215">Une fois les en-têtes d’une réponse envoyés :</span><span class="sxs-lookup"><span data-stu-id="7d26d-215">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="7d26d-216">L’application ne peut pas modifier le code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="7d26d-216">The app can't change the response's status code.</span></span>
* <span data-ttu-id="7d26d-217">Il est impossible d’exécuter les pages d’exception ou les gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="7d26d-217">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="7d26d-218">La réponse doit être accomplie ou la connexion abandonnée.</span><span class="sxs-lookup"><span data-stu-id="7d26d-218">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="7d26d-219">Gestion des exceptions de serveur</span><span class="sxs-lookup"><span data-stu-id="7d26d-219">Server exception handling</span></span>

<span data-ttu-id="7d26d-220">En plus de la logique de gestion des exceptions dans une application, l' [implémentation du serveur http](xref:fundamentals/servers/index) peut gérer certaines exceptions.</span><span class="sxs-lookup"><span data-stu-id="7d26d-220">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="7d26d-221">Si le serveur intercepte une exception avant l’envoi des en-têtes de réponse, le serveur envoie une `500 - Internal Server Error` réponse sans corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="7d26d-221">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="7d26d-222">Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion.</span><span class="sxs-lookup"><span data-stu-id="7d26d-222">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="7d26d-223">Les demandes qui ne sont pas gérées par l’application sont gérées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="7d26d-223">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="7d26d-224">Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur.</span><span class="sxs-lookup"><span data-stu-id="7d26d-224">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="7d26d-225">Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.</span><span class="sxs-lookup"><span data-stu-id="7d26d-225">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="7d26d-226">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="7d26d-226">Startup exception handling</span></span>

<span data-ttu-id="7d26d-227">Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d26d-227">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="7d26d-228">L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="7d26d-228">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="7d26d-229">La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port.</span><span class="sxs-lookup"><span data-stu-id="7d26d-229">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="7d26d-230">Si la liaison échoue :</span><span class="sxs-lookup"><span data-stu-id="7d26d-230">If binding fails:</span></span>

* <span data-ttu-id="7d26d-231">La couche d’hébergement journalise une exception critique.</span><span class="sxs-lookup"><span data-stu-id="7d26d-231">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="7d26d-232">Le processus dotnet tombe en panne.</span><span class="sxs-lookup"><span data-stu-id="7d26d-232">The dotnet process crashes.</span></span>
* <span data-ttu-id="7d26d-233">Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="7d26d-233">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7d26d-234">En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="7d26d-234">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="7d26d-235">Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="7d26d-235">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="7d26d-236">Page d’erreur de base de données</span><span class="sxs-lookup"><span data-stu-id="7d26d-236">Database error page</span></span>

<span data-ttu-id="7d26d-237">Le filtre d’exception de la page développeur de bases de données `AddDatabaseDeveloperPageExceptionFilter` capture les exceptions liées aux bases de données qui peuvent être résolues à l’aide de Entity Framework Core migrations.</span><span class="sxs-lookup"><span data-stu-id="7d26d-237">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="7d26d-238">Lorsque ces exceptions se produisent, une réponse HTML est générée avec des détails sur les actions possibles pour résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="7d26d-238">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="7d26d-239">Cette page est activée uniquement dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="7d26d-239">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="7d26d-240">Le code suivant a été généré par les Razor modèles de Pages ASP.net core lorsque des comptes d’utilisateur individuels ont été spécifiés :</span><span class="sxs-lookup"><span data-stu-id="7d26d-240">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="7d26d-241">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="7d26d-241">Exception filters</span></span>

<span data-ttu-id="7d26d-242">Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action.</span><span class="sxs-lookup"><span data-stu-id="7d26d-242">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="7d26d-243">Dans Razor les applications pages, elles peuvent être configurées globalement ou par modèle de page.</span><span class="sxs-lookup"><span data-stu-id="7d26d-243">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="7d26d-244">Ces filtres gèrent les exceptions non gérées qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="7d26d-244">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="7d26d-245">Pour plus d'informations, consultez <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="7d26d-245">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="7d26d-246">Les filtres d’exception sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais elles ne sont pas aussi flexibles que l’intergiciel (middleware) de [gestion des exceptions](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)intégré `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-246">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="7d26d-247">Nous vous recommandons `UseExceptionHandler` d’utiliser, sauf si vous devez effectuer la gestion des erreurs différemment en fonction de l’action MVC choisie.</span><span class="sxs-lookup"><span data-stu-id="7d26d-247">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="7d26d-248">Erreurs d’état de modèle</span><span class="sxs-lookup"><span data-stu-id="7d26d-248">Model state errors</span></span>

<span data-ttu-id="7d26d-249">Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="7d26d-249">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d26d-250">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7d26d-250">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7d26d-251">Par  [Tom Dykstra](https://github.com/tdykstra/)et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7d26d-251">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7d26d-252">Cet article décrit les approches courantes de gestion des erreurs dans ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="7d26d-252">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="7d26d-253">Consultez <xref:web-api/handle-errors> pour les API Web.</span><span class="sxs-lookup"><span data-stu-id="7d26d-253">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="7d26d-254">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="7d26d-254">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="7d26d-255">([Guide pratique de téléchargement](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="7d26d-255">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="7d26d-256">Page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="7d26d-256">Developer Exception Page</span></span>

<span data-ttu-id="7d26d-257">La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes.</span><span class="sxs-lookup"><span data-stu-id="7d26d-257">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="7d26d-258">Les modèles ASP.NET Core génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7d26d-258">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="7d26d-259">Le code précédent permet à la page d’exception de développeur lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="7d26d-259">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="7d26d-260">Les modèles <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> sont placés avant tout intergiciel, de sorte que les exceptions sont interceptées dans l’intergiciel (middleware) qui suit.</span><span class="sxs-lookup"><span data-stu-id="7d26d-260">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="7d26d-261">Le code précédent active la page d’exception du développeur **uniquement lorsque l’application s’exécute dans l’environnement de développement** .</span><span class="sxs-lookup"><span data-stu-id="7d26d-261">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment** .</span></span> <span data-ttu-id="7d26d-262">Les informations détaillées sur les exceptions ne doivent pas être affichées publiquement lorsque l’application s’exécute en production.</span><span class="sxs-lookup"><span data-stu-id="7d26d-262">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="7d26d-263">Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7d26d-263">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="7d26d-264">La page exception du développeur contient les informations suivantes sur l’exception et la requête :</span><span class="sxs-lookup"><span data-stu-id="7d26d-264">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="7d26d-265">Arborescence des appels de procédure</span><span class="sxs-lookup"><span data-stu-id="7d26d-265">Stack trace</span></span>
* <span data-ttu-id="7d26d-266">Paramètres de chaîne de requête, le cas échéant</span><span class="sxs-lookup"><span data-stu-id="7d26d-266">Query string parameters if any</span></span>
* <span data-ttu-id="7d26d-267">Cookiele cas échéant</span><span class="sxs-lookup"><span data-stu-id="7d26d-267">Cookies if any</span></span>
* <span data-ttu-id="7d26d-268">En-têtes</span><span class="sxs-lookup"><span data-stu-id="7d26d-268">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="7d26d-269">Page Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="7d26d-269">Exception handler page</span></span>

<span data-ttu-id="7d26d-270">Pour configurer une page de gestion des erreurs personnalisée en fonction de l’environnement de production, utilisez le middleware de gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="7d26d-270">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="7d26d-271">Le middleware :</span><span class="sxs-lookup"><span data-stu-id="7d26d-271">The middleware:</span></span>

* <span data-ttu-id="7d26d-272">Intercepte et consigne les exceptions.</span><span class="sxs-lookup"><span data-stu-id="7d26d-272">Catches and logs exceptions.</span></span>
* <span data-ttu-id="7d26d-273">Réexécute la requête dans un autre pipeline pour la page ou le contrôleur indiqué(e).</span><span class="sxs-lookup"><span data-stu-id="7d26d-273">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="7d26d-274">La demande n’est pas réexécutée si la réponse a démarré.</span><span class="sxs-lookup"><span data-stu-id="7d26d-274">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="7d26d-275">Le code généré par le modèle ré-exécute la demande à `/Error` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-275">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="7d26d-276">Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ajoute le middleware de gestion des exceptions dans des environnements autres que les environnements de développement :</span><span class="sxs-lookup"><span data-stu-id="7d26d-276">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="7d26d-277">Le Razor modèle d’application pages fournit une page d’erreur ( *. cshtml* ) et une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-277">The Razor Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="7d26d-278">Pour une application MVC, le modèle de projet comprend une méthode d’action d’erreur et un affichage des erreurs dans le contrôleur d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="7d26d-278">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="7d26d-279">Ne Marquez pas la méthode d’action du gestionnaire d’erreurs avec des attributs de méthode HTTP, tels que `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-279">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="7d26d-280">Les verbes explicites empêchent certaines demandes d’atteindre la méthode.</span><span class="sxs-lookup"><span data-stu-id="7d26d-280">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="7d26d-281">Autorisez l’accès anonyme à la méthode si les utilisateurs non authentifiés doivent voir l’affichage des erreurs.</span><span class="sxs-lookup"><span data-stu-id="7d26d-281">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="7d26d-282">Accéder à l'exception</span><span class="sxs-lookup"><span data-stu-id="7d26d-282">Access the exception</span></span>

<span data-ttu-id="7d26d-283">Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin de la demande d’origine dans une page ou un contrôleur de gestionnaire d’erreurs :</span><span class="sxs-lookup"><span data-stu-id="7d26d-283">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="7d26d-284">Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients.</span><span class="sxs-lookup"><span data-stu-id="7d26d-284">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="7d26d-285">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="7d26d-285">Serving errors is a security risk.</span></span>

<span data-ttu-id="7d26d-286">Pour déclencher la page de gestion des exceptions précédente, définissez l’environnement sur productions et forcez une exception.</span><span class="sxs-lookup"><span data-stu-id="7d26d-286">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="7d26d-287">Expression lambda Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="7d26d-287">Exception handler lambda</span></span>

<span data-ttu-id="7d26d-288">En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>,</span><span class="sxs-lookup"><span data-stu-id="7d26d-288">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="7d26d-289">ce qui permet d’accéder à l’erreur avant de retourner la réponse.</span><span class="sxs-lookup"><span data-stu-id="7d26d-289">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="7d26d-290">Voici un exemple d’utilisation d’une expression lambda pour la gestion des exceptions :</span><span class="sxs-lookup"><span data-stu-id="7d26d-290">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="7d26d-291">Dans le code précédent, `await context.Response.WriteAsync(new string(' ', 512));` est ajouté afin que le navigateur Internet Explorer affiche le message d’erreur plutôt qu’un message d’erreur IE.</span><span class="sxs-lookup"><span data-stu-id="7d26d-291">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="7d26d-292">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="7d26d-292">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="7d26d-293">Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients.</span><span class="sxs-lookup"><span data-stu-id="7d26d-293">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="7d26d-294">Cela représenterait un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="7d26d-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="7d26d-295">Pour afficher le résultat de l’expression lambda de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerLambda` et sélectionnez **Déclencher une exception** sur la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="7d26d-295">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="7d26d-296">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="7d26d-296">UseStatusCodePages</span></span>

<span data-ttu-id="7d26d-297">Par défaut, une application ASP.NET Core ne fournit pas une page de codes d’état pour les codes d’état HTTP, comme *404 - Introuvable* .</span><span class="sxs-lookup"><span data-stu-id="7d26d-297">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found* .</span></span> <span data-ttu-id="7d26d-298">L’application retourne un code d’état et un corps de réponse vide.</span><span class="sxs-lookup"><span data-stu-id="7d26d-298">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="7d26d-299">Pour fournir des pages de codes d’état, utilisez le middleware Pages de codes d’état.</span><span class="sxs-lookup"><span data-stu-id="7d26d-299">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="7d26d-300">L’intergiciel est mis à disposition par le package [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .</span><span class="sxs-lookup"><span data-stu-id="7d26d-300">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="7d26d-301">Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> dans la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7d26d-301">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="7d26d-302">Appelez `UseStatusCodePages` avant les middlewares de gestion des demandes (par exemple, le middleware de fichiers statiques et le middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="7d26d-302">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="7d26d-303">Lorsque `UseStatusCodePages` n’est pas utilisé, la navigation vers une URL sans point de terminaison retourne un message d’erreur dépendant du navigateur qui indique que le point de terminaison est introuvable.</span><span class="sxs-lookup"><span data-stu-id="7d26d-303">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="7d26d-304">Par exemple, la navigation vers `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-304">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="7d26d-305">Lorsque `UseStatusCodePages` est appelé, le navigateur retourne :</span><span class="sxs-lookup"><span data-stu-id="7d26d-305">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="7d26d-306">UseStatusCodePages avec chaîne de format</span><span class="sxs-lookup"><span data-stu-id="7d26d-306">UseStatusCodePages with format string</span></span>

<span data-ttu-id="7d26d-307">Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend un type de contenu et une chaîne de format :</span><span class="sxs-lookup"><span data-stu-id="7d26d-307">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="7d26d-308">UseStatusCodePages avec expression lambda</span><span class="sxs-lookup"><span data-stu-id="7d26d-308">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="7d26d-309">Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend une expression lambda :</span><span class="sxs-lookup"><span data-stu-id="7d26d-309">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="7d26d-310">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="7d26d-310">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="7d26d-311">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> :</span><span class="sxs-lookup"><span data-stu-id="7d26d-311">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="7d26d-312">Envoie un code d’état *302 - Trouvé* au client.</span><span class="sxs-lookup"><span data-stu-id="7d26d-312">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="7d26d-313">Redirige le client à l’emplacement fourni dans le modèle d’URL.</span><span class="sxs-lookup"><span data-stu-id="7d26d-313">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="7d26d-314">Le modèle d’URL peut comporter un espace réservé `{0}` pour le code d’état, comme dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="7d26d-314">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="7d26d-315">Si le modèle d’URL commence par `~` (tilde), le `~` est remplacé par le de l’application `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="7d26d-315">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="7d26d-316">Si vous pointez vers un point de terminaison au sein de l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7d26d-316">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="7d26d-317">Pour obtenir un Razor exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="7d26d-317">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="7d26d-318">Cette méthode est généralement utilisée lorsque l’application :</span><span class="sxs-lookup"><span data-stu-id="7d26d-318">This method is commonly used when the app:</span></span>

* <span data-ttu-id="7d26d-319">Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur.</span><span class="sxs-lookup"><span data-stu-id="7d26d-319">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="7d26d-320">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.</span><span class="sxs-lookup"><span data-stu-id="7d26d-320">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="7d26d-321">Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.</span><span class="sxs-lookup"><span data-stu-id="7d26d-321">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="7d26d-322">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="7d26d-322">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="7d26d-323">La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> :</span><span class="sxs-lookup"><span data-stu-id="7d26d-323">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="7d26d-324">Retourne le code d’état d’origine au client.</span><span class="sxs-lookup"><span data-stu-id="7d26d-324">Returns the original status code to the client.</span></span>
* <span data-ttu-id="7d26d-325">Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.</span><span class="sxs-lookup"><span data-stu-id="7d26d-325">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="7d26d-326">Si vous pointez vers un point de terminaison au sein de l’application, créez une vue ou une Razor page MVC pour le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7d26d-326">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="7d26d-327">Vérifiez que `UseStatusCodePagesWithReExecute` est placé avant `UseRouting` que la demande puisse être redirigée vers la page d’État.</span><span class="sxs-lookup"><span data-stu-id="7d26d-327">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="7d26d-328">Pour obtenir un Razor exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="7d26d-328">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="7d26d-329">Cette méthode est généralement utilisée lorsque l’application doit :</span><span class="sxs-lookup"><span data-stu-id="7d26d-329">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="7d26d-330">Traiter la demande sans la rediriger vers un autre point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7d26d-330">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="7d26d-331">Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.</span><span class="sxs-lookup"><span data-stu-id="7d26d-331">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="7d26d-332">Conserver et retourner le code d’état d’origine avec la réponse.</span><span class="sxs-lookup"><span data-stu-id="7d26d-332">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="7d26d-333">Les modèles d’URL et de chaîne de requête peuvent comporter un espace réservé (`{0}`) pour le code d’état.</span><span class="sxs-lookup"><span data-stu-id="7d26d-333">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="7d26d-334">Le modèle d’URL doit commencer par une barre oblique (`/`).</span><span class="sxs-lookup"><span data-stu-id="7d26d-334">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="7d26d-335">Si vous utilisez un espace réservé dans le chemin, vérifiez que le point de terminaison (page ou contrôleur) peut traiter le segment de chemin.</span><span class="sxs-lookup"><span data-stu-id="7d26d-335">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="7d26d-336">Par exemple, une Razor page pour les erreurs doit accepter la valeur de segment de chemin d’accès facultative avec la `@page` directive :</span><span class="sxs-lookup"><span data-stu-id="7d26d-336">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="7d26d-337">Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7d26d-337">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="7d26d-338">Désactiver les pages de codes d’état</span><span class="sxs-lookup"><span data-stu-id="7d26d-338">Disable status code pages</span></span>

<span data-ttu-id="7d26d-339">Pour désactiver les pages de codes d’État pour un contrôleur MVC ou une méthode d’action, utilisez l' [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="7d26d-339">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="7d26d-340">Pour désactiver les pages de codes d’État pour des demandes spécifiques dans une Razor méthode de gestionnaire de pages ou dans un contrôleur MVC, utilisez <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="7d26d-340">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="7d26d-341">Code de gestion des exceptions</span><span class="sxs-lookup"><span data-stu-id="7d26d-341">Exception-handling code</span></span>

<span data-ttu-id="7d26d-342">Le code dans les pages de gestion des exceptions peut lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="7d26d-342">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="7d26d-343">Il est souvent judicieux de mettre dans les pages d’erreur de production du contenu purement statique.</span><span class="sxs-lookup"><span data-stu-id="7d26d-343">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="7d26d-344">En-têtes de réponse</span><span class="sxs-lookup"><span data-stu-id="7d26d-344">Response headers</span></span>

<span data-ttu-id="7d26d-345">Une fois les en-têtes d’une réponse envoyés :</span><span class="sxs-lookup"><span data-stu-id="7d26d-345">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="7d26d-346">L’application ne peut pas modifier le code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="7d26d-346">The app can't change the response's status code.</span></span>
* <span data-ttu-id="7d26d-347">Il est impossible d’exécuter les pages d’exception ou les gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="7d26d-347">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="7d26d-348">La réponse doit être accomplie ou la connexion abandonnée.</span><span class="sxs-lookup"><span data-stu-id="7d26d-348">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="7d26d-349">Gestion des exceptions de serveur</span><span class="sxs-lookup"><span data-stu-id="7d26d-349">Server exception handling</span></span>

<span data-ttu-id="7d26d-350">En plus de la logique de gestion des exceptions de votre application, [l’implémentation de serveur HTTP](xref:fundamentals/servers/index) peut gérer certaines exceptions.</span><span class="sxs-lookup"><span data-stu-id="7d26d-350">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="7d26d-351">Si le serveur intercepte une exception avant que les en-têtes de réponse ne soient envoyés, le serveur envoie une réponse *500 Erreur interne du serveur* sans corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="7d26d-351">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="7d26d-352">Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion.</span><span class="sxs-lookup"><span data-stu-id="7d26d-352">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="7d26d-353">Les demandes qui ne sont pas gérées par votre application sont gérées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="7d26d-353">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="7d26d-354">Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur.</span><span class="sxs-lookup"><span data-stu-id="7d26d-354">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="7d26d-355">Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.</span><span class="sxs-lookup"><span data-stu-id="7d26d-355">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="7d26d-356">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="7d26d-356">Startup exception handling</span></span>

<span data-ttu-id="7d26d-357">Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d26d-357">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="7d26d-358">L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="7d26d-358">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="7d26d-359">La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port.</span><span class="sxs-lookup"><span data-stu-id="7d26d-359">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="7d26d-360">Si la liaison échoue :</span><span class="sxs-lookup"><span data-stu-id="7d26d-360">If binding fails:</span></span>

* <span data-ttu-id="7d26d-361">La couche d’hébergement journalise une exception critique.</span><span class="sxs-lookup"><span data-stu-id="7d26d-361">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="7d26d-362">Le processus dotnet tombe en panne.</span><span class="sxs-lookup"><span data-stu-id="7d26d-362">The dotnet process crashes.</span></span>
* <span data-ttu-id="7d26d-363">Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="7d26d-363">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7d26d-364">En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="7d26d-364">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="7d26d-365">Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="7d26d-365">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="7d26d-366">Page d’erreur de base de données</span><span class="sxs-lookup"><span data-stu-id="7d26d-366">Database error page</span></span>

<span data-ttu-id="7d26d-367">L’intergiciel (middleware) de page d’erreur de base de données capture des exceptions liées à la base de données qui peuvent être résolues à l’aide de Entity Framework migrations.</span><span class="sxs-lookup"><span data-stu-id="7d26d-367">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="7d26d-368">Lorsque ces exceptions se produisent, une réponse HTML comportant le détail des actions possibles pour résoudre le problème est générée.</span><span class="sxs-lookup"><span data-stu-id="7d26d-368">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="7d26d-369">Cette page ne doit être activée que dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="7d26d-369">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="7d26d-370">Pour cela, ajoutez du code à `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7d26d-370">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="7d26d-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requiert le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="7d26d-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="7d26d-372">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="7d26d-372">Exception filters</span></span>

<span data-ttu-id="7d26d-373">Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action.</span><span class="sxs-lookup"><span data-stu-id="7d26d-373">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="7d26d-374">Dans Razor les applications pages, elles peuvent être configurées globalement ou par modèle de page.</span><span class="sxs-lookup"><span data-stu-id="7d26d-374">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="7d26d-375">Ces filtres gèrent les exceptions non prises en charge qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="7d26d-375">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="7d26d-376">Pour plus d'informations, consultez <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="7d26d-376">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="7d26d-377">Les filtres d’exceptions sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais n’offrent pas la même souplesse que le middleware de gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="7d26d-377">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="7d26d-378">Nous vous recommandons d’utiliser le middleware.</span><span class="sxs-lookup"><span data-stu-id="7d26d-378">We recommend using the middleware.</span></span> <span data-ttu-id="7d26d-379">N’utilisez des filtres que si vous devez gérer les erreurs différemment en fonction de l’action MVC choisie.</span><span class="sxs-lookup"><span data-stu-id="7d26d-379">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="7d26d-380">Erreurs d’état de modèle</span><span class="sxs-lookup"><span data-stu-id="7d26d-380">Model state errors</span></span>

<span data-ttu-id="7d26d-381">Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="7d26d-381">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d26d-382">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7d26d-382">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
