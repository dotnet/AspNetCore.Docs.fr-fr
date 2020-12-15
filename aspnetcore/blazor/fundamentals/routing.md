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
ms.openlocfilehash: d6f64e67ad799847c0992bad8e4353bac07c9901
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485951"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="b6dba-103">Routage de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b6dba-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="b6dba-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b6dba-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b6dba-105">Dans cet article, vous allez apprendre à gérer le routage des demandes et à utiliser le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant pour créer des liens de navigation dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="b6dba-105">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="b6dba-106">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="b6dba-106">Route templates</span></span>

<span data-ttu-id="b6dba-107">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet le routage vers Razor des composants dans une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="b6dba-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="b6dba-108">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant est utilisé dans le `App` composant des Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="b6dba-108">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="b6dba-109">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-109">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

<span data-ttu-id="b6dba-110">Quand un Razor composant ( `.razor` ) avec une [ `@page` directive](xref:mvc/views/razor#page) est compilé, la classe de composant générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage du composant.</span><span class="sxs-lookup"><span data-stu-id="b6dba-110">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="b6dba-111">Lorsque l’application démarre, l’assembly spécifié en tant que routeur `AppAssembly` est analysé pour collecter des informations de routage pour les composants de l’application qui ont un <xref:Microsoft.AspNetCore.Components.RouteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-111">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="b6dba-112">Au moment de l’exécution, le <xref:Microsoft.AspNetCore.Components.RouteView> composant :</span><span class="sxs-lookup"><span data-stu-id="b6dba-112">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="b6dba-113">Reçoit du avec <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> tous les paramètres d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="b6dba-113">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="b6dba-114">Restitue le composant spécifié avec sa [disposition](xref:blazor/layouts), y compris les dispositions imbriquées supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="b6dba-114">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="b6dba-115">Vous pouvez également spécifier un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> paramètre avec une classe de disposition pour les composants qui ne spécifient pas de disposition avec la [ `@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="b6dba-115">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="b6dba-116">Les modèles de projet de l’infrastructure Blazor spécifient le `MainLayout` composant ( `Shared/MainLayout.razor` ) comme disposition par défaut de l’application.</span><span class="sxs-lookup"><span data-stu-id="b6dba-116">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="b6dba-117">Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-117">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="b6dba-118">Les composants prennent en charge plusieurs modèles de routage à l’aide de plusieurs [ `@page` directives](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="b6dba-118">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="b6dba-119">L’exemple de composant suivant se charge sur les demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-119">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="b6dba-120">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-120">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="b6dba-121">Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son `wwwroot/index.html` fichier ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` fichier ( Blazor Server ) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut.</span><span class="sxs-lookup"><span data-stu-id="b6dba-121">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="b6dba-122">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="b6dba-122">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="b6dba-123">Fournir du contenu personnalisé lorsque le contenu est introuvable</span><span class="sxs-lookup"><span data-stu-id="b6dba-123">Provide custom content when content isn't found</span></span>

<span data-ttu-id="b6dba-124">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="b6dba-124">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="b6dba-125">Dans le `App` composant, définissez le contenu personnalisé dans le <xref:Microsoft.AspNetCore.Components.Routing.Router> modèle du composant <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-125">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="b6dba-126">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-126">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="b6dba-127">Les éléments arbitraires sont pris en charge en tant que contenu des `<NotFound>` balises, comme d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="b6dba-127">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="b6dba-128">Pour appliquer une disposition par défaut au <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, consultez <xref:blazor/layouts#default-layout> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-128">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="b6dba-129">Acheminer vers des composants à partir de plusieurs assemblys</span><span class="sxs-lookup"><span data-stu-id="b6dba-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="b6dba-130">Utilisez le <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> paramètre pour spécifier des assemblys supplémentaires <xref:Microsoft.AspNetCore.Components.Routing.Router> que le composant doit prendre en compte lors de la recherche de composants routables.</span><span class="sxs-lookup"><span data-stu-id="b6dba-130">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="b6dba-131">Les assemblys supplémentaires sont analysés en plus de l’assembly spécifié à `AppAssembly` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-131">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="b6dba-132">Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de [classes de composants](xref:blazor/components/class-libraries)référencée.</span><span class="sxs-lookup"><span data-stu-id="b6dba-132">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="b6dba-133">L' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> exemple suivant entraîne la prise en charge du routage pour `Component1` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-133">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="b6dba-134">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-134">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="b6dba-135">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="b6dba-135">Route parameters</span></span>

<span data-ttu-id="b6dba-136">Le routeur utilise des paramètres de routage pour remplir les [paramètres de composant](xref:blazor/components/index#component-parameters) correspondants portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="b6dba-136">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="b6dba-137">Les noms de paramètres d’itinéraire ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="b6dba-137">Route parameter names are case insensitive.</span></span> <span data-ttu-id="b6dba-138">Dans l’exemple suivant, le `text` paramètre assigne la valeur du segment de route à la propriété du composant `Text` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-138">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b6dba-139">Quand une demande est effectuée pour `/RouteParameter/amazing` , le `<h1>` contenu de la balise est restitué sous la forme `Blazor is amazing!` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-139">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="b6dba-140">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-140">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b6dba-141">Les paramètres facultatifs sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="b6dba-141">Optional parameters are supported.</span></span> <span data-ttu-id="b6dba-142">Dans l’exemple suivant, le `text` paramètre facultatif assigne la valeur du segment de routage à la propriété du composant `Text` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-142">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="b6dba-143">Si le segment n’est pas présent, la valeur de `Text` est définie sur `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-143">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="b6dba-144">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-144">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b6dba-145">Les paramètres facultatifs ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="b6dba-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="b6dba-146">Dans l’exemple suivant, deux [ `@page` directives](xref:mvc/views/razor#page) sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="b6dba-146">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="b6dba-147">La première directive autorise la navigation vers le composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="b6dba-147">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b6dba-148">La deuxième directive assigne la `{text}` valeur de paramètre d’itinéraire à la `Text` propriété du composant.</span><span class="sxs-lookup"><span data-stu-id="b6dba-148">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="b6dba-149">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-149">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b6dba-150">Utilisez sur [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) au lieu de [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) pour autoriser la navigation de l’application vers le même composant avec une valeur de paramètre facultative différente.</span><span class="sxs-lookup"><span data-stu-id="b6dba-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="b6dba-151">En fonction de l’exemple précédent, utilisez `OnParametersSet` lorsque l’utilisateur doit pouvoir naviguer de `/RouteParameter` vers `/RouteParameter/amazing` ou de `/RouteParameter/amazing` vers `/RouteParameter` :</span><span class="sxs-lookup"><span data-stu-id="b6dba-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="b6dba-152">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="b6dba-152">Route constraints</span></span>

<span data-ttu-id="b6dba-153">Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.</span><span class="sxs-lookup"><span data-stu-id="b6dba-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="b6dba-154">Dans l’exemple suivant, l’itinéraire vers le `User` composant correspond uniquement à si :</span><span class="sxs-lookup"><span data-stu-id="b6dba-154">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="b6dba-155">Un `Id` segment de routage est présent dans l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="b6dba-155">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="b6dba-156">Le `Id` segment est un type entier ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="b6dba-156">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="b6dba-157">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-157">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="b6dba-158">Les contraintes de routage indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="b6dba-158">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="b6dba-159">Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.</span><span class="sxs-lookup"><span data-stu-id="b6dba-159">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="b6dba-160">Contrainte</span><span class="sxs-lookup"><span data-stu-id="b6dba-160">Constraint</span></span> | <span data-ttu-id="b6dba-161">Exemple</span><span class="sxs-lookup"><span data-stu-id="b6dba-161">Example</span></span>           | <span data-ttu-id="b6dba-162">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="b6dba-162">Example Matches</span></span>                                                                  | <span data-ttu-id="b6dba-163">Invariant</span><span class="sxs-lookup"><span data-stu-id="b6dba-163">Invariant</span></span><br><span data-ttu-id="b6dba-164">culture</span><span class="sxs-lookup"><span data-stu-id="b6dba-164">culture</span></span><br><span data-ttu-id="b6dba-165">correspondance</span><span class="sxs-lookup"><span data-stu-id="b6dba-165">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="b6dba-166">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="b6dba-166">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="b6dba-167">Non</span><span class="sxs-lookup"><span data-stu-id="b6dba-167">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="b6dba-168">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="b6dba-168">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="b6dba-169">Oui</span><span class="sxs-lookup"><span data-stu-id="b6dba-169">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="b6dba-170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="b6dba-170">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="b6dba-171">Oui</span><span class="sxs-lookup"><span data-stu-id="b6dba-171">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="b6dba-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b6dba-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b6dba-173">Oui</span><span class="sxs-lookup"><span data-stu-id="b6dba-173">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="b6dba-174">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="b6dba-174">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="b6dba-175">Oui</span><span class="sxs-lookup"><span data-stu-id="b6dba-175">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="b6dba-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="b6dba-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="b6dba-177">Non</span><span class="sxs-lookup"><span data-stu-id="b6dba-177">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="b6dba-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b6dba-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b6dba-179">Oui</span><span class="sxs-lookup"><span data-stu-id="b6dba-179">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="b6dba-180">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="b6dba-180">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="b6dba-181">Oui</span><span class="sxs-lookup"><span data-stu-id="b6dba-181">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="b6dba-182">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou <xref:System.DateTime>) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="b6dba-182">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="b6dba-183">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="b6dba-183">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="b6dba-184">Routage avec des URL qui contiennent des points</span><span class="sxs-lookup"><span data-stu-id="b6dba-184">Routing with URLs that contain dots</span></span>

<span data-ttu-id="b6dba-185">Pour Blazor WebAssembly les applications hébergées et Blazor Server , le modèle de routage par défaut côté serveur suppose que si le dernier segment d’une URL de demande contient un point ( `.` ) qu’un fichier est demandé.</span><span class="sxs-lookup"><span data-stu-id="b6dba-185">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="b6dba-186">Par exemple, l’URL `https://localhost.com:5001/example/some.thing` est interprétée par le routeur comme une demande pour un fichier nommé `some.thing` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-186">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="b6dba-187">Sans configuration supplémentaire, une application retourne une réponse *404-introuvable* si `some.thing` a été conçu pour router vers un composant avec une [ `@page` directive](xref:mvc/views/razor#page) et `some.thing` est une valeur de paramètre d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="b6dba-187">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="b6dba-188">Pour utiliser un itinéraire avec un ou plusieurs paramètres contenant un point, l’application doit configurer l’itinéraire avec un modèle personnalisé.</span><span class="sxs-lookup"><span data-stu-id="b6dba-188">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="b6dba-189">Prenons le `Example` composant suivant qui peut recevoir un paramètre d’itinéraire à partir du dernier segment de l’URL.</span><span class="sxs-lookup"><span data-stu-id="b6dba-189">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="b6dba-190">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-190">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="b6dba-191">Pour permettre à l' *`Server`* application d’une Blazor WebAssembly solution hébergée d’acheminer la demande avec un point dans le `param` paramètre d’itinéraire, ajoutez un modèle d’itinéraire de fichier de secours avec le paramètre facultatif dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-191">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b6dba-192">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-192">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="b6dba-193">Pour configurer une Blazor Server application afin d’acheminer la demande avec un point dans le `param` paramètre d’itinéraire, ajoutez un modèle de routage de page de secours avec le paramètre facultatif dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-193">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="b6dba-194">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-194">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="b6dba-195">Pour plus d’informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b6dba-195">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="b6dba-196">Paramètres d’itinéraire de rattrapage</span><span class="sxs-lookup"><span data-stu-id="b6dba-196">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b6dba-197">Les paramètres d’itinéraire Catch-All, qui capturent les chemins d’accès dans plusieurs limites de dossiers, sont pris en charge dans les composants.</span><span class="sxs-lookup"><span data-stu-id="b6dba-197">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="b6dba-198">Les paramètres d’itinéraire Catch-All sont :</span><span class="sxs-lookup"><span data-stu-id="b6dba-198">Catch-all route parameters are:</span></span>

* <span data-ttu-id="b6dba-199">Nommée pour correspondre au nom du segment de routage.</span><span class="sxs-lookup"><span data-stu-id="b6dba-199">Named to match the route segment name.</span></span> <span data-ttu-id="b6dba-200">Le nom ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="b6dba-200">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="b6dba-201">Type `string`.</span><span class="sxs-lookup"><span data-stu-id="b6dba-201">A `string` type.</span></span> <span data-ttu-id="b6dba-202">L’infrastructure ne fournit pas de conversion automatique.</span><span class="sxs-lookup"><span data-stu-id="b6dba-202">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="b6dba-203">À la fin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="b6dba-203">At the end of the URL.</span></span>

<span data-ttu-id="b6dba-204">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-204">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

<span data-ttu-id="b6dba-205">Pour l’URL `/catch-all/this/is/a/test` avec un modèle de routage de `/catch-all/{*pageRoute}` , la valeur de `PageRoute` est définie sur `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-205">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="b6dba-206">Les barres obliques et les segments du chemin capturé sont décodés.</span><span class="sxs-lookup"><span data-stu-id="b6dba-206">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="b6dba-207">Pour un modèle de routage de `/catch-all/{*pageRoute}` , l’URL `/catch-all/this/is/a%2Ftest%2A` génère `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-207">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b6dba-208">Les paramètres d’itinéraire Catch-All sont pris en charge dans ASP.NET Core 5,0 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="b6dba-208">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="b6dba-209">Pour plus d’informations, sélectionnez la version 5,0 de cet article.</span><span class="sxs-lookup"><span data-stu-id="b6dba-209">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="b6dba-210">Aide des URI et de l’état de navigation</span><span class="sxs-lookup"><span data-stu-id="b6dba-210">URI and navigation state helpers</span></span>

<span data-ttu-id="b6dba-211">Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour gérer les URI et la navigation dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="b6dba-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="b6dba-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> fournit l’événement et les méthodes répertoriées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b6dba-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="b6dba-213">Membre</span><span class="sxs-lookup"><span data-stu-id="b6dba-213">Member</span></span> | <span data-ttu-id="b6dba-214">Description</span><span class="sxs-lookup"><span data-stu-id="b6dba-214">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="b6dba-215">Obtient l’URI absolu actuel.</span><span class="sxs-lookup"><span data-stu-id="b6dba-215">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="b6dba-216">Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu.</span><span class="sxs-lookup"><span data-stu-id="b6dba-216">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="b6dba-217">En général, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> correspond à l' `href` attribut sur l’élément du document `<base>` dans `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="b6dba-217">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="b6dba-218">Navigue vers l’URI spécifié.</span><span class="sxs-lookup"><span data-stu-id="b6dba-218">Navigates to the specified URI.</span></span> <span data-ttu-id="b6dba-219">Si `forceLoad` est `true` :</span><span class="sxs-lookup"><span data-stu-id="b6dba-219">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="b6dba-220">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="b6dba-220">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="b6dba-221">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="b6dba-221">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="b6dba-222">Événement qui se déclenche lorsque l’emplacement de navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="b6dba-222">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="b6dba-223">Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="b6dba-223">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="b6dba-224">À partir d’un URI de base (par exemple, un URI précédemment retourné par <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), convertit un URI absolu en un URI relatif au préfixe URI de base.</span><span class="sxs-lookup"><span data-stu-id="b6dba-224">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="b6dba-225">Pour l' <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> événement, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fournit les informations suivantes sur les événements de navigation :</span><span class="sxs-lookup"><span data-stu-id="b6dba-225">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="b6dba-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL du nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="b6dba-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="b6dba-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Si `true` , a Blazor intercepté la navigation à partir du navigateur.</span><span class="sxs-lookup"><span data-stu-id="b6dba-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="b6dba-228">Si `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> la valeur est, la navigation a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="b6dba-228">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="b6dba-229">Le composant suivant :</span><span class="sxs-lookup"><span data-stu-id="b6dba-229">The following component:</span></span>

* <span data-ttu-id="b6dba-230">Accède au composant de l’application `Counter` ( `Pages/Counter.razor` ) lorsque le bouton est sélectionné à l’aide de <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-230">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="b6dba-231">Gère l’événement de modification d’emplacement en s’abonnant à <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-231">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="b6dba-232">La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="b6dba-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="b6dba-233">Le déraccordement de la méthode permet de garbage collection du composant.</span><span class="sxs-lookup"><span data-stu-id="b6dba-233">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="b6dba-234">L’implémentation du journal enregistre les informations suivantes lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="b6dba-234">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="b6dba-235">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-235">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

<span data-ttu-id="b6dba-236">Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-236">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="b6dba-237">Chaîne de requête et paramètres d’analyse</span><span class="sxs-lookup"><span data-stu-id="b6dba-237">Query string and parse parameters</span></span>

<span data-ttu-id="b6dba-238">La chaîne de requête d’une requête est obtenue à partir de la <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> propriété :</span><span class="sxs-lookup"><span data-stu-id="b6dba-238">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="b6dba-239">Pour analyser les paramètres d’une chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="b6dba-239">To parse a query string's parameters:</span></span>

* <span data-ttu-id="b6dba-240">Une application peut utiliser l' <xref:Microsoft.AspNetCore.WebUtilities> API.</span><span class="sxs-lookup"><span data-stu-id="b6dba-240">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="b6dba-241">Si l’API n’est pas disponible pour l’application, ajoutez une référence de package dans le fichier projet de l’application pour [Microsoft. AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="b6dba-241">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="b6dba-242">Obtenez la valeur après l’analyse de la chaîne de requête avec <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-242">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b6dba-243">L' `ParseQueryString` exemple de composant suivant analyse une clé de paramètre de chaîne de requête nommée `ship` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-243">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="b6dba-244">Par exemple, la paire clé-valeur de la chaîne de requête d’URL `?ship=Tardis` capture la valeur `Tardis` dans `queryValue` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-244">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="b6dba-245">Dans l’exemple suivant, accédez à l’application avec l’URL `https://localhost:5001/parse-query-string?ship=Tardis` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-245">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="b6dba-246">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-246">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="b6dba-247">Composant `NavLink`</span><span class="sxs-lookup"><span data-stu-id="b6dba-247">`NavLink` component</span></span>

<span data-ttu-id="b6dba-248">Utilisez un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation.</span><span class="sxs-lookup"><span data-stu-id="b6dba-248">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="b6dba-249">Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="b6dba-249">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="b6dba-250">La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="b6dba-250">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="b6dba-251">Éventuellement, assignez un nom de classe CSS à pour <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> appliquer une classe CSS personnalisée au lien rendu lorsque l’itinéraire actuel correspond à `href` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-251">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="b6dba-252">Le `NavMenu` composant suivant crée une [`Bootstrap`](https://getbootstrap.com/docs/) barre de navigation qui montre comment utiliser des <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composants :</span><span class="sxs-lookup"><span data-stu-id="b6dba-252">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b6dba-253">Le `NavMenu` composant ( `NavMenu.razor` ) est fourni dans le `Shared` dossier d’une application générée à partir des Blazor modèles de projet.</span><span class="sxs-lookup"><span data-stu-id="b6dba-253">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="b6dba-254">Il existe deux <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :</span><span class="sxs-lookup"><span data-stu-id="b6dba-254">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="b6dba-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à la totalité de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="b6dba-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="b6dba-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*valeur par défaut*) : le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="b6dba-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="b6dba-257">Dans l’exemple précédent, la page <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="b6dba-257">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="b6dba-258">Le deuxième <xref:Microsoft.AspNetCore.Components.Routing.NavLink> reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `component` préfixe (par exemple, `https://localhost:5001/component` et `https://localhost:5001/component/another-segment` ).</span><span class="sxs-lookup"><span data-stu-id="b6dba-258">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="b6dba-259"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue.</span><span class="sxs-lookup"><span data-stu-id="b6dba-259">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="b6dba-260">Dans l’exemple suivant, le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant comprend l' `target` attribut :</span><span class="sxs-lookup"><span data-stu-id="b6dba-260">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="b6dba-261">Le balisage HTML suivant est rendu :</span><span class="sxs-lookup"><span data-stu-id="b6dba-261">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="b6dba-262">En raison de la façon dont le Blazor contenu enfant est rendu, `NavLink` les composants de rendu à l’intérieur d’une `for` boucle requièrent une variable d’index local si la variable de boucle d’incrémentation est utilisée dans le `NavLink` contenu du composant (enfant) :</span><span class="sxs-lookup"><span data-stu-id="b6dba-262">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="b6dba-263">L’utilisation d’une variable d’index dans ce scénario est requise pour **tout** composant enfant qui utilise une variable de boucle dans son [contenu enfant](xref:blazor/components/index#child-content), et pas seulement pour le `NavLink` composant.</span><span class="sxs-lookup"><span data-stu-id="b6dba-263">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="b6dba-264">Vous pouvez également utiliser une `foreach` boucle avec <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="b6dba-264">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="b6dba-265">Intégration du routage du point de terminaison ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6dba-265">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="b6dba-266">*Cette section s’applique uniquement aux Blazor Server applications.*</span><span class="sxs-lookup"><span data-stu-id="b6dba-266">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="b6dba-267">Blazor Server est intégré dans [ASP.net core le routage du point de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="b6dba-267">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="b6dba-268">Une ASP.NET Core application est configurée pour accepter les connexions entrantes pour les composants interactifs avec <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b6dba-268">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="b6dba-269">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="b6dba-269">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="b6dba-270">La configuration classique consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="b6dba-270">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="b6dba-271">Par Convention, la page *hôte* est généralement nommée `_Host.cshtml` dans le `Pages` dossier de l’application.</span><span class="sxs-lookup"><span data-stu-id="b6dba-271">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="b6dba-272">L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="b6dba-272">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="b6dba-273">L’itinéraire de secours est utilisé lorsque les autres itinéraires ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="b6dba-273">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="b6dba-274">Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec le routage des composants dans l' Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="b6dba-274">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="b6dba-275">Pour plus d’informations sur la configuration <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> de pour l’hébergement de serveur d’URL non racine, consultez <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="b6dba-275">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
