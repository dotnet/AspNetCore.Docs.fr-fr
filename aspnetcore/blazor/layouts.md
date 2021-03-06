---
title: Dispositions de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment créer des composants de disposition réutilisables pour les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2021
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
ms.openlocfilehash: 9f3400b766a043fdf3872838bffe392eddba4049
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102394943"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="522f4-103">Dispositions de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="522f4-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="522f4-104">Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la présentation générale de l’application.</span><span class="sxs-lookup"><span data-stu-id="522f4-104">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall presentation.</span></span> <span data-ttu-id="522f4-105">Le fait de placer une copie du balisage pour ces éléments dans tous les composants d’une application n’est pas efficace.</span><span class="sxs-lookup"><span data-stu-id="522f4-105">Placing a copy of the markup for these elements into all of the components of an app isn't efficient.</span></span> <span data-ttu-id="522f4-106">Chaque fois que l’un de ces éléments est mis à jour, chaque composant qui utilise l’élément doit être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="522f4-106">Every time that one of these elements is updated, every component that uses the element must be updated.</span></span> <span data-ttu-id="522f4-107">Cette approche est coûteuse à gérer et peut entraîner un contenu incohérent si une mise à jour est manquée.</span><span class="sxs-lookup"><span data-stu-id="522f4-107">This approach is costly to maintain and can lead to inconsistent content if an update is missed.</span></span> <span data-ttu-id="522f4-108">Les *dispositions* résolvent ces problèmes.</span><span class="sxs-lookup"><span data-stu-id="522f4-108">*Layouts* solve these problems.</span></span>

<span data-ttu-id="522f4-109">Une Blazor disposition est un Razor composant qui partage le balisage avec des composants qui le référencent.</span><span class="sxs-lookup"><span data-stu-id="522f4-109">A Blazor layout is a Razor component that shares markup with components that reference it.</span></span> <span data-ttu-id="522f4-110">Les dispositions peuvent utiliser la [liaison de données](xref:blazor/components/data-binding), l’injection de [dépendances](xref:blazor/fundamentals/dependency-injection)et d’autres fonctionnalités de composants.</span><span class="sxs-lookup"><span data-stu-id="522f4-110">Layouts can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other features of components.</span></span>

## <a name="layout-components"></a><span data-ttu-id="522f4-111">Composants de la disposition</span><span class="sxs-lookup"><span data-stu-id="522f4-111">Layout components</span></span>

### <a name="create-a-layout-component"></a><span data-ttu-id="522f4-112">Créer un composant de disposition</span><span class="sxs-lookup"><span data-stu-id="522f4-112">Create a layout component</span></span>

<span data-ttu-id="522f4-113">Pour créer un composant de disposition :</span><span class="sxs-lookup"><span data-stu-id="522f4-113">To create a layout component:</span></span>

