---
title: Composants ASP.NET Core basés sur un Blazor modèle
author: guardrex
description: Découvrez comment les composants basés sur des modèles peuvent accepter un ou plusieurs modèles d’interface utilisateur en tant que paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: 579cabd9e6b7141ec6af4c6e221b805272a2fe40
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280033"
---
# <a name="aspnet-core-blazor-templated-components"></a>Composants ASP.NET Core basés sur un Blazor modèle

Les composants basés sur un modèle sont des composants qui acceptent un ou plusieurs modèles d’interface utilisateur en tant que paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant. Les composants basés sur un modèle vous permettent de créer des composants de niveau supérieur qui sont plus réutilisables que les composants normaux. Voici quelques exemples :

* Composant de table qui permet à un utilisateur de spécifier des modèles pour l’en-tête, les lignes et le pied de page de la table.
* Composant de liste qui permet à un utilisateur de spécifier un modèle pour afficher des éléments dans une liste.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="template-parameters"></a>Paramètres de modèle

Un composant basé sur un modèle est défini en spécifiant un ou plusieurs paramètres de composant de type <xref:Microsoft.AspNetCore.Components.RenderFragment> ou <xref:Microsoft.AspNetCore.Components.RenderFragment%601> . Un fragment de rendu représente un segment de l’interface utilisateur à restituer. <xref:Microsoft.AspNetCore.Components.RenderFragment%601> prend un paramètre de type qui peut être spécifié lors de l’appel du fragment de rendu.

`TableTemplate` composant ( `TableTemplate.razor` ) :

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Lorsque vous utilisez un composant basé sur un modèle, les paramètres de modèle peuvent être spécifiés à l’aide d’éléments enfants qui correspondent aux noms des paramètres ( `TableHeader` et `RowTemplate` dans l’exemple suivant) :

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> Les contraintes de type générique seront prises en charge dans une version ultérieure. Pour plus d’informations, consultez [autoriser les contraintes de type générique (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Paramètres de contexte de modèle

Les arguments de composant de type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passé comme éléments ont un paramètre implicite nommé `context` (par exemple, à partir de l’exemple de code précédent, `@context.PetId` ), mais vous pouvez modifier le nom de paramètre à l’aide de l' `Context` attribut sur l’élément enfant. Dans l’exemple suivant, l' `RowTemplate` attribut de l’élément `Context` spécifie le `pet` paramètre :

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

Vous pouvez également spécifier l' `Context` attribut sur l’élément de composant. L' `Context` attribut spécifié s’applique à tous les paramètres de modèle spécifiés. Cela peut être utile lorsque vous souhaitez spécifier le nom du paramètre de contenu pour le contenu enfant implicite (sans élément enfant d’encapsulation). Dans l’exemple suivant, l' `Context` attribut apparaît sur l' `TableTemplate` élément et s’applique à tous les paramètres de modèle :

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

## <a name="generic-typed-components"></a>Composants génériques

Les composants basés sur un modèle sont souvent typés de façon générique. Par exemple, un `ListViewTemplate` composant générique ( `ListViewTemplate.razor` ) peut être utilisé pour restituer des `IEnumerable<T>` valeurs. Pour définir un composant générique, utilisez la [`@typeparam`](xref:mvc/views/razor#typeparam) directive pour spécifier les paramètres de type :

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Lorsque vous utilisez des composants génériques, le paramètre de type est déduit si possible :

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Sinon, le paramètre de type doit être spécifié explicitement à l’aide d’un attribut qui correspond au nom du paramètre de type. Dans l’exemple suivant, `TItem="Pet"` spécifie le type :

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
