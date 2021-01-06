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
ms.openlocfilehash: 920a23aee0d0555e93c829142700709d5881afd2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753090"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="275b9-103">BlazorVirtualisation des composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="275b9-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="275b9-104">Par [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="275b9-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="275b9-105">Améliorez les performances perçues du rendu des composants à l’aide de la Blazor prise en charge intégrée de la virtualisation du Framework.</span><span class="sxs-lookup"><span data-stu-id="275b9-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="275b9-106">La virtualisation est une technique qui permet de limiter le rendu de l’interface utilisateur uniquement aux parties qui sont actuellement visibles.</span><span class="sxs-lookup"><span data-stu-id="275b9-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="275b9-107">Par exemple, la virtualisation est utile lorsque l’application doit afficher une longue liste d’éléments et que seul un sous-ensemble d’éléments doit être visible à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="275b9-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="275b9-108">Blazor fournit le `Virtualize` composant qui peut être utilisé pour ajouter la virtualisation aux composants d’une application.</span><span class="sxs-lookup"><span data-stu-id="275b9-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="275b9-109">Sans virtualisation, une liste standard peut utiliser une boucle C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pour afficher chaque élément de la liste :</span><span class="sxs-lookup"><span data-stu-id="275b9-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="275b9-110">Si la liste contient des milliers d’éléments, le rendu de la liste peut prendre beaucoup de temps.</span><span class="sxs-lookup"><span data-stu-id="275b9-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="275b9-111">L’utilisateur peut rencontrer un décalage de l’interface utilisateur notable.</span><span class="sxs-lookup"><span data-stu-id="275b9-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="275b9-112">Au lieu de restituer tous les éléments de la liste en même temps, remplacez la [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle par le `Virtualize` composant et spécifiez une source d’élément fixe avec `Items` .</span><span class="sxs-lookup"><span data-stu-id="275b9-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="275b9-113">Seuls les éléments qui sont actuellement visibles sont rendus :</span><span class="sxs-lookup"><span data-stu-id="275b9-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="275b9-114">Si vous ne spécifiez pas de contexte pour le composant avec `Context` , utilisez la `context` valeur ( `@context.{PROPERTY}` ) dans le modèle de contenu de l’élément :</span><span class="sxs-lookup"><span data-stu-id="275b9-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="275b9-115">Le `Virtualize` composant calcule le nombre d’éléments à afficher en fonction de la hauteur du conteneur et de la taille des éléments rendus.</span><span class="sxs-lookup"><span data-stu-id="275b9-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="275b9-116">Le contenu de l’élément pour le `Virtualize` composant peut inclure les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="275b9-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="275b9-117">Code HTML et Razor code brut, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="275b9-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="275b9-118">Un ou plusieurs Razor composants.</span><span class="sxs-lookup"><span data-stu-id="275b9-118">One or more Razor components.</span></span>
* <span data-ttu-id="275b9-119">Combinaison de composants HTML/ Razor et Razor .</span><span class="sxs-lookup"><span data-stu-id="275b9-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="275b9-120">Délégué du fournisseur d’éléments</span><span class="sxs-lookup"><span data-stu-id="275b9-120">Item provider delegate</span></span>

<span data-ttu-id="275b9-121">Si vous ne souhaitez pas charger tous les éléments dans la mémoire, vous pouvez spécifier une méthode déléguée du fournisseur items pour le paramètre du composant `ItemsProvider` qui récupère de manière asynchrone les éléments demandés à la demande :</span><span class="sxs-lookup"><span data-stu-id="275b9-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="275b9-122">Le fournisseur items reçoit un `ItemsProviderRequest` , qui spécifie le nombre d’éléments requis commençant à un index de début spécifique.</span><span class="sxs-lookup"><span data-stu-id="275b9-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="275b9-123">Le fournisseur d’éléments récupère ensuite les éléments demandés à partir d’une base de données ou d’un autre service et les retourne sous la forme d’un, ainsi que du `ItemsProviderResult<TItem>` nombre total d’éléments.</span><span class="sxs-lookup"><span data-stu-id="275b9-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="275b9-124">Le fournisseur d’éléments peut choisir de récupérer les éléments avec chaque demande ou de les mettre en cache afin qu’ils soient facilement disponibles.</span><span class="sxs-lookup"><span data-stu-id="275b9-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="275b9-125">Un `Virtualize` composant ne peut accepter qu' **une seule source d’élément** à partir de ses paramètres. par conséquent, n’essayez pas d’utiliser simultanément un fournisseur d’éléments et d’affecter une collection à `Items` .</span><span class="sxs-lookup"><span data-stu-id="275b9-125">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="275b9-126">Si les deux sont assignés, une <xref:System.InvalidOperationException> exception est levée lorsque les paramètres du composant sont définis au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="275b9-126">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="275b9-127">L’exemple suivant charge des employés à partir d’un `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="275b9-127">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="275b9-128">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="275b9-128">Placeholder</span></span>

<span data-ttu-id="275b9-129">Étant donné que la demande d’éléments à partir d’une source de données distante peut prendre un certain temps, vous avez la possibilité d’afficher un espace réservé ( `<Placeholder>...</Placeholder>` ) jusqu’à ce que les données de l’élément soient disponibles :</span><span class="sxs-lookup"><span data-stu-id="275b9-129">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="275b9-130">Taille de l’élément</span><span class="sxs-lookup"><span data-stu-id="275b9-130">Item size</span></span>

<span data-ttu-id="275b9-131">La taille de chaque élément en pixels peut être définie avec `ItemSize` (valeur par défaut : 50px) :</span><span class="sxs-lookup"><span data-stu-id="275b9-131">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="275b9-132">Nombre de suranalyses</span><span class="sxs-lookup"><span data-stu-id="275b9-132">Overscan count</span></span>

<span data-ttu-id="275b9-133">`OverscanCount` détermine le nombre d’éléments supplémentaires qui sont rendus avant et après la zone visible.</span><span class="sxs-lookup"><span data-stu-id="275b9-133">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="275b9-134">Ce paramètre permet de réduire la fréquence de rendu lors du défilement.</span><span class="sxs-lookup"><span data-stu-id="275b9-134">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="275b9-135">Toutefois, des valeurs plus élevées entraînent le rendu d’un plus grand nombre d’éléments dans la page (valeur par défaut : 3) :</span><span class="sxs-lookup"><span data-stu-id="275b9-135">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="275b9-136">Modifications d'état</span><span class="sxs-lookup"><span data-stu-id="275b9-136">State changes</span></span>

<span data-ttu-id="275b9-137">Lorsque vous apportez des modifications aux éléments restitués par le `Virtualize` composant, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour forcer la réévaluation et le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="275b9-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
