---
title: Gérer les erreurs dans les Blazor applications ASP.net Core
author: guardrex
description: Découvrez comment ASP.NET Core Blazor comment Blazor gère les exceptions non gérées et comment développer des applications qui détectent et gèrent les erreurs.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
uid: blazor/fundamentals/handle-errors
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d4c8054afc3818d58bc2a047a0aa74613ae6047b
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395095"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="dff93-103">Gérer les erreurs dans les Blazor applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="dff93-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="dff93-104">Cet article explique comment Blazor gère les exceptions non gérées et comment développer des applications qui détectent et gèrent les erreurs.</span><span class="sxs-lookup"><span data-stu-id="dff93-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

::: zone pivot="webassembly"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="dff93-105">Erreurs détaillées pendant le développement</span><span class="sxs-lookup"><span data-stu-id="dff93-105">Detailed errors during development</span></span>

<span data-ttu-id="dff93-106">Quand une Blazor application ne fonctionne pas correctement pendant le développement, la réception d’informations d’erreur détaillées de l’application vous aide à résoudre les problèmes et à résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="dff93-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="dff93-107">Lorsqu’une erreur se produit, Blazor les applications affichent une barre jaune clair au bas de l’écran :</span><span class="sxs-lookup"><span data-stu-id="dff93-107">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="dff93-108">Pendant le développement, la barre vous dirige vers la console du navigateur, où vous pouvez voir l’exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-108">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="dff93-109">En production, la barre avertit l’utilisateur qu’une erreur s’est produite et recommande l’actualisation du navigateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-109">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="dff93-110">L’interface utilisateur de cette expérience de gestion des erreurs fait partie des [ Blazor modèles de projet](xref:blazor/project-structure).</span><span class="sxs-lookup"><span data-stu-id="dff93-110">The UI for this error handling experience is part of the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="dff93-111">Dans une Blazor WebAssembly application, personnalisez l’expérience dans le `wwwroot/index.html` fichier :</span><span class="sxs-lookup"><span data-stu-id="dff93-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="dff93-112">L' `blazor-error-ui` élément est normalement masqué en raison de la présence du `display: none` style de la `blazor-error-ui` classe CSS dans la feuille de style de l’application ( `wwwroot/css/app.css` ).</span><span class="sxs-lookup"><span data-stu-id="dff93-112">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the app's stylesheet (`wwwroot/css/app.css`).</span></span> <span data-ttu-id="dff93-113">Lorsqu’une erreur se produit, l’infrastructure s’applique `display: block` à l’élément.</span><span class="sxs-lookup"><span data-stu-id="dff93-113">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="dff93-114">Gérer les exceptions non gérées dans le code du développeur</span><span class="sxs-lookup"><span data-stu-id="dff93-114">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="dff93-115">Pour qu’une application continue après une erreur, l’application doit avoir une logique de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="dff93-115">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="dff93-116">Les sections suivantes de cet article décrivent les sources potentielles d’exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="dff93-116">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="dff93-117">En production, ne rendez pas les messages d’exception d’infrastructure ou les traces de pile dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-117">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="dff93-118">Le rendu des messages d’exception ou des traces de pile peut :</span><span class="sxs-lookup"><span data-stu-id="dff93-118">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="dff93-119">Divulguer des informations sensibles aux utilisateurs finaux.</span><span class="sxs-lookup"><span data-stu-id="dff93-119">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="dff93-120">Aidez un utilisateur malveillant à découvrir des faiblesses dans une application qui peuvent compromettre la sécurité de l’application, du serveur ou du réseau.</span><span class="sxs-lookup"><span data-stu-id="dff93-120">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="dff93-121">Gestion globale des exceptions</span><span class="sxs-lookup"><span data-stu-id="dff93-121">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="dff93-122">Consigner les erreurs avec un fournisseur persistant</span><span class="sxs-lookup"><span data-stu-id="dff93-122">Log errors with a persistent provider</span></span>

<span data-ttu-id="dff93-123">Si une exception non gérée se produit, l’exception est consignée dans <xref:Microsoft.Extensions.Logging.ILogger> les instances configurées dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="dff93-123">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="dff93-124">Par défaut, Blazor les applications se connectent à la sortie de la console avec le fournisseur d’informations de journalisation de la console.</span><span class="sxs-lookup"><span data-stu-id="dff93-124">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="dff93-125">Envisagez de vous connecter à un emplacement plus permanent sur le serveur en envoyant les informations d’erreur à une API Web principale qui utilise un fournisseur de journalisation avec la gestion de la taille du journal et la rotation des journaux.</span><span class="sxs-lookup"><span data-stu-id="dff93-125">Consider logging to a more permanent location on the server by sending error information to a backend web API that uses a logging provider with log size management and log rotation.</span></span> <span data-ttu-id="dff93-126">L’application API Web principale peut également utiliser un service de gestion des performances des applications (APM), tel que [Azure application Insights (Azure Monitor) &dagger; ](/azure/azure-monitor/app/app-insights-overview), pour enregistrer les informations d’erreur qu’elle reçoit des clients.</span><span class="sxs-lookup"><span data-stu-id="dff93-126">Alternatively, the backend web API app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)&dagger;](/azure/azure-monitor/app/app-insights-overview), to record error information that it receives from clients.</span></span>

<span data-ttu-id="dff93-127">Vous devez choisir les incidents à enregistrer et le niveau de gravité des incidents journalisés.</span><span class="sxs-lookup"><span data-stu-id="dff93-127">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="dff93-128">Les utilisateurs hostiles peuvent être en mesure de déclencher délibérément des erreurs.</span><span class="sxs-lookup"><span data-stu-id="dff93-128">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="dff93-129">Par exemple, ne consignez pas un incident à partir d’une erreur où un inconnu `ProductId` est fourni dans l’URL d’un composant qui affiche les détails du produit.</span><span class="sxs-lookup"><span data-stu-id="dff93-129">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="dff93-130">Toutes les erreurs ne doivent pas être traitées comme des incidents pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-130">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="dff93-131">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="dff93-131">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&Dagger;
* <xref:web-api/index>

