---
title: BlazorVirtualisation des composants ASP.net Core
author: guardrex
description: Découvrez comment utiliser la virtualisation de composants dans des Blazor applications ASP.net core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
ms.openlocfilehash: d9fc767a4b5160c616053b075ba92194bcffa275
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280020"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="a6c53-103">BlazorVirtualisation des composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="a6c53-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="a6c53-104">Améliorez les performances perçues du rendu des composants à l’aide de la Blazor prise en charge intégrée de la virtualisation du Framework.</span><span class="sxs-lookup"><span data-stu-id="a6c53-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="a6c53-105">La virtualisation est une technique qui permet de limiter le rendu de l’interface utilisateur uniquement aux parties qui sont actuellement visibles.</span><span class="sxs-lookup"><span data-stu-id="a6c53-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="a6c53-106">Par exemple, la virtualisation est utile lorsque l’application doit afficher une longue liste d’éléments et que seul un sous-ensemble d’éléments doit être visible à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="a6c53-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="a6c53-107">Blazorfournit le [ `Virtualize` composant](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) qui peut être utilisé pour ajouter la virtualisation aux composants d’une application.</span><span class="sxs-lookup"><span data-stu-id="a6c53-107">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="a6c53-108">Le `Virtualize` composant peut être utilisé dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="a6c53-108">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="a6c53-109">Rendu d’un ensemble d’éléments de données dans une boucle.</span><span class="sxs-lookup"><span data-stu-id="a6c53-109">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="a6c53-110">La plupart des éléments ne sont pas visibles en raison du défilement.</span><span class="sxs-lookup"><span data-stu-id="a6c53-110">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="a6c53-111">Les éléments rendus ont exactement la même taille.</span><span class="sxs-lookup"><span data-stu-id="a6c53-111">The rendered items are exactly the same size.</span></span> <span data-ttu-id="a6c53-112">Lorsque l’utilisateur fait défiler jusqu’à un point arbitraire, le composant peut calculer les éléments visibles à afficher.</span><span class="sxs-lookup"><span data-stu-id="a6c53-112">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="a6c53-113">Sans virtualisation, une liste standard peut utiliser une boucle C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pour afficher chaque élément de la liste :</span><span class="sxs-lookup"><span data-stu-id="a6c53-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="a6c53-114">Si la liste contient des milliers d’éléments, le rendu de la liste peut prendre beaucoup de temps.</span><span class="sxs-lookup"><span data-stu-id="a6c53-114">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="a6c53-115">L’utilisateur peut rencontrer un décalage de l’interface utilisateur notable.</span><span class="sxs-lookup"><span data-stu-id="a6c53-115">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="a6c53-116">Au lieu de restituer tous les éléments de la liste en même temps, remplacez la [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle par le `Virtualize` composant et spécifiez une source d’élément fixe avec <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a6c53-116">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a6c53-117">Seuls les éléments qui sont actuellement visibles sont rendus :</span><span class="sxs-lookup"><span data-stu-id="a6c53-117">Only the items that are currently visible are rendered:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="a6c53-118">Si vous ne spécifiez pas de contexte pour le composant avec `Context` , utilisez la `context` valeur dans le modèle de contenu de l’élément :</span><span class="sxs-lookup"><span data-stu-id="a6c53-118">If not specifying a context to the component with `Context`, use the `context` value in the item content template:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="a6c53-119">Le processus de mappage des objets de modèle aux éléments et aux composants peut être contrôlé à l’aide de l' [`@key`](xref:mvc/views/razor#key) attribut directive.</span><span class="sxs-lookup"><span data-stu-id="a6c53-119">The mapping process of model objects to elements and components can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="a6c53-120">`@key` force l’algorithme de comparaison à garantir la préservation des éléments ou des composants en fonction de la valeur de la clé.</span><span class="sxs-lookup"><span data-stu-id="a6c53-120">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="a6c53-121">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="a6c53-121">For more information, see the following articles:</span></span>
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [<span data-ttu-id="a6c53-122">Razor Référence de syntaxe pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6c53-122">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor#key)

<span data-ttu-id="a6c53-123">Le `Virtualize` composant :</span><span class="sxs-lookup"><span data-stu-id="a6c53-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="a6c53-124">Calcule le nombre d’éléments à afficher en fonction de la hauteur du conteneur et de la taille des éléments rendus.</span><span class="sxs-lookup"><span data-stu-id="a6c53-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="a6c53-125">Recalcule et restitue les éléments à mesure que l’utilisateur fait défiler.</span><span class="sxs-lookup"><span data-stu-id="a6c53-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="a6c53-126">Extrait uniquement la tranche d’enregistrements d’une API externe qui correspond à la région visible actuelle, au lieu de télécharger toutes les données de la collection.</span><span class="sxs-lookup"><span data-stu-id="a6c53-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="a6c53-127">Le contenu de l’élément pour le `Virtualize` composant peut inclure les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="a6c53-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="a6c53-128">Code HTML et Razor code brut, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="a6c53-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="a6c53-129">Un ou plusieurs Razor composants.</span><span class="sxs-lookup"><span data-stu-id="a6c53-129">One or more Razor components.</span></span>
* <span data-ttu-id="a6c53-130">Combinaison de composants HTML/ Razor et Razor .</span><span class="sxs-lookup"><span data-stu-id="a6c53-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="a6c53-131">Délégué du fournisseur d’éléments</span><span class="sxs-lookup"><span data-stu-id="a6c53-131">Item provider delegate</span></span>

<span data-ttu-id="a6c53-132">Si vous ne souhaitez pas charger tous les éléments dans la mémoire, vous pouvez spécifier une méthode déléguée du fournisseur items pour le paramètre du composant <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> qui récupère de manière asynchrone les éléments demandés à la demande.</span><span class="sxs-lookup"><span data-stu-id="a6c53-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="a6c53-133">Dans l’exemple suivant, la `LoadEmployees` méthode fournit les éléments au `Virtualize` composant :</span><span class="sxs-lookup"><span data-stu-id="a6c53-133">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="a6c53-134">Le fournisseur items reçoit un <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , qui spécifie le nombre d’éléments requis commençant à un index de début spécifique.</span><span class="sxs-lookup"><span data-stu-id="a6c53-134">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="a6c53-135">Le fournisseur d’éléments récupère ensuite les éléments demandés à partir d’une base de données ou d’un autre service et les retourne sous la forme d’un, ainsi que du <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> nombre total d’éléments.</span><span class="sxs-lookup"><span data-stu-id="a6c53-135">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="a6c53-136">Le fournisseur d’éléments peut choisir de récupérer les éléments avec chaque demande ou de les mettre en cache afin qu’ils soient facilement disponibles.</span><span class="sxs-lookup"><span data-stu-id="a6c53-136">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="a6c53-137">Un `Virtualize` composant ne peut accepter qu' **une seule source d’élément** à partir de ses paramètres. par conséquent, n’essayez pas d’utiliser simultanément un fournisseur d’éléments et d’affecter une collection à `Items` .</span><span class="sxs-lookup"><span data-stu-id="a6c53-137">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="a6c53-138">Si les deux sont assignés, une <xref:System.InvalidOperationException> exception est levée lorsque les paramètres du composant sont définis au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="a6c53-138">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="a6c53-139">L' `LoadEmployees` exemple de méthode suivant charge des employés à partir d’un `EmployeeService` (non affiché) :</span><span class="sxs-lookup"><span data-stu-id="a6c53-139">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

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

<span data-ttu-id="a6c53-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> indique au composant de redemander des données à partir de son <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> .</span><span class="sxs-lookup"><span data-stu-id="a6c53-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="a6c53-141">Cela est utile lorsque des données externes sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="a6c53-141">This is useful when external data changes.</span></span> <span data-ttu-id="a6c53-142">Il n’est pas nécessaire d’appeler cette valeur lors de l’utilisation de <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .</span><span class="sxs-lookup"><span data-stu-id="a6c53-142">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="a6c53-143">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="a6c53-143">Placeholder</span></span>

<span data-ttu-id="a6c53-144">Étant donné que la demande d’éléments à partir d’une source de données distante peut prendre un certain temps, vous avez la possibilité d’afficher un espace réservé avec le contenu de l’élément :</span><span class="sxs-lookup"><span data-stu-id="a6c53-144">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="a6c53-145">Utilisez un <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ) pour afficher le contenu jusqu’à ce que les données de l’élément soient disponibles.</span><span class="sxs-lookup"><span data-stu-id="a6c53-145">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="a6c53-146">Utilisez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> pour définir le modèle d’élément pour la liste.</span><span class="sxs-lookup"><span data-stu-id="a6c53-146">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="a6c53-147">Taille de l’élément</span><span class="sxs-lookup"><span data-stu-id="a6c53-147">Item size</span></span>

<span data-ttu-id="a6c53-148">La hauteur de chaque élément en pixels peut être définie avec <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (valeur par défaut : 50) :</span><span class="sxs-lookup"><span data-stu-id="a6c53-148">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="a6c53-149">Par défaut, le `Virtualize` composant mesure la taille réelle du rendu *une fois* le rendu initial effectué.</span><span class="sxs-lookup"><span data-stu-id="a6c53-149">By default, the `Virtualize` component measures the actual rendering size *after* the initial render occurs.</span></span> <span data-ttu-id="a6c53-150">Utilisez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> pour fournir une taille d’élément exacte à l’avance afin d’aider à obtenir des performances de rendu initiales précises et à garantir la position de défilement correcte pour les rechargements de pages.</span><span class="sxs-lookup"><span data-stu-id="a6c53-150">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="a6c53-151">Si la valeur par défaut <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> provoque le rendu de certains éléments en dehors de l’affichage actuellement visible, un deuxième rendu est déclenché.</span><span class="sxs-lookup"><span data-stu-id="a6c53-151">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="a6c53-152">Pour conserver correctement la position de défilement du navigateur dans une liste virtualisée, le rendu initial doit être correct.</span><span class="sxs-lookup"><span data-stu-id="a6c53-152">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="a6c53-153">Si ce n’est pas le cas, les utilisateurs peuvent afficher les éléments incorrects.</span><span class="sxs-lookup"><span data-stu-id="a6c53-153">If not, users might view the wrong items.</span></span> 

## <a name="overscan-count"></a><span data-ttu-id="a6c53-154">Nombre de suranalyses</span><span class="sxs-lookup"><span data-stu-id="a6c53-154">Overscan count</span></span>

<span data-ttu-id="a6c53-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> détermine le nombre d’éléments supplémentaires qui sont rendus avant et après la zone visible.</span><span class="sxs-lookup"><span data-stu-id="a6c53-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="a6c53-156">Ce paramètre permet de réduire la fréquence de rendu lors du défilement.</span><span class="sxs-lookup"><span data-stu-id="a6c53-156">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="a6c53-157">Toutefois, des valeurs plus élevées entraînent le rendu d’un plus grand nombre d’éléments dans la page (valeur par défaut : 3) :</span><span class="sxs-lookup"><span data-stu-id="a6c53-157">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="a6c53-158">Modifications d'état</span><span class="sxs-lookup"><span data-stu-id="a6c53-158">State changes</span></span>

<span data-ttu-id="a6c53-159">Lorsque vous apportez des modifications aux éléments restitués par le `Virtualize` composant, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour forcer la réévaluation et le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="a6c53-159">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="a6c53-160">Pour plus d’informations, consultez <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="a6c53-160">For more information, see <xref:blazor/components/rendering>.</span></span>
