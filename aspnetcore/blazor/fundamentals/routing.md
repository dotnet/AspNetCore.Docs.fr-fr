---
title: Routage de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment gérer le routage des demandes dans les applications et comment utiliser le composant NavLink dans les Blazor applications pour la navigation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: ee6de9a13a69154eef6b677663091667d391452f
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395056"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="ce045-103">Routage de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ce045-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="ce045-104">Dans cet article, vous allez apprendre à gérer le routage des demandes et à utiliser le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant pour créer des liens de navigation dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="ce045-104">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="ce045-105">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="ce045-105">Route templates</span></span>

<span data-ttu-id="ce045-106">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet le routage vers Razor des composants dans une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="ce045-106">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="ce045-107">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant est utilisé dans le `App` composant des Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="ce045-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="ce045-108">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-108">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

<span data-ttu-id="ce045-109">Quand un Razor composant ( `.razor` ) avec une [ `@page` directive](xref:mvc/views/razor#page) est compilé, la classe de composant générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage du composant.</span><span class="sxs-lookup"><span data-stu-id="ce045-109">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="ce045-110">Lorsque l’application démarre, l’assembly spécifié en tant que routeur `AppAssembly` est analysé pour collecter des informations de routage pour les composants de l’application qui ont un <xref:Microsoft.AspNetCore.Components.RouteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="ce045-110">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="ce045-111">Au moment de l’exécution, le <xref:Microsoft.AspNetCore.Components.RouteView> composant :</span><span class="sxs-lookup"><span data-stu-id="ce045-111">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="ce045-112">Reçoit du avec <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> tous les paramètres d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ce045-112">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="ce045-113">Restitue le composant spécifié avec sa [disposition](xref:blazor/layouts), y compris les dispositions imbriquées supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="ce045-113">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="ce045-114">Vous pouvez également spécifier un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> paramètre avec une classe de disposition pour les composants qui ne spécifient pas de disposition avec la [ `@layout` directive](xref:blazor/layouts#apply-a-layout-to-a-component).</span><span class="sxs-lookup"><span data-stu-id="ce045-114">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#apply-a-layout-to-a-component).</span></span> <span data-ttu-id="ce045-115">Les modèles de [ Blazor projet](xref:blazor/project-structure) de l’infrastructure spécifient le `MainLayout` composant ( `Shared/MainLayout.razor` ) comme disposition par défaut de l’application.</span><span class="sxs-lookup"><span data-stu-id="ce045-115">The framework's [Blazor project templates](xref:blazor/project-structure) specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="ce045-116">Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="ce045-116">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="ce045-117">Les composants prennent en charge plusieurs modèles de routage à l’aide de plusieurs [ `@page` directives](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="ce045-117">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="ce045-118">L’exemple de composant suivant se charge sur les demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` .</span><span class="sxs-lookup"><span data-stu-id="ce045-118">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="ce045-119">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-119">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="ce045-120">Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son `wwwroot/index.html` fichier ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` fichier ( Blazor Server ) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut.</span><span class="sxs-lookup"><span data-stu-id="ce045-120">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="ce045-121">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="ce045-121">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="ce045-122">Fournir du contenu personnalisé lorsque le contenu est introuvable</span><span class="sxs-lookup"><span data-stu-id="ce045-122">Provide custom content when content isn't found</span></span>

<span data-ttu-id="ce045-123">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="ce045-123">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="ce045-124">Dans le `App` composant, définissez le contenu personnalisé dans le <xref:Microsoft.AspNetCore.Components.Routing.Router> modèle du composant <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .</span><span class="sxs-lookup"><span data-stu-id="ce045-124">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="ce045-125">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-125">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="ce045-126">Les éléments arbitraires sont pris en charge en tant que contenu des `<NotFound>` balises, comme d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="ce045-126">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="ce045-127">Pour appliquer une disposition par défaut au <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, consultez <xref:blazor/layouts#apply-a-layout-to-arbitrary-content-layoutview-component> .</span><span class="sxs-lookup"><span data-stu-id="ce045-127">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#apply-a-layout-to-arbitrary-content-layoutview-component>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="ce045-128">Acheminer vers des composants à partir de plusieurs assemblys</span><span class="sxs-lookup"><span data-stu-id="ce045-128">Route to components from multiple assemblies</span></span>

<span data-ttu-id="ce045-129">Utilisez le <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> paramètre pour spécifier des assemblys supplémentaires <xref:Microsoft.AspNetCore.Components.Routing.Router> que le composant doit prendre en compte lors de la recherche de composants routables.</span><span class="sxs-lookup"><span data-stu-id="ce045-129">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="ce045-130">Les assemblys supplémentaires sont analysés en plus de l’assembly spécifié à `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="ce045-130">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="ce045-131">Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de [classes de composants](xref:blazor/components/class-libraries)référencée.</span><span class="sxs-lookup"><span data-stu-id="ce045-131">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="ce045-132">L' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> exemple suivant entraîne la prise en charge du routage pour `Component1` .</span><span class="sxs-lookup"><span data-stu-id="ce045-132">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="ce045-133">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-133">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="ce045-134">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="ce045-134">Route parameters</span></span>

<span data-ttu-id="ce045-135">Le routeur utilise des paramètres de routage pour remplir les [paramètres de composant](xref:blazor/components/index#component-parameters) correspondants portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="ce045-135">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="ce045-136">Les noms de paramètres d’itinéraire ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="ce045-136">Route parameter names are case insensitive.</span></span> <span data-ttu-id="ce045-137">Dans l’exemple suivant, le `text` paramètre assigne la valeur du segment de route à la propriété du composant `Text` .</span><span class="sxs-lookup"><span data-stu-id="ce045-137">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="ce045-138">Quand une demande est effectuée pour `/RouteParameter/amazing` , le `<h1>` contenu de la balise est restitué sous la forme `Blazor is amazing!` .</span><span class="sxs-lookup"><span data-stu-id="ce045-138">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="ce045-139">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-139">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ce045-140">Les paramètres facultatifs sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ce045-140">Optional parameters are supported.</span></span> <span data-ttu-id="ce045-141">Dans l’exemple suivant, le `text` paramètre facultatif assigne la valeur du segment de routage à la propriété du composant `Text` .</span><span class="sxs-lookup"><span data-stu-id="ce045-141">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="ce045-142">Si le segment n’est pas présent, la valeur de `Text` est définie sur `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="ce045-142">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="ce045-143">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-143">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ce045-144">Les paramètres facultatifs ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ce045-144">Optional parameters aren't supported.</span></span> <span data-ttu-id="ce045-145">Dans l’exemple suivant, deux [ `@page` directives](xref:mvc/views/razor#page) sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="ce045-145">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="ce045-146">La première directive autorise la navigation vers le composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="ce045-146">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ce045-147">La deuxième directive assigne la `{text}` valeur de paramètre d’itinéraire à la `Text` propriété du composant.</span><span class="sxs-lookup"><span data-stu-id="ce045-147">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="ce045-148">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-148">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="ce045-149">Utilisez [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) à la place de pour autoriser la navigation d’application vers le même composant avec une valeur de paramètre facultative différente.</span><span class="sxs-lookup"><span data-stu-id="ce045-149">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="ce045-150">En fonction de l’exemple précédent, utilisez `OnParametersSet` lorsque l’utilisateur doit pouvoir naviguer de `/RouteParameter` vers `/RouteParameter/amazing` ou de `/RouteParameter/amazing` vers `/RouteParameter` :</span><span class="sxs-lookup"><span data-stu-id="ce045-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="ce045-151">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="ce045-151">Route constraints</span></span>

<span data-ttu-id="ce045-152">Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.</span><span class="sxs-lookup"><span data-stu-id="ce045-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="ce045-153">Dans l’exemple suivant, l’itinéraire vers le `User` composant correspond uniquement à si :</span><span class="sxs-lookup"><span data-stu-id="ce045-153">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="ce045-154">Un `Id` segment de routage est présent dans l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="ce045-154">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="ce045-155">Le `Id` segment est un type entier ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="ce045-155">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="ce045-156">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-156">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="ce045-157">Les contraintes de routage indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="ce045-157">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="ce045-158">Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.</span><span class="sxs-lookup"><span data-stu-id="ce045-158">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="ce045-159">Contrainte</span><span class="sxs-lookup"><span data-stu-id="ce045-159">Constraint</span></span> | <span data-ttu-id="ce045-160">Exemple</span><span class="sxs-lookup"><span data-stu-id="ce045-160">Example</span></span>           | <span data-ttu-id="ce045-161">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="ce045-161">Example Matches</span></span>                                                                  | <span data-ttu-id="ce045-162">Invariant</span><span class="sxs-lookup"><span data-stu-id="ce045-162">Invariant</span></span><br><span data-ttu-id="ce045-163">culture</span><span class="sxs-lookup"><span data-stu-id="ce045-163">culture</span></span><br><span data-ttu-id="ce045-164">correspondance</span><span class="sxs-lookup"><span data-stu-id="ce045-164">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="ce045-165">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="ce045-165">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="ce045-166">Non</span><span class="sxs-lookup"><span data-stu-id="ce045-166">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="ce045-167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="ce045-167">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="ce045-168">Oui</span><span class="sxs-lookup"><span data-stu-id="ce045-168">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="ce045-169">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="ce045-169">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="ce045-170">Oui</span><span class="sxs-lookup"><span data-stu-id="ce045-170">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="ce045-171">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="ce045-171">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="ce045-172">Oui</span><span class="sxs-lookup"><span data-stu-id="ce045-172">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="ce045-173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="ce045-173">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="ce045-174">Oui</span><span class="sxs-lookup"><span data-stu-id="ce045-174">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="ce045-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="ce045-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="ce045-176">Non</span><span class="sxs-lookup"><span data-stu-id="ce045-176">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="ce045-177">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="ce045-177">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="ce045-178">Oui</span><span class="sxs-lookup"><span data-stu-id="ce045-178">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="ce045-179">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="ce045-179">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="ce045-180">Oui</span><span class="sxs-lookup"><span data-stu-id="ce045-180">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="ce045-181">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou <xref:System.DateTime>) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="ce045-181">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="ce045-182">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="ce045-182">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="ce045-183">Routage avec des URL qui contiennent des points</span><span class="sxs-lookup"><span data-stu-id="ce045-183">Routing with URLs that contain dots</span></span>

