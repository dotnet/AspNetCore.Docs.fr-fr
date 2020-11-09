---
title: 'Dispositions de ASP.NET Core Blazor'
author: guardrex
description: 'Découvrez comment créer des composants de disposition réutilisables pour les Blazor applications.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/layouts
ms.openlocfilehash: e61c76b5d53ad7646961632d00b047ecd2d9e477
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055605"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="fee5d-103">Dispositions de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="fee5d-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="fee5d-104">Par [Rainer Stropek](https://www.timecockpit.com) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fee5d-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fee5d-105">Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la mise en page globale de l’application et sont utilisés par chaque composant de l’application.</span><span class="sxs-lookup"><span data-stu-id="fee5d-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="fee5d-106">La copie du code de ces éléments dans tous les composants d’une application n’est pas une approche efficace.</span><span class="sxs-lookup"><span data-stu-id="fee5d-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="fee5d-107">Chaque fois que l’un des éléments requiert une mise à jour, chaque composant doit être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="fee5d-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="fee5d-108">Une telle duplication est difficile à gérer et peut entraîner une incohérence du contenu au fil du temps.</span><span class="sxs-lookup"><span data-stu-id="fee5d-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="fee5d-109">Les *dispositions* résolvent ce problème.</span><span class="sxs-lookup"><span data-stu-id="fee5d-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="fee5d-110">Techniquement, une disposition est simplement un autre composant.</span><span class="sxs-lookup"><span data-stu-id="fee5d-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="fee5d-111">Une disposition est définie dans un Razor modèle ou dans du code C# et peut utiliser la [liaison de données](xref:blazor/components/data-binding), l' [injection de dépendances](xref:blazor/fundamentals/dependency-injection)et d’autres scénarios de composants.</span><span class="sxs-lookup"><span data-stu-id="fee5d-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="fee5d-112">Pour transformer un *composant* en une *disposition* , le composant :</span><span class="sxs-lookup"><span data-stu-id="fee5d-112">To turn a *component* into a *layout* , the component:</span></span>

* <span data-ttu-id="fee5d-113">Hérite de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , qui définit une <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriété pour le contenu rendu à l’intérieur de la disposition.</span><span class="sxs-lookup"><span data-stu-id="fee5d-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="fee5d-114">Utilise la Razor syntaxe `@Body` pour spécifier l’emplacement dans la balise de mise en page où le contenu est restitué.</span><span class="sxs-lookup"><span data-stu-id="fee5d-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="fee5d-115">L’exemple de code suivant montre le Razor modèle d’un composant de disposition, `MainLayout.razor` .</span><span class="sxs-lookup"><span data-stu-id="fee5d-115">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="fee5d-116">La disposition hérite <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> de et définit la `@Body` entre la barre de navigation et le pied de page :</span><span class="sxs-lookup"><span data-stu-id="fee5d-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="mainlayout-component"></a><span data-ttu-id="fee5d-117">Composant `MainLayout`</span><span class="sxs-lookup"><span data-stu-id="fee5d-117">`MainLayout` component</span></span>

<span data-ttu-id="fee5d-118">Dans une application basée sur l’un des Blazor modèles de projet, le `MainLayout` composant ( `MainLayout.razor` ) se trouve dans le dossier de l’application `Shared` :</span><span class="sxs-lookup"><span data-stu-id="fee5d-118">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

## <a name="default-layout"></a><span data-ttu-id="fee5d-119">Disposition par défaut</span><span class="sxs-lookup"><span data-stu-id="fee5d-119">Default layout</span></span>

<span data-ttu-id="fee5d-120">Spécifiez la disposition de l’application par défaut dans le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant dans le fichier de l’application `App.razor` .</span><span class="sxs-lookup"><span data-stu-id="fee5d-120">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="fee5d-121">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant suivant, qui est fourni par les modèles par défaut Blazor , définit la disposition par défaut sur le `MainLayout` composant :</span><span class="sxs-lookup"><span data-stu-id="fee5d-121">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="fee5d-122">Pour fournir une disposition par défaut pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, spécifiez un <xref:Microsoft.AspNetCore.Components.LayoutView> pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu :</span><span class="sxs-lookup"><span data-stu-id="fee5d-122">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="fee5d-123">Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, consultez <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="fee5d-123">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="fee5d-124">La spécification de la disposition comme disposition par défaut dans le routeur est une pratique utile, car elle peut être remplacée par composant ou par dossier.</span><span class="sxs-lookup"><span data-stu-id="fee5d-124">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="fee5d-125">Préférez utiliser le routeur pour définir la disposition par défaut de l’application, car il s’agit de la technique la plus générale.</span><span class="sxs-lookup"><span data-stu-id="fee5d-125">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="fee5d-126">Spécifier une disposition dans un composant</span><span class="sxs-lookup"><span data-stu-id="fee5d-126">Specify a layout in a component</span></span>

<span data-ttu-id="fee5d-127">Utilisez la Razor directive `@layout` pour appliquer une disposition à un composant.</span><span class="sxs-lookup"><span data-stu-id="fee5d-127">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="fee5d-128">Le compilateur convertit `@layout` en <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , qui est appliqué à la classe de composant.</span><span class="sxs-lookup"><span data-stu-id="fee5d-128">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="fee5d-129">Le contenu du composant suivant `MasterList` est inséré dans le `MasterLayout` à la position de `@Body` :</span><span class="sxs-lookup"><span data-stu-id="fee5d-129">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="fee5d-130">La spécification de la disposition directement dans un composant remplace un ensemble de *dispositions par défaut* dans le routeur ou une `@layout` directive importée à partir de `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="fee5d-130">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="fee5d-131">Sélection de la disposition centralisée</span><span class="sxs-lookup"><span data-stu-id="fee5d-131">Centralized layout selection</span></span>

<span data-ttu-id="fee5d-132">Chaque dossier d’une application peut éventuellement contenir un fichier de modèle nommé `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="fee5d-132">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="fee5d-133">Le compilateur comprend les directives spécifiées dans le fichier Imports dans tous les Razor modèles du même dossier et de manière récursive dans tous ses sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="fee5d-133">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="fee5d-134">Par conséquent, un `_Imports.razor` fichier contenant `@layout MyCoolLayout` s’assure que tous les composants d’un dossier utilisent `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="fee5d-134">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="fee5d-135">Il n’est pas nécessaire d’ajouter à plusieurs reprises `@layout MyCoolLayout` à tous les `.razor` fichiers dans le dossier et les sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="fee5d-135">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="fee5d-136">`@using` les directives sont également appliquées aux composants de la même façon.</span><span class="sxs-lookup"><span data-stu-id="fee5d-136">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="fee5d-137">Les `_Imports.razor` importations de fichiers suivantes :</span><span class="sxs-lookup"><span data-stu-id="fee5d-137">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="fee5d-138">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="fee5d-138">`MyCoolLayout`.</span></span>
* <span data-ttu-id="fee5d-139">Tous les Razor composants du même dossier et de tous les sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="fee5d-139">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="fee5d-140">Espace de noms `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="fee5d-140">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="fee5d-141">Le `_Imports.razor` fichier est semblable au [fichier _ViewImports. cshtml pour les Razor affichages et les pages,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux Razor fichiers de composants.</span><span class="sxs-lookup"><span data-stu-id="fee5d-141">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="fee5d-142">La spécification d’une disposition dans `_Imports.razor` remplace une disposition spécifiée comme *disposition par défaut* du routeur.</span><span class="sxs-lookup"><span data-stu-id="fee5d-142">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout* .</span></span>

> [!WARNING]
> <span data-ttu-id="fee5d-143">N’ajoutez **pas** de Razor `@layout` directive au fichier racine `_Imports.razor` , ce qui entraîne une boucle infinie de dispositions dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fee5d-143">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="fee5d-144">Pour contrôler la disposition de l’application par défaut, spécifiez la disposition dans le `Router` composant.</span><span class="sxs-lookup"><span data-stu-id="fee5d-144">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="fee5d-145">Pour plus d’informations, consultez la section [disposition par défaut](#default-layout) .</span><span class="sxs-lookup"><span data-stu-id="fee5d-145">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="fee5d-146">Dispositions imbriquées</span><span class="sxs-lookup"><span data-stu-id="fee5d-146">Nested layouts</span></span>

<span data-ttu-id="fee5d-147">Les applications peuvent se composer de dispositions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="fee5d-147">Apps can consist of nested layouts.</span></span> <span data-ttu-id="fee5d-148">Un composant peut faire référence à une disposition qui, à son tour, fait référence à une autre disposition.</span><span class="sxs-lookup"><span data-stu-id="fee5d-148">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="fee5d-149">Par exemple, les dispositions d’imbrication sont utilisées pour créer une structure de menus à plusieurs niveaux.</span><span class="sxs-lookup"><span data-stu-id="fee5d-149">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="fee5d-150">L’exemple suivant montre comment utiliser des dispositions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="fee5d-150">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="fee5d-151">Le `EpisodesComponent.razor` fichier est le composant à afficher.</span><span class="sxs-lookup"><span data-stu-id="fee5d-151">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="fee5d-152">Le composant fait référence à `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="fee5d-152">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="fee5d-153">Le `MasterListLayout.razor` fichier fournit le `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="fee5d-153">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="fee5d-154">La disposition fait référence à une autre disposition, `MasterLayout` , où elle est rendue.</span><span class="sxs-lookup"><span data-stu-id="fee5d-154">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="fee5d-155">`EpisodesComponent` l’emplacement est affiché `@Body` :</span><span class="sxs-lookup"><span data-stu-id="fee5d-155">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="fee5d-156">Enfin, `MasterLayout` dans `MasterLayout.razor` contient les éléments de disposition de niveau supérieur, tels que l’en-tête, le menu principal et le pied de page.</span><span class="sxs-lookup"><span data-stu-id="fee5d-156">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="fee5d-157">`MasterListLayout` avec `EpisodesComponent` est rendu où `@Body` s’affiche :</span><span class="sxs-lookup"><span data-stu-id="fee5d-157">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="fee5d-158">Partager une Razor disposition de pages avec des composants intégrés</span><span class="sxs-lookup"><span data-stu-id="fee5d-158">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="fee5d-159">Lorsque des composants routables sont intégrés à une Razor application pages, la disposition partagée de l’application peut être utilisée avec les composants.</span><span class="sxs-lookup"><span data-stu-id="fee5d-159">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="fee5d-160">Pour plus d'informations, consultez <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="fee5d-160">For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fee5d-161">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fee5d-161">Additional resources</span></span>

* <xref:mvc/views/layout>
