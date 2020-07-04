---
title: BlazorGestion des événements ASP.net Core
author: guardrex
description: Découvrez Blazor les fonctionnalités de gestion des événements de, notamment les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: 60a2ff43945bcb92136b3bb8e622669d51ea3b19
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944456"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="cada6-103">BlazorGestion des événements ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="cada6-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="cada6-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cada6-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="cada6-105">les composants fournissent des fonctionnalités de gestion des événements.</span><span class="sxs-lookup"><span data-stu-id="cada6-105"> components provide event handling features.</span></span> <span data-ttu-id="cada6-106">Pour un attribut d’élément HTML nommé [`@on{EVENT}`](xref:mvc/views/razor#onevent) (par exemple, `@onclick` ) avec une valeur de type délégué, un Razor composant traite la valeur de l’attribut en tant que gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="cada6-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="cada6-107">Le code suivant appelle la `UpdateHeading` méthode lorsque le bouton est sélectionné dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="cada6-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="cada6-108">Le code suivant appelle la `CheckChanged` méthode lorsque la case à cocher est modifiée dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="cada6-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="cada6-109">Les gestionnaires d’événements peuvent également être asynchrones et retourner un <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="cada6-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="cada6-110">Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="cada6-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="cada6-111">Les exceptions sont journalisées lorsqu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="cada6-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="cada6-112">Dans l’exemple suivant, `UpdateHeading` est appelé de façon asynchrone quand le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="cada6-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="cada6-113">Types d’arguments d’événement</span><span class="sxs-lookup"><span data-stu-id="cada6-113">Event argument types</span></span>

<span data-ttu-id="cada6-114">Pour certains événements, les types d’arguments d’événement sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="cada6-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="cada6-115">La spécification d’un paramètre d’événement dans une définition de méthode d’événement est facultative et n’est nécessaire que si le type d’événement est utilisé dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="cada6-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="cada6-116">Dans l’exemple suivant, l' `MouseEventArgs` argument d’événement est utilisé dans la `ShowMessage` méthode pour définir le texte du message :</span><span class="sxs-lookup"><span data-stu-id="cada6-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="cada6-117">Les informations prises en charge <xref:System.EventArgs> sont indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="cada6-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="cada6-118">Événement</span><span class="sxs-lookup"><span data-stu-id="cada6-118">Event</span></span>            | <span data-ttu-id="cada6-119">Classe</span><span class="sxs-lookup"><span data-stu-id="cada6-119">Class</span></span>                | <span data-ttu-id="cada6-120">Remarques et événements DOM</span><span class="sxs-lookup"><span data-stu-id="cada6-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="cada6-121">Presse-papiers</span><span class="sxs-lookup"><span data-stu-id="cada6-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="cada6-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="cada6-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="cada6-123">Glissement</span><span class="sxs-lookup"><span data-stu-id="cada6-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="cada6-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="cada6-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="cada6-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>et <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contiennent des données d’élément glissées.</span><span class="sxs-lookup"><span data-stu-id="cada6-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="cada6-126">Erreur</span><span class="sxs-lookup"><span data-stu-id="cada6-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="cada6-127">Événement</span><span class="sxs-lookup"><span data-stu-id="cada6-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="cada6-128">*Généralités*</span><span class="sxs-lookup"><span data-stu-id="cada6-128">*General*</span></span><br><span data-ttu-id="cada6-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="cada6-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="cada6-130">*Presse-papiers*</span><span class="sxs-lookup"><span data-stu-id="cada6-130">*Clipboard*</span></span><br><span data-ttu-id="cada6-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="cada6-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="cada6-132">*Input*</span><span class="sxs-lookup"><span data-stu-id="cada6-132">*Input*</span></span><br><span data-ttu-id="cada6-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="cada6-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="cada6-134">*Média*</span><span class="sxs-lookup"><span data-stu-id="cada6-134">*Media*</span></span><br><span data-ttu-id="cada6-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="cada6-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="cada6-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>contient des attributs permettant de configurer les mappages entre les noms d’événements et les types d’arguments d’événement.</span><span class="sxs-lookup"><span data-stu-id="cada6-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="cada6-137">Focus</span><span class="sxs-lookup"><span data-stu-id="cada6-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="cada6-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="cada6-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="cada6-139">N’inclut pas la prise en charge de `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="cada6-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="cada6-140">Entrée</span><span class="sxs-lookup"><span data-stu-id="cada6-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="cada6-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="cada6-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="cada6-142">Clavier</span><span class="sxs-lookup"><span data-stu-id="cada6-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="cada6-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="cada6-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="cada6-144">Souris</span><span class="sxs-lookup"><span data-stu-id="cada6-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="cada6-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="cada6-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="cada6-146">Pointeur de souris</span><span class="sxs-lookup"><span data-stu-id="cada6-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="cada6-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="cada6-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="cada6-148">Roulette de la souris</span><span class="sxs-lookup"><span data-stu-id="cada6-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="cada6-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="cada6-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="cada6-150">Avancement</span><span class="sxs-lookup"><span data-stu-id="cada6-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="cada6-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="cada6-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="cada6-152">Entrées tactiles</span><span class="sxs-lookup"><span data-stu-id="cada6-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="cada6-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="cada6-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="cada6-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>représente un point de contact unique sur un appareil tactile.</span><span class="sxs-lookup"><span data-stu-id="cada6-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="cada6-155">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="cada6-155">For more information, see the following resources:</span></span>

