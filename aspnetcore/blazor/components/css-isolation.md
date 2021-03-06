---
title: ASP.NET Core l' Blazor isolation CSS
author: daveabrock
description: Découvrez comment l’isolation CSS vous permet d’étendre la CSS à vos composants, ce qui peut simplifier votre CSS et éviter les collisions avec d’autres composants ou bibliothèques.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529980"
---
# <a name="aspnet-core-blazor-css-isolation"></a>ASP.NET Core l' Blazor isolation CSS

Par [Dave Brock](https://twitter.com/daveabrock)

L’isolation CSS simplifie l’empreinte CSS d’une application en empêchant les dépendances sur les styles globaux et permet d’éviter les conflits de styles entre les composants et les bibliothèques.

## <a name="enable-css-isolation"></a>Activer l’isolation CSS 

Pour définir des styles spécifiques au composant, créez un `.razor.css` fichier correspondant au nom du `.razor` fichier du composant dans le même dossier. Le `.razor.css` fichier est un *fichier CSS étendu*. 

Pour un `Example` composant dans un `Example.razor` fichier, créez un fichier avec le composant nommé `Example.razor.css` . Le `Example.razor.css` fichier doit résider dans le même dossier que le `Example` composant ( `Example.razor` ). Le `Example` nom de base du fichier n’est **pas** sensible à la casse.

`Pages/Example.razor`:

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

`Pages/Example.razor.css`:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

**Les styles définis dans `Example.razor.css` sont appliqués uniquement à la sortie rendue du `Example` composant.** L’isolation CSS est appliquée aux éléments HTML dans le Razor fichier correspondant. Les `h1` déclarations CSS définies ailleurs dans l’application ne sont pas en conflit avec les `Example` styles du composant.

> [!NOTE]
> Pour garantir l’isolement du style lors du regroupement, l’importation de CSS dans des Razor blocs de code n’est pas prise en charge.

## <a name="css-isolation-bundling"></a>Regroupement d’isolation CSS

L’isolation CSS se produit au moment de la génération. Pendant ce processus, Blazor réécrit les sélecteurs CSS pour qu’ils correspondent au balisage rendu par le composant. Ces styles CSS réécrits sont regroupés et produits sous la forme d’une ressource statique à `{PROJECT NAME}.styles.css` , où l’espace réservé `{PROJECT NAME}` est le package ou le nom de produit référencé.

Ces fichiers statiques sont référencés à partir du chemin d’accès racine de l’application par défaut. Dans l’application, référencez le fichier groupé en inspectant la référence à l’intérieur `<head>` de la balise du code HTML généré :

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

Dans le fichier groupé, chaque composant est associé à un identificateur d’étendue. Pour chaque composant stylisé, un attribut HTML est ajouté avec le format `b-<10-character-string>` . L’identificateur est unique et différent pour chaque application. Dans le `Counter` composant rendu, Blazor ajoute un identificateur de portée à l' `h1` élément :

```html
<h1 b-3xxtam6d07>
```

Le `ProjectName.styles.css` fichier utilise l’identificateur de portée pour regrouper une déclaration de style avec son composant. L’exemple suivant fournit le style de l' `<h1>` élément précédent :

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

Au moment de la génération, un bundle de projet est créé avec la convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` , où l’espace réservé `{STATIC WEB ASSETS BASE PATH}` est le chemin de base des ressources Web statiques.

Si d’autres projets sont utilisés, tels que des packages NuGet ou des [ Razor bibliothèques de classes](xref:blazor/components/class-libraries), le fichier groupé :

* Fait référence aux styles à l’aide des importations CSS.
* N’est pas publié en tant que ressource Web statique de l’application qui consomme les styles.

## <a name="child-component-support"></a>Prise en charge des composants enfants

Par défaut, l’isolation CSS s’applique uniquement au composant que vous associez au format `{COMPONENT NAME}.razor.css` , où l’espace réservé `{COMPONENT NAME}` est généralement le nom du composant. Pour appliquer des modifications à un composant enfant, utilisez `::deep` combin pour tous les éléments descendants dans le fichier du composant parent `.razor.css` . Le `::deep` combinateur sélectionne les éléments qui sont des *descendants* de l’identificateur de portée généré d’un élément. 

L’exemple suivant montre un composant parent appelé `Parent` avec un composant enfant appelé `Child` .

`Pages/Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Shared/Child.razor`:

```razor
<h1>Child Component</h1>
```

Mettez à jour la `h1` déclaration dans `Parent.razor.css` avec le `::deep` combinateur pour signifier que la `h1` déclaration de style doit s’appliquer au composant parent et à ses enfants.

`Pages/Parent.razor.css`:

```css
::deep h1 { 
    color: red;
}
```

Le `h1` style s’applique désormais aux `Parent` `Child` composants et sans qu’il soit nécessaire de créer un fichier CSS délimité distinct pour le composant enfant.

`::deep`Combin ne fonctionne qu’avec les éléments descendants. La balise suivante applique les `h1` styles aux composants comme prévu. L’identificateur de portée du composant parent est appliqué à l' `div` élément, de sorte que le navigateur sait qu’il doit hériter des styles du composant parent.

`Pages/Parent.razor`:

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

Toutefois, l’exclusion de l' `div` élément supprime la relation descendante. Dans l’exemple suivant, le style n’est **pas** appliqué au composant enfant.

`Pages/Parent.razor`:

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a>Prise en charge du préprocesseur CSS

Les préprocesseurs CSS sont utiles pour améliorer le développement CSS en utilisant des fonctionnalités telles que les variables, l’imbrication, les modules, les Mixins et l’héritage. Bien que l’isolation CSS ne prenne pas en charge en mode natif les préprocesseurs CSS tels que Sass ou Less, l’intégration des préprocesseurs CSS est transparente tant que la compilation du préprocesseur se produit avant Blazor la réécriture des sélecteurs CSS pendant le processus de génération. À l’aide de Visual Studio, par exemple, configurez la compilation de préprocesseur existante en tant que tâche **avant génération** dans l’Explorateur de l’exécuteur de tâches Visual Studio.

De nombreux packages NuGet tiers, tels que [Delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), peuvent compiler des fichiers Sass/SCSS au début du processus de génération avant l’isolation CSS et aucune configuration supplémentaire n’est requise.

## <a name="css-isolation-configuration"></a>Configuration de l’isolation CSS

L’isolation CSS est conçue pour fonctionner de façon prête à l’emploi, mais elle fournit une configuration pour certains scénarios avancés, par exemple lorsqu’il existe des dépendances avec des outils ou des workflows existants.

### <a name="customize-scope-identifier-format"></a>Personnaliser le format de l’identificateur d’étendue

Par défaut, les identificateurs d’étendue utilisent le format `b-<10-character-string>` . Pour personnaliser le format de l’identificateur de l’étendue, mettez à jour le fichier projet selon un modèle de votre choix :

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Dans l’exemple précédent, le code CSS généré pour `Example.Razor.css` remplace son identificateur de portée `b-<10-character-string>` par `my-custom-scope-identifier` .

Utilisez des identificateurs d’étendue pour obtenir un héritage avec les fichiers CSS délimités. Dans l’exemple de fichier projet suivant, un `BaseComponent.razor.css` fichier contient des styles communs entre les composants. Un `DerivedComponent.razor.css` fichier hérite de ces styles.

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Utilisez l’opérateur générique ( `*` ) pour partager des identificateurs d’étendue sur plusieurs fichiers :

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a>Modifier le chemin de base des ressources Web statiques

Le `scoped.styles.css` fichier est généré à la racine de l’application. Dans le fichier projet, utilisez la `StaticWebAssetBasePath` propriété pour modifier le chemin d’accès par défaut. L’exemple suivant place le `scoped.styles.css` fichier, et le reste des ressources de l’application, au `_content` chemin d’accès suivant :

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>Désactiver le regroupement automatique

Pour désactiver la manière dont Blazor publie et charge les fichiers délimités au moment de l’exécution, utilisez la `DisableScopedCssBundling` propriété. Quand vous utilisez cette propriété, cela signifie que d’autres outils ou processus sont chargés de placer les fichiers CSS isolés à partir du `obj` répertoire et de les publier et de les charger au moment de l’exécution :

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a>Razor prise en charge de la bibliothèque de classes (RCL)

Quand une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) fournit des styles isolés, l' `<link>` attribut de la balise `href` pointe vers `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` , où les espaces réservés sont :

* `{STATIC WEB ASSET BASE PATH}`: Le chemin d’accès de base des ressources Web statiques.
* `{ASSEMBLY NAME}`: Le nom de l’assembly de la bibliothèque de classes.

Dans l’exemple suivant :

* Le chemin d’accès de base des ressources Web statiques est `_content/ClassLib` .
* Le nom de l’assembly de la bibliothèque de classes est `ClassLib` .

`wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages_Host.cshtml` (Blazor Server):

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

Pour plus d’informations sur les bibliothèques de composants et les RCLs, consultez :

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>.
