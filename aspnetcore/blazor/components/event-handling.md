---
title: BlazorGestion des événements ASP.net Core
author: guardrex
description: Découvrez Blazor les fonctionnalités de gestion des événements de, notamment les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
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
uid: blazor/components/event-handling
ms.openlocfilehash: e8c3d6a9f2c6b50fc18da59b8e0b5475360673c7
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491459"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a>BlazorGestion des événements ASP.net Core

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Razor les composants fournissent des fonctionnalités de gestion des événements. Pour un attribut d’élément HTML nommé [`@on{EVENT}`](xref:mvc/views/razor#onevent) (par exemple, `@onclick` ) avec une valeur de type délégué, un Razor composant traite la valeur de l’attribut en tant que gestionnaire d’événements.

Le code suivant appelle la `UpdateHeading` méthode lorsque le bouton est sélectionné dans l’interface utilisateur :

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Le code suivant appelle la `CheckChanged` méthode lorsque la case à cocher est modifiée dans l’interface utilisateur :

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Les gestionnaires d’événements peuvent également être asynchrones et retourner un <xref:System.Threading.Tasks.Task> . Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/components/lifecycle#state-changes). Les exceptions sont journalisées lorsqu’elles se produisent.

Dans l’exemple suivant, `UpdateHeading` est appelé de façon asynchrone quand le bouton est sélectionné :

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a>Types d’arguments d’événement

Pour certains événements, les types d’arguments d’événement sont autorisés. La spécification d’un paramètre d’événement dans une définition de méthode d’événement est facultative et n’est nécessaire que si le type d’événement est utilisé dans la méthode. Dans l’exemple suivant, l' `MouseEventArgs` argument d’événement est utilisé dans la `ShowMessage` méthode pour définir le texte du message :

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

Les informations prises en charge <xref:System.EventArgs> sont indiquées dans le tableau suivant.

::: moniker range=">= aspnetcore-5.0"

| Événement            | Classe  | Remarques et événements DOM |
| ---------------- | ------ | -------------------- |
| Presse-papiers        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Glissement             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> et <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contiennent des données d’élément glissées.<br><br>Implémentez le glisser-déplacer dans les Blazor applications à l’aide de l' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) avec l' [API html glisser-déplacer](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API). |
| Erreur            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| Événement            | <xref:System.EventArgs> | *Général*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Presse-papiers*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Entrée*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Média*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contient des attributs permettant de configurer les mappages entre les noms d’événements et les types d’arguments d’événement. |
| Priorité            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>N’inclut pas la prise en charge de `relatedTarget` . |
| Entrée            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Clavier         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Souris            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Pointeur de souris    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Roulette de la souris      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Avancement         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toucher            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> représente un point de contact unique sur un appareil tactile. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| Événement            | Classe | Remarques et événements DOM |
| ---------------- | ----- | -------------------- |
| Presse-papiers        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Glissement             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> et <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contiennent des données d’élément glissées.<br><br>Implémentez le glisser-déplacer dans les Blazor applications à l’aide de l' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) avec l' [API html glisser-déplacer](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API). |
| Erreur            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| Événement            | <xref:System.EventArgs> | *Général*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Presse-papiers*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Entrée*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Média*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contient des attributs permettant de configurer les mappages entre les noms d’événements et les types d’arguments d’événement. |
| Priorité            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>N’inclut pas la prise en charge de `relatedTarget` . |
| Entrée            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Clavier         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Souris            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Pointeur de souris    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Roulette de la souris      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Avancement         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toucher            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> représente un point de contact unique sur un appareil tactile. |

::: moniker-end

Pour plus d’informations, consultez les ressources suivantes :

* [ `EventArgs` classes de la source de référence ASP.net Core (branche dotnet/aspnetcore `master` )](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web). La `master` branche représente l’API en cours de développement pour la *prochaine* version de ASP.net core. Pour la version actuelle, sélectionnez la branche de dépôt GitHub appropriée (par exemple, `release/3.1` ).
* [MDN Web docs : GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): contient des informations sur les éléments HTML qui prennent en charge chaque événement DOM.

## <a name="lambda-expressions"></a>Expressions lambda

Les [expressions lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Il est souvent pratique de se rapprocher de valeurs supplémentaires, par exemple lors de l’itération sur un ensemble d’éléments. L’exemple suivant crée trois boutons, chacun d’entre eux qui `UpdateHeading` passe un argument d’événement ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) et son numéro de bouton ( `buttonNumber` ) lorsqu’ils sont sélectionnés dans l’interface utilisateur :

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> N’utilisez **pas** directement une variable de boucle dans une expression lambda, comme `i` dans l' `for` exemple de boucle précédent. Dans le cas contraire, la même variable est utilisée par toutes les expressions lambda, ce qui entraîne l’utilisation de la même valeur dans toutes les expressions lambda. Capturez toujours la valeur de la variable dans une variable locale, puis utilisez-la. Dans l’exemple précédent, la variable `i` de boucle est assignée à `buttonNumber` .

## <a name="eventcallback"></a>EventCallback suivante

Un scénario courant avec des composants imbriqués est le désir d’exécuter la méthode d’un composant parent lorsqu’un événement de composant enfant se produit. Un `onclick` événement qui se produit dans le composant enfant est un cas d’usage courant. Pour exposer des événements entre les composants, utilisez un <xref:Microsoft.AspNetCore.Components.EventCallback> . Un composant parent peut affecter une méthode de rappel à un composant enfant <xref:Microsoft.AspNetCore.Components.EventCallback> .

`ChildComponent`Dans l’exemple d’application ( `Components/ChildComponent.razor` ), montre comment un gestionnaire de bouton `onclick` est configuré pour recevoir un <xref:Microsoft.AspNetCore.Components.EventCallback> délégué à partir de l’exemple de `ParentComponent` . Le <xref:Microsoft.AspNetCore.Components.EventCallback> est typé avec `MouseEventArgs` , ce qui est approprié pour un `onclick` événement à partir d’un périphérique :

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent`Définit le () de l’enfant <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` sur sa `ShowMessage` méthode.

`Pages/ParentComponent.razor`:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Lorsque le bouton est sélectionné dans le `ChildComponent` :

* La `ParentComponent` `ShowMessage` méthode de est appelée. `messageText` est mis à jour et affiché dans le `ParentComponent` .
* Un appel à [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) n’est pas requis dans la méthode du rappel ( `ShowMessage` ). <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est appelé automatiquement pour rerestituer le `ParentComponent` , tout comme les événements enfants déclenchent le rerendu des composants dans les gestionnaires d’événements qui s’exécutent dans l’enfant.

<xref:Microsoft.AspNetCore.Components.EventCallback> et <xref:Microsoft.AspNetCore.Components.EventCallback%601> autorisent les délégués asynchrones. <xref:Microsoft.AspNetCore.Components.EventCallback> est faiblement typé et permet de passer n’importe quel argument de type dans `InvokeAsync(Object)` . <xref:Microsoft.AspNetCore.Components.EventCallback%601> est fortement typé et requiert le passage d’un `T` argument dans `InvokeAsync(T)` qui peut être assigné à `TValue` .

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Appelez <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> avec <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> et en attente de <xref:System.Threading.Tasks.Task> :

```csharp
await OnClickCallback.InvokeAsync(arg);
```

Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> et <xref:Microsoft.AspNetCore.Components.EventCallback%601> pour la gestion des événements et les paramètres de composant de liaison.

Préférez le fortement typé <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> . <xref:Microsoft.AspNetCore.Components.EventCallback%601> fournit un meilleur retour d’erreur aux utilisateurs du composant. Comme pour d’autres gestionnaires d’événements d’interface utilisateur, la spécification du paramètre d’événement est facultative. Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> quand aucune valeur n’est passée au rappel.

## <a name="prevent-default-actions"></a>Empêcher les actions par défaut

Utilisez l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attribut directive pour empêcher l’action par défaut d’un événement.

Quand une clé est sélectionnée sur un appareil d’entrée et que le focus de l’élément se trouve sur une zone de texte, un navigateur affiche normalement le caractère de la clé dans la zone de texte. Dans l’exemple suivant, le comportement par défaut est évité en spécifiant l' `@onkeypress:preventDefault` attribut directive. Le compteur est incrémenté et la **+** clé n’est pas capturée dans la `<input>` valeur de l’élément :

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

La spécification de l' `@on{EVENT}:preventDefault` attribut sans valeur équivaut à `@on{EVENT}:preventDefault="true"` .

La valeur de l’attribut peut également être une expression. Dans l’exemple suivant, `shouldPreventDefault` est un `bool` champ défini sur `true` ou `false` :

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a>Arrêter la propagation des événements

Utilisez l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attribut directive pour arrêter la propagation des événements.

Dans l’exemple suivant, la sélection de la case à cocher empêche les événements Click du deuxième enfant `<div>` de se propager vers le parent `<div>` :

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a>Focus sur un élément

Appelez `FocusAsync` sur une [référence d’élément](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) pour concentrer un élément dans le code :

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
