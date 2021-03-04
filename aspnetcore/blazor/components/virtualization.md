---
title: BlazorVirtualisation des composants ASP.net Core
author: guardrex
description: Découvrez comment utiliser la virtualisation de composants dans des Blazor applications ASP.net core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2021
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
uid: blazor/components/virtualization
ms.openlocfilehash: c81732c29b262e9134a4ff7dab077a4f31db96af
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109817"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="d5da0-103">BlazorVirtualisation des composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="d5da0-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="d5da0-104">Améliorez les performances perçues du rendu des composants à l’aide de la Blazor prise en charge de la virtualisation intégrée du Framework avec le [ `Virtualize` composant](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601).</span><span class="sxs-lookup"><span data-stu-id="d5da0-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support with the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601).</span></span> <span data-ttu-id="d5da0-105">La virtualisation est une technique qui permet de limiter le rendu de l’interface utilisateur uniquement aux parties qui sont actuellement visibles.</span><span class="sxs-lookup"><span data-stu-id="d5da0-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="d5da0-106">Par exemple, la virtualisation est utile lorsque l’application doit afficher une longue liste d’éléments et que seul un sous-ensemble d’éléments doit être visible à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="d5da0-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span>

<span data-ttu-id="d5da0-107">Utilisez le `Virtualize` composant lorsque :</span><span class="sxs-lookup"><span data-stu-id="d5da0-107">Use the `Virtualize` component when:</span></span>

* <span data-ttu-id="d5da0-108">Rendu d’un ensemble d’éléments de données dans une boucle.</span><span class="sxs-lookup"><span data-stu-id="d5da0-108">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="d5da0-109">La plupart des éléments ne sont pas visibles en raison du défilement.</span><span class="sxs-lookup"><span data-stu-id="d5da0-109">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="d5da0-110">Les éléments rendus sont de la même taille.</span><span class="sxs-lookup"><span data-stu-id="d5da0-110">The rendered items are the same size.</span></span>

<span data-ttu-id="d5da0-111">Lorsque l’utilisateur fait défiler jusqu’à un point arbitraire dans la `Virtualize` liste d’éléments du composant, le composant calcule les éléments visibles à afficher.</span><span class="sxs-lookup"><span data-stu-id="d5da0-111">When the user scrolls to an arbitrary point in the `Virtualize` component's list of items, the component calculates the visible items to show.</span></span> <span data-ttu-id="d5da0-112">Les éléments non visibles ne sont pas rendus.</span><span class="sxs-lookup"><span data-stu-id="d5da0-112">Unseen items aren't rendered.</span></span>

<span data-ttu-id="d5da0-113">Sans virtualisation, une liste standard peut utiliser une boucle C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pour restituer chaque élément dans une liste.</span><span class="sxs-lookup"><span data-stu-id="d5da0-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in a list.</span></span> <span data-ttu-id="d5da0-114">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d5da0-114">In the following example:</span></span>

* <span data-ttu-id="d5da0-115">`allFlights` est une collection de vols d’avion.</span><span class="sxs-lookup"><span data-stu-id="d5da0-115">`allFlights` is a collection of airplane flights.</span></span>
* <span data-ttu-id="d5da0-116">Le `FlightSummary` composant affiche des détails sur chaque vol.</span><span class="sxs-lookup"><span data-stu-id="d5da0-116">The `FlightSummary` component displays details about each flight.</span></span>
* <span data-ttu-id="d5da0-117">L' [ `@key` attribut directive](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components) conserve la relation de chaque `FlightSummary` composant avec son vol rendu par le du vol `FlightId` .</span><span class="sxs-lookup"><span data-stu-id="d5da0-117">The [`@key` directive attribute](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components) preserves the relationship of each `FlightSummary` component to its rendered flight by the flight's `FlightId`.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="d5da0-118">Si la collection contient des milliers de vols, le rendu des vols prend beaucoup de temps et les utilisateurs rencontrent un retard notable de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d5da0-118">If the collection contains thousands of flights, rendering the flights takes a long time and users experience a noticeable UI lag.</span></span> <span data-ttu-id="d5da0-119">La plupart des vols ne sont pas rendus, car ils sont en dehors de la hauteur de l' `<div>` élément.</span><span class="sxs-lookup"><span data-stu-id="d5da0-119">Most of the flights aren't rendered because they fall outside of the height of the `<div>` element.</span></span>