<span data-ttu-id="ce045-184">Pour Blazor WebAssembly les applications hébergées et Blazor Server , le modèle de routage par défaut côté serveur suppose que si le dernier segment d’une URL de demande contient un point ( `.` ) qu’un fichier est demandé.</span><span class="sxs-lookup"><span data-stu-id="ce045-184">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="ce045-185">Par exemple, l’URL `https://localhost.com:5001/example/some.thing` est interprétée par le routeur comme une demande pour un fichier nommé `some.thing` .</span><span class="sxs-lookup"><span data-stu-id="ce045-185">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="ce045-186">Sans configuration supplémentaire, une application retourne une réponse *404-introuvable* si `some.thing` a été conçu pour router vers un composant avec une [ `@page` directive](xref:mvc/views/razor#page) et `some.thing` est une valeur de paramètre d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ce045-186">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="ce045-187">Pour utiliser un itinéraire avec un ou plusieurs paramètres contenant un point, l’application doit configurer l’itinéraire avec un modèle personnalisé.</span><span class="sxs-lookup"><span data-stu-id="ce045-187">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="ce045-188">Prenons le `Example` composant suivant qui peut recevoir un paramètre d’itinéraire à partir du dernier segment de l’URL.</span><span class="sxs-lookup"><span data-stu-id="ce045-188">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="ce045-189">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-189">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="ce045-190">Pour permettre à l' **`Server`** application d’une Blazor WebAssembly solution hébergée d’acheminer la demande avec un point dans le `param` paramètre d’itinéraire, ajoutez un modèle d’itinéraire de fichier de secours avec le paramètre facultatif dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ce045-190">To permit the **`Server`** app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="ce045-191">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="ce045-191">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="ce045-192">Pour configurer une Blazor Server application afin d’acheminer la demande avec un point dans le `param` paramètre d’itinéraire, ajoutez un modèle de routage de page de secours avec le paramètre facultatif dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ce045-192">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="ce045-193">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="ce045-193">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="ce045-194">Pour plus d’informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="ce045-194">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="ce045-195">Paramètres d’itinéraire de rattrapage</span><span class="sxs-lookup"><span data-stu-id="ce045-195">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ce045-196">Les paramètres d’itinéraire Catch-All, qui capturent les chemins d’accès dans plusieurs limites de dossiers, sont pris en charge dans les composants.</span><span class="sxs-lookup"><span data-stu-id="ce045-196">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="ce045-197">Les paramètres d’itinéraire Catch-All sont :</span><span class="sxs-lookup"><span data-stu-id="ce045-197">Catch-all route parameters are:</span></span>

* <span data-ttu-id="ce045-198">Nommée pour correspondre au nom du segment de routage.</span><span class="sxs-lookup"><span data-stu-id="ce045-198">Named to match the route segment name.</span></span> <span data-ttu-id="ce045-199">Le nom ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="ce045-199">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="ce045-200">Type `string`.</span><span class="sxs-lookup"><span data-stu-id="ce045-200">A `string` type.</span></span> <span data-ttu-id="ce045-201">L’infrastructure ne fournit pas de conversion automatique.</span><span class="sxs-lookup"><span data-stu-id="ce045-201">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="ce045-202">À la fin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="ce045-202">At the end of the URL.</span></span>

<span data-ttu-id="ce045-203">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-203">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

<span data-ttu-id="ce045-204">Pour l’URL `/catch-all/this/is/a/test` avec un modèle de routage de `/catch-all/{*pageRoute}` , la valeur de `PageRoute` est définie sur `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="ce045-204">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="ce045-205">Les barres obliques et les segments du chemin capturé sont décodés.</span><span class="sxs-lookup"><span data-stu-id="ce045-205">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="ce045-206">Pour un modèle de routage de `/catch-all/{*pageRoute}` , l’URL `/catch-all/this/is/a%2Ftest%2A` génère `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="ce045-206">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ce045-207">Les paramètres d’itinéraire Catch-All sont pris en charge dans ASP.NET Core 5,0 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ce045-207">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="ce045-208">Pour plus d’informations, sélectionnez la version 5,0 de cet article.</span><span class="sxs-lookup"><span data-stu-id="ce045-208">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="ce045-209">Aide des URI et de l’état de navigation</span><span class="sxs-lookup"><span data-stu-id="ce045-209">URI and navigation state helpers</span></span>

<span data-ttu-id="ce045-210">Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour gérer les URI et la navigation dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="ce045-210">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="ce045-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> fournit l’événement et les méthodes répertoriées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ce045-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="ce045-212">Membre</span><span class="sxs-lookup"><span data-stu-id="ce045-212">Member</span></span> | <span data-ttu-id="ce045-213">Description</span><span class="sxs-lookup"><span data-stu-id="ce045-213">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="ce045-214">Obtient l’URI absolu actuel.</span><span class="sxs-lookup"><span data-stu-id="ce045-214">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="ce045-215">Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu.</span><span class="sxs-lookup"><span data-stu-id="ce045-215">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="ce045-216">En général, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> correspond à l' `href` attribut sur l’élément du document `<base>` dans `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="ce045-216">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="ce045-217">Navigue vers l’URI spécifié.</span><span class="sxs-lookup"><span data-stu-id="ce045-217">Navigates to the specified URI.</span></span> <span data-ttu-id="ce045-218">Si `forceLoad` est `true` :</span><span class="sxs-lookup"><span data-stu-id="ce045-218">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="ce045-219">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="ce045-219">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="ce045-220">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="ce045-220">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="ce045-221">Événement qui se déclenche lorsque l’emplacement de navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="ce045-221">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="ce045-222">Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="ce045-222">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="ce045-223">À partir d’un URI de base (par exemple, un URI précédemment retourné par <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), convertit un URI absolu en un URI relatif au préfixe URI de base.</span><span class="sxs-lookup"><span data-stu-id="ce045-223">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="ce045-224">Pour l' <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> événement, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fournit les informations suivantes sur les événements de navigation :</span><span class="sxs-lookup"><span data-stu-id="ce045-224">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="ce045-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL du nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="ce045-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="ce045-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Si `true` , a Blazor intercepté la navigation à partir du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ce045-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="ce045-227">Si `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> la valeur est, la navigation a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="ce045-227">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="ce045-228">Le composant suivant :</span><span class="sxs-lookup"><span data-stu-id="ce045-228">The following component:</span></span>

* <span data-ttu-id="ce045-229">Accède au composant de l’application `Counter` ( `Pages/Counter.razor` ) lorsque le bouton est sélectionné à l’aide de <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .</span><span class="sxs-lookup"><span data-stu-id="ce045-229">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="ce045-230">Gère l’événement de modification d’emplacement en s’abonnant à <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ce045-230">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="ce045-231">La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="ce045-231">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="ce045-232">Le déraccordement de la méthode permet de garbage collection du composant.</span><span class="sxs-lookup"><span data-stu-id="ce045-232">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="ce045-233">L’implémentation du journal enregistre les informations suivantes lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="ce045-233">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="ce045-234">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-234">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

<span data-ttu-id="ce045-235">Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="ce045-235">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="ce045-236">Chaîne de requête et paramètres d’analyse</span><span class="sxs-lookup"><span data-stu-id="ce045-236">Query string and parse parameters</span></span>

<span data-ttu-id="ce045-237">La chaîne de requête d’une requête est obtenue à partir de la <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> propriété :</span><span class="sxs-lookup"><span data-stu-id="ce045-237">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="ce045-238">Pour analyser les paramètres d’une chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="ce045-238">To parse a query string's parameters:</span></span>

* <span data-ttu-id="ce045-239">Une application peut utiliser l' <xref:Microsoft.AspNetCore.WebUtilities> API.</span><span class="sxs-lookup"><span data-stu-id="ce045-239">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="ce045-240">Si l’API n’est pas disponible pour l’application, ajoutez une référence de package dans le fichier projet de l’application pour [Microsoft. AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="ce045-240">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="ce045-241">Obtenez la valeur après l’analyse de la chaîne de requête avec <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ce045-241">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="ce045-242">L' `ParseQueryString` exemple de composant suivant analyse une clé de paramètre de chaîne de requête nommée `ship` .</span><span class="sxs-lookup"><span data-stu-id="ce045-242">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="ce045-243">Par exemple, la paire clé-valeur de la chaîne de requête d’URL `?ship=Tardis` capture la valeur `Tardis` dans `queryValue` .</span><span class="sxs-lookup"><span data-stu-id="ce045-243">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="ce045-244">Dans l’exemple suivant, accédez à l’application avec l’URL `https://localhost:5001/parse-query-string?ship=Tardis` .</span><span class="sxs-lookup"><span data-stu-id="ce045-244">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="ce045-245">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="ce045-245">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="ce045-246">`NavLink` et `NavMenu` composants</span><span class="sxs-lookup"><span data-stu-id="ce045-246">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="ce045-247">Utilisez un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation.</span><span class="sxs-lookup"><span data-stu-id="ce045-247">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="ce045-248">Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="ce045-248">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="ce045-249">La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="ce045-249">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="ce045-250">Éventuellement, assignez un nom de classe CSS à pour <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> appliquer une classe CSS personnalisée au lien rendu lorsque l’itinéraire actuel correspond à `href` .</span><span class="sxs-lookup"><span data-stu-id="ce045-250">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="ce045-251">Le `NavMenu` composant suivant crée une [`Bootstrap`](https://getbootstrap.com/docs/) barre de navigation qui montre comment utiliser des <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composants :</span><span class="sxs-lookup"><span data-stu-id="ce045-251">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="ce045-252">Le `NavMenu` composant ( `NavMenu.razor` ) est fourni dans le `Shared` dossier d’une application générée à partir des [ Blazor modèles de projet](xref:blazor/project-structure).</span><span class="sxs-lookup"><span data-stu-id="ce045-252">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="ce045-253">Il existe deux <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :</span><span class="sxs-lookup"><span data-stu-id="ce045-253">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="ce045-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à la totalité de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="ce045-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="ce045-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*valeur par défaut*) : le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="ce045-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="ce045-256">Dans l’exemple précédent, la page <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="ce045-256">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="ce045-257">Le deuxième <xref:Microsoft.AspNetCore.Components.Routing.NavLink> reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `component` préfixe (par exemple, `https://localhost:5001/component` et `https://localhost:5001/component/another-segment` ).</span><span class="sxs-lookup"><span data-stu-id="ce045-257">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="ce045-258"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue.</span><span class="sxs-lookup"><span data-stu-id="ce045-258">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="ce045-259">Dans l’exemple suivant, le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant comprend l' `target` attribut :</span><span class="sxs-lookup"><span data-stu-id="ce045-259">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="ce045-260">Le balisage HTML suivant est rendu :</span><span class="sxs-lookup"><span data-stu-id="ce045-260">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="ce045-261">En raison de la façon dont le Blazor contenu enfant est rendu, `NavLink` les composants de rendu à l’intérieur d’une `for` boucle requièrent une variable d’index local si la variable de boucle d’incrémentation est utilisée dans le `NavLink` contenu du composant (enfant) :</span><span class="sxs-lookup"><span data-stu-id="ce045-261">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="ce045-262">L’utilisation d’une variable d’index dans ce scénario est requise pour **tout** composant enfant qui utilise une variable de boucle dans son [contenu enfant](xref:blazor/components/index#child-content), et pas seulement pour le `NavLink` composant.</span><span class="sxs-lookup"><span data-stu-id="ce045-262">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="ce045-263">Vous pouvez également utiliser une `foreach` boucle avec <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="ce045-263">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="ce045-264">Intégration du routage du point de terminaison ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce045-264">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="ce045-265">*Cette section s’applique uniquement aux Blazor Server applications.*</span><span class="sxs-lookup"><span data-stu-id="ce045-265">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="ce045-266">Blazor Server est intégré dans [ASP.net core le routage du point de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="ce045-266">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="ce045-267">Une ASP.NET Core application est configurée pour accepter les connexions entrantes pour les composants interactifs avec <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ce045-267">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="ce045-268">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="ce045-268">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

<span data-ttu-id="ce045-269">La configuration classique consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="ce045-269">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="ce045-270">Par Convention, la page *hôte* est généralement nommée `_Host.cshtml` dans le `Pages` dossier de l’application.</span><span class="sxs-lookup"><span data-stu-id="ce045-270">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="ce045-271">L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ce045-271">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="ce045-272">L’itinéraire de secours est utilisé lorsque les autres itinéraires ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="ce045-272">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="ce045-273">Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec le routage des composants dans l' Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="ce045-273">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="ce045-274">Pour plus d’informations sur la configuration <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> de pour l’hébergement de serveur d’URL non racine, consultez <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="ce045-274">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
