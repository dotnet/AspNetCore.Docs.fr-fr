---
title: Nouveautés de ASP.NET Core 3,1
author: rick-anderson
description: Découvrez les nouvelles fonctionnalités de ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: dd012a2104f574865ed577ab3c0e81dc9cc9596d
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431015"
---
# <a name="whats-new-in-aspnet-core-31"></a>Nouveautés de ASP.NET Core 3,1

Cet article met en évidence les modifications les plus importantes apportées à ASP.NET Core 3,1 avec des liens vers la documentation appropriée.

## <a name="partial-class-support-for-no-locrazor-components"></a>Prise en charge des classes partielles pour les Razor composants

Razor les composants sont maintenant générés en tant que classes partielles. Le code d’un Razor composant peut être écrit à l’aide d’un fichier code-behind défini en tant que classe partielle, plutôt que de définir tout le code du composant dans un fichier unique. Pour plus d’informations, consultez [prise en charge des classes partielles](xref:blazor/components/index#partial-class-support).

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor Tag Helper de composant et passer des paramètres à des composants de niveau supérieur

Dans Blazor avec ASP.NET Core 3,0, les composants étaient rendus dans des pages et des vues à l’aide d’un programme d’assistance HTML ( `Html.RenderComponentAsync` ). Dans ASP.NET Core 3,1, restituez un composant à partir d’une page ou d’une vue avec le nouveau tag Helper du composant :

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Le programme d’assistance HTML reste pris en charge dans ASP.NET Core 3,1, mais le tag Helper Component est recommandé.

Blazor Server les applications peuvent désormais passer des paramètres à des composants de niveau supérieur pendant le rendu initial. Auparavant, vous pouviez uniquement passer des paramètres à un composant de niveau supérieur avec [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). Avec cette version, [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) et [RenderMode. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) sont pris en charge. Toute valeur de paramètre spécifiée est sérialisée au format JSON et incluse dans la réponse initiale.

Par exemple, prérendez un `Counter` composant avec un volume d’incrément ( `IncrementAmount` ) :

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Pour plus d’informations, consultez [intégrer des composants dans Razor des pages et des applications MVC](xref:blazor/components/prerendering-and-integration).

## <a name="support-for-shared-queues-in-httpsys"></a>Prise en charge des files d’attente partagées dans HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) prend en charge la création de files d’attente de demandes anonymes. Dans ASP.NET Core 3,1, nous avons ajouté à la possibilité de créer ou d’attacher une file d’attente de demandes nommée HTTP.sys existante. La création ou l’attachement à une file d’attente de demandes nommée HTTP.sys existante permet de faire en sorte que le processus du contrôleur HTTP.sys qui possède la file d’attente soit indépendant du processus de l’écouteur. Cette indépendance permet de conserver les connexions existantes et les demandes mises en file d’attente entre les redémarrages du processus de l’écouteur :

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a>Modifications avec rupture pour SameSite cookie s

Le comportement de SameSite cookie s a changé pour refléter les modifications de navigateur à venir. Cela peut affecter les scénarios d’authentification tels que AzureAd, OpenIdConnect ou WsFederation. Pour plus d'informations, consultez <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a>Empêcher les actions par défaut des événements dans les Blazor applications

Utilisez l' `@on{EVENT}:preventDefault` attribut directive pour empêcher l’action par défaut d’un événement. Dans l’exemple suivant, l’action par défaut d’affichage du caractère de la clé dans la zone de texte est interdite :

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Pour plus d’informations, consultez [empêcher les actions par défaut](xref:blazor/components/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-no-locblazor-apps"></a>Arrêter la propagation des événements dans les Blazor applications

Utilisez l' `@on{EVENT}:stopPropagation` attribut directive pour arrêter la propagation des événements. Dans l’exemple suivant, la sélection de la case à cocher empêche les événements de clic de l’enfant `<div>` de se propager vers le parent `<div>` :

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Pour plus d’informations, consultez [arrêter la propagation des événements](xref:blazor/components/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-no-locblazor-app-development"></a>Erreurs détaillées lors du Blazor développement d’applications

Quand une Blazor application ne fonctionne pas correctement pendant le développement, la réception d’informations d’erreur détaillées de l’application vous aide à résoudre les problèmes et à résoudre le problème. Lorsqu’une erreur se produit, les Blazor applications affichent une barre dorée en bas de l’écran :

* Pendant le développement, la barre dorée vous dirige vers la console du navigateur, où vous pouvez voir l’exception.
* En production, la barre dorée avertit l’utilisateur qu’une erreur s’est produite et recommande l’actualisation du navigateur.

Pour plus d’informations, consultez [erreurs détaillées lors du développement](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).
