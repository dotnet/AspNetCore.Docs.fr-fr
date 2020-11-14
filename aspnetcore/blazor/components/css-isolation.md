---
title: "ASP.NET Core l' Blazor isolation CSS"
author: daveabrock
description: Découvrez comment l’isolation CSS vous permet d’étendre la CSS à vos composants, ce qui peut simplifier votre CSS et éviter les collisions avec d’autres composants ou bibliothèques.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 4fec0fa750b9209849030d0d6b7de8f4e163d62f
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570131"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="def3e-103">ASP.NET Core l' Blazor isolation CSS</span><span class="sxs-lookup"><span data-stu-id="def3e-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="def3e-104">Par [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="def3e-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="def3e-105">L’isolation CSS simplifie l’empreinte CSS d’une application en empêchant les dépendances sur les styles globaux et permet d’éviter les conflits de styles entre les composants et les bibliothèques.</span><span class="sxs-lookup"><span data-stu-id="def3e-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="def3e-106">Activer l’isolation CSS</span><span class="sxs-lookup"><span data-stu-id="def3e-106">Enable CSS isolation</span></span> 

<span data-ttu-id="def3e-107">Pour définir des styles spécifiques au composant, créez un `.razor.css` fichier correspondant au nom du `.razor` fichier du composant.</span><span class="sxs-lookup"><span data-stu-id="def3e-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="def3e-108">Ce `.razor.css` fichier est un *fichier CSS étendu*.</span><span class="sxs-lookup"><span data-stu-id="def3e-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="def3e-109">Pour un `MyComponent` composant qui possède un `MyComponent.razor` fichier, créez un fichier avec le composant appelé `MyComponent.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="def3e-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="def3e-110">La `MyComponent` valeur dans le `.razor.css` nom de fichier n’est **pas** sensible à la casse.</span><span class="sxs-lookup"><span data-stu-id="def3e-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="def3e-111">Par exemple, pour ajouter l’isolement CSS au `Counter` composant dans le Blazor modèle de projet par défaut, ajoutez un nouveau fichier nommé à `Counter.razor.css` côté du `Counter.razor` fichier, puis ajoutez le code CSS suivant :</span><span class="sxs-lookup"><span data-stu-id="def3e-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="def3e-112">Les styles définis dans `Counter.razor.css` sont appliqués uniquement à la sortie rendue du `Counter` composant.</span><span class="sxs-lookup"><span data-stu-id="def3e-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="def3e-113">Les `h1` déclarations CSS définies ailleurs dans l’application ne sont pas en conflit avec les `Counter` styles.</span><span class="sxs-lookup"><span data-stu-id="def3e-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="def3e-114">Afin de garantir l’isolement du style lors du regroupement, `@import` Razor les blocs ne sont pas pris en charge avec les fichiers CSS délimités.</span><span class="sxs-lookup"><span data-stu-id="def3e-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="def3e-115">Regroupement d’isolation CSS</span><span class="sxs-lookup"><span data-stu-id="def3e-115">CSS isolation bundling</span></span>

<span data-ttu-id="def3e-116">L’isolation CSS se produit au moment de la génération.</span><span class="sxs-lookup"><span data-stu-id="def3e-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="def3e-117">Pendant ce processus, Blazor réécrit les sélecteurs CSS pour qu’ils correspondent au balisage rendu par le composant.</span><span class="sxs-lookup"><span data-stu-id="def3e-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="def3e-118">Ces styles CSS réécrits sont regroupés et produits sous la forme d’une ressource statique à `{PROJECT NAME}.styles.css` , où l’espace réservé `{PROJECT NAME}` est le package ou le nom de produit référencé.</span><span class="sxs-lookup"><span data-stu-id="def3e-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="def3e-119">Ces fichiers statiques sont référencés à partir du chemin d’accès racine de l’application par défaut.</span><span class="sxs-lookup"><span data-stu-id="def3e-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="def3e-120">Dans l’application, référencez le fichier groupé en inspectant la référence à l’intérieur `<head>` de la balise du code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="def3e-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="def3e-121">Dans le fichier groupé, chaque composant est associé à un identificateur d’étendue.</span><span class="sxs-lookup"><span data-stu-id="def3e-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="def3e-122">Pour chaque composant stylisé, un attribut HTML est ajouté avec le format `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="def3e-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="def3e-123">L’identificateur est unique et différent pour chaque application.</span><span class="sxs-lookup"><span data-stu-id="def3e-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="def3e-124">Dans le `Counter` composant rendu, Blazor ajoute un identificateur de portée à l' `h1` élément :</span><span class="sxs-lookup"><span data-stu-id="def3e-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="def3e-125">Le `MyProjectName.styles.css` fichier utilise l’identificateur de portée pour regrouper une déclaration de style avec son composant.</span><span class="sxs-lookup"><span data-stu-id="def3e-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="def3e-126">L’exemple suivant fournit le style de l' `<h1>` élément précédent :</span><span class="sxs-lookup"><span data-stu-id="def3e-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="def3e-127">Au moment de la génération, un bundle de projet est créé avec la convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` , où l’espace réservé `{STATIC WEB ASSETS BASE PATH}` est le chemin de base des ressources Web statiques.</span><span class="sxs-lookup"><span data-stu-id="def3e-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="def3e-128">Si d’autres projets sont utilisés, tels que des packages NuGet ou des [ Razor bibliothèques de classes](xref:blazor/components/class-libraries), le fichier groupé :</span><span class="sxs-lookup"><span data-stu-id="def3e-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="def3e-129">Fait référence aux styles à l’aide des importations CSS.</span><span class="sxs-lookup"><span data-stu-id="def3e-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="def3e-130">N’est pas publié en tant que ressource Web statique de l’application qui consomme les styles.</span><span class="sxs-lookup"><span data-stu-id="def3e-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="def3e-131">Prise en charge des composants enfants</span><span class="sxs-lookup"><span data-stu-id="def3e-131">Child component support</span></span>

<span data-ttu-id="def3e-132">Par défaut, l’isolation CSS s’applique uniquement au composant que vous associez au format `{COMPONENT NAME}.razor.css` , où l’espace réservé `{COMPONENT NAME}` est généralement le nom du composant.</span><span class="sxs-lookup"><span data-stu-id="def3e-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="def3e-133">Pour appliquer des modifications à un composant enfant, utilisez `::deep` combin pour tous les éléments descendants dans le fichier du composant parent `.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="def3e-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="def3e-134">Le `::deep` combinateur sélectionne les éléments qui sont des *descendants* de l’identificateur de portée généré d’un élément.</span><span class="sxs-lookup"><span data-stu-id="def3e-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="def3e-135">L’exemple suivant montre un composant parent appelé `Parent` avec un composant enfant appelé `Child` .</span><span class="sxs-lookup"><span data-stu-id="def3e-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="def3e-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="def3e-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="def3e-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="def3e-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="def3e-138">Mettez à jour la `h1` déclaration dans `Parent.razor.css` avec le `::deep` combinateur pour signifier que la `h1` déclaration de style doit s’appliquer au composant parent et à ses enfants :</span><span class="sxs-lookup"><span data-stu-id="def3e-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="def3e-139">Le `h1` style s’applique désormais aux `Parent` `Child` composants et sans qu’il soit nécessaire de créer un fichier CSS délimité distinct pour le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="def3e-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="def3e-140">`::deep`Combin ne fonctionne qu’avec les éléments descendants.</span><span class="sxs-lookup"><span data-stu-id="def3e-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="def3e-141">La structure HTML suivante applique les `h1` styles aux composants comme prévu :</span><span class="sxs-lookup"><span data-stu-id="def3e-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="def3e-142">Dans ce scénario, ASP.NET Core applique l’identificateur de portée du composant parent à l' `div` élément, de sorte que le navigateur sache qu’il doit hériter des styles du composant parent.</span><span class="sxs-lookup"><span data-stu-id="def3e-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="def3e-143">Toutefois, l’exclusion de l' `div` élément supprime la relation descendante et le style n’est **pas** appliqué au composant enfant :</span><span class="sxs-lookup"><span data-stu-id="def3e-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="def3e-144">Prise en charge du préprocesseur CSS</span><span class="sxs-lookup"><span data-stu-id="def3e-144">CSS preprocessor support</span></span>

<span data-ttu-id="def3e-145">Les préprocesseurs CSS sont utiles pour améliorer le développement CSS en utilisant des fonctionnalités telles que les variables, l’imbrication, les modules, les Mixins et l’héritage.</span><span class="sxs-lookup"><span data-stu-id="def3e-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="def3e-146">Bien que l’isolation CSS ne prenne pas en charge en mode natif les préprocesseurs CSS tels que Sass ou Less, l’intégration des préprocesseurs CSS est transparente tant que la compilation du préprocesseur se produit avant Blazor la réécriture des sélecteurs CSS pendant le processus de génération.</span><span class="sxs-lookup"><span data-stu-id="def3e-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="def3e-147">À l’aide de Visual Studio, par exemple, configurez la compilation de préprocesseur existante en tant que tâche **avant génération** dans l’Explorateur de l’exécuteur de tâches Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="def3e-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="def3e-148">De nombreux packages NuGet tiers, tels que [Delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), peuvent compiler des fichiers Sass/SCSS au début du processus de génération avant l’isolation CSS et aucune autre configuration supplémentaire n’est requise.</span><span class="sxs-lookup"><span data-stu-id="def3e-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="def3e-149">Configuration de l’isolation CSS</span><span class="sxs-lookup"><span data-stu-id="def3e-149">CSS isolation configuration</span></span>

<span data-ttu-id="def3e-150">L’isolation CSS est conçue pour fonctionner de façon prête à l’emploi, mais elle fournit une configuration pour certains scénarios avancés, par exemple lorsqu’il existe des dépendances avec des outils ou des workflows existants.</span><span class="sxs-lookup"><span data-stu-id="def3e-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="def3e-151">Personnaliser le format de l’identificateur d’étendue</span><span class="sxs-lookup"><span data-stu-id="def3e-151">Customize scope identifier format</span></span>

<span data-ttu-id="def3e-152">Par défaut, les identificateurs d’étendue utilisent le format `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="def3e-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="def3e-153">Pour personnaliser le format de l’identificateur de l’étendue, mettez à jour le fichier projet selon un modèle de votre choix :</span><span class="sxs-lookup"><span data-stu-id="def3e-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="def3e-154">Dans l’exemple précédent, le code CSS généré pour `MyComponent.Razor.css` remplace son identificateur de portée `b-<10-character-string>` par `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="def3e-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="def3e-155">Modifier le chemin de base des ressources Web statiques</span><span class="sxs-lookup"><span data-stu-id="def3e-155">Change base path for static web assets</span></span>

<span data-ttu-id="def3e-156">Le `scoped.styles.css` fichier est généré à la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="def3e-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="def3e-157">Dans le fichier projet, utilisez la `StaticWebAssetBasePath` propriété pour modifier le chemin d’accès par défaut.</span><span class="sxs-lookup"><span data-stu-id="def3e-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="def3e-158">L’exemple suivant place le `scoped.styles.css` fichier, et le reste des ressources de l’application, au `_content` chemin d’accès suivant :</span><span class="sxs-lookup"><span data-stu-id="def3e-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="def3e-159">Désactiver le regroupement automatique</span><span class="sxs-lookup"><span data-stu-id="def3e-159">Disable automatic bundling</span></span>

<span data-ttu-id="def3e-160">Pour désactiver la manière dont Blazor publie et charge les fichiers délimités au moment de l’exécution, utilisez la `DisableScopedCssBundling` propriété.</span><span class="sxs-lookup"><span data-stu-id="def3e-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="def3e-161">Quand vous utilisez cette propriété, cela signifie que d’autres outils ou processus sont chargés de placer les fichiers CSS isolés à partir du `obj` répertoire et de les publier et de les charger au moment de l’exécution :</span><span class="sxs-lookup"><span data-stu-id="def3e-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="def3e-162">Razor prise en charge de la bibliothèque de classes (RCL)</span><span class="sxs-lookup"><span data-stu-id="def3e-162">Razor class library (RCL) support</span></span>

<span data-ttu-id="def3e-163">Quand une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) fournit des styles isolés, l' `<link>` attribut de la balise `href` pointe vers `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` , où les espaces réservés sont :</span><span class="sxs-lookup"><span data-stu-id="def3e-163">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="def3e-164">`{STATIC WEB ASSET BASE PATH}`: Le chemin d’accès de base des ressources Web statiques.</span><span class="sxs-lookup"><span data-stu-id="def3e-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="def3e-165">`{ASSEMBLY NAME}`: Le nom de l’assembly de la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="def3e-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="def3e-166">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="def3e-166">In the following example:</span></span>

* <span data-ttu-id="def3e-167">Le chemin d’accès de base des ressources Web statiques est `_content/ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="def3e-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="def3e-168">Le nom de l’assembly de la bibliothèque de classes est `ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="def3e-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="def3e-169">Pour plus d’informations sur les bibliothèques de composants et les RCLs, consultez :</span><span class="sxs-lookup"><span data-stu-id="def3e-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="def3e-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="def3e-170"><xref:blazor/components/class-libraries>.</span></span>
