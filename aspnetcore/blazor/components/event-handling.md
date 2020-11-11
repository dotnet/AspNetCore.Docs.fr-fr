---
title: :::no-loc(Blazor):::Gestion des événements ASP.net Core
author: guardrex
description: 'Découvrez :::no-loc(Blazor)::: les fonctionnalités de gestion des événements de, notamment les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: 56306d305a61d549380e847ab2754fa41130e14e
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507809"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="c2f4b-103">:::no-loc(Blazor):::Gestion des événements ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="c2f4b-103">ASP.NET Core :::no-loc(Blazor)::: event handling</span></span>

<span data-ttu-id="c2f4b-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c2f4b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c2f4b-105">:::no-loc(Razor)::: les composants fournissent des fonctionnalités de gestion des événements.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-105">:::no-loc(Razor)::: components provide event handling features.</span></span> <span data-ttu-id="c2f4b-106">Pour un attribut d’élément HTML nommé [`@on{EVENT}`](xref:mvc/views/razor#onevent) (par exemple, `@onclick` ) avec une valeur de type délégué, un :::no-loc(Razor)::: composant traite la valeur de l’attribut en tant que gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a :::no-loc(Razor)::: component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="c2f4b-107">Le code suivant appelle la `UpdateHeading` méthode lorsque le bouton est sélectionné dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="c2f4b-108">Le code suivant appelle la `CheckChanged` méthode lorsque la case à cocher est modifiée dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c2f4b-109">Les gestionnaires d’événements peuvent également être asynchrones et retourner un <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c2f4b-110">Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="c2f4b-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="c2f4b-111">Les exceptions sont journalisées lorsqu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c2f4b-112">Dans l’exemple suivant, `UpdateHeading` est appelé de façon asynchrone quand le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="c2f4b-113">Types d’arguments d’événement</span><span class="sxs-lookup"><span data-stu-id="c2f4b-113">Event argument types</span></span>