<span data-ttu-id="d5da0-120">Au lieu de rendre l’intégralité de la liste des vols à la fois, remplacez la [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle de l’exemple précédent par le `Virtualize` composant :</span><span class="sxs-lookup"><span data-stu-id="d5da0-120">Instead of rendering the entire list of flights at once, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop in the preceding example with the `Virtualize` component:</span></span>

* <span data-ttu-id="d5da0-121">Spécifiez `allFlights` comme source d’élément fixe à <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d5da0-121">Specify `allFlights` as a fixed item source to <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d5da0-122">Seuls les vols actuellement visibles sont rendus par le `Virtualize` composant.</span><span class="sxs-lookup"><span data-stu-id="d5da0-122">Only the currently visible flights are rendered by the `Virtualize` component.</span></span>
* <span data-ttu-id="d5da0-123">Spécifiez un contexte pour chaque vol avec le `Context` paramètre.</span><span class="sxs-lookup"><span data-stu-id="d5da0-123">Specify a context for each flight with the `Context` parameter.</span></span> <span data-ttu-id="d5da0-124">Dans l’exemple suivant, `flight` est utilisé comme contexte, qui fournit l’accès aux membres de chaque vol.</span><span class="sxs-lookup"><span data-stu-id="d5da0-124">In the following example, `flight` is used as the context, which provides access to each flight's members.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="d5da0-125">Si aucun contexte n’est spécifié avec le `Context` paramètre, utilisez la valeur de `context` dans le modèle de contenu de l’élément pour accéder aux membres de chaque vol :</span><span class="sxs-lookup"><span data-stu-id="d5da0-125">If a context isn't specified with the `Context` parameter, use the value of `context` in the item content template to access each flight's members:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="d5da0-126">Le `Virtualize` composant :</span><span class="sxs-lookup"><span data-stu-id="d5da0-126">The `Virtualize` component:</span></span>

* <span data-ttu-id="d5da0-127">Calcule le nombre d’éléments à afficher en fonction de la hauteur du conteneur et de la taille des éléments rendus.</span><span class="sxs-lookup"><span data-stu-id="d5da0-127">Calculates the number of items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="d5da0-128">Recalcule et restitue à nouveau les éléments au fur et à mesure que l’utilisateur fait défiler.</span><span class="sxs-lookup"><span data-stu-id="d5da0-128">Recalculates and rerenders the items as the user scrolls.</span></span>
* <span data-ttu-id="d5da0-129">Extrait uniquement la tranche d’enregistrements d’une API externe qui correspond à la région visible actuelle, au lieu de télécharger toutes les données de la collection.</span><span class="sxs-lookup"><span data-stu-id="d5da0-129">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="d5da0-130">Le contenu de l’élément pour le `Virtualize` composant peut inclure les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="d5da0-130">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="d5da0-131">Code HTML et Razor code brut, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="d5da0-131">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="d5da0-132">Un ou plusieurs Razor composants.</span><span class="sxs-lookup"><span data-stu-id="d5da0-132">One or more Razor components.</span></span>
* <span data-ttu-id="d5da0-133">Combinaison de composants HTML/ Razor et Razor .</span><span class="sxs-lookup"><span data-stu-id="d5da0-133">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="d5da0-134">Délégué du fournisseur d’éléments</span><span class="sxs-lookup"><span data-stu-id="d5da0-134">Item provider delegate</span></span>

<span data-ttu-id="d5da0-135">Si vous ne souhaitez pas charger tous les éléments dans la mémoire, vous pouvez spécifier une méthode déléguée du fournisseur items pour le paramètre du composant <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> qui récupère de manière asynchrone les éléments demandés à la demande.</span><span class="sxs-lookup"><span data-stu-id="d5da0-135">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="d5da0-136">Dans l’exemple suivant, la `LoadEmployees` méthode fournit les éléments au `Virtualize` composant :</span><span class="sxs-lookup"><span data-stu-id="d5da0-136">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="d5da0-137">Le fournisseur items reçoit un <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , qui spécifie le nombre d’éléments requis commençant à un index de début spécifique.</span><span class="sxs-lookup"><span data-stu-id="d5da0-137">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="d5da0-138">Le fournisseur d’éléments récupère ensuite les éléments demandés à partir d’une base de données ou d’un autre service et les retourne sous la forme d’un, ainsi que du <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> nombre total d’éléments.</span><span class="sxs-lookup"><span data-stu-id="d5da0-138">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="d5da0-139">Le fournisseur d’éléments peut choisir de récupérer les éléments avec chaque demande ou de les mettre en cache afin qu’ils soient facilement disponibles.</span><span class="sxs-lookup"><span data-stu-id="d5da0-139">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="d5da0-140">Un `Virtualize` composant ne peut accepter qu' **une seule source d’élément** à partir de ses paramètres. par conséquent, n’essayez pas d’utiliser simultanément un fournisseur d’éléments et d’affecter une collection à `Items` .</span><span class="sxs-lookup"><span data-stu-id="d5da0-140">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="d5da0-141">Si les deux sont assignés, une <xref:System.InvalidOperationException> exception est levée lorsque les paramètres du composant sont définis au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="d5da0-141">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="d5da0-142">L' `LoadEmployees` exemple de méthode suivant charge des employés à partir d’un `EmployeeService` (non affiché) :</span><span class="sxs-lookup"><span data-stu-id="d5da0-142">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<span data-ttu-id="d5da0-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> indique au composant de redemander des données à partir de son <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> .</span><span class="sxs-lookup"><span data-stu-id="d5da0-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="d5da0-144">Cela est utile lorsque des données externes sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="d5da0-144">This is useful when external data changes.</span></span> <span data-ttu-id="d5da0-145">Il n’est pas nécessaire d’appeler <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> lorsque vous utilisez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .</span><span class="sxs-lookup"><span data-stu-id="d5da0-145">There's no need to call <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="d5da0-146">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="d5da0-146">Placeholder</span></span>

<span data-ttu-id="d5da0-147">Étant donné que la demande d’éléments à partir d’une source de données distante peut prendre un certain temps, vous avez la possibilité d’afficher un espace réservé avec le contenu de l’élément :</span><span class="sxs-lookup"><span data-stu-id="d5da0-147">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="d5da0-148">Utilisez un <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ) pour afficher le contenu jusqu’à ce que les données de l’élément soient disponibles.</span><span class="sxs-lookup"><span data-stu-id="d5da0-148">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="d5da0-149">Utilisez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> pour définir le modèle d’élément pour la liste.</span><span class="sxs-lookup"><span data-stu-id="d5da0-149">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="d5da0-150">Taille de l’élément</span><span class="sxs-lookup"><span data-stu-id="d5da0-150">Item size</span></span>

