---
title: Dispositions de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment créer des composants de disposition réutilisables pour les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: 13b5c9db04cf23377aadc4e4f61f23855c2fd492
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430937"
---
# <a name="aspnet-core-no-locblazor-layouts"></a>Dispositions de ASP.NET Core Blazor

Par [Rainer Stropek](https://www.timecockpit.com) et [Luke Latham](https://github.com/guardrex)

Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la mise en page globale de l’application et sont utilisés par chaque composant de l’application. La copie du code de ces éléments dans tous les composants d’une application n’est pas une approche efficace. Chaque fois que l’un des éléments requiert une mise à jour, chaque composant doit être mis à jour. Une telle duplication est difficile à gérer et peut entraîner une incohérence du contenu au fil du temps. Les *dispositions* résolvent ce problème.

Techniquement, une disposition est simplement un autre composant. Une disposition est définie dans un Razor modèle ou dans du code C# et peut utiliser la [liaison de données](xref:blazor/components/data-binding), l' [injection de dépendances](xref:blazor/fundamentals/dependency-injection)et d’autres scénarios de composants.

Pour transformer un *composant* en une *disposition* , le composant :

* Hérite de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , qui définit une <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriété pour le contenu rendu à l’intérieur de la disposition.
* Utilise la Razor syntaxe `@Body` pour spécifier l’emplacement dans la balise de mise en page où le contenu est restitué.

L’exemple de code suivant montre le Razor modèle d’un composant de disposition, `MainLayout.razor` . La disposition hérite <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> de et définit la `@Body` entre la barre de navigation et le pied de page :

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="mainlayout-component"></a>Composant `MainLayout`

Dans une application basée sur l’un des Blazor modèles de projet, le `MainLayout` composant ( `MainLayout.razor` ) se trouve dans le dossier de l’application `Shared` :

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

## <a name="default-layout"></a>Disposition par défaut

Spécifiez la disposition de l’application par défaut dans le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant dans le fichier de l’application `App.razor` . Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant suivant, qui est fourni par les modèles par défaut Blazor , définit la disposition par défaut sur le `MainLayout` composant :

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Pour fournir une disposition par défaut pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, spécifiez un <xref:Microsoft.AspNetCore.Components.LayoutView> pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu :

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, consultez <xref:blazor/fundamentals/routing> .

La spécification de la disposition comme disposition par défaut dans le routeur est une pratique utile, car elle peut être remplacée par composant ou par dossier. Préférez utiliser le routeur pour définir la disposition par défaut de l’application, car il s’agit de la technique la plus générale.

## <a name="specify-a-layout-in-a-component"></a>Spécifier une disposition dans un composant

Utilisez la Razor directive `@layout` pour appliquer une disposition à un composant. Le compilateur convertit `@layout` en <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , qui est appliqué à la classe de composant.

Le contenu du composant suivant `MasterList` est inséré dans le `MasterLayout` à la position de `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

La spécification de la disposition directement dans un composant remplace un ensemble de *dispositions par défaut* dans le routeur ou une `@layout` directive importée à partir de `_Imports.razor` .

## <a name="centralized-layout-selection"></a>Sélection de la disposition centralisée

Chaque dossier d’une application peut éventuellement contenir un fichier de modèle nommé `_Imports.razor` . Le compilateur comprend les directives spécifiées dans le fichier Imports dans tous les Razor modèles du même dossier et de manière récursive dans tous ses sous-dossiers. Par conséquent, un `_Imports.razor` fichier contenant `@layout MyCoolLayout` s’assure que tous les composants d’un dossier utilisent `MyCoolLayout` . Il n’est pas nécessaire d’ajouter à plusieurs reprises `@layout MyCoolLayout` à tous les `.razor` fichiers dans le dossier et les sous-dossiers. `@using` les directives sont également appliquées aux composants de la même façon.

Les `_Imports.razor` importations de fichiers suivantes :

* `MyCoolLayout`.
* Tous les Razor composants du même dossier et de tous les sous-dossiers.
* Espace de noms `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Le `_Imports.razor` fichier est semblable au [fichier _ViewImports. cshtml pour les Razor affichages et les pages,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux Razor fichiers de composants.

La spécification d’une disposition dans `_Imports.razor` remplace une disposition spécifiée comme *disposition par défaut* du routeur.

> [!WARNING]
> N’ajoutez **pas** de Razor `@layout` directive au fichier racine `_Imports.razor` , ce qui entraîne une boucle infinie de dispositions dans l’application. Pour contrôler la disposition de l’application par défaut, spécifiez la disposition dans le `Router` composant. Pour plus d’informations, consultez la section [disposition par défaut](#default-layout) .

## <a name="nested-layouts"></a>Dispositions imbriquées

Les applications peuvent se composer de dispositions imbriquées. Un composant peut faire référence à une disposition qui, à son tour, fait référence à une autre disposition. Par exemple, les dispositions d’imbrication sont utilisées pour créer une structure de menus à plusieurs niveaux.

L’exemple suivant montre comment utiliser des dispositions imbriquées. Le `EpisodesComponent.razor` fichier est le composant à afficher. Le composant fait référence à `MasterListLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Le `MasterListLayout.razor` fichier fournit le `MasterListLayout` . La disposition fait référence à une autre disposition, `MasterLayout` , où elle est rendue. `EpisodesComponent` l’emplacement est affiché `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Enfin, `MasterLayout` dans `MasterLayout.razor` contient les éléments de disposition de niveau supérieur, tels que l’en-tête, le menu principal et le pied de page. `MasterListLayout` avec `EpisodesComponent` est rendu où `@Body` s’affiche :

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a>Partager une Razor disposition de pages avec des composants intégrés

Lorsque des composants routables sont intégrés à une Razor application pages, la disposition partagée de l’application peut être utilisée avec les composants. Pour plus d'informations, consultez <xref:blazor/components/prerendering-and-integration>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/layout>
