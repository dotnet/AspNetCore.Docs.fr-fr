---
title: Dispositions de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment créer des composants de disposition réutilisables pour les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2021
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
ms.openlocfilehash: 9f3400b766a043fdf3872838bffe392eddba4049
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102394943"
---
# <a name="aspnet-core-blazor-layouts"></a>Dispositions de ASP.NET Core Blazor

Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la présentation générale de l’application. Le fait de placer une copie du balisage pour ces éléments dans tous les composants d’une application n’est pas efficace. Chaque fois que l’un de ces éléments est mis à jour, chaque composant qui utilise l’élément doit être mis à jour. Cette approche est coûteuse à gérer et peut entraîner un contenu incohérent si une mise à jour est manquée. Les *dispositions* résolvent ces problèmes.

Une Blazor disposition est un Razor composant qui partage le balisage avec des composants qui le référencent. Les dispositions peuvent utiliser la [liaison de données](xref:blazor/components/data-binding), l’injection de [dépendances](xref:blazor/fundamentals/dependency-injection)et d’autres fonctionnalités de composants.

## <a name="layout-components"></a>Composants de la disposition

### <a name="create-a-layout-component"></a>Créer un composant de disposition

Pour créer un composant de disposition :

* Créez un Razor composant défini par un Razor modèle ou du code C#. Les composants de disposition basés sur un Razor modèle utilisent l' `.razor` extension de fichier comme les Razor composants ordinaires. Étant donné que les composants de disposition sont partagés entre les composants d’une application, ils sont généralement placés dans le dossier de l’application `Shared` . Toutefois, les dispositions peuvent être placées à n’importe quel emplacement accessible aux composants qui l’utilisent. Par exemple, une disposition peut être placée dans le même dossier que les composants qui l’utilisent.
* Hérite du composant de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> . <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>Définit une <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriété ( <xref:Microsoft.AspNetCore.Components.RenderFragment> type) pour le contenu rendu à l’intérieur de la disposition.
* Utilisez la Razor syntaxe `@Body` pour spécifier l’emplacement dans la balise de mise en page où le contenu est affiché.

Le `DoctorWhoLayout` composant suivant montre le Razor modèle d’un composant de disposition. La disposition hérite <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> de et définit la `@Body` entre la barre de navigation ( `<nav>...</nav>` ) et le pied de page ( `<footer>...</footer>` ).

`Shared/DoctorWhoLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a>Composant `MainLayout`

Dans une application créée à partir d’un [ Blazor modèle de projet](xref:blazor/project-structure), le `MainLayout` composant est la [disposition par défaut](#apply-a-default-layout-to-an-app)de l’application.

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

la [ Blazor fonctionnalité d’isolation CSS de](xref:blazor/components/css-isolation) applique les styles CSS isolés au `MainLayout` composant. Par Convention, les styles sont fournis par la feuille de style qui l’accompagne du même nom, `Shared/MainLayout.razor.css` . L’implémentation ASP.NET Core Framework de la feuille de style est disponible pour l’inspection dans la [source de référence ASP.net Core (référentiel GitHub dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a>Appliquer une disposition

### <a name="apply-a-layout-to-a-component"></a>Appliquer une disposition à un composant

Utilisez la [`@layout`](xref:mvc/views/razor#layout) Razor directive pour appliquer une disposition à un composant routable Razor qui a une [`@page`](xref:mvc/views/razor#page) directive. Le compilateur convertit `@layout` en un <xref:Microsoft.AspNetCore.Components.LayoutAttribute> et applique l’attribut à la classe de composant.

Le contenu du composant suivant `Episodes` est inséré dans le `DoctorWhoLayout` à la position de `@Body` .

`Pages/Episodes.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

Le balisage HTML rendu suivant est produit par le `DoctorWhoLayout` composant et le précédent `Episodes` . Le balisage superflu n’apparaît pas afin de se concentrer sur le contenu fourni par les deux composants impliqués :

* L’en-tête **&trade; de la base de données Doctor** ( `<h1>...</h1>` ) de l’en-tête (), de la `<header>...</header>` barre de navigation ( `<nav>...</nav>` ) et de l’élément d’information sur la marque () du `<div>...</div>` pied de page () provient du `<footer>...</footer>` `DoctorWhoLayout` composant.
* L’en-tête **épisodes** ( `<h2>...</h2>` ) et la liste épisode ( `<ul>...</ul>` ) proviennent du `Episodes` composant.

```html
<body>
    <div id="app">
        <header>
            <h1>Doctor Who&trade; Episode Database</h1>
        </header>

        <nav>
            <a href="masterlist">Master Episode List</a>
            <a href="search">Search</a>
            <a href="new">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <footer>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </footer>
    </div>
</body>
```

La spécification de la disposition directement dans un composant remplace une *disposition par défaut*:

