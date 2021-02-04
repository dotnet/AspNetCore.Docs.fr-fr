---
title: ASP.NET Core l' Blazor isolation CSS
author: daveabrock
description: Découvrez comment l’isolation CSS vous permet d’étendre la CSS à vos composants, ce qui peut simplifier votre CSS et éviter les collisions avec d’autres composants ou bibliothèques.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529980"
---
# <a name="aspnet-core-blazor-css-isolation"></a><span data-ttu-id="0773d-103">ASP.NET Core l' Blazor isolation CSS</span><span class="sxs-lookup"><span data-stu-id="0773d-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="0773d-104">Par [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="0773d-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="0773d-105">L’isolation CSS simplifie l’empreinte CSS d’une application en empêchant les dépendances sur les styles globaux et permet d’éviter les conflits de styles entre les composants et les bibliothèques.</span><span class="sxs-lookup"><span data-stu-id="0773d-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="0773d-106">Activer l’isolation CSS</span><span class="sxs-lookup"><span data-stu-id="0773d-106">Enable CSS isolation</span></span> 

<span data-ttu-id="0773d-107">Pour définir des styles spécifiques au composant, créez un `.razor.css` fichier correspondant au nom du `.razor` fichier du composant dans le même dossier.</span><span class="sxs-lookup"><span data-stu-id="0773d-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component in the same folder.</span></span> <span data-ttu-id="0773d-108">Le `.razor.css` fichier est un *fichier CSS étendu*.</span><span class="sxs-lookup"><span data-stu-id="0773d-108">The `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="0773d-109">Pour un `Example` composant dans un `Example.razor` fichier, créez un fichier avec le composant nommé `Example.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="0773d-109">For an `Example` component in an `Example.razor` file, create a file alongside the component named `Example.razor.css`.</span></span> <span data-ttu-id="0773d-110">Le `Example.razor.css` fichier doit résider dans le même dossier que le `Example` composant ( `Example.razor` ).</span><span class="sxs-lookup"><span data-stu-id="0773d-110">The `Example.razor.css` file must reside in the same folder as the `Example` component (`Example.razor`).</span></span> <span data-ttu-id="0773d-111">Le `Example` nom de base du fichier n’est **pas** sensible à la casse.</span><span class="sxs-lookup"><span data-stu-id="0773d-111">The "`Example`" base name of the file is **not** case-sensitive.</span></span>

<span data-ttu-id="0773d-112">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="0773d-112">`Pages/Example.razor`:</span></span>

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

