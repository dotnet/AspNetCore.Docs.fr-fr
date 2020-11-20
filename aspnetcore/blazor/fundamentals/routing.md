---
title: Routage de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment acheminer des requêtes dans des applications et sur le composant NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
ms.openlocfilehash: a83637bb2690407dac5994bb63c765fcfa97462f
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981893"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="77176-103">Routage de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="77176-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="77176-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="77176-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="77176-105">Découvrez comment acheminer les demandes et comment utiliser le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant pour créer des liens de navigation dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="77176-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="77176-106">Intégration du routage du point de terminaison ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77176-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="77176-107">Blazor Server est intégré dans [ASP.net core le routage du point de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="77176-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="77176-108">Une application ASP.NET Core est configurée pour accepter les connexions entrantes pour les composants interactifs avec <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="77176-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="77176-109">La configuration la plus courante consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="77176-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="77176-110">Par Convention, la page *hôte* est généralement nommée `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="77176-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="77176-111">L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="77176-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="77176-112">L’itinéraire de secours est pris en compte lorsque les autres itinéraires ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="77176-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="77176-113">Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l' Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="77176-113">This allows the app to use other controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="77176-114">Pour plus d’informations sur la configuration <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> de pour l’hébergement de serveur d’URL non racine, consultez <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="77176-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="77176-115">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="77176-115">Route templates</span></span>

<span data-ttu-id="77176-116">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant active le routage vers chaque composant avec un itinéraire spécifié.</span><span class="sxs-lookup"><span data-stu-id="77176-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="77176-117">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant apparaît dans le `App.razor` fichier :</span><span class="sxs-lookup"><span data-stu-id="77176-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="77176-118">Lorsqu’un `.razor` fichier avec une `@page` directive est compilé, la classe générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="77176-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="77176-119">Au moment de l’exécution, le <xref:Microsoft.AspNetCore.Components.RouteView> composant :</span><span class="sxs-lookup"><span data-stu-id="77176-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="77176-120">Reçoit du avec les <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> paramètres souhaités.</span><span class="sxs-lookup"><span data-stu-id="77176-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="77176-121">Restitue le composant spécifié avec sa disposition (ou une disposition par défaut facultative) à l’aide des paramètres spécifiés.</span><span class="sxs-lookup"><span data-stu-id="77176-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="77176-122">Vous pouvez éventuellement spécifier un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> paramètre avec une classe de disposition à utiliser pour les composants qui ne spécifient pas de disposition.</span><span class="sxs-lookup"><span data-stu-id="77176-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="77176-123">Les modèles par défaut Blazor spécifient le `MainLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="77176-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="77176-124">`MainLayout.razor` se trouve dans le dossier du projet de modèle `Shared` .</span><span class="sxs-lookup"><span data-stu-id="77176-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="77176-125">Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="77176-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="77176-126">Plusieurs modèles de routage peuvent être appliqués à un composant.</span><span class="sxs-lookup"><span data-stu-id="77176-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="77176-127">Le composant suivant répond aux demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="77176-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="77176-128">Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son `wwwroot/index.html` fichier ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` fichier ( Blazor Server ) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="77176-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="77176-129">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="77176-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="77176-130">Fournir du contenu personnalisé lorsque le contenu est introuvable</span><span class="sxs-lookup"><span data-stu-id="77176-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="77176-131">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="77176-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="77176-132">Dans le `App.razor` fichier, définissez le contenu personnalisé dans le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> paramètre de modèle du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant :</span><span class="sxs-lookup"><span data-stu-id="77176-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="77176-133">Le contenu des `<NotFound>` balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="77176-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="77176-134">Pour appliquer une disposition par défaut au <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="77176-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="77176-135">Acheminer vers des composants à partir de plusieurs assemblys</span><span class="sxs-lookup"><span data-stu-id="77176-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="77176-136">Utilisez le <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> paramètre pour spécifier des assemblys supplémentaires <xref:Microsoft.AspNetCore.Components.Routing.Router> que le composant doit prendre en compte lors de la recherche de composants routables.</span><span class="sxs-lookup"><span data-stu-id="77176-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="77176-137">Les assemblys spécifiés sont pris en compte en plus de l' `AppAssembly` assembly spécifié.</span><span class="sxs-lookup"><span data-stu-id="77176-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="77176-138">Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de classes référencée.</span><span class="sxs-lookup"><span data-stu-id="77176-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="77176-139">L' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> exemple suivant entraîne la prise en charge du routage pour `Component1` :</span><span class="sxs-lookup"><span data-stu-id="77176-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="77176-140">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="77176-140">Route parameters</span></span>

