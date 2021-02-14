---
title: Dispositions de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment créer des composants de disposition réutilisables pour les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: d1f3e2028ca120b5901aca0b24802d872ae52597
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279726"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="fc150-103">Dispositions de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="fc150-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="fc150-104">Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la mise en page globale de l’application et sont utilisés par chaque composant de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc150-104">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="fc150-105">La copie du code de ces éléments dans tous les composants d’une application n’est pas une approche efficace.</span><span class="sxs-lookup"><span data-stu-id="fc150-105">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="fc150-106">Chaque fois que l’un des éléments requiert une mise à jour, chaque composant doit être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="fc150-106">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="fc150-107">Une telle duplication est difficile à gérer et peut entraîner une incohérence du contenu au fil du temps.</span><span class="sxs-lookup"><span data-stu-id="fc150-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="fc150-108">Les *dispositions* résolvent ce problème.</span><span class="sxs-lookup"><span data-stu-id="fc150-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="fc150-109">Techniquement, une disposition est simplement un autre composant.</span><span class="sxs-lookup"><span data-stu-id="fc150-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="fc150-110">Une disposition est définie dans un Razor modèle ou dans du code C# et peut utiliser la [liaison de données](xref:blazor/components/data-binding), l' [injection de dépendances](xref:blazor/fundamentals/dependency-injection)et d’autres scénarios de composants.</span><span class="sxs-lookup"><span data-stu-id="fc150-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span> <span data-ttu-id="fc150-111">Les dispositions s’appliquent uniquement aux Razor composants routables qui possèdent des [`@page`](xref:mvc/views/razor#page) directives.</span><span class="sxs-lookup"><span data-stu-id="fc150-111">Layouts only apply to routable Razor components that have [`@page`](xref:mvc/views/razor#page) directives.</span></span>

<span data-ttu-id="fc150-112">Pour convertir un composant en disposition :</span><span class="sxs-lookup"><span data-stu-id="fc150-112">To convert a component into a layout:</span></span>

* <span data-ttu-id="fc150-113">Hérite du composant de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="fc150-113">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="fc150-114"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase>Définit une <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriété pour le contenu rendu à l’intérieur de la disposition.</span><span class="sxs-lookup"><span data-stu-id="fc150-114">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="fc150-115">Utilisez la Razor syntaxe `@Body` pour spécifier l’emplacement dans la balise de mise en page où le contenu est affiché.</span><span class="sxs-lookup"><span data-stu-id="fc150-115">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="fc150-116">L’exemple de code suivant montre le Razor modèle d’un composant de disposition, `MainLayout.razor` .</span><span class="sxs-lookup"><span data-stu-id="fc150-116">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="fc150-117">La disposition hérite <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> de et définit la `@Body` entre la barre de navigation et le pied de page :</span><span class="sxs-lookup"><span data-stu-id="fc150-117">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="fc150-118">Composant `MainLayout`</span><span class="sxs-lookup"><span data-stu-id="fc150-118">`MainLayout` component</span></span>

<span data-ttu-id="fc150-119">Dans une application basée sur l’un des Blazor modèles de projet, le `MainLayout` composant ( `MainLayout.razor` ) se trouve dans le dossier de l’application `Shared` :</span><span class="sxs-lookup"><span data-stu-id="fc150-119">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="fc150-120">Disposition par défaut</span><span class="sxs-lookup"><span data-stu-id="fc150-120">Default layout</span></span>

<span data-ttu-id="fc150-121">Spécifiez la disposition de l’application par défaut dans le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant dans le fichier de l’application `App.razor` .</span><span class="sxs-lookup"><span data-stu-id="fc150-121">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="fc150-122">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant suivant, qui est fourni par les modèles par défaut Blazor , définit la disposition par défaut sur le `MainLayout` composant :</span><span class="sxs-lookup"><span data-stu-id="fc150-122">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="fc150-123">Pour fournir une disposition par défaut pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, spécifiez un <xref:Microsoft.AspNetCore.Components.LayoutView> pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu :</span><span class="sxs-lookup"><span data-stu-id="fc150-123">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="fc150-124">Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, consultez <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="fc150-124">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="fc150-125">La spécification de la disposition comme disposition par défaut dans le routeur est une pratique utile, car elle peut être remplacée par composant ou par dossier.</span><span class="sxs-lookup"><span data-stu-id="fc150-125">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="fc150-126">Préférez utiliser le routeur pour définir la disposition par défaut de l’application, car il s’agit de la technique la plus générale.</span><span class="sxs-lookup"><span data-stu-id="fc150-126">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="fc150-127">Spécifier une disposition dans un composant</span><span class="sxs-lookup"><span data-stu-id="fc150-127">Specify a layout in a component</span></span>