<span data-ttu-id="0773d-113">`Pages/Example.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="0773d-113">`Pages/Example.razor.css`:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="0773d-114">**Les styles définis dans `Example.razor.css` sont appliqués uniquement à la sortie rendue du `Example` composant.**</span><span class="sxs-lookup"><span data-stu-id="0773d-114">**The styles defined in `Example.razor.css` are only applied to the rendered output of the `Example` component.**</span></span> <span data-ttu-id="0773d-115">L’isolation CSS est appliquée aux éléments HTML dans le Razor fichier correspondant.</span><span class="sxs-lookup"><span data-stu-id="0773d-115">CSS isolation is applied to HTML elements in the matching Razor file.</span></span> <span data-ttu-id="0773d-116">Les `h1` déclarations CSS définies ailleurs dans l’application ne sont pas en conflit avec les `Example` styles du composant.</span><span class="sxs-lookup"><span data-stu-id="0773d-116">Any `h1` CSS declarations defined elsewhere in the app don't conflict with the `Example` component's styles.</span></span>

> [!NOTE]
> <span data-ttu-id="0773d-117">Pour garantir l’isolement du style lors du regroupement, l’importation de CSS dans des Razor blocs de code n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="0773d-117">In order to guarantee style isolation when bundling occurs, importing CSS in Razor code blocks isn't supported.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="0773d-118">Regroupement d’isolation CSS</span><span class="sxs-lookup"><span data-stu-id="0773d-118">CSS isolation bundling</span></span>

<span data-ttu-id="0773d-119">L’isolation CSS se produit au moment de la génération.</span><span class="sxs-lookup"><span data-stu-id="0773d-119">CSS isolation occurs at build time.</span></span> <span data-ttu-id="0773d-120">Pendant ce processus, Blazor réécrit les sélecteurs CSS pour qu’ils correspondent au balisage rendu par le composant.</span><span class="sxs-lookup"><span data-stu-id="0773d-120">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="0773d-121">Ces styles CSS réécrits sont regroupés et produits sous la forme d’une ressource statique à `{PROJECT NAME}.styles.css` , où l’espace réservé `{PROJECT NAME}` est le package ou le nom de produit référencé.</span><span class="sxs-lookup"><span data-stu-id="0773d-121">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="0773d-122">Ces fichiers statiques sont référencés à partir du chemin d’accès racine de l’application par défaut.</span><span class="sxs-lookup"><span data-stu-id="0773d-122">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="0773d-123">Dans l’application, référencez le fichier groupé en inspectant la référence à l’intérieur `<head>` de la balise du code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="0773d-123">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="0773d-124">Dans le fichier groupé, chaque composant est associé à un identificateur d’étendue.</span><span class="sxs-lookup"><span data-stu-id="0773d-124">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="0773d-125">Pour chaque composant stylisé, un attribut HTML est ajouté avec le format `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="0773d-125">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="0773d-126">L’identificateur est unique et différent pour chaque application.</span><span class="sxs-lookup"><span data-stu-id="0773d-126">The identifier is unique and different for each app.</span></span> <span data-ttu-id="0773d-127">Dans le `Counter` composant rendu, Blazor ajoute un identificateur de portée à l' `h1` élément :</span><span class="sxs-lookup"><span data-stu-id="0773d-127">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="0773d-128">Le `ProjectName.styles.css` fichier utilise l’identificateur de portée pour regrouper une déclaration de style avec son composant.</span><span class="sxs-lookup"><span data-stu-id="0773d-128">The `ProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="0773d-129">L’exemple suivant fournit le style de l' `<h1>` élément précédent :</span><span class="sxs-lookup"><span data-stu-id="0773d-129">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="0773d-130">Au moment de la génération, un bundle de projet est créé avec la convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` , où l’espace réservé `{STATIC WEB ASSETS BASE PATH}` est le chemin de base des ressources Web statiques.</span><span class="sxs-lookup"><span data-stu-id="0773d-130">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="0773d-131">Si d’autres projets sont utilisés, tels que des packages NuGet ou des [ Razor bibliothèques de classes](xref:blazor/components/class-libraries), le fichier groupé :</span><span class="sxs-lookup"><span data-stu-id="0773d-131">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="0773d-132">Fait référence aux styles à l’aide des importations CSS.</span><span class="sxs-lookup"><span data-stu-id="0773d-132">References the styles using CSS imports.</span></span>
* <span data-ttu-id="0773d-133">N’est pas publié en tant que ressource Web statique de l’application qui consomme les styles.</span><span class="sxs-lookup"><span data-stu-id="0773d-133">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="0773d-134">Prise en charge des composants enfants</span><span class="sxs-lookup"><span data-stu-id="0773d-134">Child component support</span></span>

<span data-ttu-id="0773d-135">Par défaut, l’isolation CSS s’applique uniquement au composant que vous associez au format `{COMPONENT NAME}.razor.css` , où l’espace réservé `{COMPONENT NAME}` est généralement le nom du composant.</span><span class="sxs-lookup"><span data-stu-id="0773d-135">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="0773d-136">Pour appliquer des modifications à un composant enfant, utilisez `::deep` combin pour tous les éléments descendants dans le fichier du composant parent `.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="0773d-136">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="0773d-137">Le `::deep` combinateur sélectionne les éléments qui sont des *descendants* de l’identificateur de portée généré d’un élément.</span><span class="sxs-lookup"><span data-stu-id="0773d-137">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="0773d-138">L’exemple suivant montre un composant parent appelé `Parent` avec un composant enfant appelé `Child` .</span><span class="sxs-lookup"><span data-stu-id="0773d-138">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="0773d-139">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="0773d-139">`Pages/Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="0773d-140">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="0773d-140">`Shared/Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="0773d-141">Mettez à jour la `h1` déclaration dans `Parent.razor.css` avec le `::deep` combinateur pour signifier que la `h1` déclaration de style doit s’appliquer au composant parent et à ses enfants.</span><span class="sxs-lookup"><span data-stu-id="0773d-141">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children.</span></span>

