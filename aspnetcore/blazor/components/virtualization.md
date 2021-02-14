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
# <a name="aspnet-core-blazor-component-virtualization"></a>BlazorVirtualisation des composants ASP.net Core

Améliorez les performances perçues du rendu des composants à l’aide de la Blazor prise en charge intégrée de la virtualisation du Framework. La virtualisation est une technique qui permet de limiter le rendu de l’interface utilisateur uniquement aux parties qui sont actuellement visibles. Par exemple, la virtualisation est utile lorsque l’application doit afficher une longue liste d’éléments et que seul un sous-ensemble d’éléments doit être visible à un moment donné. Blazorfournit le [ `Virtualize` composant](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) qui peut être utilisé pour ajouter la virtualisation aux composants d’une application.

Le `Virtualize` composant peut être utilisé dans les cas suivants :

* Rendu d’un ensemble d’éléments de données dans une boucle.
* La plupart des éléments ne sont pas visibles en raison du défilement.
* Les éléments rendus ont exactement la même taille. Lorsque l’utilisateur fait défiler jusqu’à un point arbitraire, le composant peut calculer les éléments visibles à afficher.

Sans virtualisation, une liste standard peut utiliser une boucle C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pour afficher chaque élément de la liste :

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

Si la liste contient des milliers d’éléments, le rendu de la liste peut prendre beaucoup de temps. L’utilisateur peut rencontrer un décalage de l’interface utilisateur notable.

Au lieu de restituer tous les éléments de la liste en même temps, remplacez la [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle par le `Virtualize` composant et spécifiez une source d’élément fixe avec <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> . Seuls les éléments qui sont actuellement visibles sont rendus :

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

Si vous ne spécifiez pas de contexte pour le composant avec `Context` , utilisez la `context` valeur dans le modèle de contenu de l’élément :

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> Le processus de mappage des objets de modèle aux éléments et aux composants peut être contrôlé à l’aide de l' [`@key`](xref:mvc/views/razor#key) attribut directive. `@key` force l’algorithme de comparaison à garantir la préservation des éléments ou des composants en fonction de la valeur de la clé.
>
> Pour plus d’informations, consultez les articles suivants :
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [Razor Référence de syntaxe pour ASP.NET Core](xref:mvc/views/razor#key)

Le `Virtualize` composant :

* Calcule le nombre d’éléments à afficher en fonction de la hauteur du conteneur et de la taille des éléments rendus.
* Recalcule et restitue les éléments à mesure que l’utilisateur fait défiler.
* Extrait uniquement la tranche d’enregistrements d’une API externe qui correspond à la région visible actuelle, au lieu de télécharger toutes les données de la collection.

Le contenu de l’élément pour le `Virtualize` composant peut inclure les éléments suivants :

* Code HTML et Razor code brut, comme le montre l’exemple précédent.
* Un ou plusieurs Razor composants.
* Combinaison de composants HTML/ Razor et Razor .

## <a name="item-provider-delegate"></a>Délégué du fournisseur d’éléments

Si vous ne souhaitez pas charger tous les éléments dans la mémoire, vous pouvez spécifier une méthode déléguée du fournisseur items pour le paramètre du composant <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> qui récupère de manière asynchrone les éléments demandés à la demande. Dans l’exemple suivant, la `LoadEmployees` méthode fournit les éléments au `Virtualize` composant :

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

L' `LoadEmployees` exemple de méthode suivant charge des employés à partir d’un `EmployeeService` (non affiché) :

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

La hauteur de chaque élément en pixels peut être définie avec <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (valeur par défaut : 50) :

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

Par défaut, le `Virtualize` composant mesure la taille réelle du rendu *une fois* le rendu initial effectué. Utilisez <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> pour fournir une taille d’élément exacte à l’avance afin d’aider à obtenir des performances de rendu initiales précises et à garantir la position de défilement correcte pour les rechargements de pages. Si la valeur par défaut <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> provoque le rendu de certains éléments en dehors de l’affichage actuellement visible, un deuxième rendu est déclenché. Pour conserver correctement la position de défilement du navigateur dans une liste virtualisée, le rendu initial doit être correct. Si ce n’est pas le cas, les utilisateurs peuvent afficher les éléments incorrects. 

## <a name="overscan-count"></a>Nombre de suranalyses

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> détermine le nombre d’éléments supplémentaires qui sont rendus avant et après la zone visible. Ce paramètre permet de réduire la fréquence de rendu lors du défilement. Toutefois, des valeurs plus élevées entraînent le rendu d’un plus grand nombre d’éléments dans la page (valeur par défaut : 3) :

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Modifications d'état

Lorsque vous apportez des modifications aux éléments restitués par le `Virtualize` composant, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour forcer la réévaluation et le rerendu du composant. Pour plus d’informations, consultez <xref:blazor/components/rendering>.