<span data-ttu-id="fc150-128">Utilisez la [`@layout`](xref:mvc/views/razor#layout) Razor directive pour appliquer une disposition à un composant routable Razor qui possède également une [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="fc150-128">Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that also has an [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="fc150-129">Le compilateur convertit `@layout` en <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , qui est appliqué à la classe de composant.</span><span class="sxs-lookup"><span data-stu-id="fc150-129">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="fc150-130">Le contenu du composant suivant `MasterList` est inséré dans le `MasterLayout` à la position de `@Body` :</span><span class="sxs-lookup"><span data-stu-id="fc150-130">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="fc150-131">La spécification de la disposition directement dans un composant remplace un ensemble de *dispositions par défaut* dans le routeur ou une `@layout` directive importée à partir de `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="fc150-131">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="fc150-132">Sélection de la disposition centralisée</span><span class="sxs-lookup"><span data-stu-id="fc150-132">Centralized layout selection</span></span>

<span data-ttu-id="fc150-133">Chaque dossier d’une application peut éventuellement contenir un fichier de modèle nommé `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="fc150-133">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="fc150-134">Le compilateur comprend les directives spécifiées dans le fichier Imports dans tous les Razor modèles du même dossier et de manière récursive dans tous ses sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="fc150-134">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="fc150-135">Par conséquent, un `_Imports.razor` fichier contenant `@layout MyCoolLayout` s’assure que tous les composants d’un dossier utilisent `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="fc150-135">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="fc150-136">Il n’est pas nécessaire d’ajouter à plusieurs reprises `@layout MyCoolLayout` à tous les `.razor` fichiers dans le dossier et les sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="fc150-136">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="fc150-137">`@using` les directives sont également appliquées aux composants de la même façon.</span><span class="sxs-lookup"><span data-stu-id="fc150-137">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="fc150-138">Les `_Imports.razor` importations de fichiers suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc150-138">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="fc150-139">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="fc150-139">`MyCoolLayout`.</span></span>
* <span data-ttu-id="fc150-140">Tous les Razor composants du même dossier et de tous les sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="fc150-140">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="fc150-141">Espace de noms `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="fc150-141">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="fc150-142">Le `_Imports.razor` fichier est semblable au [fichier _ViewImports. cshtml pour les Razor affichages et les pages,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux Razor fichiers de composants.</span><span class="sxs-lookup"><span data-stu-id="fc150-142">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="fc150-143">La spécification d’une disposition dans `_Imports.razor` remplace une disposition spécifiée comme *disposition par défaut* du routeur.</span><span class="sxs-lookup"><span data-stu-id="fc150-143">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="fc150-144">N’ajoutez **pas** de Razor `@layout` directive au fichier racine `_Imports.razor` , ce qui entraîne une boucle infinie de dispositions dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fc150-144">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="fc150-145">Pour contrôler la disposition de l’application par défaut, spécifiez la disposition dans le `Router` composant.</span><span class="sxs-lookup"><span data-stu-id="fc150-145">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="fc150-146">Pour plus d’informations, consultez la section [disposition par défaut](#default-layout) .</span><span class="sxs-lookup"><span data-stu-id="fc150-146">For more information, see the [Default layout](#default-layout) section.</span></span>

> [!NOTE]
> <span data-ttu-id="fc150-147">La [`@layout`](xref:mvc/views/razor#layout) Razor directive applique uniquement une disposition aux composants routables Razor avec des [`@page`](xref:mvc/views/razor#page) directives.</span><span class="sxs-lookup"><span data-stu-id="fc150-147">The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with [`@page`](xref:mvc/views/razor#page) directives.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="fc150-148">Dispositions imbriquées</span><span class="sxs-lookup"><span data-stu-id="fc150-148">Nested layouts</span></span>

<span data-ttu-id="fc150-149">Les applications peuvent se composer de dispositions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="fc150-149">Apps can consist of nested layouts.</span></span> <span data-ttu-id="fc150-150">Un composant peut faire référence à une disposition qui, à son tour, fait référence à une autre disposition.</span><span class="sxs-lookup"><span data-stu-id="fc150-150">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="fc150-151">Par exemple, les dispositions d’imbrication sont utilisées pour créer une structure de menus à plusieurs niveaux.</span><span class="sxs-lookup"><span data-stu-id="fc150-151">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="fc150-152">L’exemple suivant montre comment utiliser des dispositions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="fc150-152">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="fc150-153">Le `EpisodesComponent.razor` fichier est le composant à afficher.</span><span class="sxs-lookup"><span data-stu-id="fc150-153">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="fc150-154">Le composant fait référence à `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="fc150-154">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="fc150-155">Le `MasterListLayout.razor` fichier fournit le `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="fc150-155">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="fc150-156">La disposition fait référence à une autre disposition, `MasterLayout` , où elle est rendue.</span><span class="sxs-lookup"><span data-stu-id="fc150-156">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="fc150-157">`EpisodesComponent` l’emplacement est affiché `@Body` :</span><span class="sxs-lookup"><span data-stu-id="fc150-157">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="fc150-158">Enfin, `MasterLayout` dans `MasterLayout.razor` contient les éléments de disposition de niveau supérieur, tels que l’en-tête, le menu principal et le pied de page.</span><span class="sxs-lookup"><span data-stu-id="fc150-158">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="fc150-159">`MasterListLayout` avec `EpisodesComponent` est rendu où `@Body` s’affiche :</span><span class="sxs-lookup"><span data-stu-id="fc150-159">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="fc150-160">Partager une Razor disposition de pages avec des composants intégrés</span><span class="sxs-lookup"><span data-stu-id="fc150-160">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="fc150-161">Lorsque des composants routables sont intégrés à une Razor application pages, la disposition partagée de l’application peut être utilisée avec les composants.</span><span class="sxs-lookup"><span data-stu-id="fc150-161">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="fc150-162">Pour plus d’informations, consultez <xref:blazor/components/prerendering-and-integration>.</span><span class="sxs-lookup"><span data-stu-id="fc150-162">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc150-163">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fc150-163">Additional resources</span></span>

* <xref:mvc/views/layout>