<span data-ttu-id="0773d-142">`Pages/Parent.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="0773d-142">`Pages/Parent.razor.css`:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="0773d-143">Le `h1` style s’applique désormais aux `Parent` `Child` composants et sans qu’il soit nécessaire de créer un fichier CSS délimité distinct pour le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="0773d-143">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

<span data-ttu-id="0773d-144">`::deep`Combin ne fonctionne qu’avec les éléments descendants.</span><span class="sxs-lookup"><span data-stu-id="0773d-144">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="0773d-145">La balise suivante applique les `h1` styles aux composants comme prévu.</span><span class="sxs-lookup"><span data-stu-id="0773d-145">The following markup applies the `h1` styles to components as expected.</span></span> <span data-ttu-id="0773d-146">L’identificateur de portée du composant parent est appliqué à l' `div` élément, de sorte que le navigateur sait qu’il doit hériter des styles du composant parent.</span><span class="sxs-lookup"><span data-stu-id="0773d-146">The parent component's scope identifier is applied to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>

<span data-ttu-id="0773d-147">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="0773d-147">`Pages/Parent.razor`:</span></span>

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

<span data-ttu-id="0773d-148">Toutefois, l’exclusion de l' `div` élément supprime la relation descendante.</span><span class="sxs-lookup"><span data-stu-id="0773d-148">However, excluding the `div` element removes the descendant relationship.</span></span> <span data-ttu-id="0773d-149">Dans l’exemple suivant, le style n’est **pas** appliqué au composant enfant.</span><span class="sxs-lookup"><span data-stu-id="0773d-149">In the following example, the style is **not** applied to the child component.</span></span>

<span data-ttu-id="0773d-150">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="0773d-150">`Pages/Parent.razor`:</span></span>

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a><span data-ttu-id="0773d-151">Prise en charge du préprocesseur CSS</span><span class="sxs-lookup"><span data-stu-id="0773d-151">CSS preprocessor support</span></span>

<span data-ttu-id="0773d-152">Les préprocesseurs CSS sont utiles pour améliorer le développement CSS en utilisant des fonctionnalités telles que les variables, l’imbrication, les modules, les Mixins et l’héritage.</span><span class="sxs-lookup"><span data-stu-id="0773d-152">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="0773d-153">Bien que l’isolation CSS ne prenne pas en charge en mode natif les préprocesseurs CSS tels que Sass ou Less, l’intégration des préprocesseurs CSS est transparente tant que la compilation du préprocesseur se produit avant Blazor la réécriture des sélecteurs CSS pendant le processus de génération.</span><span class="sxs-lookup"><span data-stu-id="0773d-153">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="0773d-154">À l’aide de Visual Studio, par exemple, configurez la compilation de préprocesseur existante en tant que tâche **avant génération** dans l’Explorateur de l’exécuteur de tâches Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0773d-154">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="0773d-155">De nombreux packages NuGet tiers, tels que [Delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), peuvent compiler des fichiers Sass/SCSS au début du processus de génération avant l’isolation CSS et aucune configuration supplémentaire n’est requise.</span><span class="sxs-lookup"><span data-stu-id="0773d-155">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="0773d-156">Configuration de l’isolation CSS</span><span class="sxs-lookup"><span data-stu-id="0773d-156">CSS isolation configuration</span></span>

<span data-ttu-id="0773d-157">L’isolation CSS est conçue pour fonctionner de façon prête à l’emploi, mais elle fournit une configuration pour certains scénarios avancés, par exemple lorsqu’il existe des dépendances avec des outils ou des workflows existants.</span><span class="sxs-lookup"><span data-stu-id="0773d-157">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="0773d-158">Personnaliser le format de l’identificateur d’étendue</span><span class="sxs-lookup"><span data-stu-id="0773d-158">Customize scope identifier format</span></span>

<span data-ttu-id="0773d-159">Par défaut, les identificateurs d’étendue utilisent le format `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="0773d-159">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="0773d-160">Pour personnaliser le format de l’identificateur de l’étendue, mettez à jour le fichier projet selon un modèle de votre choix :</span><span class="sxs-lookup"><span data-stu-id="0773d-160">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="0773d-161">Dans l’exemple précédent, le code CSS généré pour `Example.Razor.css` remplace son identificateur de portée `b-<10-character-string>` par `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="0773d-161">In the preceding example, the CSS generated for `Example.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

