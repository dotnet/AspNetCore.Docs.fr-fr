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
ms.openlocfilehash: 051721b62397b582f1ffdaba08ffefe5d0c9ae03
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972013"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>BlazorVirtualisation des composants ASP.net Core

Par [Daniel Roth](https://github.com/danroth27)

Améliorez les performances perçues du rendu des composants à l’aide de la Blazor prise en charge intégrée de la virtualisation du Framework. La virtualisation est une technique qui permet de limiter le rendu de l’interface utilisateur uniquement aux parties qui sont actuellement visibles. Par exemple, la virtualisation est utile lorsque l’application doit afficher une longue liste d’éléments et que seul un sous-ensemble d’éléments doit être visible à un moment donné. Blazorfournit le [ `Virtualize` composant](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) qui peut être utilisé pour ajouter la virtualisation aux composants d’une application.

Sans virtualisation, une liste standard peut utiliser une boucle C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pour afficher chaque élément de la liste :

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

Si la liste contient des milliers d’éléments, le rendu de la liste peut prendre beaucoup de temps. L’utilisateur peut rencontrer un décalage de l’interface utilisateur notable.

Au lieu de restituer tous les éléments de la liste en même temps, remplacez la [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle par le `Virtualize` composant et spécifiez une source d’élément fixe avec <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> . Seuls les éléments qui sont actuellement visibles sont rendus :

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Si vous ne spécifiez pas de contexte pour le composant avec `Context` , utilisez la `context` valeur ( `@context.{PROPERTY}` ) dans le modèle de contenu de l’élément :

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

Le `Virtualize` composant calcule le nombre d’éléments à afficher en fonction de la hauteur du conteneur et de la taille des éléments rendus.

Le contenu de l’élément pour le `Virtualize` composant peut inclure les éléments suivants :

* Code HTML et Razor code brut, comme le montre l’exemple précédent.
* Un ou plusieurs Razor composants.
* Combinaison de composants HTML/ Razor et Razor .

## <a name="item-provider-delegate"></a>Délégué du fournisseur d’éléments

Si vous ne souhaitez pas charger tous les éléments dans la mémoire, vous pouvez spécifier une méthode déléguée du fournisseur items pour le paramètre du composant <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> qui récupère de manière asynchrone les éléments demandés à la demande :

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Le fournisseur items reçoit un <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> , qui spécifie le nombre d’éléments requis commençant à un index de début spécifique. Le fournisseur d’éléments récupère ensuite les éléments demandés à partir d’une base de données ou d’un autre service et les retourne sous la forme d’un, ainsi que du <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> nombre total d’éléments. Le fournisseur d’éléments peut choisir de récupérer les éléments avec chaque demande ou de les mettre en cache afin qu’ils soient facilement disponibles.

Un `Virtualize` composant ne peut accepter qu' **une seule source d’élément** à partir de ses paramètres. par conséquent, n’essayez pas d’utiliser simultanément un fournisseur d’éléments et d’affecter une collection à `Items` . Si les deux sont assignés, une <xref:System.InvalidOperationException> exception est levée lorsque les paramètres du composant sont définis au moment de l’exécution.

L’exemple suivant charge des employés à partir d’un `EmployeeService` :

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

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> indique au composant de redemander des données à partir de son <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> . Cela est utile lorsque des données externes sont modifiées. Il n’est pas nécessaire d’appeler cette valeur lors de l’utilisation de <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> .

## <a name="placeholder"></a>Espace réservé

Étant donné que la demande d’éléments à partir d’une source de données distante peut prendre un certain temps, vous avez la possibilité d’afficher un espace réservé avec le contenu de l’élément :

* Utilisez un <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> ( `<Placeholder>...</Placeholder>` ) pour afficher le contenu jusqu’à ce que les données de l’élément soient disponibles.
* Utilisez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> pour définir le modèle d’élément pour la liste.

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

## <a name="item-size"></a>Taille de l’élément

La taille de chaque élément en pixels peut être définie avec <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (valeur par défaut : 50px) :

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Nombre de suranalyses

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> détermine le nombre d’éléments supplémentaires qui sont rendus avant et après la zone visible. Ce paramètre permet de réduire la fréquence de rendu lors du défilement. Toutefois, des valeurs plus élevées entraînent le rendu d’un plus grand nombre d’éléments dans la page (valeur par défaut : 3) :

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Modifications d'état

Lorsque vous apportez des modifications aux éléments restitués par le `Virtualize` composant, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour forcer la réévaluation et le rerendu du composant.