<span data-ttu-id="77176-141">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants avec le même nom (sans respect de la casse).</span><span class="sxs-lookup"><span data-stu-id="77176-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="77176-142">Les paramètres facultatifs sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="77176-142">Optional parameters are supported.</span></span> <span data-ttu-id="77176-143">Dans l’exemple suivant, le `text` paramètre facultatif assigne la valeur du segment de routage à la propriété du composant `Text` .</span><span class="sxs-lookup"><span data-stu-id="77176-143">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="77176-144">Si le segment n’est pas présent, la valeur de `Text` est définie sur `fantastic` :</span><span class="sxs-lookup"><span data-stu-id="77176-144">If the segment isn't present, the value of `Text` is set to `fantastic`:</span></span>

```razor
@page "/RouteParameter/{text?}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="77176-145">Les paramètres facultatifs ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="77176-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="77176-146">Deux `@page` directives sont appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="77176-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="77176-147">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="77176-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="77176-148">La deuxième `@page` directive prend le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.</span><span class="sxs-lookup"><span data-stu-id="77176-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="77176-149">Utilisez sur [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) au lieu de [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) pour autoriser la navigation de l’application vers le même composant avec une valeur de paramètre facultative différente.</span><span class="sxs-lookup"><span data-stu-id="77176-149">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="77176-150">En fonction de l’exemple précédent, utilisez `OnParametersSet` lorsque l’utilisateur doit pouvoir naviguer de `/RouteParameter` vers `/RouteParameter/awesome` ou de `/RouteParameter/awesome` vers `/RouteParameter` :</span><span class="sxs-lookup"><span data-stu-id="77176-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/awesome` or from `/RouteParameter/awesome` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="77176-151">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="77176-151">Route constraints</span></span>

<span data-ttu-id="77176-152">Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.</span><span class="sxs-lookup"><span data-stu-id="77176-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="77176-153">Dans l’exemple suivant, l’itinéraire vers le `Users` composant correspond uniquement à si :</span><span class="sxs-lookup"><span data-stu-id="77176-153">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="77176-154">Un `Id` segment de routage est présent sur l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="77176-154">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="77176-155">Le `Id` segment est un entier ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="77176-155">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="77176-156">Les contraintes de routage indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="77176-156">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="77176-157">Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.</span><span class="sxs-lookup"><span data-stu-id="77176-157">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="77176-158">Contrainte</span><span class="sxs-lookup"><span data-stu-id="77176-158">Constraint</span></span> | <span data-ttu-id="77176-159">Exemple</span><span class="sxs-lookup"><span data-stu-id="77176-159">Example</span></span>           | <span data-ttu-id="77176-160">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="77176-160">Example Matches</span></span>                                                                  | <span data-ttu-id="77176-161">Invariant</span><span class="sxs-lookup"><span data-stu-id="77176-161">Invariant</span></span><br><span data-ttu-id="77176-162">culture</span><span class="sxs-lookup"><span data-stu-id="77176-162">culture</span></span><br><span data-ttu-id="77176-163">correspondance</span><span class="sxs-lookup"><span data-stu-id="77176-163">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="77176-164">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="77176-164">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="77176-165">Non</span><span class="sxs-lookup"><span data-stu-id="77176-165">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="77176-166">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="77176-166">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="77176-167">Oui</span><span class="sxs-lookup"><span data-stu-id="77176-167">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="77176-168">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="77176-168">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="77176-169">Oui</span><span class="sxs-lookup"><span data-stu-id="77176-169">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="77176-170">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="77176-170">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="77176-171">Oui</span><span class="sxs-lookup"><span data-stu-id="77176-171">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="77176-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="77176-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="77176-173">Oui</span><span class="sxs-lookup"><span data-stu-id="77176-173">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="77176-174">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="77176-174">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="77176-175">Non</span><span class="sxs-lookup"><span data-stu-id="77176-175">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="77176-176">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="77176-176">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="77176-177">Oui</span><span class="sxs-lookup"><span data-stu-id="77176-177">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="77176-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="77176-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="77176-179">Oui</span><span class="sxs-lookup"><span data-stu-id="77176-179">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="77176-180">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou <xref:System.DateTime>) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="77176-180">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="77176-181">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="77176-181">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="77176-182">Routage avec des URL qui contiennent des points</span><span class="sxs-lookup"><span data-stu-id="77176-182">Routing with URLs that contain dots</span></span>