<span data-ttu-id="d5da0-151">La hauteur de chaque élément en pixels peut être définie avec <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (valeur par défaut : 50).</span><span class="sxs-lookup"><span data-stu-id="d5da0-151">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50).</span></span> <span data-ttu-id="d5da0-152">L’exemple suivant modifie la hauteur de chaque élément de 50 pixels par défaut à 25 pixels :</span><span class="sxs-lookup"><span data-stu-id="d5da0-152">The following example changes the height of each item from the default of 50 pixels to 25 pixels:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="d5da0-153">Par défaut, le `Virtualize` composant mesure la taille de rendu (hauteur) des éléments individuels *une fois* le rendu initial effectué.</span><span class="sxs-lookup"><span data-stu-id="d5da0-153">By default, the `Virtualize` component measures the rendering size (height) of individual items *after* the initial render occurs.</span></span> <span data-ttu-id="d5da0-154">Utilisez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> pour fournir une taille d’élément exacte à l’avance afin d’aider à obtenir des performances de rendu initiales précises et à garantir la position de défilement correcte pour les rechargements de pages.</span><span class="sxs-lookup"><span data-stu-id="d5da0-154">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="d5da0-155">Si la valeur par défaut <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> provoque le rendu de certains éléments en dehors de l’affichage actuellement visible, un deuxième rendu est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d5da0-155">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="d5da0-156">Pour conserver correctement la position de défilement du navigateur dans une liste virtualisée, le rendu initial doit être correct.</span><span class="sxs-lookup"><span data-stu-id="d5da0-156">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="d5da0-157">Si ce n’est pas le cas, les utilisateurs peuvent afficher les éléments incorrects.</span><span class="sxs-lookup"><span data-stu-id="d5da0-157">If not, users might view the wrong items.</span></span>

## <a name="overscan-count"></a><span data-ttu-id="d5da0-158">Nombre de suranalyses</span><span class="sxs-lookup"><span data-stu-id="d5da0-158">Overscan count</span></span>

<span data-ttu-id="d5da0-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> détermine le nombre d’éléments supplémentaires qui sont rendus avant et après la zone visible.</span><span class="sxs-lookup"><span data-stu-id="d5da0-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="d5da0-160">Ce paramètre permet de réduire la fréquence de rendu lors du défilement.</span><span class="sxs-lookup"><span data-stu-id="d5da0-160">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="d5da0-161">Toutefois, des valeurs plus élevées entraînent le rendu d’un plus grand nombre d’éléments dans la page (valeur par défaut : 3).</span><span class="sxs-lookup"><span data-stu-id="d5da0-161">However, higher values result in more elements rendered in the page (default: 3).</span></span> <span data-ttu-id="d5da0-162">L’exemple suivant remplace le nombre de suranalyses par défaut de trois éléments par quatre éléments :</span><span class="sxs-lookup"><span data-stu-id="d5da0-162">The following example changes the overscan count from the default of three items to four items:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="d5da0-163">Modifications d'état</span><span class="sxs-lookup"><span data-stu-id="d5da0-163">State changes</span></span>

<span data-ttu-id="d5da0-164">Lorsque vous apportez des modifications aux éléments restitués par le `Virtualize` composant, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour forcer la réévaluation et le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="d5da0-164">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="d5da0-165">Pour plus d’informations, consultez <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="d5da0-165">For more information, see <xref:blazor/components/rendering>.</span></span>