* <span data-ttu-id="522f4-114">Créez un Razor composant défini par un Razor modèle ou du code C#.</span><span class="sxs-lookup"><span data-stu-id="522f4-114">Create a Razor component defined by a Razor template or C# code.</span></span> <span data-ttu-id="522f4-115">Les composants de disposition basés sur un Razor modèle utilisent l' `.razor` extension de fichier comme les Razor composants ordinaires.</span><span class="sxs-lookup"><span data-stu-id="522f4-115">Layout components based on a Razor template use the `.razor` file extension just like ordinary Razor components.</span></span> <span data-ttu-id="522f4-116">Étant donné que les composants de disposition sont partagés entre les composants d’une application, ils sont généralement placés dans le dossier de l’application `Shared` .</span><span class="sxs-lookup"><span data-stu-id="522f4-116">Because layout components are shared across an app's components, they're usually placed in the app's `Shared` folder.</span></span> <span data-ttu-id="522f4-117">Toutefois, les dispositions peuvent être placées à n’importe quel emplacement accessible aux composants qui l’utilisent.</span><span class="sxs-lookup"><span data-stu-id="522f4-117">However, layouts can be placed in any location accessible to the components that use it.</span></span> <span data-ttu-id="522f4-118">Par exemple, une disposition peut être placée dans le même dossier que les composants qui l’utilisent.</span><span class="sxs-lookup"><span data-stu-id="522f4-118">For example, a layout can be placed in the same folder as the components that use it.</span></span>
* <span data-ttu-id="522f4-119">Hérite du composant de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="522f4-119">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="522f4-120"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase>Définit une <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriété ( <xref:Microsoft.AspNetCore.Components.RenderFragment> type) pour le contenu rendu à l’intérieur de la disposition.</span><span class="sxs-lookup"><span data-stu-id="522f4-120">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property (<xref:Microsoft.AspNetCore.Components.RenderFragment> type) for the rendered content inside the layout.</span></span>
* <span data-ttu-id="522f4-121">Utilisez la Razor syntaxe `@Body` pour spécifier l’emplacement dans la balise de mise en page où le contenu est affiché.</span><span class="sxs-lookup"><span data-stu-id="522f4-121">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="522f4-122">Le `DoctorWhoLayout` composant suivant montre le Razor modèle d’un composant de disposition.</span><span class="sxs-lookup"><span data-stu-id="522f4-122">The following `DoctorWhoLayout` component shows the Razor template of a layout component.</span></span> <span data-ttu-id="522f4-123">La disposition hérite <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> de et définit la `@Body` entre la barre de navigation ( `<nav>...</nav>` ) et le pied de page ( `<footer>...</footer>` ).</span><span class="sxs-lookup"><span data-stu-id="522f4-123">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar (`<nav>...</nav>`) and the footer (`<footer>...</footer>`).</span></span>

<span data-ttu-id="522f4-124">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="522f4-124">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a><span data-ttu-id="522f4-125">Composant `MainLayout`</span><span class="sxs-lookup"><span data-stu-id="522f4-125">`MainLayout` component</span></span>