<span data-ttu-id="0773d-162">Utilisez des identificateurs d’étendue pour obtenir un héritage avec les fichiers CSS délimités.</span><span class="sxs-lookup"><span data-stu-id="0773d-162">Use scope identifiers to achieve inheritance with scoped CSS files.</span></span> <span data-ttu-id="0773d-163">Dans l’exemple de fichier projet suivant, un `BaseComponent.razor.css` fichier contient des styles communs entre les composants.</span><span class="sxs-lookup"><span data-stu-id="0773d-163">In the following project file example, a `BaseComponent.razor.css` file contains common styles across components.</span></span> <span data-ttu-id="0773d-164">Un `DerivedComponent.razor.css` fichier hérite de ces styles.</span><span class="sxs-lookup"><span data-stu-id="0773d-164">A `DerivedComponent.razor.css` file inherits these styles.</span></span>

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="0773d-165">Utilisez l’opérateur générique ( `*` ) pour partager des identificateurs d’étendue sur plusieurs fichiers :</span><span class="sxs-lookup"><span data-stu-id="0773d-165">Use the wildcard (`*`) operator to share scope identifiers across multiple files:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="0773d-166">Modifier le chemin de base des ressources Web statiques</span><span class="sxs-lookup"><span data-stu-id="0773d-166">Change base path for static web assets</span></span>

<span data-ttu-id="0773d-167">Le `scoped.styles.css` fichier est généré à la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="0773d-167">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="0773d-168">Dans le fichier projet, utilisez la `StaticWebAssetBasePath` propriété pour modifier le chemin d’accès par défaut.</span><span class="sxs-lookup"><span data-stu-id="0773d-168">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="0773d-169">L’exemple suivant place le `scoped.styles.css` fichier, et le reste des ressources de l’application, au `_content` chemin d’accès suivant :</span><span class="sxs-lookup"><span data-stu-id="0773d-169">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="0773d-170">Désactiver le regroupement automatique</span><span class="sxs-lookup"><span data-stu-id="0773d-170">Disable automatic bundling</span></span>

<span data-ttu-id="0773d-171">Pour désactiver la manière dont Blazor publie et charge les fichiers délimités au moment de l’exécution, utilisez la `DisableScopedCssBundling` propriété.</span><span class="sxs-lookup"><span data-stu-id="0773d-171">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="0773d-172">Quand vous utilisez cette propriété, cela signifie que d’autres outils ou processus sont chargés de placer les fichiers CSS isolés à partir du `obj` répertoire et de les publier et de les charger au moment de l’exécution :</span><span class="sxs-lookup"><span data-stu-id="0773d-172">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a><span data-ttu-id="0773d-173">Razor prise en charge de la bibliothèque de classes (RCL)</span><span class="sxs-lookup"><span data-stu-id="0773d-173">Razor class library (RCL) support</span></span>

<span data-ttu-id="0773d-174">Quand une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) fournit des styles isolés, l' `<link>` attribut de la balise `href` pointe vers `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` , où les espaces réservés sont :</span><span class="sxs-lookup"><span data-stu-id="0773d-174">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="0773d-175">`{STATIC WEB ASSET BASE PATH}`: Le chemin d’accès de base des ressources Web statiques.</span><span class="sxs-lookup"><span data-stu-id="0773d-175">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="0773d-176">`{ASSEMBLY NAME}`: Le nom de l’assembly de la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="0773d-176">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="0773d-177">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0773d-177">In the following example:</span></span>

* <span data-ttu-id="0773d-178">Le chemin d’accès de base des ressources Web statiques est `_content/ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="0773d-178">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="0773d-179">Le nom de l’assembly de la bibliothèque de classes est `ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="0773d-179">The class library's assembly name is `ClassLib`.</span></span>

<span data-ttu-id="0773d-180">`wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="0773d-180">`wwwroot/index.html` (Blazor WebAssembly) or `Pages_Host.cshtml` (Blazor Server):</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="0773d-181">Pour plus d’informations sur les bibliothèques de composants et les RCLs, consultez :</span><span class="sxs-lookup"><span data-stu-id="0773d-181">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="0773d-182"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="0773d-182"><xref:blazor/components/class-libraries>.</span></span>
