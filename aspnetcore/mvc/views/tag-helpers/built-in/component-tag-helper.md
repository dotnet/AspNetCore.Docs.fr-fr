---
title: Tag Helper Component dans ASP.NET Core
author: guardrex
ms.author: riande
description: Découvrez comment utiliser le tag Helper du composant ASP.NET Core pour afficher les Razor composants dans les pages et les vues.
ms.custom: mvc
ms.date: 10/29/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431041"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Tag Helper Component dans ASP.NET Core

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

## <a name="prerequisites"></a>Prérequis

Suivez les instructions de la section *configuration* pour :

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a>Tag Helper Component

Pour afficher un composant à partir d’une page ou d’une vue, utilisez le [tag Helper du composant](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` balise).

> [!NOTE]
> L’intégration de composants dans des Razor Razor pages et des applications MVC dans une *Blazor WebAssembly application hébergée* est prise en charge dans ASP.net core dans .net 5,0 ou version ultérieure.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Configure si le composant :

* Est prérendu dans la page.
* Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly les modes de rendu de l’application sont présentés dans le tableau suivant.

| Mode de rendu | Description |
| ----------- | ----------- |
| `WebAssembly` | Restitue un marqueur pour une Blazor WebAssembly application à utiliser pour inclure un composant interactif lorsqu’il est chargé dans le navigateur. Le composant n’est pas prérendu. Cette option facilite le rendu de différents Blazor WebAssembly composants sur des pages différentes. |
| `WebAssemblyPrerendered` | Prérend le composant en HTML statique et comprend un marqueur pour une Blazor WebAssembly application pour une utilisation ultérieure afin de rendre le composant interactif lorsqu’il est chargé dans le navigateur. |

Blazor Server les modes de rendu de l’application sont présentés dans le tableau suivant.

| Mode de rendu | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Restitue le composant en HTML statique et comprend un marqueur pour une Blazor Server application. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor Server application. La sortie du composant n’est pas incluse. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du composant en HTML statique. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor Server les modes de rendu de l’application sont présentés dans le tableau suivant.

| Mode de rendu | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Restitue le composant en HTML statique et comprend un marqueur pour une Blazor Server application. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor Server application. La sortie du composant n’est pas incluse. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du composant en HTML statique. |

::: moniker-end

Les caractéristiques supplémentaires sont les suivantes :

* Plusieurs balises de composant qui restituent plusieurs Razor composants sont autorisées.
* Les composants ne peuvent pas être rendus dynamiquement après le démarrage de l’application.
* Alors que les pages et les vues peuvent utiliser des composants, la réciproque n’est pas vraie. Les composants ne peuvent pas utiliser les fonctionnalités spécifiques aux vues et aux pages, telles que les vues partielles et les sections. Pour utiliser la logique d’une vue partielle dans un composant, factorisez la logique de la vue partielle dans un composant.
* Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.

Le tag Helper Component suivant restitue le `Counter` composant dans une page ou une vue dans une Blazor Server application avec `ServerPrerendered` :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

L’exemple précédent suppose que le `Counter` composant se trouve dans le dossier *pages* de l’application. L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using BlazorSample.Pages` ou `@using BlazorSample.Client.Pages` dans une solution hébergée Blazor ).

Le tag Helper Component peut également transmettre des paramètres à des composants. Prenons le `ColorfulCheckbox` composant suivant qui définit la couleur et la taille de l’étiquette de case à cocher :

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
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

Les `Size` `int` paramètres du composant () et `Color` ( `string` ) peuvent être définis par le tag Helper du composant : [component parameters](xref:blazor/components/index#component-parameters)

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

L’exemple précédent suppose que le `ColorfulCheckbox` composant se trouve dans le dossier *partagé* de l’application. L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using BlazorSample.Shared` ).

Le code HTML suivant est affiché dans la page ou la vue :

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Le passage d’une chaîne entre guillemets requiert une [ Razor expression explicite](xref:mvc/views/razor#explicit-razor-expressions), comme indiqué `param-Color` dans l’exemple précédent. Le Razor comportement d’analyse d’une `string` valeur de type ne s’applique pas à un `param-*` attribut, car l’attribut est un `object` type.

Tous les types de paramètres sont pris en charge, à l’exception des suivants :

* Paramètres génériques.
* Paramètres non sérialisables.
* Héritage dans les paramètres de collection.
* Paramètres dont le type est défini en dehors de l' Blazor WebAssembly application ou dans un assembly chargé tardivement.

Le type de paramètre doit être sérialisable JSON, ce qui signifie généralement que le type doit avoir un constructeur par défaut et des propriétés définissables. Par exemple, vous pouvez spécifier une valeur pour `Size` et `Color` dans l’exemple précédent, car les types de `Size` et `Color` sont des types primitifs ( `int` et `string` ), qui sont pris en charge par le sérialiseur JSON.

Dans l’exemple suivant, un objet de classe est passé au composant :

*MyClass.cs* :

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

**La classe doit avoir un constructeur sans paramètre public.**

*Shared/MyComponent. Razor* :

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

*Pages/MyPage. cshtml* :

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

L’exemple précédent suppose que le `MyComponent` composant se trouve dans le dossier *partagé* de l’application. L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using BlazorSample` et `@using BlazorSample.Shared` ). `MyClass` se trouve dans l’espace de noms de l’application.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