* <span data-ttu-id="cada6-156">[ `EventArgs` classes de la source de référence ASP.net Core (branche dotnet/aspnetcore ou version 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="cada6-156">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="cada6-157">[MDN Web docs : GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): contient des informations sur les éléments HTML qui prennent en charge chaque événement DOM.</span><span class="sxs-lookup"><span data-stu-id="cada6-157">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="cada6-158">Expressions lambda</span><span class="sxs-lookup"><span data-stu-id="cada6-158">Lambda expressions</span></span>

<span data-ttu-id="cada6-159">Les [expressions lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :</span><span class="sxs-lookup"><span data-stu-id="cada6-159">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="cada6-160">Il est souvent pratique de se rapprocher de valeurs supplémentaires, par exemple lors de l’itération sur un ensemble d’éléments.</span><span class="sxs-lookup"><span data-stu-id="cada6-160">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="cada6-161">L’exemple suivant crée trois boutons, chacun d’entre eux qui `UpdateHeading` passe un argument d’événement ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) et son numéro de bouton ( `buttonNumber` ) lorsqu’ils sont sélectionnés dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="cada6-161">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="cada6-162">N’utilisez **pas** directement une variable de boucle dans une expression lambda, comme `i` dans l' `for` exemple de boucle précédent.</span><span class="sxs-lookup"><span data-stu-id="cada6-162">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="cada6-163">Dans le cas contraire, la même variable est utilisée par toutes les expressions lambda, ce qui entraîne l’utilisation de la même valeur dans toutes les expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="cada6-163">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="cada6-164">Capturez toujours la valeur de la variable dans une variable locale, puis utilisez-la.</span><span class="sxs-lookup"><span data-stu-id="cada6-164">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="cada6-165">Dans l’exemple précédent, la variable `i` de boucle est assignée à `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="cada6-165">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="cada6-166">EventCallback suivante</span><span class="sxs-lookup"><span data-stu-id="cada6-166">EventCallback</span></span>

<span data-ttu-id="cada6-167">Un scénario courant avec des composants imbriqués est le désir d’exécuter la méthode d’un composant parent lorsqu’un événement de composant enfant se produit.</span><span class="sxs-lookup"><span data-stu-id="cada6-167">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="cada6-168">Un `onclick` événement qui se produit dans le composant enfant est un cas d’usage courant.</span><span class="sxs-lookup"><span data-stu-id="cada6-168">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="cada6-169">Pour exposer des événements entre les composants, utilisez un <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="cada6-169">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="cada6-170">Un composant parent peut affecter une méthode de rappel à un composant enfant <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="cada6-170">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="cada6-171">`ChildComponent`Dans l’exemple d’application ( `Components/ChildComponent.razor` ), montre comment un gestionnaire de bouton `onclick` est configuré pour recevoir un <xref:Microsoft.AspNetCore.Components.EventCallback> délégué à partir de l’exemple de `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="cada6-171">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="cada6-172">Le <xref:Microsoft.AspNetCore.Components.EventCallback> est typé avec `MouseEventArgs` , ce qui est approprié pour un `onclick` événement à partir d’un périphérique :</span><span class="sxs-lookup"><span data-stu-id="cada6-172">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="cada6-173">`ParentComponent`Définit le () de l’enfant <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` sur sa `ShowMessage` méthode.</span><span class="sxs-lookup"><span data-stu-id="cada6-173">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="cada6-174">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="cada6-174">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="cada6-175">Lorsque le bouton est sélectionné dans le `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="cada6-175">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="cada6-176">La `ParentComponent` `ShowMessage` méthode de est appelée.</span><span class="sxs-lookup"><span data-stu-id="cada6-176">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="cada6-177">`messageText`est mis à jour et affiché dans le `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="cada6-177">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="cada6-178">Un appel à [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) n’est pas requis dans la méthode du rappel ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="cada6-178">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="cada6-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé automatiquement pour rerestituer le `ParentComponent` , tout comme les événements enfants déclenchent le rerendu des composants dans les gestionnaires d’événements qui s’exécutent dans l’enfant.</span><span class="sxs-lookup"><span data-stu-id="cada6-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="cada6-180"><xref:Microsoft.AspNetCore.Components.EventCallback>et <xref:Microsoft.AspNetCore.Components.EventCallback%601> autorisent les délégués asynchrones.</span><span class="sxs-lookup"><span data-stu-id="cada6-180"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="cada6-181"><xref:Microsoft.AspNetCore.Components.EventCallback%601>est fortement typé et requiert un type d’argument spécifique.</span><span class="sxs-lookup"><span data-stu-id="cada6-181"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="cada6-182"><xref:Microsoft.AspNetCore.Components.EventCallback>est faiblement typé et autorise tout type d’argument.</span><span class="sxs-lookup"><span data-stu-id="cada6-182"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="cada6-183">Appelez <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> avec <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> et en attente de <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="cada6-183">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="cada6-184">Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> et <xref:Microsoft.AspNetCore.Components.EventCallback%601> pour la gestion des événements et les paramètres de composant de liaison.</span><span class="sxs-lookup"><span data-stu-id="cada6-184">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="cada6-185">Préférez le fortement typé <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="cada6-185">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="cada6-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601>fournit un meilleur retour d’erreur aux utilisateurs du composant.</span><span class="sxs-lookup"><span data-stu-id="cada6-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="cada6-187">Comme pour d’autres gestionnaires d’événements d’interface utilisateur, la spécification du paramètre d’événement est facultative.</span><span class="sxs-lookup"><span data-stu-id="cada6-187">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="cada6-188">Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> quand aucune valeur n’est passée au rappel.</span><span class="sxs-lookup"><span data-stu-id="cada6-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="cada6-189">Empêcher les actions par défaut</span><span class="sxs-lookup"><span data-stu-id="cada6-189">Prevent default actions</span></span>

<span data-ttu-id="cada6-190">Utilisez l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attribut directive pour empêcher l’action par défaut d’un événement.</span><span class="sxs-lookup"><span data-stu-id="cada6-190">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="cada6-191">Quand une clé est sélectionnée sur un appareil d’entrée et que le focus de l’élément se trouve sur une zone de texte, un navigateur affiche normalement le caractère de la clé dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="cada6-191">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="cada6-192">Dans l’exemple suivant, le comportement par défaut est évité en spécifiant l' `@onkeypress:preventDefault` attribut directive.</span><span class="sxs-lookup"><span data-stu-id="cada6-192">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="cada6-193">Le compteur est incrémenté et la **+** clé n’est pas capturée dans la `<input>` valeur de l’élément :</span><span class="sxs-lookup"><span data-stu-id="cada6-193">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="cada6-194">La spécification de l' `@on{EVENT}:preventDefault` attribut sans valeur équivaut à `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="cada6-194">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="cada6-195">La valeur de l’attribut peut également être une expression.</span><span class="sxs-lookup"><span data-stu-id="cada6-195">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="cada6-196">Dans l’exemple suivant, `shouldPreventDefault` est un `bool` champ défini sur `true` ou `false` :</span><span class="sxs-lookup"><span data-stu-id="cada6-196">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="cada6-197">Arrêter la propagation des événements</span><span class="sxs-lookup"><span data-stu-id="cada6-197">Stop event propagation</span></span>

<span data-ttu-id="cada6-198">Utilisez l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attribut directive pour arrêter la propagation des événements.</span><span class="sxs-lookup"><span data-stu-id="cada6-198">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="cada6-199">Dans l’exemple suivant, la sélection de la case à cocher empêche les événements Click du deuxième enfant `<div>` de se propager vers le parent `<div>` :</span><span class="sxs-lookup"><span data-stu-id="cada6-199">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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