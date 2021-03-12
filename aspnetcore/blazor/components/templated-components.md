---
title: Composants ASP.NET Core basés sur un Blazor modèle
author: guardrex
description: Découvrez comment les composants basés sur des modèles peuvent accepter un ou plusieurs modèles d’interface utilisateur en tant que paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/04/2021
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
ms.openlocfilehash: 6c94218f3808baca18f23a53688bafdd6354e760
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589476"
---
# <a name="aspnet-core-blazor-templated-components"></a>Composants ASP.NET Core basés sur un Blazor modèle

Les composants basés sur un modèle sont des composants qui acceptent un ou plusieurs modèles d’interface utilisateur en tant que paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant. Les composants basés sur un modèle vous permettent de créer des composants de niveau supérieur qui sont plus réutilisables que les composants normaux. Voici quelques exemples :

* Composant de table qui permet à un utilisateur de spécifier des modèles pour l’en-tête, les lignes et le pied de page de la table.
* Composant de liste qui permet à un utilisateur de spécifier un modèle pour afficher des éléments dans une liste.

Un composant basé sur un modèle est défini en spécifiant un ou plusieurs paramètres de composant de type <xref:Microsoft.AspNetCore.Components.RenderFragment> ou <xref:Microsoft.AspNetCore.Components.RenderFragment%601> . Un fragment de rendu représente un segment de l’interface utilisateur à restituer. <xref:Microsoft.AspNetCore.Components.RenderFragment%601> prend un paramètre de type qui peut être spécifié lors de l’appel du fragment de rendu.

Souvent, les composants basés sur un modèle sont typés de façon générique, comme le `TableTemplate` montre le composant suivant. Le type générique `<T>` dans cet exemple est utilisé pour restituer des `IReadOnlyList<T>` valeurs, qui dans ce cas est une série de lignes PET dans un composant qui affiche une table d’animaux familiers.

`Shared/TableTemplate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

Lorsque vous utilisez un composant basé sur un modèle, les paramètres de modèle peuvent être spécifiés à l’aide d’éléments enfants qui correspondent aux noms des paramètres. Dans l’exemple suivant, `<TableHeader>...</TableHeader>` et `<RowTemplate>...<RowTemplate>` fournissent <xref:Microsoft.AspNetCore.Components.RenderFragment%601> des modèles pour `TableHeader` et `RowTemplate` du `TableTemplate` composant.

Spécifiez l' `Context` attribut sur l’élément composant lorsque vous souhaitez spécifier le nom du paramètre de contenu pour le contenu enfant implicite (sans élément enfant d’encapsulation). Dans l’exemple suivant, l' `Context` attribut apparaît sur l' `TableTemplate` élément et s’applique à tous les <xref:Microsoft.AspNetCore.Components.RenderFragment%601> paramètres de modèle.

`Pages/Pets.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

Vous pouvez également modifier le nom du paramètre à l’aide `Context` de l’attribut sur l' <xref:Microsoft.AspNetCore.Components.RenderFragment%601> élément enfant. Dans l’exemple suivant, le `Context` est défini sur `RowTemplate` au lieu de `TableTemplate` :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

Les arguments de composant de type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> ont un paramètre implicite nommé `context` , qui peut être utilisé. Dans l’exemple suivant, `Context` n’est pas défini. `@context.{PROPERTY}` fournit des valeurs de PET au modèle, où `{PROPERTY}` est une `Pet` propriété :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

Lorsque vous utilisez des composants génériques, le paramètre de type est déduit si possible. Toutefois, vous pouvez spécifier explicitement le type avec un attribut dont le nom correspond au paramètre de type, qui est `TItem` dans l’exemple précédent :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="generic-type-constraints"></a>Contraintes de type générique

> [!NOTE]
> Les contraintes de type générique seront prises en charge dans une version ultérieure. Pour plus d’informations, consultez [autoriser les contraintes de type générique (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