<span data-ttu-id="dff93-132">&dagger;Les fonctionnalités [application Insights](/azure/azure-monitor/app/app-insights-overview) natives pour la prise en charge des Blazor WebAssembly applications et de la Blazor prise en charge native de [Google Analytics](https://analytics.google.com/analytics/web/) peuvent devenir disponibles dans les versions ultérieures de ces technologies.</span><span class="sxs-lookup"><span data-stu-id="dff93-132">&dagger;Native [Application Insights](/azure/azure-monitor/app/app-insights-overview) features to support Blazor WebAssembly apps and native Blazor framework support for [Google Analytics](https://analytics.google.com/analytics/web/) might become available in future releases of these technologies.</span></span> <span data-ttu-id="dff93-133">Pour plus d’informations, consultez [prise en charge d’application Insights dans Blazor WASM client (Microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) et [Web Analytics et diagnostics (y compris des liens vers les implémentations de la Communauté) (dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461).</span><span class="sxs-lookup"><span data-stu-id="dff93-133">For more information, see [Support App Insights in Blazor WASM Client Side (microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) and [Web analytics and diagnostics (includes links to community implementations) (dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461).</span></span> <span data-ttu-id="dff93-134">En attendant, une application côté client Blazor WebAssembly peut utiliser le kit de [développement logiciel (SDK) JavaScript application Insights](/azure/azure-monitor/app/javascript) avec l' [interopérabilité de JS](xref:blazor/call-javascript-from-dotnet) pour consigner les erreurs directement sur application Insights à partir d’une application côté client.</span><span class="sxs-lookup"><span data-stu-id="dff93-134">In the meantime, a client-side Blazor WebAssembly app can use the [Application Insights JavaScript SDK](/azure/azure-monitor/app/javascript) with [JS interop](xref:blazor/call-javascript-from-dotnet) to log errors directly to Application Insights from a client-side app.</span></span>

<span data-ttu-id="dff93-135">&Dagger;S’applique aux applications ASP.NET Core côté serveur qui sont des applications principales d’API Web pour les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="dff93-135">&Dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span> <span data-ttu-id="dff93-136">Les applications côté client interceptent et envoient des informations sur les erreurs à une API Web, qui enregistre les informations d’erreur dans un fournisseur de journalisation persistant.</span><span class="sxs-lookup"><span data-stu-id="dff93-136">Client-side apps trap and send error information to a web API, which logs the error information to a persistent logging provider.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="dff93-137">Emplacements où des erreurs peuvent se produire</span><span class="sxs-lookup"><span data-stu-id="dff93-137">Places where errors may occur</span></span>

<span data-ttu-id="dff93-138">Le code d’infrastructure et d’application peut déclencher des exceptions non prises en charge dans l’un des emplacements suivants, décrits plus en détail dans les sections suivantes de cet article :</span><span class="sxs-lookup"><span data-stu-id="dff93-138">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="dff93-139">Instanciation du composant</span><span class="sxs-lookup"><span data-stu-id="dff93-139">Component instantiation</span></span>](#component-instantiation-webassembly)
* [<span data-ttu-id="dff93-140">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="dff93-140">Lifecycle methods</span></span>](#lifecycle-methods-webassembly)
* [<span data-ttu-id="dff93-141">Logique de rendu</span><span class="sxs-lookup"><span data-stu-id="dff93-141">Rendering logic</span></span>](#rendering-logic-webassembly)
* [<span data-ttu-id="dff93-142">Gestionnaires d’événements</span><span class="sxs-lookup"><span data-stu-id="dff93-142">Event handlers</span></span>](#event-handlers-webassembly)
* [<span data-ttu-id="dff93-143">Suppression de composants</span><span class="sxs-lookup"><span data-stu-id="dff93-143">Component disposal</span></span>](#component-disposal-webassembly)
* [<span data-ttu-id="dff93-144">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="dff93-144">JavaScript interop</span></span>](#javascript-interop-webassembly)

<h3 id="component-instantiation-webassembly"><span data-ttu-id="dff93-145">Instanciation du composant</span><span class="sxs-lookup"><span data-stu-id="dff93-145">Component instantiation</span></span></h3>

<span data-ttu-id="dff93-146">Lorsque Blazor crée une instance d’un composant :</span><span class="sxs-lookup"><span data-stu-id="dff93-146">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="dff93-147">Le constructeur du composant est appelé.</span><span class="sxs-lookup"><span data-stu-id="dff93-147">The component's constructor is invoked.</span></span>
* <span data-ttu-id="dff93-148">Les constructeurs de tous les services d’injection de code non Singleton fournis au constructeur du composant via la [`@inject`](xref:mvc/views/razor#inject) directive ou l' [ `[Inject]` attribut](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) sont appelés.</span><span class="sxs-lookup"><span data-stu-id="dff93-148">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="dff93-149">Une erreur dans un constructeur exécuté ou un accesseur Set pour une `[Inject]` propriété entraîne une exception non gérée et arrête l’infrastructure de l’instanciation du composant.</span><span class="sxs-lookup"><span data-stu-id="dff93-149">An error in an executed constructor or a setter for any `[Inject]` property results in an unhandled exception and stops the framework from instantiating the component.</span></span> <span data-ttu-id="dff93-150">Si la logique du constructeur peut lever des exceptions, l’application doit intercepter les exceptions à l’aide d’une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-150">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-webassembly"><span data-ttu-id="dff93-151">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="dff93-151">Lifecycle methods</span></span></h3>

<span data-ttu-id="dff93-152">Pendant la durée de vie d’un composant, Blazor appelle des [méthodes de cycle de vie](xref:blazor/components/lifecycle#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="dff93-152">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="dff93-153">Pour les composants qui gèrent les erreurs dans les méthodes de cycle de vie, ajoutez une logique de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="dff93-153">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="dff93-154">Dans l’exemple suivant, où <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> appelle une méthode pour obtenir un produit :</span><span class="sxs-lookup"><span data-stu-id="dff93-154">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="dff93-155">Une exception levée dans la `ProductRepository.GetProductByIdAsync` méthode est gérée par une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction.</span><span class="sxs-lookup"><span data-stu-id="dff93-155">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="dff93-156">Lorsque le `catch` bloc est exécuté :</span><span class="sxs-lookup"><span data-stu-id="dff93-156">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="dff93-157">`loadFailed` a la valeur `true` , qui est utilisée pour afficher un message d’erreur à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-157">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="dff93-158">L’erreur est consignée.</span><span class="sxs-lookup"><span data-stu-id="dff93-158">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-webassembly"><span data-ttu-id="dff93-159">Logique de rendu</span><span class="sxs-lookup"><span data-stu-id="dff93-159">Rendering logic</span></span></h3>

<span data-ttu-id="dff93-160">Le balisage déclaratif dans un Razor fichier de composant ( `.razor` ) est compilé dans une méthode C# appelée <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> .</span><span class="sxs-lookup"><span data-stu-id="dff93-160">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="dff93-161">Lorsqu’un composant affiche, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> exécute et génère une structure de données décrivant les éléments, le texte et les composants enfants du composant rendu.</span><span class="sxs-lookup"><span data-stu-id="dff93-161">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="dff93-162">La logique de rendu peut lever une exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-162">Rendering logic can throw an exception.</span></span> <span data-ttu-id="dff93-163">Un exemple de ce scénario se produit lorsque `@someObject.PropertyName` est évalué `@someObject` , mais est `null` .</span><span class="sxs-lookup"><span data-stu-id="dff93-163">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span>

<span data-ttu-id="dff93-164">Pour empêcher une <xref:System.NullReferenceException> dans la logique de rendu, recherchez un `null` objet avant d’accéder à ses membres.</span><span class="sxs-lookup"><span data-stu-id="dff93-164">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="dff93-165">Dans l’exemple suivant, les `person.Address` Propriétés ne sont pas accessibles si `person.Address` est `null` :</span><span class="sxs-lookup"><span data-stu-id="dff93-165">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="dff93-166">Le code précédent suppose que `person` ne l’est pas `null` .</span><span class="sxs-lookup"><span data-stu-id="dff93-166">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="dff93-167">Souvent, la structure du code garantit l’existence d’un objet au moment du rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="dff93-167">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="dff93-168">Dans ce cas, il n’est pas nécessaire de rechercher `null` dans la logique de rendu.</span><span class="sxs-lookup"><span data-stu-id="dff93-168">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="dff93-169">Dans l’exemple précédent, `person` peut être garanti qu’il existe, car `person` est créé lors de l’instanciation du composant, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="dff93-169">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-webassembly"><span data-ttu-id="dff93-170">Gestionnaires d’événements</span><span class="sxs-lookup"><span data-stu-id="dff93-170">Event handlers</span></span></h3>

<span data-ttu-id="dff93-171">Le code côté client déclenche des appels de code C# lors de la création de gestionnaires d’événements à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="dff93-171">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="dff93-172">Autres `@on...` attributs</span><span class="sxs-lookup"><span data-stu-id="dff93-172">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="dff93-173">Le code du gestionnaire d’événements peut lever une exception non gérée dans ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="dff93-173">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="dff93-174">Si l’application appelle du code qui pourrait échouer pour des raisons externes, interceptez les exceptions à l’aide d’une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction avec gestion des erreurs et journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-174">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="dff93-175">Si le code utilisateur n’intercepte pas et ne gère pas l’exception, le Framework journalise l’exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-175">If user code doesn't trap and handle the exception, the framework logs the exception.</span></span>

<h3 id="component-disposal-webassembly"><span data-ttu-id="dff93-176">Suppression de composants</span><span class="sxs-lookup"><span data-stu-id="dff93-176">Component disposal</span></span></h3>

<span data-ttu-id="dff93-177">Un composant peut être supprimé de l’interface utilisateur, par exemple, parce que l’utilisateur a accédé à une autre page.</span><span class="sxs-lookup"><span data-stu-id="dff93-177">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="dff93-178">Quand un composant qui implémente <xref:System.IDisposable?displayProperty=fullName> est supprimé de l’interface utilisateur, le Framework appelle la méthode du composant <xref:System.IDisposable.Dispose%2A> .</span><span class="sxs-lookup"><span data-stu-id="dff93-178">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="dff93-179">Si la logique de suppression peut lever des exceptions, l’application doit intercepter les exceptions à l’aide d’une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-179">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="dff93-180">Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="dff93-180">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-webassembly"><span data-ttu-id="dff93-181">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="dff93-181">JavaScript interop</span></span></h3>

<span data-ttu-id="dff93-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> permet au code .NET d’effectuer des appels asynchrones au runtime JavaScript dans le navigateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="dff93-183">Les conditions suivantes s’appliquent à la gestion des erreurs avec <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="dff93-183">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="dff93-184">Si un appel à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> échoue de façon synchrone, une exception .net se produit.</span><span class="sxs-lookup"><span data-stu-id="dff93-184">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="dff93-185">Un appel à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> peut échouer, par exemple, car les arguments fournis ne peuvent pas être sérialisés.</span><span class="sxs-lookup"><span data-stu-id="dff93-185">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="dff93-186">Le code du développeur doit intercepter l’exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-186">Developer code must catch the exception.</span></span>
* <span data-ttu-id="dff93-187">Si un appel à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> échoue de manière asynchrone, le .NET <xref:System.Threading.Tasks.Task> échoue.</span><span class="sxs-lookup"><span data-stu-id="dff93-187">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="dff93-188">Un appel à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> peut échouer, par exemple, parce que le code côté JavaScript lève une exception ou retourne un `Promise` qui s’est terminé comme `rejected` .</span><span class="sxs-lookup"><span data-stu-id="dff93-188">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="dff93-189">Le code du développeur doit intercepter l’exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-189">Developer code must catch the exception.</span></span> <span data-ttu-id="dff93-190">Si vous utilisez l' [`await`](/dotnet/csharp/language-reference/keywords/await) opérateur, encapsulez l’appel de méthode dans une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-190">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>
* <span data-ttu-id="dff93-191">Par défaut, les appels à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> doivent se terminer dans un laps de temps donné, sinon l’appel expire. Le délai d’expiration par défaut est d’une minute.</span><span class="sxs-lookup"><span data-stu-id="dff93-191">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="dff93-192">Le délai d’attente protège le code contre toute perte de connectivité réseau ou de code JavaScript qui ne renvoie jamais de message d’achèvement.</span><span class="sxs-lookup"><span data-stu-id="dff93-192">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="dff93-193">Si l’appel expire, le résultant <xref:System.Threading.Tasks> échoue avec un <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="dff93-193">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="dff93-194">Interceptez et traitez l’exception avec la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-194">Trap and process the exception with logging.</span></span>

<span data-ttu-id="dff93-195">De même, le code JavaScript peut initier des appels à des méthodes .NET indiquées par l' [ `[JSInvokable]` attribut](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="dff93-195">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="dff93-196">Si ces méthodes .NET lèvent une exception non gérée, le côté JavaScript `Promise` est rejeté.</span><span class="sxs-lookup"><span data-stu-id="dff93-196">If these .NET methods throw an unhandled exception, the JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="dff93-197">Vous avez la possibilité d’utiliser le code de gestion des erreurs côté .NET ou JavaScript de l’appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="dff93-197">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="dff93-198">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="dff93-198">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="advanced-scenarios"></a><span data-ttu-id="dff93-199">Scénarios avancés</span><span class="sxs-lookup"><span data-stu-id="dff93-199">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="dff93-200">Rendu récursif</span><span class="sxs-lookup"><span data-stu-id="dff93-200">Recursive rendering</span></span>

<span data-ttu-id="dff93-201">Les composants peuvent être imbriqués de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="dff93-201">Components can be nested recursively.</span></span> <span data-ttu-id="dff93-202">Cela est utile pour représenter des structures de données récursives.</span><span class="sxs-lookup"><span data-stu-id="dff93-202">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="dff93-203">Par exemple, un `TreeNode` composant peut restituer plus de `TreeNode` composants pour chacun des enfants du nœud.</span><span class="sxs-lookup"><span data-stu-id="dff93-203">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="dff93-204">Lors du rendu de manière récursive, évitez les modèles de codage qui se traduisent par une récurrence infinie :</span><span class="sxs-lookup"><span data-stu-id="dff93-204">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="dff93-205">Ne rendez pas de manière récursive une structure de données qui contient un cycle.</span><span class="sxs-lookup"><span data-stu-id="dff93-205">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="dff93-206">Par exemple, n’affichez pas un nœud d’arbre dont les enfants s’y trouvent.</span><span class="sxs-lookup"><span data-stu-id="dff93-206">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="dff93-207">Ne créez pas une chaîne de dispositions qui contiennent un cycle.</span><span class="sxs-lookup"><span data-stu-id="dff93-207">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="dff93-208">Par exemple, ne créez pas une disposition dont la disposition est elle-même.</span><span class="sxs-lookup"><span data-stu-id="dff93-208">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="dff93-209">N’autorisez pas un utilisateur final à enfreindre les invariants de récurrence (règles) par le biais d’une entrée de données malveillante ou d’appels Interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dff93-209">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="dff93-210">Boucles infinies pendant le rendu :</span><span class="sxs-lookup"><span data-stu-id="dff93-210">Infinite loops during rendering:</span></span>

* <span data-ttu-id="dff93-211">Fait en sorte que le processus de rendu continue de façon permanente.</span><span class="sxs-lookup"><span data-stu-id="dff93-211">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="dff93-212">Équivaut à créer une boucle non terminée.</span><span class="sxs-lookup"><span data-stu-id="dff93-212">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="dff93-213">Dans ces scénarios, le thread tente généralement d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="dff93-213">In these scenarios, the thread usually attempts to:</span></span>

* <span data-ttu-id="dff93-214">Consommez le plus de temps processeur autorisé par le système d’exploitation, indéfiniment.</span><span class="sxs-lookup"><span data-stu-id="dff93-214">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="dff93-215">Consommez une quantité illimitée de mémoire client.</span><span class="sxs-lookup"><span data-stu-id="dff93-215">Consume an unlimited amount of client memory.</span></span> <span data-ttu-id="dff93-216">La consommation de mémoire illimitée est équivalente au scénario dans lequel une boucle non terminée ajoute des entrées à une collection à chaque itération.</span><span class="sxs-lookup"><span data-stu-id="dff93-216">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="dff93-217">Pour éviter les modèles de récurrence infinis, assurez-vous que le code de rendu récursif contient des conditions d’arrêt appropriées.</span><span class="sxs-lookup"><span data-stu-id="dff93-217">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="dff93-218">Logique d’arborescence de rendu personnalisé</span><span class="sxs-lookup"><span data-stu-id="dff93-218">Custom render tree logic</span></span>

<span data-ttu-id="dff93-219">La plupart des Blazor composants sont implémentés en tant que Razor fichiers de composants ( `.razor` ) et sont compilés par l’infrastructure pour produire une logique qui opère sur un <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> pour afficher leur sortie.</span><span class="sxs-lookup"><span data-stu-id="dff93-219">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="dff93-220">Toutefois, un développeur peut implémenter la <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logique manuellement à l’aide du code C# procédural.</span><span class="sxs-lookup"><span data-stu-id="dff93-220">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="dff93-221">Pour plus d’informations, consultez <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="dff93-221">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="dff93-222">L’utilisation de la logique du générateur d’arborescence de rendu manuel est considérée comme un scénario avancé et risqué, non recommandé pour le développement de composants généraux.</span><span class="sxs-lookup"><span data-stu-id="dff93-222">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="dff93-223">Si le <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code est écrit, le développeur doit garantir l’exactitude du code.</span><span class="sxs-lookup"><span data-stu-id="dff93-223">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="dff93-224">Par exemple, le développeur doit s’assurer que :</span><span class="sxs-lookup"><span data-stu-id="dff93-224">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="dff93-225">Les appels à <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> et <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> sont correctement équilibrés.</span><span class="sxs-lookup"><span data-stu-id="dff93-225">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="dff93-226">Les attributs sont ajoutés uniquement aux emplacements appropriés.</span><span class="sxs-lookup"><span data-stu-id="dff93-226">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="dff93-227">Une logique de générateur d’arborescence de rendu manuel incorrecte peut entraîner un comportement arbitraire indéfini, y compris des incidents, des blocages d’application et des failles de sécurité.</span><span class="sxs-lookup"><span data-stu-id="dff93-227">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, app hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="dff93-228">Considérez la logique du générateur d’arborescence de rendu manuel sur le même niveau de complexité et avec le même niveau de *danger* que l’écriture de code assembleur ou d’instructions [MSIL (Microsoft Intermediate Language)](/dotnet/standard/managed-code) manuellement.</span><span class="sxs-lookup"><span data-stu-id="dff93-228">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dff93-229">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="dff93-229">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;
* <xref:web-api/index>

<span data-ttu-id="dff93-230">&dagger;S’applique au serveur principal ASP.NET Core applications API Web que Blazor WebAssembly les applications côté client utilisent pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-230">&dagger;Applies to backend ASP.NET Core web API apps that client-side Blazor WebAssembly apps use for logging.</span></span>

::: zone-end

::: zone pivot="server"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="dff93-231">Erreurs détaillées pendant le développement</span><span class="sxs-lookup"><span data-stu-id="dff93-231">Detailed errors during development</span></span>

<span data-ttu-id="dff93-232">Quand une Blazor application ne fonctionne pas correctement pendant le développement, la réception d’informations d’erreur détaillées de l’application vous aide à résoudre les problèmes et à résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="dff93-232">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="dff93-233">Lorsqu’une erreur se produit, Blazor les applications affichent une barre jaune clair au bas de l’écran :</span><span class="sxs-lookup"><span data-stu-id="dff93-233">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="dff93-234">Pendant le développement, la barre vous dirige vers la console du navigateur, où vous pouvez voir l’exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-234">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="dff93-235">En production, la barre avertit l’utilisateur qu’une erreur s’est produite et recommande l’actualisation du navigateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-235">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="dff93-236">L’interface utilisateur de cette expérience de gestion des erreurs fait partie des [ Blazor modèles de projet](xref:blazor/project-structure).</span><span class="sxs-lookup"><span data-stu-id="dff93-236">The UI for this error handling experience is part of the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="dff93-237">Dans une Blazor Server application, personnalisez l’expérience dans le `Pages/_Host.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="dff93-237">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="dff93-238">L' `blazor-error-ui` élément est normalement masqué en raison de la présence du `display: none` style de la `blazor-error-ui` classe CSS dans la feuille de style du site ( `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="dff93-238">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the site's stylesheet (`wwwroot/css/site.css`).</span></span> <span data-ttu-id="dff93-239">Lorsqu’une erreur se produit, l’infrastructure s’applique `display: block` à l’élément.</span><span class="sxs-lookup"><span data-stu-id="dff93-239">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="dff93-240">Blazor Server Erreurs de circuit détaillées</span><span class="sxs-lookup"><span data-stu-id="dff93-240">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="dff93-241">Les erreurs côté client n’incluent pas la pile des appels et ne fournissent pas de détails sur la cause de l’erreur, mais les journaux du serveur contiennent ces informations.</span><span class="sxs-lookup"><span data-stu-id="dff93-241">Client-side errors don't include the call stack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="dff93-242">À des fins de développement, des informations sur les erreurs de circuit sensible peuvent être mises à la disposition du client en activant des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="dff93-242">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="dff93-243">Définissez <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="dff93-243">Set <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> to `true`.</span></span> <span data-ttu-id="dff93-244">Pour plus d'informations et pour obtenir un exemple, consultez <xref:blazor/fundamentals/signalr#circuit-handler-options>.</span><span class="sxs-lookup"><span data-stu-id="dff93-244">For more information and an example, see <xref:blazor/fundamentals/signalr#circuit-handler-options>.</span></span>

<span data-ttu-id="dff93-245">Une alternative à la définition de <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> est de définir la `DetailedErrors` clé de configuration sur `true` dans le fichier de paramètres de l’environnement de développement de l’application ( `appsettings.Development.json` ).</span><span class="sxs-lookup"><span data-stu-id="dff93-245">An alternative to setting <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> is to set the `DetailedErrors` configuration key to `true` in the app's Development environment settings file (`appsettings.Development.json`).</span></span>  <span data-ttu-id="dff93-246">En outre, définissez la [ SignalR journalisation côté serveur](xref:signalr/diagnostics#server-side-logging) ( `Microsoft.AspNetCore.SignalR` ) à [Déboguer](xref:Microsoft.Extensions.Logging.LogLevel) ou à [suivre](xref:Microsoft.Extensions.Logging.LogLevel) pour la SignalR journalisation détaillée.</span><span class="sxs-lookup"><span data-stu-id="dff93-246">Additionally, set [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="dff93-247">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="dff93-247">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

<span data-ttu-id="dff93-248">La <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> clé de configuration peut également être définie pour `true` utiliser la `ASPNETCORE_DETAILEDERRORS` variable d’environnement avec la valeur sur les serveurs d’environnement de `true` développement/intermédiaire ou sur votre système local.</span><span class="sxs-lookup"><span data-stu-id="dff93-248">The <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> configuration key can also be set to `true` using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true` on Development/Staging environment servers or on your local system.</span></span>

> [!WARNING]
> <span data-ttu-id="dff93-249">Évitez toujours d’exposer les informations d’erreur aux clients sur Internet, ce qui constitue un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="dff93-249">Always avoid exposing error information to clients on the Internet, which is a security risk.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="dff93-250">Comment une Blazor Server application réagit aux exceptions non gérées</span><span class="sxs-lookup"><span data-stu-id="dff93-250">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="dff93-251">Blazor Server est une infrastructure avec état.</span><span class="sxs-lookup"><span data-stu-id="dff93-251">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="dff93-252">Tandis que les utilisateurs interagissent avec une application, ils maintiennent une connexion au serveur appelé « *circuit*».</span><span class="sxs-lookup"><span data-stu-id="dff93-252">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="dff93-253">Le circuit contient des instances de composant actives, ainsi que de nombreux autres aspects de l’État, tels que :</span><span class="sxs-lookup"><span data-stu-id="dff93-253">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="dff93-254">Sortie du rendu le plus récent des composants.</span><span class="sxs-lookup"><span data-stu-id="dff93-254">The most recent rendered output of components.</span></span>
* <span data-ttu-id="dff93-255">Ensemble actuel de délégués de gestion d’événements qui peuvent être déclenchés par les événements côté client.</span><span class="sxs-lookup"><span data-stu-id="dff93-255">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="dff93-256">Si un utilisateur ouvre l’application dans plusieurs onglets de navigateur, il crée plusieurs circuits indépendants.</span><span class="sxs-lookup"><span data-stu-id="dff93-256">If a user opens the app in multiple browser tabs, the user creates multiple independent circuits.</span></span>

<span data-ttu-id="dff93-257">Blazor traite la plupart des exceptions non gérées comme étant irrécupérables par le circuit dans lequel elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="dff93-257">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="dff93-258">Si un circuit est arrêté en raison d’une exception non gérée, l’utilisateur ne peut continuer à interagir avec l’application qu’en rechargeant la page pour créer un nouveau circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-258">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="dff93-259">Les circuits en dehors de celui qui est terminé, qui sont des circuits pour d’autres utilisateurs ou d’autres onglets de navigateur, ne sont pas affectés.</span><span class="sxs-lookup"><span data-stu-id="dff93-259">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="dff93-260">Ce scénario est similaire à une application de bureau qui se bloque.</span><span class="sxs-lookup"><span data-stu-id="dff93-260">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="dff93-261">L’application bloquée doit être redémarrée, mais les autres applications ne sont pas affectées.</span><span class="sxs-lookup"><span data-stu-id="dff93-261">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="dff93-262">Le Framework met fin à un circuit lorsqu’une exception non gérée se produit pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="dff93-262">The framework terminates a circuit when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="dff93-263">Une exception non gérée rend souvent le circuit dans un État indéfini.</span><span class="sxs-lookup"><span data-stu-id="dff93-263">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="dff93-264">L’opération normale de l’application ne peut pas être garantie après une exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="dff93-264">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="dff93-265">Des failles de sécurité peuvent apparaître dans l’application si le circuit continue dans un État indéfini.</span><span class="sxs-lookup"><span data-stu-id="dff93-265">Security vulnerabilities may appear in the app if the circuit continues in an undefined state.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="dff93-266">Gérer les exceptions non gérées dans le code du développeur</span><span class="sxs-lookup"><span data-stu-id="dff93-266">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="dff93-267">Pour qu’une application continue après une erreur, l’application doit avoir une logique de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="dff93-267">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="dff93-268">Les sections suivantes de cet article décrivent les sources potentielles d’exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="dff93-268">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="dff93-269">En production, ne rendez pas les messages d’exception d’infrastructure ou les traces de pile dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-269">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="dff93-270">Le rendu des messages d’exception ou des traces de pile peut :</span><span class="sxs-lookup"><span data-stu-id="dff93-270">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="dff93-271">Divulguer des informations sensibles aux utilisateurs finaux.</span><span class="sxs-lookup"><span data-stu-id="dff93-271">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="dff93-272">Aidez un utilisateur malveillant à découvrir des faiblesses dans une application qui peuvent compromettre la sécurité de l’application, du serveur ou du réseau.</span><span class="sxs-lookup"><span data-stu-id="dff93-272">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="dff93-273">Gestion globale des exceptions</span><span class="sxs-lookup"><span data-stu-id="dff93-273">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

<span data-ttu-id="dff93-274">Étant donné que les approches de cette section gèrent les erreurs avec une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction, la SignalR connexion entre le client et le serveur n’est pas interrompue lorsqu’une erreur se produit et que le circuit reste actif.</span><span class="sxs-lookup"><span data-stu-id="dff93-274">Because the approaches in this section handle errors with a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, the SignalR connection between the client and server isn't broken when an error occurs and the circuit remains alive.</span></span> <span data-ttu-id="dff93-275">Toute exception non gérée est irrémédiable pour un circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-275">Any unhandled exception is fatal to a circuit.</span></span> <span data-ttu-id="dff93-276">Pour plus d’informations, consultez la section précédente sur [la manière dont une Blazor Server application réagit aux exceptions non gérées](#how-a-blazor-server-app-reacts-to-unhandled-exceptions).</span><span class="sxs-lookup"><span data-stu-id="dff93-276">For more information, see the preceding section on [how a Blazor Server app reacts to unhandled exceptions](#how-a-blazor-server-app-reacts-to-unhandled-exceptions).</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="dff93-277">Consigner les erreurs avec un fournisseur persistant</span><span class="sxs-lookup"><span data-stu-id="dff93-277">Log errors with a persistent provider</span></span>

<span data-ttu-id="dff93-278">Si une exception non gérée se produit, l’exception est consignée dans <xref:Microsoft.Extensions.Logging.ILogger> les instances configurées dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="dff93-278">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="dff93-279">Par défaut, Blazor les applications se connectent à la sortie de la console avec le fournisseur d’informations de journalisation de la console.</span><span class="sxs-lookup"><span data-stu-id="dff93-279">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="dff93-280">Envisagez de vous connecter à un emplacement plus permanent sur le serveur avec un fournisseur qui gère la taille du journal et la rotation des journaux.</span><span class="sxs-lookup"><span data-stu-id="dff93-280">Consider logging to a more permanent location on the server with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="dff93-281">L’application peut également utiliser un service de gestion des performances des applications (APM), tel que [Azure application Insights (Azure Monitor)](/azure/azure-monitor/app/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="dff93-281">Alternatively, the app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)](/azure/azure-monitor/app/app-insights-overview).</span></span>

<span data-ttu-id="dff93-282">Pendant le développement, une Blazor Server application envoie généralement les détails complets des exceptions à la console du navigateur pour faciliter le débogage.</span><span class="sxs-lookup"><span data-stu-id="dff93-282">During development, a Blazor Server app usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="dff93-283">En production, les erreurs détaillées ne sont pas envoyées aux clients, mais les détails complets d’une exception sont enregistrés sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="dff93-283">In production, detailed errors aren't sent to clients, but an exception's full details are logged on the server.</span></span>

<span data-ttu-id="dff93-284">Vous devez choisir les incidents à enregistrer et le niveau de gravité des incidents journalisés.</span><span class="sxs-lookup"><span data-stu-id="dff93-284">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="dff93-285">Les utilisateurs hostiles peuvent être en mesure de déclencher délibérément des erreurs.</span><span class="sxs-lookup"><span data-stu-id="dff93-285">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="dff93-286">Par exemple, ne consignez pas un incident à partir d’une erreur où un inconnu `ProductId` est fourni dans l’URL d’un composant qui affiche les détails du produit.</span><span class="sxs-lookup"><span data-stu-id="dff93-286">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="dff93-287">Toutes les erreurs ne doivent pas être traitées comme des incidents pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-287">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="dff93-288">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="dff93-288">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="dff93-289">&dagger;S’applique aux applications ASP.NET Core côté serveur qui sont des applications principales d’API Web pour les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="dff93-289">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="dff93-290">Emplacements où des erreurs peuvent se produire</span><span class="sxs-lookup"><span data-stu-id="dff93-290">Places where errors may occur</span></span>

<span data-ttu-id="dff93-291">Le code d’infrastructure et d’application peut déclencher des exceptions non prises en charge dans l’un des emplacements suivants, décrits plus en détail dans les sections suivantes de cet article :</span><span class="sxs-lookup"><span data-stu-id="dff93-291">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="dff93-292">Instanciation du composant</span><span class="sxs-lookup"><span data-stu-id="dff93-292">Component instantiation</span></span>](#component-instantiation-server)
* [<span data-ttu-id="dff93-293">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="dff93-293">Lifecycle methods</span></span>](#lifecycle-methods-server)
* [<span data-ttu-id="dff93-294">Logique de rendu</span><span class="sxs-lookup"><span data-stu-id="dff93-294">Rendering logic</span></span>](#rendering-logic-server)
* [<span data-ttu-id="dff93-295">Gestionnaires d’événements</span><span class="sxs-lookup"><span data-stu-id="dff93-295">Event handlers</span></span>](#event-handlers-server)
* [<span data-ttu-id="dff93-296">Suppression de composants</span><span class="sxs-lookup"><span data-stu-id="dff93-296">Component disposal</span></span>](#component-disposal-server)
* [<span data-ttu-id="dff93-297">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="dff93-297">JavaScript interop</span></span>](#javascript-interop-server)
* [<span data-ttu-id="dff93-298">Blazor Server rerendu</span><span class="sxs-lookup"><span data-stu-id="dff93-298">Blazor Server rerendering</span></span>](#blazor-server-prerendering-server)

<h3 id="component-instantiation-server"><span data-ttu-id="dff93-299">Instanciation du composant</span><span class="sxs-lookup"><span data-stu-id="dff93-299">Component instantiation</span></span></h3>

<span data-ttu-id="dff93-300">Lorsque Blazor crée une instance d’un composant :</span><span class="sxs-lookup"><span data-stu-id="dff93-300">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="dff93-301">Le constructeur du composant est appelé.</span><span class="sxs-lookup"><span data-stu-id="dff93-301">The component's constructor is invoked.</span></span>
* <span data-ttu-id="dff93-302">Les constructeurs de tous les services d’injection de code non Singleton fournis au constructeur du composant via la [`@inject`](xref:mvc/views/razor#inject) directive ou l' [ `[Inject]` attribut](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) sont appelés.</span><span class="sxs-lookup"><span data-stu-id="dff93-302">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="dff93-303">Un Blazor Server circuit échoue quand un constructeur exécuté ou une méthode setter pour une `[Inject]` propriété lève une exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="dff93-303">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="dff93-304">L’exception est irrécupérable, car l’infrastructure ne peut pas instancier le composant.</span><span class="sxs-lookup"><span data-stu-id="dff93-304">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="dff93-305">Si la logique du constructeur peut lever des exceptions, l’application doit intercepter les exceptions à l’aide d’une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-305">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-server"><span data-ttu-id="dff93-306">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="dff93-306">Lifecycle methods</span></span></h3>

<span data-ttu-id="dff93-307">Pendant la durée de vie d’un composant, Blazor appelle des [méthodes de cycle de vie](xref:blazor/components/lifecycle#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="dff93-307">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="dff93-308">Si une méthode de cycle de vie lève une exception, de manière synchrone ou asynchrone, l’exception est irrécupérable pour un Blazor Server circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-308">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="dff93-309">Pour les composants qui gèrent les erreurs dans les méthodes de cycle de vie, ajoutez une logique de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="dff93-309">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="dff93-310">Dans l’exemple suivant, où <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> appelle une méthode pour obtenir un produit :</span><span class="sxs-lookup"><span data-stu-id="dff93-310">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="dff93-311">Une exception levée dans la `ProductRepository.GetProductByIdAsync` méthode est gérée par une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction.</span><span class="sxs-lookup"><span data-stu-id="dff93-311">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="dff93-312">Lorsque le `catch` bloc est exécuté :</span><span class="sxs-lookup"><span data-stu-id="dff93-312">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="dff93-313">`loadFailed` a la valeur `true` , qui est utilisée pour afficher un message d’erreur à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-313">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="dff93-314">L’erreur est consignée.</span><span class="sxs-lookup"><span data-stu-id="dff93-314">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-server"><span data-ttu-id="dff93-315">Logique de rendu</span><span class="sxs-lookup"><span data-stu-id="dff93-315">Rendering logic</span></span></h3>

<span data-ttu-id="dff93-316">Le balisage déclaratif dans un Razor fichier de composant ( `.razor` ) est compilé dans une méthode C# appelée <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> .</span><span class="sxs-lookup"><span data-stu-id="dff93-316">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="dff93-317">Lorsqu’un composant affiche, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> exécute et génère une structure de données décrivant les éléments, le texte et les composants enfants du composant rendu.</span><span class="sxs-lookup"><span data-stu-id="dff93-317">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="dff93-318">La logique de rendu peut lever une exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-318">Rendering logic can throw an exception.</span></span> <span data-ttu-id="dff93-319">Un exemple de ce scénario se produit lorsque `@someObject.PropertyName` est évalué `@someObject` , mais est `null` .</span><span class="sxs-lookup"><span data-stu-id="dff93-319">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="dff93-320">Une exception non gérée levée par la logique de rendu est irrécupérable pour un Blazor Server circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-320">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="dff93-321">Pour empêcher une <xref:System.NullReferenceException> dans la logique de rendu, recherchez un `null` objet avant d’accéder à ses membres.</span><span class="sxs-lookup"><span data-stu-id="dff93-321">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="dff93-322">Dans l’exemple suivant, les `person.Address` Propriétés ne sont pas accessibles si `person.Address` est `null` :</span><span class="sxs-lookup"><span data-stu-id="dff93-322">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="dff93-323">Le code précédent suppose que `person` ne l’est pas `null` .</span><span class="sxs-lookup"><span data-stu-id="dff93-323">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="dff93-324">Souvent, la structure du code garantit l’existence d’un objet au moment du rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="dff93-324">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="dff93-325">Dans ce cas, il n’est pas nécessaire de rechercher `null` dans la logique de rendu.</span><span class="sxs-lookup"><span data-stu-id="dff93-325">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="dff93-326">Dans l’exemple précédent, `person` peut être garanti qu’il existe, car `person` est créé lors de l’instanciation du composant, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="dff93-326">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-server"><span data-ttu-id="dff93-327">Gestionnaires d’événements</span><span class="sxs-lookup"><span data-stu-id="dff93-327">Event handlers</span></span></h3>

<span data-ttu-id="dff93-328">Le code côté client déclenche des appels de code C# lors de la création de gestionnaires d’événements à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="dff93-328">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="dff93-329">Autres `@on...` attributs</span><span class="sxs-lookup"><span data-stu-id="dff93-329">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="dff93-330">Le code du gestionnaire d’événements peut lever une exception non gérée dans ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="dff93-330">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="dff93-331">Si un gestionnaire d’événements lève une exception non gérée (par exemple, une requête de base de données échoue), l’exception est irrécupérable pour un Blazor Server circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-331">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="dff93-332">Si l’application appelle du code qui pourrait échouer pour des raisons externes, interceptez les exceptions à l’aide d’une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction avec gestion des erreurs et journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-332">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="dff93-333">Si le code utilisateur n’intercepte pas et ne gère pas l’exception, le Framework journalise l’exception et met fin au circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-333">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

<h3 id="component-disposal-server"><span data-ttu-id="dff93-334">Suppression de composants</span><span class="sxs-lookup"><span data-stu-id="dff93-334">Component disposal</span></span></h3>

<span data-ttu-id="dff93-335">Un composant peut être supprimé de l’interface utilisateur, par exemple, parce que l’utilisateur a accédé à une autre page.</span><span class="sxs-lookup"><span data-stu-id="dff93-335">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="dff93-336">Quand un composant qui implémente <xref:System.IDisposable?displayProperty=fullName> est supprimé de l’interface utilisateur, le Framework appelle la méthode du composant <xref:System.IDisposable.Dispose%2A> .</span><span class="sxs-lookup"><span data-stu-id="dff93-336">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="dff93-337">Si la méthode du composant `Dispose` lève une exception non gérée, l’exception est irrécupérable pour un Blazor Server circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-337">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="dff93-338">Si la logique de suppression peut lever des exceptions, l’application doit intercepter les exceptions à l’aide d’une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-338">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="dff93-339">Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="dff93-339">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-server"><span data-ttu-id="dff93-340">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="dff93-340">JavaScript interop</span></span></h3>

<span data-ttu-id="dff93-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> permet au code .NET d’effectuer des appels asynchrones au runtime JavaScript dans le navigateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="dff93-342">Les conditions suivantes s’appliquent à la gestion des erreurs avec <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="dff93-342">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="dff93-343">Si un appel à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> échoue de façon synchrone, une exception .net se produit.</span><span class="sxs-lookup"><span data-stu-id="dff93-343">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="dff93-344">Un appel à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> peut échouer, par exemple, car les arguments fournis ne peuvent pas être sérialisés.</span><span class="sxs-lookup"><span data-stu-id="dff93-344">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="dff93-345">Le code du développeur doit intercepter l’exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-345">Developer code must catch the exception.</span></span> <span data-ttu-id="dff93-346">Si le code d’application dans une méthode de gestionnaire d’événements ou de cycle de vie de composant ne gère pas une exception, l’exception résultante est irrécupérable pour un Blazor Server circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-346">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="dff93-347">Si un appel à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> échoue de manière asynchrone, le .NET <xref:System.Threading.Tasks.Task> échoue.</span><span class="sxs-lookup"><span data-stu-id="dff93-347">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="dff93-348">Un appel à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> peut échouer, par exemple, parce que le code côté JavaScript lève une exception ou retourne un `Promise` qui s’est terminé comme `rejected` .</span><span class="sxs-lookup"><span data-stu-id="dff93-348">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="dff93-349">Le code du développeur doit intercepter l’exception.</span><span class="sxs-lookup"><span data-stu-id="dff93-349">Developer code must catch the exception.</span></span> <span data-ttu-id="dff93-350">Si vous utilisez l' [`await`](/dotnet/csharp/language-reference/keywords/await) opérateur, encapsulez l’appel de méthode dans une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-350">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="dff93-351">Dans le cas contraire, le code défaillant entraîne une exception non gérée qui est irrécupérable pour un Blazor Server circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-351">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="dff93-352">Par défaut, les appels à <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> doivent se terminer dans un laps de temps donné, sinon l’appel expire. Le délai d’expiration par défaut est d’une minute.</span><span class="sxs-lookup"><span data-stu-id="dff93-352">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="dff93-353">Le délai d’attente protège le code contre toute perte de connectivité réseau ou de code JavaScript qui ne renvoie jamais de message d’achèvement.</span><span class="sxs-lookup"><span data-stu-id="dff93-353">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="dff93-354">Si l’appel expire, le résultant <xref:System.Threading.Tasks> échoue avec un <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="dff93-354">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="dff93-355">Interceptez et traitez l’exception avec la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-355">Trap and process the exception with logging.</span></span>

<span data-ttu-id="dff93-356">De même, le code JavaScript peut initier des appels à des méthodes .NET indiquées par l' [ `[JSInvokable]` attribut](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="dff93-356">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="dff93-357">Si ces méthodes .NET lèvent une exception non gérée :</span><span class="sxs-lookup"><span data-stu-id="dff93-357">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="dff93-358">L’exception n’est pas traitée comme étant irrécupérable pour un Blazor Server circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-358">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="dff93-359">Le côté JavaScript `Promise` est rejeté.</span><span class="sxs-lookup"><span data-stu-id="dff93-359">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="dff93-360">Vous avez la possibilité d’utiliser le code de gestion des erreurs côté .NET ou JavaScript de l’appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="dff93-360">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="dff93-361">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="dff93-361">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

<h3 id="blazor-server-prerendering-server"><span data-ttu-id="dff93-362">Blazor Server préaffichant</span><span class="sxs-lookup"><span data-stu-id="dff93-362">Blazor Server prerendering</span></span></h3>

<span data-ttu-id="dff93-363">Blazor les composants peuvent être prérendus à l’aide du [tag Helper du composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , afin que le balisage HTML rendu soit renvoyé dans le cadre de la requête http initiale de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dff93-363">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="dff93-364">Cela fonctionne de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="dff93-364">This works by:</span></span>

* <span data-ttu-id="dff93-365">Création d’un nouveau circuit pour tous les composants prérendus qui font partie de la même page.</span><span class="sxs-lookup"><span data-stu-id="dff93-365">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="dff93-366">Génération du code HTML initial.</span><span class="sxs-lookup"><span data-stu-id="dff93-366">Generating the initial HTML.</span></span>
* <span data-ttu-id="dff93-367">Traitement du circuit comme `disconnected` jusqu’à ce que le navigateur de l’utilisateur établisse une SignalR connexion avec le même serveur.</span><span class="sxs-lookup"><span data-stu-id="dff93-367">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="dff93-368">Lorsque la connexion est établie, l’interactivité sur le circuit est reprise et le balisage HTML des composants est mis à jour.</span><span class="sxs-lookup"><span data-stu-id="dff93-368">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="dff93-369">Si un composant lève une exception non gérée pendant le prérendu, par exemple, pendant une méthode de cycle de vie ou dans une logique de rendu :</span><span class="sxs-lookup"><span data-stu-id="dff93-369">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="dff93-370">L’exception est irrécupérable pour le circuit.</span><span class="sxs-lookup"><span data-stu-id="dff93-370">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="dff93-371">L’exception est levée dans la pile des appels à partir du <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> tag Helper.</span><span class="sxs-lookup"><span data-stu-id="dff93-371">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="dff93-372">Par conséquent, la requête HTTP entière échoue, sauf si l’exception est explicitement interceptée par le code du développeur.</span><span class="sxs-lookup"><span data-stu-id="dff93-372">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="dff93-373">Dans des circonstances normales, lorsque le prérendu échoue, la création et le rendu du composant n’ont pas de sens, car un composant de travail ne peut pas être rendu.</span><span class="sxs-lookup"><span data-stu-id="dff93-373">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="dff93-374">Pour tolérer les erreurs qui peuvent se produire pendant le prérendu, la logique de gestion des erreurs doit être placée à l’intérieur d’un composant qui peut lever des exceptions.</span><span class="sxs-lookup"><span data-stu-id="dff93-374">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="dff93-375">Utilisez des [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instructions avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="dff93-375">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="dff93-376">Au lieu d’encapsuler le <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> tag Helper dans une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction, placez la logique de gestion des erreurs dans le composant rendu par le <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> tag Helper.</span><span class="sxs-lookup"><span data-stu-id="dff93-376">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="dff93-377">Scénarios avancés</span><span class="sxs-lookup"><span data-stu-id="dff93-377">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="dff93-378">Rendu récursif</span><span class="sxs-lookup"><span data-stu-id="dff93-378">Recursive rendering</span></span>

<span data-ttu-id="dff93-379">Les composants peuvent être imbriqués de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="dff93-379">Components can be nested recursively.</span></span> <span data-ttu-id="dff93-380">Cela est utile pour représenter des structures de données récursives.</span><span class="sxs-lookup"><span data-stu-id="dff93-380">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="dff93-381">Par exemple, un `TreeNode` composant peut restituer plus de `TreeNode` composants pour chacun des enfants du nœud.</span><span class="sxs-lookup"><span data-stu-id="dff93-381">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="dff93-382">Lors du rendu de manière récursive, évitez les modèles de codage qui se traduisent par une récurrence infinie :</span><span class="sxs-lookup"><span data-stu-id="dff93-382">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="dff93-383">Ne rendez pas de manière récursive une structure de données qui contient un cycle.</span><span class="sxs-lookup"><span data-stu-id="dff93-383">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="dff93-384">Par exemple, n’affichez pas un nœud d’arbre dont les enfants s’y trouvent.</span><span class="sxs-lookup"><span data-stu-id="dff93-384">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="dff93-385">Ne créez pas une chaîne de dispositions qui contiennent un cycle.</span><span class="sxs-lookup"><span data-stu-id="dff93-385">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="dff93-386">Par exemple, ne créez pas une disposition dont la disposition est elle-même.</span><span class="sxs-lookup"><span data-stu-id="dff93-386">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="dff93-387">N’autorisez pas un utilisateur final à enfreindre les invariants de récurrence (règles) par le biais d’une entrée de données malveillante ou d’appels Interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dff93-387">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="dff93-388">Boucles infinies pendant le rendu :</span><span class="sxs-lookup"><span data-stu-id="dff93-388">Infinite loops during rendering:</span></span>

* <span data-ttu-id="dff93-389">Fait en sorte que le processus de rendu continue de façon permanente.</span><span class="sxs-lookup"><span data-stu-id="dff93-389">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="dff93-390">Équivaut à créer une boucle non terminée.</span><span class="sxs-lookup"><span data-stu-id="dff93-390">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="dff93-391">Dans ces scénarios, un Blazor Server circuit affecté échoue et le thread tente généralement d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="dff93-391">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="dff93-392">Consommez le plus de temps processeur autorisé par le système d’exploitation, indéfiniment.</span><span class="sxs-lookup"><span data-stu-id="dff93-392">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="dff93-393">Consommez une quantité illimitée de mémoire serveur.</span><span class="sxs-lookup"><span data-stu-id="dff93-393">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="dff93-394">La consommation de mémoire illimitée est équivalente au scénario dans lequel une boucle non terminée ajoute des entrées à une collection à chaque itération.</span><span class="sxs-lookup"><span data-stu-id="dff93-394">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="dff93-395">Pour éviter les modèles de récurrence infinis, assurez-vous que le code de rendu récursif contient des conditions d’arrêt appropriées.</span><span class="sxs-lookup"><span data-stu-id="dff93-395">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="dff93-396">Logique d’arborescence de rendu personnalisé</span><span class="sxs-lookup"><span data-stu-id="dff93-396">Custom render tree logic</span></span>

<span data-ttu-id="dff93-397">La plupart des Blazor composants sont implémentés en tant que Razor fichiers de composants ( `.razor` ) et sont compilés par l’infrastructure pour produire une logique qui opère sur un <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> pour afficher leur sortie.</span><span class="sxs-lookup"><span data-stu-id="dff93-397">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="dff93-398">Toutefois, un développeur peut implémenter la <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logique manuellement à l’aide du code C# procédural.</span><span class="sxs-lookup"><span data-stu-id="dff93-398">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="dff93-399">Pour plus d’informations, consultez <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="dff93-399">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="dff93-400">L’utilisation de la logique du générateur d’arborescence de rendu manuel est considérée comme un scénario avancé et risqué, non recommandé pour le développement de composants généraux.</span><span class="sxs-lookup"><span data-stu-id="dff93-400">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="dff93-401">Si le <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code est écrit, le développeur doit garantir l’exactitude du code.</span><span class="sxs-lookup"><span data-stu-id="dff93-401">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="dff93-402">Par exemple, le développeur doit s’assurer que :</span><span class="sxs-lookup"><span data-stu-id="dff93-402">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="dff93-403">Les appels à <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> et <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> sont correctement équilibrés.</span><span class="sxs-lookup"><span data-stu-id="dff93-403">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="dff93-404">Les attributs sont ajoutés uniquement aux emplacements appropriés.</span><span class="sxs-lookup"><span data-stu-id="dff93-404">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="dff93-405">Une logique incorrecte du générateur d’arborescence de rendu manuel peut entraîner un comportement arbitraire non défini, y compris des pannes, des blocages du serveur et des failles de sécurité.</span><span class="sxs-lookup"><span data-stu-id="dff93-405">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="dff93-406">Considérez la logique du générateur d’arborescence de rendu manuel sur le même niveau de complexité et avec le même niveau de *danger* que l’écriture de code assembleur ou d’instructions [MSIL (Microsoft Intermediate Language)](/dotnet/standard/managed-code) manuellement.</span><span class="sxs-lookup"><span data-stu-id="dff93-406">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dff93-407">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="dff93-407">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="dff93-408">&dagger;S’applique aux applications ASP.NET Core côté serveur qui sont des applications principales d’API Web pour les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="dff93-408">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

::: zone-end
