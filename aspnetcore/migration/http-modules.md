---
title: Migrer des gestionnaires et des modules HTTP vers ASP.NET Core intergiciel
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/http-modules
ms.openlocfilehash: 9664f49bd709d2c9e46130773211c339e391d1f6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060701"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="18824-102">Migrer des gestionnaires et des modules HTTP vers ASP.NET Core intergiciel</span><span class="sxs-lookup"><span data-stu-id="18824-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="18824-103">Cet article explique comment migrer [des modules et des gestionnaires HTTP ASP.NET existants à partir de System. webServer](/iis/configuration/system.webserver/) vers ASP.net Core [intergiciel](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="18824-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="18824-104">Modules et gestionnaires revisités</span><span class="sxs-lookup"><span data-stu-id="18824-104">Modules and handlers revisited</span></span>

<span data-ttu-id="18824-105">Avant de passer à ASP.NET Core intergiciel, nous allons tout d’abord récapituler le fonctionnement des modules et gestionnaires HTTP :</span><span class="sxs-lookup"><span data-stu-id="18824-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Gestionnaire de modules](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="18824-107">**Les gestionnaires sont les suivants :**</span><span class="sxs-lookup"><span data-stu-id="18824-107">**Handlers are:**</span></span>

* <span data-ttu-id="18824-108">Classes qui implémentent [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="18824-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="18824-109">Utilisé pour gérer les demandes avec un nom de fichier ou une extension donné (par exemple, *. Report* )</span><span class="sxs-lookup"><span data-stu-id="18824-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="18824-110">[Configuré](/iis/configuration/system.webserver/handlers/) dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="18824-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="18824-111">**Les modules sont les suivants :**</span><span class="sxs-lookup"><span data-stu-id="18824-111">**Modules are:**</span></span>

* <span data-ttu-id="18824-112">Classes qui implémentent [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="18824-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="18824-113">Appelé pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="18824-113">Invoked for every request</span></span>

* <span data-ttu-id="18824-114">Possibilité de court-circuit (arrêter le traitement d’une demande)</span><span class="sxs-lookup"><span data-stu-id="18824-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="18824-115">Peut être ajouté à la réponse HTTP ou créer son propre</span><span class="sxs-lookup"><span data-stu-id="18824-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="18824-116">[Configuré](/iis/configuration/system.webserver/modules/) dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="18824-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="18824-117">**L’ordre dans lequel les modules traitent les demandes entrantes est déterminé par :**</span><span class="sxs-lookup"><span data-stu-id="18824-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="18824-118"><https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>, Qui est un événement de série déclenché par ASP.net : [beginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Chaque module peut créer un gestionnaire pour un ou plusieurs événements.</span><span class="sxs-lookup"><span data-stu-id="18824-118">The <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>, which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="18824-119">Pour le même événement, ordre dans lequel ils sont configurés dans *Web.config* .</span><span class="sxs-lookup"><span data-stu-id="18824-119">For the same event, the order in which they're configured in *Web.config* .</span></span>

<span data-ttu-id="18824-120">En plus des modules, vous pouvez ajouter des gestionnaires pour les événements de cycle de vie à votre fichier *global.asax.cs* .</span><span class="sxs-lookup"><span data-stu-id="18824-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="18824-121">Ces gestionnaires s’exécutent après les gestionnaires dans les modules configurés.</span><span class="sxs-lookup"><span data-stu-id="18824-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="18824-122">Des gestionnaires et des modules à l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="18824-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="18824-123">**Les intergiciels (middleware) sont plus simples que les gestionnaires et les modules HTTP :**</span><span class="sxs-lookup"><span data-stu-id="18824-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="18824-124">Les modules, les gestionnaires, les *global.asax.cs* , les *Web.config* (à l’exception de la configuration IIS) et le cycle de vie de l’application ont disparu</span><span class="sxs-lookup"><span data-stu-id="18824-124">Modules, handlers, *Global.asax.cs* , *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="18824-125">Les rôles des modules et des gestionnaires ont été pris en charge par l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="18824-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="18824-126">Les intergiciels (middleware) sont configurés à l’aide du code plutôt que dans *Web.config*</span><span class="sxs-lookup"><span data-stu-id="18824-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="18824-127">La [branche de pipeline](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) vous permet d’envoyer des demandes à un intergiciel (middleware) spécifique, en fonction de l’URL, mais également des en-têtes de demande, des chaînes de requête, etc.</span><span class="sxs-lookup"><span data-stu-id="18824-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="18824-128">La [branche de pipeline](xref:fundamentals/middleware/index#use-run-and-map) vous permet d’envoyer des demandes à un intergiciel (middleware) spécifique, en fonction de l’URL, mais également des en-têtes de demande, des chaînes de requête, etc.</span><span class="sxs-lookup"><span data-stu-id="18824-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="18824-129">**Les intergiciels sont très similaires aux modules :**</span><span class="sxs-lookup"><span data-stu-id="18824-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="18824-130">Appelé en principe pour chaque requête</span><span class="sxs-lookup"><span data-stu-id="18824-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="18824-131">Capable de court-circuiter une requête, en [ne transmettant pas la demande à l’intergiciel suivant](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="18824-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="18824-132">Possibilité de créer leur propre réponse HTTP</span><span class="sxs-lookup"><span data-stu-id="18824-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="18824-133">**Les intergiciels et les modules sont traités dans un ordre différent :**</span><span class="sxs-lookup"><span data-stu-id="18824-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="18824-134">L’ordre des intergiciels est basé sur l’ordre dans lequel ils sont insérés dans le pipeline de demande, alors que l’ordre des modules est principalement basé sur les <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> événements</span><span class="sxs-lookup"><span data-stu-id="18824-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> events</span></span>

* <span data-ttu-id="18824-135">L’ordre de l’intergiciel pour les réponses est l’inverse par rapport à celui des demandes, tandis que l’ordre des modules est le même pour les demandes et les réponses</span><span class="sxs-lookup"><span data-stu-id="18824-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="18824-136">Consultez [création d’un pipeline d’intergiciel (middleware) avec IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="18824-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="18824-138">Notez comment dans l’image ci-dessus, l’intergiciel (middleware) d’authentification a court-circuité la demande.</span><span class="sxs-lookup"><span data-stu-id="18824-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="18824-139">Migration du code du module vers l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="18824-139">Migrating module code to middleware</span></span>

<span data-ttu-id="18824-140">Un module HTTP existant doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="18824-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="18824-141">Comme indiqué dans la page [intergiciel (middleware](xref:fundamentals/middleware/index) ), un intergiciel (middleware) ASP.net Core est une classe qui expose une méthode qui `Invoke` prend un `HttpContext` et retourne un `Task` .</span><span class="sxs-lookup"><span data-stu-id="18824-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="18824-142">Votre nouvel intergiciel ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="18824-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="18824-143">Le modèle d’intergiciel (middleware) précédent a été extrait de la section sur l’écriture d’un [intergiciel (middleware](xref:fundamentals/middleware/write)).</span><span class="sxs-lookup"><span data-stu-id="18824-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="18824-144">La classe d’assistance *MyMiddlewareExtensions* facilite la configuration de votre intergiciel dans votre `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="18824-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="18824-145">La `UseMyMiddleware` méthode ajoute votre classe middleware au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="18824-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="18824-146">Les services requis par l’intergiciel (middleware) sont injectés dans le constructeur de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="18824-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="18824-147">Votre module peut mettre fin à une demande, par exemple si l’utilisateur n’est pas autorisé :</span><span class="sxs-lookup"><span data-stu-id="18824-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="18824-148">Un intergiciel gère cela en n’appelant pas `Invoke` sur l’intergiciel (middleware) suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="18824-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="18824-149">N’oubliez pas que cela ne termine pas complètement la requête, car les intergiciels précédents sont toujours appelés quand la réponse fait remonter le pipeline.</span><span class="sxs-lookup"><span data-stu-id="18824-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="18824-150">Lorsque vous migrez les fonctionnalités de votre module vers votre nouvel intergiciel, vous constaterez peut-être que votre code n’est pas compilé, car la `HttpContext` classe a été considérablement modifiée dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="18824-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="18824-151">[Plus tard](#migrating-to-the-new-httpcontext), vous verrez comment migrer vers le nouveau ASP.net Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="18824-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="18824-152">Migration de l’insertion du module dans le pipeline de requête</span><span class="sxs-lookup"><span data-stu-id="18824-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="18824-153">Les modules HTTP sont généralement ajoutés au pipeline de requêtes à l’aide de *Web.config* :</span><span class="sxs-lookup"><span data-stu-id="18824-153">HTTP modules are typically added to the request pipeline using *Web.config* :</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="18824-154">Convertissez ceci en [ajoutant votre nouvel intergiciel](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) au pipeline de requête dans votre `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="18824-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="18824-155">L’emplacement exact dans le pipeline où vous insérez votre nouvel intergiciel dépend de l’événement qu’il a traité en tant que module ( `BeginRequest` , `EndRequest` , etc.) et de son ordre dans la liste des modules de *Web.config* .</span><span class="sxs-lookup"><span data-stu-id="18824-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config* .</span></span>

<span data-ttu-id="18824-156">Comme indiqué précédemment, il n’existe aucun cycle de vie d’application dans ASP.NET Core et l’ordre dans lequel les réponses sont traitées par l’intergiciel diffère de l’ordre utilisé par les modules.</span><span class="sxs-lookup"><span data-stu-id="18824-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="18824-157">Cela pourrait rendre votre décision de classement plus complexe.</span><span class="sxs-lookup"><span data-stu-id="18824-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="18824-158">Si le classement devient un problème, vous pouvez fractionner votre module en plusieurs composants de l’intergiciel (middleware) qui peuvent être classés indépendamment.</span><span class="sxs-lookup"><span data-stu-id="18824-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="18824-159">Migration du code du gestionnaire vers l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="18824-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="18824-160">Un gestionnaire HTTP ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="18824-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="18824-161">Dans votre projet ASP.NET Core, vous pouvez traduire cela en un intergiciel (middleware) semblable à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="18824-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="18824-162">Cet intergiciel est très similaire à l’intergiciel (middleware) correspondant aux modules.</span><span class="sxs-lookup"><span data-stu-id="18824-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="18824-163">La seule différence réelle est qu’il n’y a pas d’appel à `_next.Invoke(context)` .</span><span class="sxs-lookup"><span data-stu-id="18824-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="18824-164">Cela est logique, étant donné que le gestionnaire se trouve à la fin du pipeline de requête, il n’y aura donc aucun middleware suivant à appeler.</span><span class="sxs-lookup"><span data-stu-id="18824-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="18824-165">Migration de l’insertion du gestionnaire dans le pipeline de requête</span><span class="sxs-lookup"><span data-stu-id="18824-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="18824-166">La configuration d’un gestionnaire HTTP est effectuée dans *Web.config* et ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="18824-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="18824-167">Vous pouvez convertir cela en ajoutant votre nouvel intergiciel de gestionnaire au pipeline de requête de votre `Startup` classe, similaire aux middlewares convertis à partir de modules.</span><span class="sxs-lookup"><span data-stu-id="18824-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="18824-168">Le problème avec cette approche est qu’elle envoie toutes les demandes à votre intergiciel de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="18824-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="18824-169">Toutefois, vous souhaitez que les demandes avec une extension donnée atteignent votre intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="18824-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="18824-170">Cela vous donnera les mêmes fonctionnalités que celles que vous aviez avec votre gestionnaire HTTP.</span><span class="sxs-lookup"><span data-stu-id="18824-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="18824-171">Une solution consiste à créer une branche du pipeline pour les requêtes avec une extension donnée, à l’aide de la `MapWhen` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="18824-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="18824-172">Pour ce faire, utilisez la même `Configure` méthode que celle où vous ajoutez l’autre intergiciel :</span><span class="sxs-lookup"><span data-stu-id="18824-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="18824-173">`MapWhen` prend les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="18824-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="18824-174">Une expression lambda qui prend le `HttpContext` et retourne la valeur `true` si la demande doit descendre dans la branche.</span><span class="sxs-lookup"><span data-stu-id="18824-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="18824-175">Cela signifie que vous pouvez créer des branches de requêtes non seulement en fonction de leur extension, mais également sur des en-têtes de demande, des paramètres de chaîne de requête, etc.</span><span class="sxs-lookup"><span data-stu-id="18824-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="18824-176">Lambda qui prend un `IApplicationBuilder` et ajoute tous les intergiciels (middleware) pour la branche.</span><span class="sxs-lookup"><span data-stu-id="18824-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="18824-177">Cela signifie que vous pouvez ajouter un intergiciel (middleware) supplémentaire à la branche devant l’intergiciel (middleware) de votre gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="18824-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="18824-178">Intergiciel ajouté au pipeline avant que la branche ne soit appelée sur toutes les demandes ; la branche n’aura aucun impact sur ces dernières.</span><span class="sxs-lookup"><span data-stu-id="18824-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="18824-179">Chargement des options de l’intergiciel (middleware) à l’aide du modèle options</span><span class="sxs-lookup"><span data-stu-id="18824-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="18824-180">Certains modules et gestionnaires possèdent des options de configuration qui sont stockées dans *Web.config* . Toutefois, dans ASP.NET Core un nouveau modèle de configuration est utilisé à la place de *Web.config* .</span><span class="sxs-lookup"><span data-stu-id="18824-180">Some modules and handlers have configuration options that are stored in *Web.config* . However, in ASP.NET Core a new configuration model is used in place of *Web.config* .</span></span>

<span data-ttu-id="18824-181">Le nouveau [système de configuration](xref:fundamentals/configuration/index) vous offre les options suivantes pour résoudre ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="18824-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="18824-182">Injectez directement les options dans l’intergiciel (middleware), comme indiqué dans la [section suivante](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="18824-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="18824-183">Utilisez le [modèle d’options](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="18824-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="18824-184">Créez une classe pour contenir vos options d’intergiciel, par exemple :</span><span class="sxs-lookup"><span data-stu-id="18824-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="18824-185">Stocker les valeurs d’option</span><span class="sxs-lookup"><span data-stu-id="18824-185">Store the option values</span></span>

   <span data-ttu-id="18824-186">Le système de configuration vous permet de stocker les valeurs d’option où vous le souhaitez.</span><span class="sxs-lookup"><span data-stu-id="18824-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="18824-187">Toutefois, la plupart des sites utilisent *:::no-loc(appsettings.json):::* . nous allons donc adopter cette approche :</span><span class="sxs-lookup"><span data-stu-id="18824-187">However, most sites use *:::no-loc(appsettings.json):::* , so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/:::no-loc(appsettings.json):::?range=1,14-18)]

   <span data-ttu-id="18824-188">*MyMiddlewareOptionsSection* ici est un nom de section.</span><span class="sxs-lookup"><span data-stu-id="18824-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="18824-189">Il ne doit pas nécessairement être le même que le nom de votre classe d’options.</span><span class="sxs-lookup"><span data-stu-id="18824-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="18824-190">Associer les valeurs d’option à la classe options</span><span class="sxs-lookup"><span data-stu-id="18824-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="18824-191">Le modèle d’options utilise l’infrastructure d’injection de dépendances de ASP.NET Core pour associer le type d’options (tel que `MyMiddlewareOptions` ) à un `MyMiddlewareOptions` objet qui a les options réelles.</span><span class="sxs-lookup"><span data-stu-id="18824-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="18824-192">Mettez à jour votre `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="18824-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="18824-193">Si vous utilisez *:::no-loc(appsettings.json):::* , ajoutez-le au générateur de configuration dans le `Startup` constructeur :</span><span class="sxs-lookup"><span data-stu-id="18824-193">If you're using *:::no-loc(appsettings.json):::* , add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="18824-194">Configurer le service d’options :</span><span class="sxs-lookup"><span data-stu-id="18824-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="18824-195">Associez vos options à votre classe d’options :</span><span class="sxs-lookup"><span data-stu-id="18824-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="18824-196">Injectez les options dans votre constructeur d’intergiciel.</span><span class="sxs-lookup"><span data-stu-id="18824-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="18824-197">Cela revient à injecter des options dans un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="18824-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="18824-198">La méthode d’extension [UseMiddleware](#http-modules-usemiddleware) qui ajoute votre intergiciel à l' `IApplicationBuilder` prend en charge l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="18824-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="18824-199">Cela n’est pas limité aux `IOptions` objets.</span><span class="sxs-lookup"><span data-stu-id="18824-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="18824-200">Tout autre objet requis par votre intergiciel peut être injecté de cette façon.</span><span class="sxs-lookup"><span data-stu-id="18824-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="18824-201">Chargement des options d’intergiciel par injection directe</span><span class="sxs-lookup"><span data-stu-id="18824-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="18824-202">Le modèle d’options présente l’avantage de créer un couplage faible entre les valeurs d’options et leurs consommateurs.</span><span class="sxs-lookup"><span data-stu-id="18824-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="18824-203">Une fois que vous avez associé une classe options avec les valeurs d’options réelles, toute autre classe peut accéder aux options par le biais de l’infrastructure d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="18824-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="18824-204">Il n’est pas nécessaire de transmettre des valeurs d’options.</span><span class="sxs-lookup"><span data-stu-id="18824-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="18824-205">Cela s’arrête si vous souhaitez utiliser le même intergiciel deux fois, avec des options différentes.</span><span class="sxs-lookup"><span data-stu-id="18824-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="18824-206">Par exemple, un intergiciel d’autorisation utilisé dans différentes branches autorisant des rôles différents.</span><span class="sxs-lookup"><span data-stu-id="18824-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="18824-207">Vous ne pouvez pas associer deux objets d’options différents à la classe d’options.</span><span class="sxs-lookup"><span data-stu-id="18824-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="18824-208">La solution consiste à récupérer les objets d’options avec les valeurs d’options réelles dans votre `Startup` classe et à les transmettre directement à chaque instance de votre intergiciel.</span><span class="sxs-lookup"><span data-stu-id="18824-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="18824-209">Ajouter une deuxième clé à *:::no-loc(appsettings.json):::*</span><span class="sxs-lookup"><span data-stu-id="18824-209">Add a second key to *:::no-loc(appsettings.json):::*</span></span>

   <span data-ttu-id="18824-210">Pour ajouter un deuxième ensemble d’options au *:::no-loc(appsettings.json):::* fichier, utilisez une nouvelle clé pour l’identifier de manière unique :</span><span class="sxs-lookup"><span data-stu-id="18824-210">To add a second set of options to the *:::no-loc(appsettings.json):::* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/:::no-loc(appsettings.json):::?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="18824-211">Récupérez les valeurs des options et transmettez-les à l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="18824-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="18824-212">La `Use...` méthode d’extension (qui ajoute votre intergiciel au pipeline) est un emplacement logique à passer dans les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="18824-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="18824-213">Activez l’intergiciel (middleware) pour accepter un paramètre d’options.</span><span class="sxs-lookup"><span data-stu-id="18824-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="18824-214">Fournissez une surcharge de la `Use...` méthode d’extension (qui accepte le paramètre options et le passe à `UseMiddleware` ).</span><span class="sxs-lookup"><span data-stu-id="18824-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="18824-215">Lorsque `UseMiddleware` est appelé avec des paramètres, il passe les paramètres à votre constructeur middleware lorsqu’il instancie l’objet intergiciel.</span><span class="sxs-lookup"><span data-stu-id="18824-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="18824-216">Notez comment cela encapsule l’objet d’options dans un `OptionsWrapper` objet.</span><span class="sxs-lookup"><span data-stu-id="18824-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="18824-217">Cela implémente `IOptions` , comme attendu par le constructeur d’intergiciel.</span><span class="sxs-lookup"><span data-stu-id="18824-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="18824-218">Migration vers le nouveau HttpContext</span><span class="sxs-lookup"><span data-stu-id="18824-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="18824-219">Vous avez vu précédemment que la `Invoke` méthode de votre intergiciel prend un paramètre de type `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="18824-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="18824-220">`HttpContext` a été considérablement modifié dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18824-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="18824-221">Cette section montre comment convertir les propriétés les plus couramment utilisées de [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) vers la nouvelle `Microsoft.AspNetCore.Http.HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="18824-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="18824-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="18824-222">HttpContext</span></span>

<span data-ttu-id="18824-223">**HttpContext. Items** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="18824-224">**ID de demande unique (aucun équivalent System. Web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="18824-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="18824-225">Donne un ID unique pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="18824-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="18824-226">Très utile pour inclure dans vos journaux.</span><span class="sxs-lookup"><span data-stu-id="18824-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="18824-227">HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="18824-227">HttpContext.Request</span></span>

<span data-ttu-id="18824-228">**HttpContext. Request. HttpMethod** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="18824-229">**HttpContext. Request. QueryString** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="18824-230">**HttpContext. Request. URL** et **HttpContext. Request. RawUrl** se traduisent par :</span><span class="sxs-lookup"><span data-stu-id="18824-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="18824-231">**HttpContext. Request. IsSecureConnection** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="18824-232">**HttpContext. Request. UserHostAddress** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="18824-233">**HttpContext. Request. :::no-loc(Cookie)::: s** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-233">**HttpContext.Request.:::no-loc(Cookie):::s** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_:::no-loc(Cookie):::s)]

<span data-ttu-id="18824-234">**HttpContext. Request. RequestContext. RouteData** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="18824-235">**Les en-têtes HttpContext. Request.** sont traduits par :</span><span class="sxs-lookup"><span data-stu-id="18824-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="18824-236">**HttpContext. Request. UserAgent** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="18824-237">**HttpContext. Request. UrlReferrer** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="18824-238">**HttpContext. Request. ContentType** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="18824-239">**HttpContext. Request. Form** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="18824-240">Lire les valeurs de formulaire uniquement si le sous-type de contenu est *x-www-form-urlencoded* ou *Form-Data* .</span><span class="sxs-lookup"><span data-stu-id="18824-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data* .</span></span>

<span data-ttu-id="18824-241">**HttpContext. Request. InputStream** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="18824-242">Utilisez ce code uniquement dans un intergiciel de type de gestionnaire, à la fin d’un pipeline.</span><span class="sxs-lookup"><span data-stu-id="18824-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="18824-243">Vous pouvez lire le corps brut comme indiqué ci-dessus une seule fois par requête.</span><span class="sxs-lookup"><span data-stu-id="18824-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="18824-244">L’intergiciel tentant de lire le corps après la première lecture lira un corps vide.</span><span class="sxs-lookup"><span data-stu-id="18824-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="18824-245">Cela ne s’applique pas à la lecture d’un formulaire comme indiqué plus haut, car cela s’effectue à partir d’une mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="18824-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="18824-246">HttpContext. Response</span><span class="sxs-lookup"><span data-stu-id="18824-246">HttpContext.Response</span></span>

<span data-ttu-id="18824-247">**HttpContext. Response. Status** et **HttpContext. Response. StatusDescription** se traduisent par :</span><span class="sxs-lookup"><span data-stu-id="18824-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="18824-248">**HttpContext. Response. ContentEncoding** et **HttpContext. Response. ContentType** se traduisent par :</span><span class="sxs-lookup"><span data-stu-id="18824-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="18824-249">**HttpContext. Response. ContentType** se traduit également par :</span><span class="sxs-lookup"><span data-stu-id="18824-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="18824-250">**HttpContext. Response. Output** se traduit par :</span><span class="sxs-lookup"><span data-stu-id="18824-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="18824-251">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="18824-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="18824-252">Le traitement d’un fichier est abordé dans l’article [middleware et les fonctionnalités de demande](xref:fundamentals/request-features#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="18824-252">Serving up a file is discussed in [Middleware and request features](xref:fundamentals/request-features#middleware-and-request-features).</span></span>

<span data-ttu-id="18824-253">**HttpContext. Response. en-têtes**</span><span class="sxs-lookup"><span data-stu-id="18824-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="18824-254">L’envoi d’en-têtes de réponse est compliqué par le fait que si vous les définissez après quoi tout a été écrit dans le corps de la réponse, ils ne sont pas envoyés.</span><span class="sxs-lookup"><span data-stu-id="18824-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="18824-255">La solution consiste à définir une méthode de rappel qui sera appelée juste avant le début de l’écriture dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="18824-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="18824-256">Cette opération est effectuée au début de la `Invoke` méthode dans votre intergiciel.</span><span class="sxs-lookup"><span data-stu-id="18824-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="18824-257">C’est cette méthode de rappel qui définit vos en-têtes de réponse.</span><span class="sxs-lookup"><span data-stu-id="18824-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="18824-258">Le code suivant définit une méthode de rappel appelée `SetHeaders` :</span><span class="sxs-lookup"><span data-stu-id="18824-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="18824-259">La `SetHeaders` méthode de rappel ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="18824-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="18824-260">**HttpContext. Response. :::no-loc(Cookie)::: x**</span><span class="sxs-lookup"><span data-stu-id="18824-260">**HttpContext.Response.:::no-loc(Cookie):::s**</span></span>

<span data-ttu-id="18824-261">:::no-loc(Cookie):::se déplace vers le navigateur dans un en-tête *Set- :::no-loc(Cookie):::* Response.</span><span class="sxs-lookup"><span data-stu-id="18824-261">:::no-loc(Cookie):::s travel to the browser in a *Set-:::no-loc(Cookie):::* response header.</span></span> <span data-ttu-id="18824-262">Par conséquent, l’envoi :::no-loc(cookie)::: de s requiert le même rappel que celui utilisé pour l’envoi des en-têtes de réponse :</span><span class="sxs-lookup"><span data-stu-id="18824-262">As a result, sending :::no-loc(cookie):::s requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(Set:::no-loc(Cookie):::s, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="18824-263">La `Set:::no-loc(Cookie):::s` méthode de rappel ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="18824-263">The `Set:::no-loc(Cookie):::s` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Set:::no-loc(Cookie):::s)]

## <a name="additional-resources"></a><span data-ttu-id="18824-264">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="18824-264">Additional resources</span></span>

* [<span data-ttu-id="18824-265">Vue d’ensemble des gestionnaires HTTP et des modules HTTP</span><span class="sxs-lookup"><span data-stu-id="18824-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="18824-266">Configuration</span><span class="sxs-lookup"><span data-stu-id="18824-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="18824-267">Démarrage de l'application</span><span class="sxs-lookup"><span data-stu-id="18824-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="18824-268">Middleware</span><span class="sxs-lookup"><span data-stu-id="18824-268">Middleware</span></span>](xref:fundamentals/middleware/index)
