---
title: ASP.NET Core Blazor les valeurs et paramètres en cascade
author: guardrex
description: Apprenez à transmettre des données d’un composant ancêtre à des composants descendants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280242"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor les valeurs et paramètres en cascade

Les *valeurs et les paramètres en cascade* offrent un moyen convienent de transmettre les données vers le niveau d’une hiérarchie de composants à partir d’un composant ancêtre vers un nombre quelconque de composants decendent. Contrairement aux [paramètres de composant](xref:blazor/components/index#component-parameters), les valeurs et paramètres en cascade ne nécessitent pas d’affectation d’attribut pour chaque composant de descendant dans lequel les données sont consommées. Les valeurs et les paramètres en cascade permettent également de coordonner les composants entre eux dans une hiérarchie de composants.

## <a name="cascadingvalue-component"></a>Composant `CascadingValue`

Un composant ancêtre fournit une valeur en cascade à l’aide du Blazor composant de l’infrastructure [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) , qui encapsule un sous-arbre d’une hiérarchie de composants et fournit une valeur unique à tous les composants de sa sous-arborescence.

L’exemple suivant montre le déroulement des informations de thème dans la hiérarchie des composants d’un composant de disposition pour fournir une classe de style CSS aux boutons des composants enfants.

La `ThemeInfo` classe C# suivante est placée dans un dossier nommé `UIThemeClasses` et spécifie les informations de thème.

> [!NOTE]
> Pour les exemples de cette section, l’espace de noms de l’application est `BlazorSample` . Quand vous expérimentez le code dans votre propre exemple d’application, remplacez l’espace de noms de l’application par l’espace de noms de votre application d’exemple.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

Le [composant de disposition](xref:blazor/layouts) suivant spécifie les informations de thème ( `ThemeInfo` ) comme valeur en cascade pour tous les composants qui composent le corps de la disposition de la <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriété. `ButtonClass` la valeur est affectée à [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) , qui est un style de bouton de démarrage. Tout composant descendant dans la hiérarchie des composants peut utiliser la `ButtonClass` propriété à travers la `ThemeInfo` valeur en cascade.

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a>Attribut `[CascadingParameter]`

Pour utiliser des valeurs en cascade, les composants descendants déclarent des paramètres en cascade à l’aide de l' [ `[CascadingParameter]` attribut](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute). Les valeurs en cascade sont liées aux paramètres **en cascade par type**. Le fait de disposer en cascade de plusieurs valeurs du même type est abordé dans la section [plusieurs valeurs en cascade](#cascade-multiple-values) plus loin dans cet article.

Le composant suivant lie la `ThemeInfo` valeur en cascade à un paramètre en cascade, en utilisant éventuellement le même nom `ThemeInfo` . Le paramètre est utilisé pour définir la classe CSS pour le **`Increment Counter (Themed)`** bouton.

`Pages/ThemedCounter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a>En cascade plusieurs valeurs

Pour mettre en cascade plusieurs valeurs du même type dans la même sous-arborescence, fournissez une <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> chaîne unique à chaque [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) composant et à ses [ `[CascadingParameter]` attributs](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)correspondants.

Dans l’exemple suivant, deux [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) composants montent en cascade différentes instances de `CascadingType` :

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs en cascade du composant ancêtre en <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> :

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a>Transmettre des données dans une hiérarchie de composants

Les paramètres en cascade permettent également aux composants de transmettre des données à travers une hiérarchie de composants. Prenons l’exemple de jeu d’onglets de l’interface utilisateur suivant, où un composant de jeu d’onglets gère une série d’onglets individuels.

> [!NOTE]
> Pour les exemples de cette section, l’espace de noms de l’application est `BlazorSample` . Quand vous expérimentez le code dans votre propre exemple d’application, remplacez l’espace de noms par l’espace de noms de votre application d’exemple.

Créez une `ITab` interface que les onglets implémentent dans un dossier nommé `UIInterfaces` .

`UIInterfaces/ITab.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

Le `TabSet` composant suivant gère un ensemble d’onglets. Les composants du jeu d’onglets `Tab` , qui sont créés plus loin dans cette section, fournissent les éléments de liste ( `<li>...</li>` ) de la liste ( `<ul>...</ul>` ).

`Tab`Les composants enfants ne sont pas explicitement passés comme paramètres à `TabSet` . Au lieu de cela, les `Tab` composants enfants font partie du contenu enfant de `TabSet` . Toutefois, le `TabSet` nécessite toujours une référence `Tab` à chaque composant pour pouvoir afficher les en-têtes et l’onglet actif. Pour activer cette coordination sans nécessiter de code supplémentaire, le `TabSet` composant *peut se présenter comme une valeur en cascade* qui est ensuite récupérée par les `Tab` composants descendants.

`Shared/TabSet.razor`:

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

`Tab`Les composants descendants capturent le conteneur `TabSet` sous la forme d’un paramètre en cascade. Les `Tab` composants s’ajoutent à la `TabSet` coordonnée et pour définir l’onglet actif.

`Shared/Tab.razor`:

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

Le `ExampleTabSet` composant suivant utilise le `TabSet` composant, qui contient trois `Tab` composants.

`Pages/ExampleTabSet.razor`:

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```