* Défini par une `@layout` directive importée à partir d’un `_Imports` composant ( `_Imports.razor` ), comme décrit dans la section suivante [appliquer une disposition à un dossier de composants](#apply-a-layout-to-a-folder-of-components) .
* Définissez comme disposition par défaut de l’application, comme décrit dans la section [appliquer une disposition par défaut à une application](#apply-a-default-layout-to-an-app) plus loin dans cet article.

### <a name="apply-a-layout-to-a-folder-of-components"></a>Appliquer une disposition à un dossier de composants

Chaque dossier d’une application peut éventuellement contenir un fichier de modèle nommé `_Imports.razor` . Le compilateur comprend les directives spécifiées dans le fichier Imports dans tous les Razor modèles du même dossier et de manière récursive dans tous ses sous-dossiers. Par conséquent, un `_Imports.razor` fichier contenant `@layout DoctorWhoLayout` s’assure que tous les composants d’un dossier utilisent le `DoctorWhoLayout` composant. Il n’est pas nécessaire d’ajouter à plusieurs reprises `@layout DoctorWhoLayout` à tous les Razor composants ( `.razor` ) dans le dossier et les sous-dossiers.

`_Imports.razor`:

```razor
@layout DoctorWhoLayout
...
```

Le `_Imports.razor` fichier est semblable au [fichier _ViewImports. cshtml pour les Razor affichages et les pages,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux Razor fichiers de composants.

La spécification d’une disposition dans `_Imports.razor` remplace une disposition spécifiée comme [disposition d’application par défaut](#apply-a-default-layout-to-an-app)du routeur, qui est décrite dans la section suivante.

> [!WARNING]
> N’ajoutez **pas** de Razor `@layout` directive au fichier racine `_Imports.razor` , ce qui se traduit par une boucle infinie de dispositions. Pour contrôler la disposition de l’application par défaut, spécifiez la disposition dans le `Router` composant. Pour plus d’informations, consultez la section [appliquer une disposition par défaut à une application](#apply-a-default-layout-to-an-app) .

> [!NOTE]
> La [`@layout`](xref:mvc/views/razor#layout) Razor directive applique uniquement une disposition aux composants routables Razor avec une [`@page`](xref:mvc/views/razor#page) directive.

### <a name="apply-a-default-layout-to-an-app"></a>Appliquer une disposition par défaut à une application

Spécifiez la disposition de l’application par défaut dans le dans le `App` composant du composant <xref:Microsoft.AspNetCore.Components.Routing.Router> . L’exemple suivant à partir d’une application basée sur un [ Blazor modèle de projet](xref:blazor/project-structure) définit la disposition par défaut sur le `MainLayout` composant.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, consultez <xref:blazor/fundamentals/routing> .

La spécification de la disposition comme disposition par défaut dans le `Router` composant est une pratique utile, car vous pouvez remplacer la disposition par composant ou par dossier, comme décrit dans les sections précédentes de cet article. Nous vous recommandons d’utiliser le `Router` composant pour définir la disposition par défaut de l’application, car il s’agit de l’approche la plus générale et la plus souple pour l’utilisation des dispositions.

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a>Appliquer une disposition à du contenu arbitraire ( `LayoutView` composant)

Pour définir une disposition pour Razor le contenu de modèle arbitraire, spécifiez la disposition avec un <xref:Microsoft.AspNetCore.Components.LayoutView> composant. Vous pouvez utiliser <xref:Microsoft.AspNetCore.Components.LayoutView> dans n’importe quel Razor composant. L’exemple suivant définit un composant de disposition nommé `ErrorLayout` pour le `MainLayout` modèle du composant <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> ( `<NotFound>...</NotFound>` ).

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a>Dispositions imbriquées

Un composant peut faire référence à une disposition qui, à son tour, fait référence à une autre disposition. Par exemple, les dispositions imbriquées sont utilisées pour créer des structures de menus à plusieurs niveaux.

L’exemple suivant montre comment utiliser des dispositions imbriquées. Le `Episodes` composant affiché dans la section [appliquer une disposition à un composant](#apply-a-layout-to-a-component) est le composant à afficher. Le composant fait référence au `DoctorWhoLayout` composant.

Le `DoctorWhoLayout` composant suivant est une version modifiée de l’exemple indiqué plus haut dans cet article. Les éléments d’en-tête et de pied de page sont supprimés et la disposition référence une autre disposition, `ProductionsLayout` . Le `Episodes` composant est rendu où `@Body` apparaît dans le `DoctorWhoLayout` .

`Shared/DoctorWhoLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

Le `ProductionsLayout` composant contient les éléments de disposition de niveau supérieur, où les éléments d’en-tête ( `<header>...</header>` ) et de pied de page ( `<footer>...</footer>` ) résident maintenant. Le `DoctorWhoLayout` avec le `Episodes` composant est rendu où `@Body` apparaît.

`Shared/ProductionsLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

Le balisage HTML rendu suivant est produit par la disposition imbriquée précédente. Le balisage superflu n’apparaît pas afin de se concentrer sur le contenu imbriqué fourni par les trois composants impliqués :

* L’en-tête ( `<header>...</header>` ), la barre de navigation de production ( `<nav>...</nav>` ) et les éléments de pied de page ( `<footer>...</footer>` ) et leur contenu proviennent du `ProductionsLayout` composant.
* Le titre de l’en-tête **&trade; de la base de données du docteur** (), de la barre de `<h1>...</h1>` navigation épisode ( `<nav>...</nav>` ) et de l’élément des informations sur les marques provient du `<div>...</div>` `DoctorWhoLayout` composant.
* L’en-tête **épisodes** ( `<h2>...</h2>` ) et la liste épisode ( `<ul>...</ul>` ) proviennent du `Episodes` composant.

```html
<body>
    <div id="app">
        <header>
            <h1>Productions</h1>
        </header>

        <nav>
            <a href="master-production-list">Master Production List</a>
            <a href="production-search">Search</a>
            <a href="new-production">Add Production</a>
        </nav>

        <h1>Doctor Who&trade; Episode Database</h1>

        <nav>
            <a href="episode-masterlist">Master Episode List</a>
            <a href="episode-search">Search</a>
            <a href="new-episode">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <div>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </div>

        <footer>
            Footer of Productions Layout
        </footer>
    </div>
</body>
```

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Partager une Razor disposition de pages avec des composants intégrés

Lorsque des composants routables sont intégrés à une Razor application pages, la disposition partagée de l’application peut être utilisée avec les composants. Pour plus d’informations, consultez <xref:blazor/components/prerendering-and-integration>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/layout>
