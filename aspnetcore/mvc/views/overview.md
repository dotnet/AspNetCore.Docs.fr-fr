---
title: Vues dans ASP.NET Core MVC
author: ardalis
description: Découvrez comment les vues gèrent la présentation des données et les interactions utilisateur dans les applications ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/views/overview
ms.openlocfilehash: 373b17377740441d3859e3b7d942017a22bc7a68
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060623"
---
# <a name="views-in-aspnet-core-mvc"></a>Vues dans ASP.NET Core MVC

Par [Steve Smith](https://ardalis.com/)

Ce document explique l’utilisation des vues dans les applications ASP.NET Core MVC. Pour plus d’informations sur les Razor pages, consultez [Présentation des Razor pages](xref:razor-pages/index).

Selon le schéma MVC (Modèle-Vue-Contrôleur, la *vue* gère la présentation des données et les interactions utilisateur dans l’application. Une vue est un modèle HTML avec [ Razor balisage](xref:mvc/views/razor)incorporé. Razor le balisage est du code qui interagit avec le balisage HTML pour générer une page Web envoyée au client.

Dans ASP.NET Core MVC, les vues sont des fichiers *. cshtml* qui utilisent le [langage de programmation C#](/dotnet/csharp/) dans le Razor balisage. En règle générale, les fichiers de vue sont regroupés dans des dossiers nommés correspondant aux différents [contrôleurs](xref:mvc/controllers/actions) de l’application. Ces dossiers sont eux-mêmes regroupés sous le dossier *Vues* situé à la racine de l’application :

![Dossier Vues développé dans l’Explorateur de solutions de Visual Studio, avec le dossier Accueil ouvert contenant les fichiers About.cshtml, Contact.cshtml et Index.cshtml](overview/_static/views_solution_explorer.png)

Le contrôleur *Home* est représenté par un dossier *Accueil* dans le dossier *Vues* . Le dossier *Accueil* contient les vues associées aux pages d’accueil web *About* , *Contact* et *Index* . Quand un utilisateur demande l’une de ces trois pages web, les actions dans le contrôleur *Home* déterminent laquelle des trois vues est utilisée pour générer une page web et la retourner à l’utilisateur.

Utilisez des [dispositions](xref:mvc/views/layout) pour rendre les sections de page web homogènes et réduire la répétition de code. Les dispositions contiennent souvent l’en-tête, des éléments de menu et de navigation, ainsi que le pied de page. L’en-tête et le pied de page renferment généralement le code de balisage réutilisable pour divers éléments de métadonnées, et les liens vers des ressources de script et de style. Les dispositions vous permettent de limiter l’usage de ce balisage réutilisable dans vos vues.

Les [vues partielles](xref:mvc/views/partial) réduisent la répétition de code grâce à la gestion des parties réutilisables dans les vues. Par exemple, une vue partielle est utile dans le cas d’une biographie d’auteur publiée sur un site web de blog qui doit s’afficher dans plusieurs vues. Une biographie d’auteur présente un contenu de vue standard et ne nécessite pas d’exécution de code particulier pour générer le contenu à afficher sur la page web. Le contenu de la biographie d’auteur est fourni à la vue uniquement par la liaison de données. C’est pourquoi l’utilisation d’une vue partielle pour ce type de contenu est la solution la plus appropriée.

Les [composants de vue](xref:mvc/views/view-components) sont similaires aux vues partielles dans le sens où ils vous permettent aussi de réduire la répétition de code. La différence est qu’ils sont plutôt conçus pour du contenu de vue qui nécessite l’exécution de code sur le serveur pour afficher la page web. Les composants de vue sont utiles quand le contenu affiché doit interagir avec une base de données, comme c’est le cas pour un panier d’achat sur un site web. Les composants de vue ne dépendent pas d’une liaison de données pour pouvoir générer la sortie d’une page web.

## <a name="benefits-of-using-views"></a>Avantages de l’utilisation des vues

Les vues facilitent l’implémentation du principe de conception basé sur la séparation des préoccupations ([separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)) au sein d’une application MVC. En effet, elles permettent de séparer le balisage de l’interface utilisateur des autres parties de l’application. Une application conçue selon ce principe présente une plus grande modularité, ce qui offre plusieurs avantages :

* L’application est plus facile à gérer, car elle est mieux organisée. Les vues sont généralement regroupées par fonctionnalité dans l’application. Vous pouvez ainsi trouver plus rapidement les vues associées quand vous utilisez une fonctionnalité.
* Les différentes parties de l’application sont faiblement couplées. Vous pouvez générer et mettre à jour les vues de l’application séparément de la logique métier et des composants d’accès aux données. Vous pouvez modifier les vues de l’application sans avoir nécessairement besoin de mettre à jour d’autres parties de l’application.
* Il est plus facile de tester les parties de l’interface utilisateur de l’application, car les vues constituent des unités individuelles séparées.
* Du fait d’une meilleure organisation, vous risquez moins de répéter par inadvertance certaines sections de l’interface utilisateur.

## <a name="creating-a-view"></a>Création d’une vue

Les vues associées à un contrôleur spécifique sont créées dans le dossier *Vues/[nom_contrôleur]* . Les vues communes à plusieurs contrôleurs sont créées dans le dossier *Vues/Partagé* . Pour créer une vue, ajoutez un nouveau fichier et attribuez-lui le même nom que son action de contrôleur associée, avec l’extension de fichier *.cshtml* . Pour créer une vue correspondant à l’action *About* dans le contrôleur *Home* , créez un fichier *About.cshtml* dans le dossier *Vues/Accueil* :

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* le balisage commence par le `@` symbole. Exécutez des instructions C# en plaçant du code C# dans des [ Razor blocs de code](xref:mvc/views/razor#razor-code-blocks) définis par des accolades ( `{ ... }` ). L’exemple ci-dessus montre comment attribuer la valeur « About » à `ViewData["Title"]`. Vous pouvez afficher des valeurs dans le code HTML simplement en référençant chaque valeur avec le symbole `@`. Le code ci-dessus donne un exemple de contenu des éléments `<h2>` et `<h3>`.

Le contenu de la vue illustré ci-dessus est uniquement une partie de la page web entière qui est affichée à l’utilisateur. Le reste de la disposition de la page et les autres aspects communs de la vue sont spécifiés dans d’autres fichiers de vue. Pour en savoir plus, consultez l’article [Disposition](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Comment les contrôleurs spécifient-ils les vues ?

Les vues sont généralement retournées par des actions sous forme d’objet [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), qui est un type d’objet [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). Votre méthode d’action peut créer et retourner un `ViewResult` directement, mais cela n’est pas le plus fréquent. Étant donné que la plupart des contrôleurs héritent de [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), utilisez simplement la méthode d’assistance `View` pour retourner le `ViewResult` :

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Quand cette action est retournée, la vue *About.cshtml* figurant dans la dernière section s’affiche dans la page web comme ceci :

![Page About affichée dans le navigateur Edge](overview/_static/about-page.png)

La méthode d’assistance `View` a plusieurs surcharges. Vous pouvez éventuellement spécifier :

* Une vue explicite à retourner :

  ```csharp
  return View("Orders");
  ```

* Un [modèle](xref:mvc/models/model-binding) à passer à la vue :

  ```csharp
  return View(Orders);
  ```

* Une vue et un modèle :

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Détection de la vue

Quand une action doit retourner une vue, un processus appelé *détection de la vue* s’enclenche. Ce processus détermine quel fichier de vue est utilisé en fonction du nom de la vue. 

Le comportement par défaut de la méthode `View` (`return View();`) est de retourner une vue du même nom que la méthode d’action à partir de laquelle elle est appelée. Par exemple, le *About* `ActionResult` nom de la méthode about du contrôleur est utilisé pour rechercher un fichier de vue nommé *about. cshtml* . Le Runtime commence par rechercher la vue dans le dossier *Vues/[nom_contrôleur]* . S’il ne trouve pas de vue correspondante, il recherche ensuite la vue dans le dossier *Partagé* .

Peu importe si vous retournez implicitement `ViewResult` avec `return View();` ou si vous passez explicitement le nom de la vue à la méthode `View` avec `return View("<ViewName>");`. Dans les deux cas, la détection de la vue recherche un fichier de vue correspondant dans cet ordre :

   1. *Views/\[NomContrôleur]/\[NomVue].cshtml*
   1. *Vues/Partagé/\[nom_vue].cshtml*

Vous pouvez spécifier le chemin du fichier de vue au lieu du nom de la vue. Si vous utilisez un chemin absolu à partir de la racine de l’application (commençant éventuellement par « / » ou « ~/ »), vous devez indiquer l’extension *.cshtml* :

```csharp
return View("Views/Home/About.cshtml");
```

Vous pouvez également utiliser un chemin relatif pour spécifier des vues situées dans des répertoires différents. Dans ce cas, n’indiquez pas l’extension *.cshtml* . Dans `HomeController`, vous pouvez retourner la vue *Index* du dossier de vues *Gérer* avec un chemin relatif :

```csharp
return View("../Manage/Index");
```

De même, vous pouvez indiquer le dossier spécifique du contrôleur actif avec le préfixe « ./ » :

```csharp
return View("./About");
```

Les [vues partielles](xref:mvc/views/partial) et les [composants de vue](xref:mvc/views/view-components) utilisent des mécanismes de détection presque identiques.

Vous pouvez personnaliser la convention par défaut de recherche des vues dans l’application à l’aide d’un [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) personnalisé.

La détection des vues recherche les fichiers de vue d’après le nom de fichier. Si le système de fichiers sous-jacent respecte la casse, les noms de vue respectent probablement la casse eux aussi. Pour garantir la compatibilité entre les systèmes d’exploitation, utilisez la même casse pour les noms de contrôleur et d’action et pour les noms de leurs dossiers et fichiers de vues associés. Si vous utilisez un système de fichiers qui respecte la casse et que vous voyez un message d’erreur indiquant qu’un fichier de vue est introuvable, vérifiez que le nom du fichier de vue demandé et le nom du fichier de vue réel ont une casse identique.

Suivez les bonnes pratiques en matière d’organisation de la structure des fichiers de vue. Votre structure doit refléter au mieux les relations entre les contrôleurs, les actions et les vues pour être plus claire et facile à gérer.

## <a name="passing-data-to-views"></a>Passage de données aux vues

Plusieurs approches sont possibles pour passer des données aux vues :

* Données fortement typées : viewmodel
* Données faiblement typées
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Données fortement typées (viewmodel)

L’approche la plus fiable consiste à spécifier un type de [modèle](xref:mvc/models/model-binding) dans la vue. Ce modèle est communément appelé *ViewModel* . Vous passez une instance de type ViewModel à la vue à partir de l’action.

L’utilisation d’un ViewModel pour passer des données à une vue vous permet d’effectuer un contrôle de type *fort* dans la vue. Le terme *typage fort* (ou *fortement typé* ) signifie que chaque variable et constante a un type défini explicitement (par exemple, `string`, `int` ou `DateTime`). La validité des types utilisés dans une vue est contrôlée au moment de la compilation.

[Visual Studio](https://visualstudio.microsoft.com) et [Visual Studio Code](https://code.visualstudio.com/) répertorient les membres de classe fortement typés à l’aide d’une fonctionnalité appelée [IntelliSense](/visualstudio/ide/using-intellisense). Quand vous voulez afficher les propriétés d’un ViewModel, tapez le nom de variable pour le ViewModel suivi d’un point (`.`). Cela vous permet d’écrire du code plus rapidement et avec moins d’erreurs.

Spécifiez un modèle à l’aide de la directive `@model`. Utilisez le modèle avec `@Model` :

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Pour fournir le modèle à la vue, le contrôleur le passe en tant que paramètre :

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Il n’y a pas de restrictions relatives aux types de modèles que vous pouvez fournir à une vue. Nous vous recommandons d’utiliser des ViewModels POCO (Plain Old CLR Object), avec peu ou pas de méthodes de comportement définies. En règle générale, les classes ViewModel sont stockées dans le dossier *Modèles* ou dans un dossier *ViewModels* distinct à la racine de l’application. Le ViewModel *Address* utilisé dans l’exemple ci-dessus est un ViewModel OCT stocké dans un fichier nommé *Address.cs* :

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Rien ne vous empêche d’utiliser les mêmes classes pour vos types de ViewModel et vos types de modèle métier. Toutefois, l’utilisation de modèles distincts vous permet de changer les vues indépendamment de la logique métier et des composants d’accès aux données de votre application. La séparation des modèles et des ViewModel est également un gage de sécurité si vous avez des modèles qui utilisent la [liaison de données](xref:mvc/models/model-binding) et la [validation](xref:mvc/models/validation) pour les données envoyées à l’application par l’utilisateur.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Données faiblement typées (ViewData, attribut ViewData et ViewBag)

`ViewBag`*n’est pas disponible dans Razor Pages.*

En plus des vues fortement typées, les vues ont accès à une collection de données *faiblement typées* (ou *librement typées* ). Contrairement aux types forts, les *types faibles* (ou *types libres* ) ne nécessitent pas de déclarer explicitement le type de données utilisé. Vous pouvez utiliser la collection de données faiblement typées pour passer de petites quantités de données entre les contrôleurs et les vues.

| Passer des données entre...                        | Exemple                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Un contrôleur et une vue                             | Remplissage d’une liste déroulante avec des données.                                          |
| Une vue et une [disposition](xref:mvc/views/layout)   | Définition du **\<title>** contenu de l’élément en mode mise en page à partir d’un fichier de vue.  |
| Une [vue partielle](xref:mvc/views/partial) et une vue | Widget qui affiche des données en fonction de la page web demandée par l’utilisateur.      |

Cette collection peut être référencée par les propriétés `ViewData` ou `ViewBag` sur les contrôleurs et les vues. La propriété `ViewData` est un dictionnaire d’objets faiblement typés. La propriété `ViewBag` est un wrapper autour de `ViewData` qui fournit des propriétés dynamiques pour la collection `ViewData` sous-jacente. Remarque : les recherches de clés ne respectent pas la casse pour `ViewData` et `ViewBag` .

`ViewData` et `ViewBag` sont résolues dynamiquement au moment de l’exécution. Dans la mesure où elles n’effectuent pas de contrôle de type à la compilation, ces deux propriétés sont généralement davantage sujettes aux erreurs qu’un ViewModel. Pour cette raison, certains développeurs préfèrent ne jamais utiliser les propriétés `ViewData` et `ViewBag`, ou les utiliser le moins possible.

<a name="VD"></a>

**ViewData**

`ViewData` est un objet [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) accessible à l’aide de clés `string`. Vous pouvez stocker et utiliser des données de type chaîne directement, sans avoir à les caster, mais vous devez effectuer un cast des autres valeurs de l’objet `ViewData` vers des types spécifiques lors de leur extraction. Vous pouvez utiliser `ViewData` pour passer des données des contrôleurs aux vues et au sein même des vues, y compris les [vues partielles](xref:mvc/views/partial) et les [dispositions](xref:mvc/views/layout).

L’exemple suivant utilise un objet `ViewData` dans une action pour définir les valeurs d’un message d’accueil et d’une adresse :

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Utilisation des données dans une vue :

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**Attribut ViewData**

Une autre approche qui utilise le [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) est [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Les propriétés sur les contrôleurs ou Razor les modèles de page marqués avec l' `[ViewData]` attribut ont leurs valeurs stockées et chargées à partir du dictionnaire.

Dans l’exemple suivant, le contrôleur d’hébergement contient une `Title` propriété marquée avec `[ViewData]` . La méthode `About` définit le titre de la vue About :

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

Dans la disposition, le titre est lu à partir du dictionnaire ViewData :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**ViewBag**

`ViewBag`*n’est pas disponible dans Razor Pages.*

`ViewBag` est un objet [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) qui fournit un accès dynamique aux objets stockés dans `ViewData` . `ViewBag` est parfois plus pratique à utiliser, car il ne nécessite pas de cast. L’exemple suivant montre comment utiliser `ViewBag` pour obtenir le même résultat qu’avec l’objet `ViewData` ci-dessus :

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**Utilisation simultanée de ViewData et ViewBag**

`ViewBag`*n’est pas disponible dans Razor Pages.*

Comme `ViewData` et `ViewBag` font référence à la même collection `ViewData` sous-jacente, vous pouvez utiliser `ViewData` et `ViewBag` simultanément, en les combinant et en les associant pour lire et écrire des valeurs.

Définissez le titre avec `ViewBag` et la description avec `ViewData` au début d’une vue *About.cshtml* :

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Lisez les propriétés, mais inversez l’utilisation de `ViewData` et `ViewBag`. Dans le fichier *_Layout.cshtml* , obtenez le titre et la description avec `ViewData` et `ViewBag`, respectivement :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Souvenez-vous que les chaînes ne nécessitent pas de cast pour `ViewData`. Vous pouvez utiliser `@ViewData["Title"]` sans cast.

L’utilisation simultanée de `ViewData` et `ViewBag` est possible, de la même manière que la combinaison et l’association des propriétés de lecture et d’écriture. Le balisage suivant est affiché :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Récapitulatif des différences entre ViewData et ViewBag**

 `ViewBag` n’est pas disponible dans les Razor pages.

* `ViewData`
  * Dérivé de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), il possède des propriétés de dictionnaire qui peuvent être utiles, telles que `ContainsKey` , `Add` , `Remove` et `Clear` .
  * Les clés contenues dans le dictionnaire sont des chaînes ; les espaces blancs sont donc autorisés. Exemple : `ViewData["Some Key With Whitespace"]`
  * Les autres types que `string` doivent être castés dans la vue pour utiliser `ViewData`.
* `ViewBag`
  * Dérivé de [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), cet objet permet de créer des propriétés dynamiques avec la notation par points (`@ViewBag.SomeKey = <value or object>`). Aucun cast n’est nécessaire. La syntaxe de `ViewBag` facilite son ajout aux contrôleurs et aux vues.
  * Simplifie la vérification des valeurs Null. Exemple : `@ViewBag.Person?.Name`

**Quand utiliser ViewData ou ViewBag ?**

`ViewData` et `ViewBag` constituent deux approches appropriées pour passer de petites quantités de données entre les contrôleurs et les vues. Choisissez celle qui vous convient le mieux. Vous pouvez combiner et associer les objets `ViewData` et `ViewBag`. Toutefois, il est recommandé d’utiliser une seule approche pour faciliter la lecture et la gestion du code. Les deux approches sont résolues dynamiquement au moment de l’exécution et sont donc susceptibles d’engendrer des erreurs d’exécution. C’est la raison pour laquelle certains développeurs préfèrent ne pas les utiliser.

### <a name="dynamic-views"></a>Vues dynamiques

Les vues qui ne déclarent pas de modèle de type à l’aide de `@model` mais qui reçoivent une instance de modèle (par exemple, `return View(Address);`) peuvent référencer dynamiquement les propriétés de l’instance :

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Cette fonctionnalité offre beaucoup de flexibilité, mais elle ne fournit pas de protection de la compilation ou la fonction IntelliSense. Si la propriété n’existe pas, la génération de la page web échoue au moment de l’exécution.

## <a name="more-view-features"></a>Autres fonctionnalités de vue

Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent d’ajouter facilement un comportement côté serveur dans des balises HTML existantes. Leur utilisation vous évite d’avoir à écrire du code personnalisé ou des méthodes d’assistance dans les vues. Les Tag Helpers sont appliqués comme attributs aux éléments HTML et sont ignorés par les éditeurs qui ne peuvent pas les traiter. Vous pouvez ainsi modifier et afficher le balisage des vues dans divers outils.

La génération d’un balisage HTML personnalisé est possible avec de nombreux HTML Helpers intégrés. Si vous avez une logique d’interface utilisateur plus complexe, gérez-la avec les [composants de vue](xref:mvc/views/view-components). Les composants de vue sont conçus sur le même principe de séparation des préoccupations (SoC) que les contrôleurs et les vues. Ils vous évitent de devoir utiliser des actions et des vues pour le traitement des données utilisées par les éléments d’interface utilisateur communs.

Comme de nombreux autres aspects d’ASP.NET Core, les vues prennent en charge [l’injection de dépendances](xref:fundamentals/dependency-injection), ce qui permet aux services d’être [injectés dans les vues](xref:mvc/views/dependency-injection).