<span data-ttu-id="522f4-126">Dans une application créée à partir d’un [ Blazor modèle de projet](xref:blazor/project-structure), le `MainLayout` composant est la [disposition par défaut](#apply-a-default-layout-to-an-app)de l’application.</span><span class="sxs-lookup"><span data-stu-id="522f4-126">In an app created from a [Blazor project template](xref:blazor/project-structure), the `MainLayout` component is the app's [default layout](#apply-a-default-layout-to-an-app).</span></span>

<span data-ttu-id="522f4-127">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="522f4-127">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

<span data-ttu-id="522f4-128">la [ Blazor fonctionnalité d’isolation CSS de](xref:blazor/components/css-isolation) applique les styles CSS isolés au `MainLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-128">[Blazor's CSS isolation feature](xref:blazor/components/css-isolation) applies isolated CSS styles to the `MainLayout` component.</span></span> <span data-ttu-id="522f4-129">Par Convention, les styles sont fournis par la feuille de style qui l’accompagne du même nom, `Shared/MainLayout.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="522f4-129">By convention, the styles are provided by the accompanying stylesheet of the same name, `Shared/MainLayout.razor.css`.</span></span> <span data-ttu-id="522f4-130">L’implémentation ASP.NET Core Framework de la feuille de style est disponible pour l’inspection dans la [source de référence ASP.net Core (référentiel GitHub dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).</span><span class="sxs-lookup"><span data-stu-id="522f4-130">The ASP.NET Core framework implementation of the stylesheet is available for inspection in the [ASP.NET Core reference source (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).</span></span>

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a><span data-ttu-id="522f4-131">Appliquer une disposition</span><span class="sxs-lookup"><span data-stu-id="522f4-131">Apply a layout</span></span>

### <a name="apply-a-layout-to-a-component"></a><span data-ttu-id="522f4-132">Appliquer une disposition à un composant</span><span class="sxs-lookup"><span data-stu-id="522f4-132">Apply a layout to a component</span></span>

<span data-ttu-id="522f4-133">Utilisez la [`@layout`](xref:mvc/views/razor#layout) Razor directive pour appliquer une disposition à un composant routable Razor qui a une [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="522f4-133">Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that has an [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="522f4-134">Le compilateur convertit `@layout` en un <xref:Microsoft.AspNetCore.Components.LayoutAttribute> et applique l’attribut à la classe de composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-134">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute> and applies the attribute to the component class.</span></span>

<span data-ttu-id="522f4-135">Le contenu du composant suivant `Episodes` est inséré dans le `DoctorWhoLayout` à la position de `@Body` .</span><span class="sxs-lookup"><span data-stu-id="522f4-135">The content of the following `Episodes` component is inserted into the `DoctorWhoLayout` at the position of `@Body`.</span></span>

<span data-ttu-id="522f4-136">`Pages/Episodes.razor`:</span><span class="sxs-lookup"><span data-stu-id="522f4-136">`Pages/Episodes.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="522f4-137">Le balisage HTML rendu suivant est produit par le `DoctorWhoLayout` composant et le précédent `Episodes` .</span><span class="sxs-lookup"><span data-stu-id="522f4-137">The following rendered HTML markup is produced by the preceding `DoctorWhoLayout` and `Episodes` component.</span></span> <span data-ttu-id="522f4-138">Le balisage superflu n’apparaît pas afin de se concentrer sur le contenu fourni par les deux composants impliqués :</span><span class="sxs-lookup"><span data-stu-id="522f4-138">Extraneous markup doesn't appear in order to focus on the content provided by the two components involved:</span></span>

* <span data-ttu-id="522f4-139">L’en-tête **&trade; de la base de données Doctor** ( `<h1>...</h1>` ) de l’en-tête (), de la `<header>...</header>` barre de navigation ( `<nav>...</nav>` ) et de l’élément d’information sur la marque () du `<div>...</div>` pied de page () provient du `<footer>...</footer>` `DoctorWhoLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-139">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`) in the header (`<header>...</header>`), navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) in the footer (`<footer>...</footer>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="522f4-140">L’en-tête **épisodes** ( `<h2>...</h2>` ) et la liste épisode ( `<ul>...</ul>` ) proviennent du `Episodes` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-140">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

```html
<body>
    <div id="app">
        <header>
            <h1>Doctor Who&trade; Episode Database</h1>
        </header>

        <nav>
            <a href="masterlist">Master Episode List</a>
            <a href="search">Search</a>
            <a href="new">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <footer>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </footer>
    </div>
</body>
```

<span data-ttu-id="522f4-141">La spécification de la disposition directement dans un composant remplace une *disposition par défaut*:</span><span class="sxs-lookup"><span data-stu-id="522f4-141">Specifying the layout directly in a component overrides a *default layout*:</span></span>

* <span data-ttu-id="522f4-142">Défini par une `@layout` directive importée à partir d’un `_Imports` composant ( `_Imports.razor` ), comme décrit dans la section suivante [appliquer une disposition à un dossier de composants](#apply-a-layout-to-a-folder-of-components) .</span><span class="sxs-lookup"><span data-stu-id="522f4-142">Set by an `@layout` directive imported from an `_Imports` component (`_Imports.razor`), as described in the following [Apply a layout to a folder of components](#apply-a-layout-to-a-folder-of-components) section.</span></span>
* <span data-ttu-id="522f4-143">Définissez comme disposition par défaut de l’application, comme décrit dans la section [appliquer une disposition par défaut à une application](#apply-a-default-layout-to-an-app) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="522f4-143">Set as the app's default layout, as described in the [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section later in this article.</span></span>

### <a name="apply-a-layout-to-a-folder-of-components"></a><span data-ttu-id="522f4-144">Appliquer une disposition à un dossier de composants</span><span class="sxs-lookup"><span data-stu-id="522f4-144">Apply a layout to a folder of components</span></span>

<span data-ttu-id="522f4-145">Chaque dossier d’une application peut éventuellement contenir un fichier de modèle nommé `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="522f4-145">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="522f4-146">Le compilateur comprend les directives spécifiées dans le fichier Imports dans tous les Razor modèles du même dossier et de manière récursive dans tous ses sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="522f4-146">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="522f4-147">Par conséquent, un `_Imports.razor` fichier contenant `@layout DoctorWhoLayout` s’assure que tous les composants d’un dossier utilisent le `DoctorWhoLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-147">Therefore, an `_Imports.razor` file containing `@layout DoctorWhoLayout` ensures that all of the components in a folder use the `DoctorWhoLayout` component.</span></span> <span data-ttu-id="522f4-148">Il n’est pas nécessaire d’ajouter à plusieurs reprises `@layout DoctorWhoLayout` à tous les Razor composants ( `.razor` ) dans le dossier et les sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="522f4-148">There's no need to repeatedly add `@layout DoctorWhoLayout` to all of the Razor components (`.razor`) within the folder and subfolders.</span></span>

<span data-ttu-id="522f4-149">`_Imports.razor`:</span><span class="sxs-lookup"><span data-stu-id="522f4-149">`_Imports.razor`:</span></span>

```razor
@layout DoctorWhoLayout
...
```

<span data-ttu-id="522f4-150">Le `_Imports.razor` fichier est semblable au [fichier _ViewImports. cshtml pour les Razor affichages et les pages,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux Razor fichiers de composants.</span><span class="sxs-lookup"><span data-stu-id="522f4-150">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="522f4-151">La spécification d’une disposition dans `_Imports.razor` remplace une disposition spécifiée comme [disposition d’application par défaut](#apply-a-default-layout-to-an-app)du routeur, qui est décrite dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="522f4-151">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's [default app layout](#apply-a-default-layout-to-an-app), which is described in the following section.</span></span>

> [!WARNING]
> <span data-ttu-id="522f4-152">N’ajoutez **pas** de Razor `@layout` directive au fichier racine `_Imports.razor` , ce qui se traduit par une boucle infinie de dispositions.</span><span class="sxs-lookup"><span data-stu-id="522f4-152">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts.</span></span> <span data-ttu-id="522f4-153">Pour contrôler la disposition de l’application par défaut, spécifiez la disposition dans le `Router` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-153">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="522f4-154">Pour plus d’informations, consultez la section [appliquer une disposition par défaut à une application](#apply-a-default-layout-to-an-app) .</span><span class="sxs-lookup"><span data-stu-id="522f4-154">For more information, see the following [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section.</span></span>

> [!NOTE]
> <span data-ttu-id="522f4-155">La [`@layout`](xref:mvc/views/razor#layout) Razor directive applique uniquement une disposition aux composants routables Razor avec une [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="522f4-155">The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>

### <a name="apply-a-default-layout-to-an-app"></a><span data-ttu-id="522f4-156">Appliquer une disposition par défaut à une application</span><span class="sxs-lookup"><span data-stu-id="522f4-156">Apply a default layout to an app</span></span>

<span data-ttu-id="522f4-157">Spécifiez la disposition de l’application par défaut dans le dans le `App` composant du composant <xref:Microsoft.AspNetCore.Components.Routing.Router> .</span><span class="sxs-lookup"><span data-stu-id="522f4-157">Specify the default app layout in the in the `App` component's <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="522f4-158">L’exemple suivant à partir d’une application basée sur un [ Blazor modèle de projet](xref:blazor/project-structure) définit la disposition par défaut sur le `MainLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-158">The following example from an app based on a [Blazor project template](xref:blazor/project-structure) sets the default layout to the `MainLayout` component.</span></span>

<span data-ttu-id="522f4-159">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="522f4-159">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="522f4-160">Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, consultez <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="522f4-160">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="522f4-161">La spécification de la disposition comme disposition par défaut dans le `Router` composant est une pratique utile, car vous pouvez remplacer la disposition par composant ou par dossier, comme décrit dans les sections précédentes de cet article.</span><span class="sxs-lookup"><span data-stu-id="522f4-161">Specifying the layout as a default layout in the `Router` component is a useful practice because you can override the layout on a per-component or per-folder basis, as described in the preceding sections of this article.</span></span> <span data-ttu-id="522f4-162">Nous vous recommandons d’utiliser le `Router` composant pour définir la disposition par défaut de l’application, car il s’agit de l’approche la plus générale et la plus souple pour l’utilisation des dispositions.</span><span class="sxs-lookup"><span data-stu-id="522f4-162">We recommend using the `Router` component to set the app's default layout because it's the most general and flexible approach for using layouts.</span></span>

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a><span data-ttu-id="522f4-163">Appliquer une disposition à du contenu arbitraire ( `LayoutView` composant)</span><span class="sxs-lookup"><span data-stu-id="522f4-163">Apply a layout to arbitrary content (`LayoutView` component)</span></span>

<span data-ttu-id="522f4-164">Pour définir une disposition pour Razor le contenu de modèle arbitraire, spécifiez la disposition avec un <xref:Microsoft.AspNetCore.Components.LayoutView> composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-164">To set a layout for arbitrary Razor template content, specify the layout with a <xref:Microsoft.AspNetCore.Components.LayoutView> component.</span></span> <span data-ttu-id="522f4-165">Vous pouvez utiliser <xref:Microsoft.AspNetCore.Components.LayoutView> dans n’importe quel Razor composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-165">You can use a <xref:Microsoft.AspNetCore.Components.LayoutView> in any Razor component.</span></span> <span data-ttu-id="522f4-166">L’exemple suivant définit un composant de disposition nommé `ErrorLayout` pour le `MainLayout` modèle du composant <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> ( `<NotFound>...</NotFound>` ).</span><span class="sxs-lookup"><span data-stu-id="522f4-166">The following example sets a layout component named `ErrorLayout` for the `MainLayout` component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template (`<NotFound>...</NotFound>`).</span></span>

<span data-ttu-id="522f4-167">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="522f4-167">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a><span data-ttu-id="522f4-168">Dispositions imbriquées</span><span class="sxs-lookup"><span data-stu-id="522f4-168">Nested layouts</span></span>

<span data-ttu-id="522f4-169">Un composant peut faire référence à une disposition qui, à son tour, fait référence à une autre disposition.</span><span class="sxs-lookup"><span data-stu-id="522f4-169">A component can reference a layout that in turn references another layout.</span></span> <span data-ttu-id="522f4-170">Par exemple, les dispositions imbriquées sont utilisées pour créer des structures de menus à plusieurs niveaux.</span><span class="sxs-lookup"><span data-stu-id="522f4-170">For example, nested layouts are used to create a multi-level menu structures.</span></span>

<span data-ttu-id="522f4-171">L’exemple suivant montre comment utiliser des dispositions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="522f4-171">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="522f4-172">Le `Episodes` composant affiché dans la section [appliquer une disposition à un composant](#apply-a-layout-to-a-component) est le composant à afficher.</span><span class="sxs-lookup"><span data-stu-id="522f4-172">The `Episodes` component shown in the [Apply a layout to a component](#apply-a-layout-to-a-component) section is the component to display.</span></span> <span data-ttu-id="522f4-173">Le composant fait référence au `DoctorWhoLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-173">The component references the `DoctorWhoLayout` component.</span></span>

<span data-ttu-id="522f4-174">Le `DoctorWhoLayout` composant suivant est une version modifiée de l’exemple indiqué plus haut dans cet article.</span><span class="sxs-lookup"><span data-stu-id="522f4-174">The following `DoctorWhoLayout` component is a modified version of the example shown earlier in this article.</span></span> <span data-ttu-id="522f4-175">Les éléments d’en-tête et de pied de page sont supprimés et la disposition référence une autre disposition, `ProductionsLayout` .</span><span class="sxs-lookup"><span data-stu-id="522f4-175">The header and footer elements are removed, and the layout references another layout, `ProductionsLayout`.</span></span> <span data-ttu-id="522f4-176">Le `Episodes` composant est rendu où `@Body` apparaît dans le `DoctorWhoLayout` .</span><span class="sxs-lookup"><span data-stu-id="522f4-176">The `Episodes` component is rendered where `@Body` appears in the `DoctorWhoLayout`.</span></span>

<span data-ttu-id="522f4-177">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="522f4-177">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

<span data-ttu-id="522f4-178">Le `ProductionsLayout` composant contient les éléments de disposition de niveau supérieur, où les éléments d’en-tête ( `<header>...</header>` ) et de pied de page ( `<footer>...</footer>` ) résident maintenant.</span><span class="sxs-lookup"><span data-stu-id="522f4-178">The `ProductionsLayout` component contains the top-level layout elements, where the header (`<header>...</header>`) and footer (`<footer>...</footer>`) elements now reside.</span></span> <span data-ttu-id="522f4-179">Le `DoctorWhoLayout` avec le `Episodes` composant est rendu où `@Body` apparaît.</span><span class="sxs-lookup"><span data-stu-id="522f4-179">The `DoctorWhoLayout` with the `Episodes` component is rendered where `@Body` appears.</span></span>

<span data-ttu-id="522f4-180">`Shared/ProductionsLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="522f4-180">`Shared/ProductionsLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

<span data-ttu-id="522f4-181">Le balisage HTML rendu suivant est produit par la disposition imbriquée précédente.</span><span class="sxs-lookup"><span data-stu-id="522f4-181">The following rendered HTML markup is produced by the preceding nested layout.</span></span> <span data-ttu-id="522f4-182">Le balisage superflu n’apparaît pas afin de se concentrer sur le contenu imbriqué fourni par les trois composants impliqués :</span><span class="sxs-lookup"><span data-stu-id="522f4-182">Extraneous markup doesn't appear in order to focus on the nested content provided by the three components involved:</span></span>

* <span data-ttu-id="522f4-183">L’en-tête ( `<header>...</header>` ), la barre de navigation de production ( `<nav>...</nav>` ) et les éléments de pied de page ( `<footer>...</footer>` ) et leur contenu proviennent du `ProductionsLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-183">The header (`<header>...</header>`), production navigation bar (`<nav>...</nav>`), and footer (`<footer>...</footer>`) elements and their content come from the `ProductionsLayout` component.</span></span>
* <span data-ttu-id="522f4-184">Le titre de l’en-tête **&trade; de la base de données du docteur** (), de la barre de `<h1>...</h1>` navigation épisode ( `<nav>...</nav>` ) et de l’élément des informations sur les marques provient du `<div>...</div>` `DoctorWhoLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-184">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`), episode navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="522f4-185">L’en-tête **épisodes** ( `<h2>...</h2>` ) et la liste épisode ( `<ul>...</ul>` ) proviennent du `Episodes` composant.</span><span class="sxs-lookup"><span data-stu-id="522f4-185">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

```html
<body>
    <div id="app">
        <header>
            <h1>Productions</h1>
        </header>

        <nav>
            <a href="master-production-list">Master Production List</a>
            <a href="production-search">Search</a>
            <a href="new-production">Add Production</a>
        </nav>

        <h1>Doctor Who&trade; Episode Database</h1>

        <nav>
            <a href="episode-masterlist">Master Episode List</a>
            <a href="episode-search">Search</a>
            <a href="new-episode">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <div>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </div>

        <footer>
            Footer of Productions Layout
        </footer>
    </div>
</body>
```

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="522f4-186">Partager une Razor disposition de pages avec des composants intégrés</span><span class="sxs-lookup"><span data-stu-id="522f4-186">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="522f4-187">Lorsque des composants routables sont intégrés à une Razor application pages, la disposition partagée de l’application peut être utilisée avec les composants.</span><span class="sxs-lookup"><span data-stu-id="522f4-187">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="522f4-188">Pour plus d’informations, consultez <xref:blazor/components/prerendering-and-integration>.</span><span class="sxs-lookup"><span data-stu-id="522f4-188">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="522f4-189">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="522f4-189">Additional resources</span></span>

* <xref:mvc/views/layout>
