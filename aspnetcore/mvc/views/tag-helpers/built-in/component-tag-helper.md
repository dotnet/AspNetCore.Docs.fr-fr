---
title: Tag Helper Component dans ASP.NET Core
author: guardrex
ms.author: riande
description: 'Découvrez comment utiliser le tag Helper du composant ASP.NET Core pour afficher les :::no-loc(Razor)::: composants dans les pages et les vues.'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431041"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="ddd4c-103">Tag Helper Component dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ddd4c-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="ddd4c-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ddd4c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ddd4c-105">Prérequis</span><span class="sxs-lookup"><span data-stu-id="ddd4c-105">Prerequisites</span></span>

<span data-ttu-id="ddd4c-106">Suivez les instructions de la section *configuration* pour :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a><span data-ttu-id="ddd4c-107">Tag Helper Component</span><span class="sxs-lookup"><span data-stu-id="ddd4c-107">Component Tag Helper</span></span>

<span data-ttu-id="ddd4c-108">Pour afficher un composant à partir d’une page ou d’une vue, utilisez le [tag Helper du composant](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` balise).</span><span class="sxs-lookup"><span data-stu-id="ddd4c-108">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="ddd4c-109">L’intégration de composants dans des :::no-loc(Razor)::: :::no-loc(Razor)::: pages et des applications MVC dans une *:::no-loc(Blazor WebAssembly)::: application hébergée* est prise en charge dans ASP.net core dans .net 5,0 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-109">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="ddd4c-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="ddd4c-111">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-111">Is prerendered into the page.</span></span>
* <span data-ttu-id="ddd4c-112">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une :::no-loc(Blazor)::: application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-112">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ddd4c-113">:::no-loc(Blazor WebAssembly)::: les modes de rendu de l’application sont présentés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-113">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="ddd4c-114">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="ddd4c-114">Render Mode</span></span> | <span data-ttu-id="ddd4c-115">Description</span><span class="sxs-lookup"><span data-stu-id="ddd4c-115">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="ddd4c-116">Restitue un marqueur pour une :::no-loc(Blazor WebAssembly)::: application à utiliser pour inclure un composant interactif lorsqu’il est chargé dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-116">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="ddd4c-117">Le composant n’est pas prérendu.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-117">The component isn't prerendered.</span></span> <span data-ttu-id="ddd4c-118">Cette option facilite le rendu de différents :::no-loc(Blazor WebAssembly)::: composants sur des pages différentes.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-118">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="ddd4c-119">Prérend le composant en HTML statique et comprend un marqueur pour une :::no-loc(Blazor WebAssembly)::: application pour une utilisation ultérieure afin de rendre le composant interactif lorsqu’il est chargé dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-119">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="ddd4c-120">:::no-loc(Blazor Server)::: les modes de rendu de l’application sont présentés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-120">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="ddd4c-121">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="ddd4c-121">Render Mode</span></span> | <span data-ttu-id="ddd4c-122">Description</span><span class="sxs-lookup"><span data-stu-id="ddd4c-122">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ddd4c-123">Restitue le composant en HTML statique et comprend un marqueur pour une :::no-loc(Blazor Server)::: application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-123">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="ddd4c-124">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une :::no-loc(Blazor)::: application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-124">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ddd4c-125">Restitue un marqueur pour une :::no-loc(Blazor Server)::: application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-125">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="ddd4c-126">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-126">Output from the component isn't included.</span></span> <span data-ttu-id="ddd4c-127">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une :::no-loc(Blazor)::: application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-127">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ddd4c-128">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-128">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ddd4c-129">:::no-loc(Blazor Server)::: les modes de rendu de l’application sont présentés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-129">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="ddd4c-130">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="ddd4c-130">Render Mode</span></span> | <span data-ttu-id="ddd4c-131">Description</span><span class="sxs-lookup"><span data-stu-id="ddd4c-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ddd4c-132">Restitue le composant en HTML statique et comprend un marqueur pour une :::no-loc(Blazor Server)::: application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-132">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="ddd4c-133">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une :::no-loc(Blazor)::: application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-133">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ddd4c-134">Restitue un marqueur pour une :::no-loc(Blazor Server)::: application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-134">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="ddd4c-135">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-135">Output from the component isn't included.</span></span> <span data-ttu-id="ddd4c-136">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une :::no-loc(Blazor)::: application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-136">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ddd4c-137">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-137">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="ddd4c-138">Les caractéristiques supplémentaires sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-138">Additional characteristics include:</span></span>

* <span data-ttu-id="ddd4c-139">Plusieurs balises de composant qui restituent plusieurs :::no-loc(Razor)::: composants sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-139">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="ddd4c-140">Les composants ne peuvent pas être rendus dynamiquement après le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-140">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="ddd4c-141">Alors que les pages et les vues peuvent utiliser des composants, la réciproque n’est pas vraie.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="ddd4c-142">Les composants ne peuvent pas utiliser les fonctionnalités spécifiques aux vues et aux pages, telles que les vues partielles et les sections.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="ddd4c-143">Pour utiliser la logique d’une vue partielle dans un composant, factorisez la logique de la vue partielle dans un composant.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="ddd4c-144">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-144">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="ddd4c-145">Le tag Helper Component suivant restitue le `Counter` composant dans une page ou une vue dans une :::no-loc(Blazor Server)::: application avec `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-145">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="ddd4c-146">L’exemple précédent suppose que le `Counter` composant se trouve dans le dossier *pages* de l’application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-146">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="ddd4c-147">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using :::no-loc(Blazor):::Sample.Pages` ou `@using :::no-loc(Blazor):::Sample.Client.Pages` dans une solution hébergée :::no-loc(Blazor)::: ).</span><span class="sxs-lookup"><span data-stu-id="ddd4c-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="ddd4c-148">Le tag Helper Component peut également transmettre des paramètres à des composants.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-148">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="ddd4c-149">Prenons le `ColorfulCheckbox` composant suivant qui définit la couleur et la taille de l’étiquette de case à cocher :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-149">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="ddd4c-150">Les `Size` `int` paramètres du composant () et `Color` ( `string` ) peuvent être définis par le tag Helper du composant : [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="ddd4c-150">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="ddd4c-151">L’exemple précédent suppose que le `ColorfulCheckbox` composant se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-151">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="ddd4c-152">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="ddd4c-152">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="ddd4c-153">Le code HTML suivant est affiché dans la page ou la vue :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-153">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="ddd4c-154">Le passage d’une chaîne entre guillemets requiert une [ :::no-loc(Razor)::: expression explicite](xref:mvc/views/razor#explicit-razor-expressions), comme indiqué `param-Color` dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-154">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="ddd4c-155">Le :::no-loc(Razor)::: comportement d’analyse d’une `string` valeur de type ne s’applique pas à un `param-*` attribut, car l’attribut est un `object` type.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-155">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="ddd4c-156">Tous les types de paramètres sont pris en charge, à l’exception des suivants :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-156">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="ddd4c-157">Paramètres génériques.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-157">Generic parameters.</span></span>
* <span data-ttu-id="ddd4c-158">Paramètres non sérialisables.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-158">Non-serializable parameters.</span></span>
* <span data-ttu-id="ddd4c-159">Héritage dans les paramètres de collection.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-159">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="ddd4c-160">Paramètres dont le type est défini en dehors de l' :::no-loc(Blazor WebAssembly)::: application ou dans un assembly chargé tardivement.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-160">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="ddd4c-161">Le type de paramètre doit être sérialisable JSON, ce qui signifie généralement que le type doit avoir un constructeur par défaut et des propriétés définissables.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-161">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="ddd4c-162">Par exemple, vous pouvez spécifier une valeur pour `Size` et `Color` dans l’exemple précédent, car les types de `Size` et `Color` sont des types primitifs ( `int` et `string` ), qui sont pris en charge par le sérialiseur JSON.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-162">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="ddd4c-163">Dans l’exemple suivant, un objet de classe est passé au composant :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-163">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="ddd4c-164">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-164">*MyClass.cs* :</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="ddd4c-165">**La classe doit avoir un constructeur sans paramètre public.**</span><span class="sxs-lookup"><span data-stu-id="ddd4c-165">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="ddd4c-166">*Shared/MyComponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-166">*Shared/MyComponent.razor* :</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="ddd4c-167">*Pages/MyPage. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ddd4c-167">*Pages/MyPage.cshtml* :</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="ddd4c-168">L’exemple précédent suppose que le `MyComponent` composant se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-168">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="ddd4c-169">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using :::no-loc(Blazor):::Sample` et `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="ddd4c-169">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="ddd4c-170">`MyClass` se trouve dans l’espace de noms de l’application.</span><span class="sxs-lookup"><span data-stu-id="ddd4c-170">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ddd4c-171">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ddd4c-171">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