<span data-ttu-id="77176-183">Pour les Blazor WebAssembly applications hébergées et Blazor Server , le modèle de routage par défaut côté serveur suppose que si le dernier segment d’une URL de demande contient un point ( `.` ) qu’un fichier est demandé (par exemple, `https://localhost.com:5001/example/some.thing` ).</span><span class="sxs-lookup"><span data-stu-id="77176-183">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="77176-184">Sans configuration supplémentaire, une application renvoie une réponse *404-introuvable* si cela devait être acheminé vers un composant.</span><span class="sxs-lookup"><span data-stu-id="77176-184">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="77176-185">Pour utiliser un itinéraire avec un ou plusieurs paramètres contenant un point, l’application doit configurer l’itinéraire avec un modèle personnalisé.</span><span class="sxs-lookup"><span data-stu-id="77176-185">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="77176-186">Prenons le `Example` composant suivant qui peut recevoir un paramètre d’itinéraire à partir du dernier segment de l’URL :</span><span class="sxs-lookup"><span data-stu-id="77176-186">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="77176-187">Pour autoriser l’application *serveur* d’une solution hébergée Blazor WebAssembly à acheminer la demande avec un point dans le `param` paramètre, ajoutez un modèle d’itinéraire de fichier de secours avec le paramètre facultatif dans `Startup.Configure` ( `Startup.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="77176-187">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="77176-188">Pour configurer une Blazor Server application pour acheminer la demande avec un point dans le `param` paramètre, ajoutez un modèle de routage de page de secours avec le paramètre facultatif dans `Startup.Configure` ( `Startup.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="77176-188">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="77176-189">Pour plus d’informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="77176-189">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="77176-190">Paramètres d’itinéraire de rattrapage</span><span class="sxs-lookup"><span data-stu-id="77176-190">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="77176-191">*Cette section s’applique à ASP.NET Core dans .NET 5 Release Candidate 1 (RC1) ou version ultérieure.*</span><span class="sxs-lookup"><span data-stu-id="77176-191">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="77176-192">Les paramètres d’itinéraire Catch-All, qui capturent les chemins d’accès dans plusieurs limites de dossiers, sont pris en charge dans les composants.</span><span class="sxs-lookup"><span data-stu-id="77176-192">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="77176-193">Le paramètre d’itinéraire Catch-All doit être :</span><span class="sxs-lookup"><span data-stu-id="77176-193">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="77176-194">Nommée pour correspondre au nom du segment de routage.</span><span class="sxs-lookup"><span data-stu-id="77176-194">Named to match the route segment name.</span></span> <span data-ttu-id="77176-195">Le nom ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="77176-195">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="77176-196">Type `string`.</span><span class="sxs-lookup"><span data-stu-id="77176-196">A `string` type.</span></span> <span data-ttu-id="77176-197">L’infrastructure ne fournit pas de conversion automatique.</span><span class="sxs-lookup"><span data-stu-id="77176-197">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="77176-198">À la fin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="77176-198">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="77176-199">Pour l’URL `/page/this/is/a/test` avec un modèle de routage de `/page/{*pageRoute}` , la valeur de `PageRoute` est définie sur `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="77176-199">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="77176-200">Les barres obliques et les segments du chemin capturé sont décodés.</span><span class="sxs-lookup"><span data-stu-id="77176-200">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="77176-201">Pour un modèle de routage de `/page/{*pageRoute}` , l’URL `/page/this/is/a%2Ftest%2A` génère `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="77176-201">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="77176-202">Les paramètres d’itinéraire Catch-All sont pris en charge dans ASP.NET Core 5,0 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="77176-202">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="77176-203">Composant NavLink</span><span class="sxs-lookup"><span data-stu-id="77176-203">NavLink component</span></span>

<span data-ttu-id="77176-204">Utilisez un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation.</span><span class="sxs-lookup"><span data-stu-id="77176-204">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="77176-205">Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="77176-205">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="77176-206">La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="77176-206">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="77176-207">Éventuellement, assignez un nom de classe CSS à pour <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> appliquer une classe CSS personnalisée au lien rendu lorsque l’itinéraire actuel correspond à `href` .</span><span class="sxs-lookup"><span data-stu-id="77176-207">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="77176-208">Le `NavMenu` composant suivant crée une [`Bootstrap`](https://getbootstrap.com/docs/) barre de navigation qui montre comment utiliser des <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composants :</span><span class="sxs-lookup"><span data-stu-id="77176-208">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="77176-209">Il existe deux <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :</span><span class="sxs-lookup"><span data-stu-id="77176-209">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="77176-210"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à la totalité de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="77176-210"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="77176-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*valeur par défaut*) : le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="77176-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="77176-212">Dans l’exemple précédent, la page <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="77176-212">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="77176-213">Le deuxième <xref:Microsoft.AspNetCore.Components.Routing.NavLink> reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `MyComponent` préfixe (par exemple, `https://localhost:5001/MyComponent` et `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="77176-213">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="77176-214"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue.</span><span class="sxs-lookup"><span data-stu-id="77176-214">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="77176-215">Dans l’exemple suivant, le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant comprend l' `target` attribut :</span><span class="sxs-lookup"><span data-stu-id="77176-215">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="77176-216">Le balisage HTML suivant est rendu :</span><span class="sxs-lookup"><span data-stu-id="77176-216">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="77176-217">En raison de la façon dont le Blazor contenu enfant est rendu, `NavLink` les composants de rendu à l’intérieur d’une `for` boucle requièrent une variable d’index local si la variable de boucle d’incrémentation est utilisée dans le `NavLink` contenu du composant (enfant) :</span><span class="sxs-lookup"><span data-stu-id="77176-217">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="77176-218">L’utilisation d’une variable d’index dans ce scénario est requise pour **tout** composant enfant qui utilise une variable de boucle dans son [contenu enfant](xref:blazor/components/index#child-content), et pas seulement pour le `NavLink` composant.</span><span class="sxs-lookup"><span data-stu-id="77176-218">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="77176-219">Vous pouvez également utiliser une `foreach` boucle avec <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="77176-219">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="77176-220">Aide des URI et de l’état de navigation</span><span class="sxs-lookup"><span data-stu-id="77176-220">URI and navigation state helpers</span></span>

<span data-ttu-id="77176-221">Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les URI et la navigation dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="77176-221">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="77176-222"><xref:Microsoft.AspNetCore.Components.NavigationManager> fournit l’événement et les méthodes répertoriées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="77176-222"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="77176-223">Membre</span><span class="sxs-lookup"><span data-stu-id="77176-223">Member</span></span> | <span data-ttu-id="77176-224">Description</span><span class="sxs-lookup"><span data-stu-id="77176-224">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="77176-225">Obtient l’URI absolu actuel.</span><span class="sxs-lookup"><span data-stu-id="77176-225">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="77176-226">Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu.</span><span class="sxs-lookup"><span data-stu-id="77176-226">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="77176-227">En général, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> correspond à l' `href` attribut sur l’élément du document `<base>` dans `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="77176-227">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="77176-228">Navigue vers l’URI spécifié.</span><span class="sxs-lookup"><span data-stu-id="77176-228">Navigates to the specified URI.</span></span> <span data-ttu-id="77176-229">Si `forceLoad` est `true` :</span><span class="sxs-lookup"><span data-stu-id="77176-229">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="77176-230">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="77176-230">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="77176-231">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="77176-231">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="77176-232">Événement qui se déclenche lorsque l’emplacement de navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="77176-232">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="77176-233">Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="77176-233">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="77176-234">À partir d’un URI de base (par exemple, un URI précédemment retourné par <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), convertit un URI absolu en un URI relatif au préfixe URI de base.</span><span class="sxs-lookup"><span data-stu-id="77176-234">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="77176-235">Le composant suivant accède au composant de l’application `Counter` lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="77176-235">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="77176-236">Le composant suivant gère un événement de modification d’emplacement en s’abonnant à <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="77176-236">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="77176-237">La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="77176-237">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="77176-238">Le déraccordement de la méthode permet de garbage collection du composant.</span><span class="sxs-lookup"><span data-stu-id="77176-238">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="77176-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fournit les informations suivantes sur l’événement :</span><span class="sxs-lookup"><span data-stu-id="77176-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="77176-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL du nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="77176-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="77176-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Si `true` , a Blazor intercepté la navigation à partir du navigateur.</span><span class="sxs-lookup"><span data-stu-id="77176-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="77176-242">Si `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> la valeur est, la navigation a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="77176-242">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="77176-243">Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="77176-243">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="77176-244">Chaîne de requête et paramètres d’analyse</span><span class="sxs-lookup"><span data-stu-id="77176-244">Query string and parse parameters</span></span>

<span data-ttu-id="77176-245">La chaîne de requête d’une demande peut être obtenue à partir de la <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> propriété de :</span><span class="sxs-lookup"><span data-stu-id="77176-245">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="77176-246">Pour analyser les paramètres d’une chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="77176-246">To parse a query string's parameters:</span></span>

* <span data-ttu-id="77176-247">Ajoutez une référence de package pour [Microsoft. AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="77176-247">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="77176-248">Obtenez la valeur après l’analyse de la chaîne de requête avec <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="77176-248">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="77176-249">L’espace réservé `{KEY}` dans l’exemple précédent est la clé de paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="77176-249">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="77176-250">Par exemple, la paire clé-valeur `?ship=Tardis` d’URL utilise une clé de `ship` .</span><span class="sxs-lookup"><span data-stu-id="77176-250">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
