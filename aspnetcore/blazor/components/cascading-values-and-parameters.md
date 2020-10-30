---
title: ASP.NET Core Blazor les valeurs et paramètres en cascade
author: guardrex
description: Apprenez à transmettre des données d’un composant ancêtre à des composants descendants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
ms.openlocfilehash: dec71f881dc815a26d62da6df92118926b1dcf06
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056424"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor les valeurs et paramètres en cascade

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Dans certains scénarios, il est peu commode de transmettre des données d’un composant ancêtre à un composant descendant à l’aide de [paramètres de composant](xref:blazor/components/index#component-parameters), en particulier lorsqu’il existe plusieurs couches de composant. Les valeurs et paramètres en cascade résolvent ce problème en fournissant un moyen pratique pour un composant ancêtre de fournir une valeur à tous ses composants descendants. Les valeurs et les paramètres en cascade fournissent également une approche pour les composants à coordonner.

### <a name="theme-example"></a>Exemple de thème

Dans l’exemple suivant tiré de l’exemple d’application, la `ThemeInfo` classe spécifie les informations de thème pour descendre dans la hiérarchie des composants, de sorte que tous les boutons d’une partie donnée de l’application partagent le même style.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Un composant ancêtre peut fournir une valeur en cascade à l’aide du composant de valeur en cascade. Le <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composant encapsule une sous-arborescence de la hiérarchie des composants et fournit une valeur unique à tous les composants de cette sous-arborescence.

Par exemple, l’exemple d’application spécifie les informations de thème ( `ThemeInfo` ) dans l’une des dispositions de l’application en tant que paramètre en cascade pour tous les composants qui composent le corps de la disposition de la `@Body` propriété. `ButtonClass` la valeur est affectée `btn-success` à dans le composant Layout. Tout composant descendant peut consommer cette propriété par le biais de l' `ThemeInfo` objet en cascade.

`CascadingValuesParametersLayout` -

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Pour utiliser des valeurs en cascade, les composants déclarent des paramètres en cascade à l’aide de l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribut. Les valeurs en cascade sont liées aux paramètres en cascade par type.

Dans l’exemple d’application, le `CascadingValuesParametersTheme` composant lie la `ThemeInfo` valeur en cascade à un paramètre en cascade. Le paramètre est utilisé pour définir la classe CSS pour l’un des boutons affichés par le composant.

`CascadingValuesParametersTheme` -

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Pour mettre en cascade plusieurs valeurs du même type dans la même sous-arborescence, fournissez une <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> chaîne unique à chaque <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composant et à son [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribut correspondant. Dans l’exemple suivant, deux <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composants montent en cascade différentes instances de `MyCascadingType` par nom :

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs des valeurs en cascade correspondantes dans le composant ancêtre par nom :

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Exemple TabSet

Les paramètres en cascade permettent également aux composants de collaborer au sein de la hiérarchie des composants. Par exemple, considérez l' `TabSet` exemple suivant dans l’exemple d’application.

L’exemple d’application possède une `ITab` interface qui implémente les onglets suivants :

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Le `CascadingValuesParametersTabSet` composant utilise le `TabSet` composant, qui contient plusieurs `Tab` composants :

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
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

Les `Tab` composants enfants ne sont pas explicitement passés comme paramètres à `TabSet` . Au lieu de cela, les `Tab` composants enfants font partie du contenu enfant de `TabSet` . Toutefois, le `TabSet` doit toujours connaître chaque `Tab` composant pour pouvoir afficher les en-têtes et l’onglet actif. Pour activer cette coordination sans nécessiter de code supplémentaire, le `TabSet` composant *peut se présenter comme une valeur en cascade* qui est ensuite récupérée par les `Tab` composants descendants.

`TabSet` -

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Les composants descendants `Tab` capturent le contenant `TabSet` comme paramètre en cascade, de sorte que les `Tab` composants s’ajoutent eux-mêmes à la et à la `TabSet` coordonnée sur laquelle l’onglet est actif.

`Tab` -

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