<span data-ttu-id="c2f4b-114">Pour certains événements, les types d’arguments d’événement sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c2f4b-115">La spécification d’un paramètre d’événement dans une définition de méthode d’événement est facultative et n’est nécessaire que si le type d’événement est utilisé dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="c2f4b-116">Dans l’exemple suivant, l' `MouseEventArgs` argument d’événement est utilisé dans la `ShowMessage` méthode pour définir le texte du message :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="c2f4b-117">Les informations prises en charge <xref:System.EventArgs> sont indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="c2f4b-118">Événement</span><span class="sxs-lookup"><span data-stu-id="c2f4b-118">Event</span></span>            | <span data-ttu-id="c2f4b-119">Classe</span><span class="sxs-lookup"><span data-stu-id="c2f4b-119">Class</span></span>  | <span data-ttu-id="c2f4b-120">Remarques et événements DOM</span><span class="sxs-lookup"><span data-stu-id="c2f4b-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="c2f4b-121">Presse-papiers</span><span class="sxs-lookup"><span data-stu-id="c2f4b-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="c2f4b-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="c2f4b-123">Glissement</span><span class="sxs-lookup"><span data-stu-id="c2f4b-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="c2f4b-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="c2f4b-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> et <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contiennent des données d’élément glissées.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="c2f4b-126">Implémentez le glisser-déplacer dans les :::no-loc(Blazor)::: applications à l’aide de l' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) avec l' [API html glisser-déplacer](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="c2f4b-126">Implement drag and drop in :::no-loc(Blazor)::: apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="c2f4b-127">Error</span><span class="sxs-lookup"><span data-stu-id="c2f4b-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="c2f4b-128">Événement</span><span class="sxs-lookup"><span data-stu-id="c2f4b-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="c2f4b-129">*Général*</span><span class="sxs-lookup"><span data-stu-id="c2f4b-129">*General*</span></span><br><span data-ttu-id="c2f4b-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="c2f4b-131">*Presse-papiers*</span><span class="sxs-lookup"><span data-stu-id="c2f4b-131">*Clipboard*</span></span><br><span data-ttu-id="c2f4b-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="c2f4b-133">*Entrée*</span><span class="sxs-lookup"><span data-stu-id="c2f4b-133">*Input*</span></span><br><span data-ttu-id="c2f4b-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="c2f4b-135">*Média*</span><span class="sxs-lookup"><span data-stu-id="c2f4b-135">*Media*</span></span><br><span data-ttu-id="c2f4b-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="c2f4b-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contient des attributs permettant de configurer les mappages entre les noms d’événements et les types d’arguments d’événement.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="c2f4b-138">Priorité</span><span class="sxs-lookup"><span data-stu-id="c2f4b-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="c2f4b-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="c2f4b-140">N’inclut pas la prise en charge de `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c2f4b-141">Entrée</span><span class="sxs-lookup"><span data-stu-id="c2f4b-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="c2f4b-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="c2f4b-143">Clavier</span><span class="sxs-lookup"><span data-stu-id="c2f4b-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="c2f4b-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="c2f4b-145">Souris</span><span class="sxs-lookup"><span data-stu-id="c2f4b-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="c2f4b-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="c2f4b-147">Pointeur de souris</span><span class="sxs-lookup"><span data-stu-id="c2f4b-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="c2f4b-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="c2f4b-149">Roulette de la souris</span><span class="sxs-lookup"><span data-stu-id="c2f4b-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="c2f4b-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="c2f4b-151">Progress</span><span class="sxs-lookup"><span data-stu-id="c2f4b-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="c2f4b-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="c2f4b-153">Toucher</span><span class="sxs-lookup"><span data-stu-id="c2f4b-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="c2f4b-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="c2f4b-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> représente un point de contact unique sur un appareil tactile.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="c2f4b-156">Événement</span><span class="sxs-lookup"><span data-stu-id="c2f4b-156">Event</span></span>            | <span data-ttu-id="c2f4b-157">Classe</span><span class="sxs-lookup"><span data-stu-id="c2f4b-157">Class</span></span> | <span data-ttu-id="c2f4b-158">Remarques et événements DOM</span><span class="sxs-lookup"><span data-stu-id="c2f4b-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="c2f4b-159">Presse-papiers</span><span class="sxs-lookup"><span data-stu-id="c2f4b-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="c2f4b-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="c2f4b-161">Glissement</span><span class="sxs-lookup"><span data-stu-id="c2f4b-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="c2f4b-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="c2f4b-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> et <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contiennent des données d’élément glissées.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="c2f4b-164">Implémentez le glisser-déplacer dans les :::no-loc(Blazor)::: applications à l’aide de l' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) avec l' [API html glisser-déplacer](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="c2f4b-164">Implement drag and drop in :::no-loc(Blazor)::: apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="c2f4b-165">Error</span><span class="sxs-lookup"><span data-stu-id="c2f4b-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="c2f4b-166">Événement</span><span class="sxs-lookup"><span data-stu-id="c2f4b-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="c2f4b-167">*Général*</span><span class="sxs-lookup"><span data-stu-id="c2f4b-167">*General*</span></span><br><span data-ttu-id="c2f4b-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="c2f4b-169">*Presse-papiers*</span><span class="sxs-lookup"><span data-stu-id="c2f4b-169">*Clipboard*</span></span><br><span data-ttu-id="c2f4b-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="c2f4b-171">*Entrée*</span><span class="sxs-lookup"><span data-stu-id="c2f4b-171">*Input*</span></span><br><span data-ttu-id="c2f4b-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="c2f4b-173">*Média*</span><span class="sxs-lookup"><span data-stu-id="c2f4b-173">*Media*</span></span><br><span data-ttu-id="c2f4b-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="c2f4b-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contient des attributs permettant de configurer les mappages entre les noms d’événements et les types d’arguments d’événement.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="c2f4b-176">Priorité</span><span class="sxs-lookup"><span data-stu-id="c2f4b-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="c2f4b-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="c2f4b-178">N’inclut pas la prise en charge de `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c2f4b-179">Entrée</span><span class="sxs-lookup"><span data-stu-id="c2f4b-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="c2f4b-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="c2f4b-181">Clavier</span><span class="sxs-lookup"><span data-stu-id="c2f4b-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="c2f4b-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="c2f4b-183">Souris</span><span class="sxs-lookup"><span data-stu-id="c2f4b-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="c2f4b-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="c2f4b-185">Pointeur de souris</span><span class="sxs-lookup"><span data-stu-id="c2f4b-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="c2f4b-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="c2f4b-187">Roulette de la souris</span><span class="sxs-lookup"><span data-stu-id="c2f4b-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="c2f4b-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="c2f4b-189">Progress</span><span class="sxs-lookup"><span data-stu-id="c2f4b-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="c2f4b-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="c2f4b-191">Toucher</span><span class="sxs-lookup"><span data-stu-id="c2f4b-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="c2f4b-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="c2f4b-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="c2f4b-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> représente un point de contact unique sur un appareil tactile.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="c2f4b-194">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="c2f4b-195">[ `EventArgs` classes de la source de référence ASP.net Core (branche dotnet/aspnetcore `master` )](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="c2f4b-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="c2f4b-196">La `master` branche représente l’API en cours de développement pour la *prochaine* version de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="c2f4b-197">Pour la version actuelle, sélectionnez la branche de dépôt GitHub appropriée (par exemple, `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="c2f4b-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="c2f4b-198">[MDN Web docs : GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): contient des informations sur les éléments HTML qui prennent en charge chaque événement DOM.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="c2f4b-199">Expressions lambda</span><span class="sxs-lookup"><span data-stu-id="c2f4b-199">Lambda expressions</span></span>

<span data-ttu-id="c2f4b-200">Les [expressions lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c2f4b-201">Il est souvent pratique de se rapprocher de valeurs supplémentaires, par exemple lors de l’itération sur un ensemble d’éléments.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c2f4b-202">L’exemple suivant crée trois boutons, chacun d’entre eux qui `UpdateHeading` passe un argument d’événement ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) et son numéro de bouton ( `buttonNumber` ) lorsqu’ils sont sélectionnés dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="c2f4b-203">N’utilisez **pas** directement une variable de boucle dans une expression lambda, comme `i` dans l' `for` exemple de boucle précédent.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="c2f4b-204">Dans le cas contraire, la même variable est utilisée par toutes les expressions lambda, ce qui entraîne l’utilisation de la même valeur dans toutes les expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="c2f4b-205">Capturez toujours la valeur de la variable dans une variable locale, puis utilisez-la.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="c2f4b-206">Dans l’exemple précédent, la variable `i` de boucle est assignée à `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="c2f4b-207">EventCallback suivante</span><span class="sxs-lookup"><span data-stu-id="c2f4b-207">EventCallback</span></span>

<span data-ttu-id="c2f4b-208">Un scénario courant avec des composants imbriqués est le désir d’exécuter la méthode d’un composant parent lorsqu’un événement de composant enfant se produit.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="c2f4b-209">Un `onclick` événement qui se produit dans le composant enfant est un cas d’usage courant.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="c2f4b-210">Pour exposer des événements entre les composants, utilisez un <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="c2f4b-211">Un composant parent peut affecter une méthode de rappel à un composant enfant <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="c2f4b-212">`ChildComponent`Dans l’exemple d’application ( `Components/ChildComponent.razor` ), montre comment un gestionnaire de bouton `onclick` est configuré pour recevoir un <xref:Microsoft.AspNetCore.Components.EventCallback> délégué à partir de l’exemple de `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c2f4b-213">Le <xref:Microsoft.AspNetCore.Components.EventCallback> est typé avec `MouseEventArgs` , ce qui est approprié pour un `onclick` événement à partir d’un périphérique :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c2f4b-214">`ParentComponent`Définit le () de l’enfant <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` sur sa `ShowMessage` méthode.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="c2f4b-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="c2f4b-215">`Pages/ParentComponent.razor`:</span></span>

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
        messageText = $"Blaze a new trail with :::no-loc(Blazor):::! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="c2f4b-216">Lorsque le bouton est sélectionné dans le `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c2f4b-217">La `ParentComponent` `ShowMessage` méthode de est appelée.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c2f4b-218">`messageText` est mis à jour et affiché dans le `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c2f4b-219">Un appel à [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) n’est pas requis dans la méthode du rappel ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="c2f4b-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c2f4b-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est appelé automatiquement pour rerestituer le `ParentComponent` , tout comme les événements enfants déclenchent le rerendu des composants dans les gestionnaires d’événements qui s’exécutent dans l’enfant.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c2f4b-221"><xref:Microsoft.AspNetCore.Components.EventCallback> et <xref:Microsoft.AspNetCore.Components.EventCallback%601> autorisent les délégués asynchrones.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="c2f4b-222"><xref:Microsoft.AspNetCore.Components.EventCallback> est faiblement typé et permet de passer n’importe quel argument de type dans `InvokeAsync(Object)` .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="c2f4b-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> est fortement typé et requiert le passage d’un `T` argument dans `InvokeAsync(T)` qui peut être assigné à `TValue` .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="c2f4b-224">Appelez <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> avec <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> et en attente de <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="c2f4b-225">Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> et <xref:Microsoft.AspNetCore.Components.EventCallback%601> pour la gestion des événements et les paramètres de composant de liaison.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="c2f4b-226">Préférez le fortement typé <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="c2f4b-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> fournit un meilleur retour d’erreur aux utilisateurs du composant.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="c2f4b-228">Comme pour d’autres gestionnaires d’événements d’interface utilisateur, la spécification du paramètre d’événement est facultative.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c2f4b-229">Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> quand aucune valeur n’est passée au rappel.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="c2f4b-230">Empêcher les actions par défaut</span><span class="sxs-lookup"><span data-stu-id="c2f4b-230">Prevent default actions</span></span>

<span data-ttu-id="c2f4b-231">Utilisez l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attribut directive pour empêcher l’action par défaut d’un événement.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="c2f4b-232">Quand une clé est sélectionnée sur un appareil d’entrée et que le focus de l’élément se trouve sur une zone de texte, un navigateur affiche normalement le caractère de la clé dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="c2f4b-233">Dans l’exemple suivant, le comportement par défaut est évité en spécifiant l' `@onkeypress:preventDefault` attribut directive.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="c2f4b-234">Le compteur est incrémenté et la **+** clé n’est pas capturée dans la `<input>` valeur de l’élément :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="c2f4b-235">La spécification de l' `@on{EVENT}:preventDefault` attribut sans valeur équivaut à `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="c2f4b-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="c2f4b-236">La valeur de l’attribut peut également être une expression.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="c2f4b-237">Dans l’exemple suivant, `shouldPreventDefault` est un `bool` champ défini sur `true` ou `false` :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="c2f4b-238">Arrêter la propagation des événements</span><span class="sxs-lookup"><span data-stu-id="c2f4b-238">Stop event propagation</span></span>

<span data-ttu-id="c2f4b-239">Utilisez l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attribut directive pour arrêter la propagation des événements.</span><span class="sxs-lookup"><span data-stu-id="c2f4b-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="c2f4b-240">Dans l’exemple suivant, la sélection de la case à cocher empêche les événements Click du deuxième enfant `<div>` de se propager vers le parent `<div>` :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

## <a name="focus-an-element"></a><span data-ttu-id="c2f4b-241">Focus sur un élément</span><span class="sxs-lookup"><span data-stu-id="c2f4b-241">Focus an element</span></span>

<span data-ttu-id="c2f4b-242">Appelez `FocusAsync` sur une [référence d’élément](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) pour concentrer un élément dans le code :</span><span class="sxs-lookup"><span data-stu-id="c2f4b-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

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
