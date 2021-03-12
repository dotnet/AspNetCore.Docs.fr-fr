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
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="4ee5c-103">Composants ASP.NET Core basés sur un Blazor modèle</span><span class="sxs-lookup"><span data-stu-id="4ee5c-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="4ee5c-104">Les composants basés sur un modèle sont des composants qui acceptent un ou plusieurs modèles d’interface utilisateur en tant que paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-104">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="4ee5c-105">Les composants basés sur un modèle vous permettent de créer des composants de niveau supérieur qui sont plus réutilisables que les composants normaux.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-105">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="4ee5c-106">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="4ee5c-106">A couple of examples include:</span></span>

* <span data-ttu-id="4ee5c-107">Composant de table qui permet à un utilisateur de spécifier des modèles pour l’en-tête, les lignes et le pied de page de la table.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-107">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="4ee5c-108">Composant de liste qui permet à un utilisateur de spécifier un modèle pour afficher des éléments dans une liste.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-108">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="4ee5c-109">Un composant basé sur un modèle est défini en spécifiant un ou plusieurs paramètres de composant de type <xref:Microsoft.AspNetCore.Components.RenderFragment> ou <xref:Microsoft.AspNetCore.Components.RenderFragment%601> .</span><span class="sxs-lookup"><span data-stu-id="4ee5c-109">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="4ee5c-110">Un fragment de rendu représente un segment de l’interface utilisateur à restituer.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-110">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="4ee5c-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> prend un paramètre de type qui peut être spécifié lors de l’appel du fragment de rendu.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="4ee5c-112">Souvent, les composants basés sur un modèle sont typés de façon générique, comme le `TableTemplate` montre le composant suivant.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-112">Often, templated components are generically typed, as the following `TableTemplate` component demonstrates.</span></span> <span data-ttu-id="4ee5c-113">Le type générique `<T>` dans cet exemple est utilisé pour restituer des `IReadOnlyList<T>` valeurs, qui dans ce cas est une série de lignes PET dans un composant qui affiche une table d’animaux familiers.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-113">The generic type `<T>` in this example is used to render `IReadOnlyList<T>` values, which in this case is a series of pet rows in a component that displays a table of pets.</span></span>

<span data-ttu-id="4ee5c-114">`Shared/TableTemplate.razor`:</span><span class="sxs-lookup"><span data-stu-id="4ee5c-114">`Shared/TableTemplate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

<span data-ttu-id="4ee5c-115">Lorsque vous utilisez un composant basé sur un modèle, les paramètres de modèle peuvent être spécifiés à l’aide d’éléments enfants qui correspondent aux noms des paramètres.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters.</span></span> <span data-ttu-id="4ee5c-116">Dans l’exemple suivant, `<TableHeader>...</TableHeader>` et `<RowTemplate>...<RowTemplate>` fournissent <xref:Microsoft.AspNetCore.Components.RenderFragment%601> des modèles pour `TableHeader` et `RowTemplate` du `TableTemplate` composant.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-116">In the following example, `<TableHeader>...</TableHeader>` and `<RowTemplate>...<RowTemplate>` supply <xref:Microsoft.AspNetCore.Components.RenderFragment%601> templates for `TableHeader` and `RowTemplate` of the `TableTemplate` component.</span></span>

<span data-ttu-id="4ee5c-117">Spécifiez l' `Context` attribut sur l’élément composant lorsque vous souhaitez spécifier le nom du paramètre de contenu pour le contenu enfant implicite (sans élément enfant d’encapsulation).</span><span class="sxs-lookup"><span data-stu-id="4ee5c-117">Specify the `Context` attribute on the component element when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="4ee5c-118">Dans l’exemple suivant, l' `Context` attribut apparaît sur l' `TableTemplate` élément et s’applique à tous les <xref:Microsoft.AspNetCore.Components.RenderFragment%601> paramètres de modèle.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-118">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all <xref:Microsoft.AspNetCore.Components.RenderFragment%601> template parameters.</span></span>

<span data-ttu-id="4ee5c-119">`Pages/Pets.razor`:</span><span class="sxs-lookup"><span data-stu-id="4ee5c-119">`Pages/Pets.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

<span data-ttu-id="4ee5c-120">Vous pouvez également modifier le nom du paramètre à l’aide `Context` de l’attribut sur l' <xref:Microsoft.AspNetCore.Components.RenderFragment%601> élément enfant.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-120">Alternatively, you can change the parameter name using the `Context` attribute on the <xref:Microsoft.AspNetCore.Components.RenderFragment%601> child element.</span></span> <span data-ttu-id="4ee5c-121">Dans l’exemple suivant, le `Context` est défini sur `RowTemplate` au lieu de `TableTemplate` :</span><span class="sxs-lookup"><span data-stu-id="4ee5c-121">In the following example, the `Context` is set on `RowTemplate` rather than `TableTemplate`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

<span data-ttu-id="4ee5c-122">Les arguments de composant de type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> ont un paramètre implicite nommé `context` , qui peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-122">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> have an implicit parameter named `context`, which can be used.</span></span> <span data-ttu-id="4ee5c-123">Dans l’exemple suivant, `Context` n’est pas défini.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-123">In the following example, `Context` isn't set.</span></span> <span data-ttu-id="4ee5c-124">`@context.{PROPERTY}` fournit des valeurs de PET au modèle, où `{PROPERTY}` est une `Pet` propriété :</span><span class="sxs-lookup"><span data-stu-id="4ee5c-124">`@context.{PROPERTY}` supplies pet values to the template, where `{PROPERTY}` is a `Pet` property:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

<span data-ttu-id="4ee5c-125">Lorsque vous utilisez des composants génériques, le paramètre de type est déduit si possible.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-125">When using generic-typed components, the type parameter is inferred if possible.</span></span> <span data-ttu-id="4ee5c-126">Toutefois, vous pouvez spécifier explicitement le type avec un attribut dont le nom correspond au paramètre de type, qui est `TItem` dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="4ee5c-126">However, you can explicitly specify the type with an attribute that has a name matching the type parameter, which is `TItem` in the preceding example:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="generic-type-constraints"></a><span data-ttu-id="4ee5c-127">Contraintes de type générique</span><span class="sxs-lookup"><span data-stu-id="4ee5c-127">Generic type constraints</span></span>

> [!NOTE]
> <span data-ttu-id="4ee5c-128">Les contraintes de type générique seront prises en charge dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="4ee5c-128">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="4ee5c-129">Pour plus d’informations, consultez [autoriser les contraintes de type générique (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="4ee5c-129">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ee5c-130">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4ee5c-130">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
