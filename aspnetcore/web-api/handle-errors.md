---
title: Gérer les erreurs dans les API Web ASP.NET Core
author: pranavkm
description: En savoir plus sur la gestion des erreurs avec ASP.NET Core API Web.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: 92e9350a7892f8f38f64d4ebd68d54a97ec7e994
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058374"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="0f8e7-103">Gérer les erreurs dans les API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f8e7-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="0f8e7-104">Cet article explique comment gérer et personnaliser la gestion des erreurs avec ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="0f8e7-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0f8e7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="0f8e7-106">Page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="0f8e7-106">Developer Exception Page</span></span>

<span data-ttu-id="0f8e7-107">La [page exception du développeur](xref:fundamentals/error-handling) est un outil utile pour obtenir des traces de pile détaillées pour les erreurs de serveur.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="0f8e7-108">Il utilise <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> pour capturer les exceptions synchrones et asynchrones à partir du pipeline http et pour générer des réponses d’erreur.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="0f8e7-109">Pour illustrer ce propos, examinez l’action de contrôleur suivante :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="0f8e7-110">Exécutez la `curl` commande suivante pour tester l’action précédente :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f8e7-111">Dans ASP.NET Core 3,0 et versions ultérieures, la page exception du développeur affiche une réponse en texte brut si le client ne demande pas de sortie au format HTML.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="0f8e7-112">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="0f8e7-113">Pour afficher une réponse au format HTML à la place, définissez l' `Accept` en-tête de la requête HTTP sur le `text/html` type de média.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="0f8e7-114">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="0f8e7-115">Examinez l’extrait suivant de la réponse HTTP :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="0f8e7-116">Dans ASP.NET Core 2,2 et versions antérieures, la page exception du développeur affiche une réponse au format HTML.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="0f8e7-117">Par exemple, considérez l’extrait suivant de la réponse HTTP :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f8e7-118">La réponse au format HTML devient utile lors des tests par le biais d’outils tels que poster.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="0f8e7-119">La capture d’écran suivante montre à la fois le texte brut et les réponses au format HTML dans le billet :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Test de page d’exception de développeur dans le poster](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="0f8e7-121">Activez la page exception du développeur **uniquement lorsque l’application s’exécute dans l’environnement de développement**.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="0f8e7-122">Ne partagez pas des informations d’exception détaillées publiquement lorsque l’application s’exécute en production.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-122">Don't share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="0f8e7-123">Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>
>
> <span data-ttu-id="0f8e7-124">Ne Marquez pas la méthode d’action du gestionnaire d’erreurs avec des attributs de méthode HTTP, tels que `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="0f8e7-124">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="0f8e7-125">Les verbes explicites empêchent certaines demandes d’atteindre la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-125">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="0f8e7-126">Autorisez l’accès anonyme à la méthode si les utilisateurs non authentifiés doivent voir l’erreur.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-126">Allow anonymous access to the method if unauthenticated users should see the error.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="0f8e7-127">Gestionnaire d’exceptions</span><span class="sxs-lookup"><span data-stu-id="0f8e7-127">Exception handler</span></span>

<span data-ttu-id="0f8e7-128">Dans les environnements non-développement, l’intergiciel (middleware) de [gestion des exceptions](xref:fundamentals/error-handling) peut être utilisé pour générer une charge utile d’erreur :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-128">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="0f8e7-129">Dans `Startup.Configure` , appelez <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> pour utiliser l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-129">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="0f8e7-130">Configurez une action de contrôleur pour répondre à l' `/error` Itinéraire :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-130">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="0f8e7-131">L' `Error` action précédente envoie une charge utile conforme à la [norme RFC 7807](https://tools.ietf.org/html/rfc7807)au client.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-131">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="0f8e7-132">L’intergiciel (middleware) de gestion des exceptions peut également fournir une sortie de négociation de contenu plus détaillée dans l’environnement de développement local.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-132">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="0f8e7-133">Utilisez les étapes suivantes pour créer un format de charge utile cohérent dans les environnements de développement et de production :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-133">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="0f8e7-134">Dans `Startup.Configure` , inscrivez les instances d’intergiciels (middleware) de gestion des exceptions spécifiques à l’environnement :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-134">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="0f8e7-135">Dans le code précédent, l’intergiciel est inscrit avec :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-135">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="0f8e7-136">Itinéraire de `/error-local-development` dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-136">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="0f8e7-137">Un itinéraire de `/error` dans des environnements qui ne sont pas des développements.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-137">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="0f8e7-138">Appliquer le routage d’attribut aux actions du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-138">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    <span data-ttu-id="0f8e7-139">Le code précédent appelle [ControllerBase. Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) pour créer une <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> réponse.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-139">The preceding code calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response.</span></span>

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="0f8e7-140">Utiliser des exceptions pour modifier la réponse</span><span class="sxs-lookup"><span data-stu-id="0f8e7-140">Use exceptions to modify the response</span></span>

<span data-ttu-id="0f8e7-141">Le contenu de la réponse peut être modifié à partir de l’extérieur du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-141">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="0f8e7-142">Dans l’API Web ASP.NET 4. x, l’une des méthodes permettant d’effectuer cette opération était d’utiliser le <xref:System.Web.Http.HttpResponseException> type.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-142">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="0f8e7-143">ASP.NET Core n’inclut pas de type équivalent.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-143">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="0f8e7-144">La prise en charge de `HttpResponseException` peut être ajoutée avec les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-144">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="0f8e7-145">Créez un type d’exception connu nommé `HttpResponseException` :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-145">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="0f8e7-146">Créez un filtre d’action nommé `HttpResponseExceptionFilter` :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-146">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="0f8e7-147">Le filtre précédent spécifie un `Order` de la valeur entière maximale moins 10.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-147">The preceding filter specifies an `Order` of the maximum integer value minus 10.</span></span> <span data-ttu-id="0f8e7-148">Cela permet à d’autres filtres de s’exécuter à la fin du pipeline.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-148">This allows other filters to run at the end of the pipeline.</span></span>

1. <span data-ttu-id="0f8e7-149">Dans `Startup.ConfigureServices` , ajoutez le filtre d’action à la collection de filtres :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-149">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="0f8e7-150">Réponse d’erreur d’échec de validation</span><span class="sxs-lookup"><span data-stu-id="0f8e7-150">Validation failure error response</span></span>

<span data-ttu-id="0f8e7-151">Pour les contrôleurs d’API Web, MVC répond avec un type de réponse lors de l’échec de la <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> validation du modèle.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-151">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="0f8e7-152">MVC utilise les résultats de <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> pour construire la réponse d’erreur pour un échec de validation.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-152">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="0f8e7-153">L’exemple suivant utilise la fabrique pour modifier le type de réponse par défaut <xref:Microsoft.AspNetCore.Mvc.SerializableError> en in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-153">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="0f8e7-154">Réponse d’erreur du client</span><span class="sxs-lookup"><span data-stu-id="0f8e7-154">Client error response</span></span>

<span data-ttu-id="0f8e7-155">Un *résultat d’erreur* est défini en tant que résultat avec le code d’état HTTP 400 ou une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-155">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="0f8e7-156">Pour les contrôleurs d’API Web, MVC transforme un résultat d’erreur en un résultat avec <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="0f8e7-156">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="0f8e7-157">ASP.NET Core 2,1 génère une réponse de détails de problème quasiment conforme à RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-157">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="0f8e7-158">Si la conformité de 100% est importante, mettez à niveau le projet vers ASP.NET Core 2,2 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-158">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f8e7-159">La réponse d’erreur peut être configurée de l’une des manières suivantes :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-159">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="0f8e7-160">Implémenter ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="0f8e7-160">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="0f8e7-161">Utilisez ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="0f8e7-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="0f8e7-162">Implémentez `ProblemDetailsFactory`</span><span class="sxs-lookup"><span data-stu-id="0f8e7-162">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="0f8e7-163">MVC utilise <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> pour générer toutes les instances de <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> et <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="0f8e7-163">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="0f8e7-164">Cela comprend les réponses d’erreur du client, les réponses d’erreur de validation et les <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> méthodes d’assistance et.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-164">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="0f8e7-165">Pour personnaliser la réponse des détails du problème, inscrivez une implémentation personnalisée de <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-165">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="0f8e7-166">La réponse d’erreur peut être configurée comme indiqué dans la section [use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="0f8e7-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="0f8e7-167">Utilisez ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="0f8e7-167">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="0f8e7-168">Utilisez la propriété <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> pour configurer le contenu de la réponse `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-168">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="0f8e7-169">Par exemple, le code suivant `Startup.ConfigureServices` met à jour la `type` propriété pour les réponses 404 :</span><span class="sxs-lookup"><span data-stu-id="0f8e7-169">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a><span data-ttu-id="0f8e7-170">Intergiciel (middleware) personnalisé pour gérer les exceptions</span><span class="sxs-lookup"><span data-stu-id="0f8e7-170">Custom Middleware to handle exceptions</span></span>

<span data-ttu-id="0f8e7-171">Les valeurs par défaut de l’intergiciel (middleware) de gestion des exceptions fonctionnent bien pour la plupart des applications.</span><span class="sxs-lookup"><span data-stu-id="0f8e7-171">The defaults in the exception handling middleware works well for most apps.</span></span> <span data-ttu-id="0f8e7-172">Pour les applications qui nécessitent une gestion spécialisée des exceptions, pensez à [personnaliser l’intergiciel (middleware) de gestion des exceptions](xref:fundamentals/error-handling#exception-handler-lambda).</span><span class="sxs-lookup"><span data-stu-id="0f8e7-172">For apps that require specialized exception handling, consider [customizing the exception handling middleware](xref:fundamentals/error-handling#exception-handler-lambda).</span></span>
