---
title: Vue d’ensemble du modèle MVC d’ASP.NET Core
author: ardalis
description: Découvrez ASP.NET Core MVC, un puissant framework qui vous permet de générer des applications web et des API à l’aide du modèle de conception Model-View-Controller.
ms.author: riande
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
uid: mvc/overview
ms.openlocfilehash: 4f0e98057c03769a672ef350cc3ec724a4f7e721
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431055"
---
# <a name="overview-of-aspnet-core-mvc"></a>Vue d’ensemble du modèle MVC d’ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC est un puissant framework qui vous permet de générer des applications web et des API à l’aide du modèle de conception Model-View-Controller.

## <a name="what-is-the-mvc-pattern"></a>Qu’est-ce que le modèle MVC ?

Le modèle d’architecture Model-View-Controller (MVC) sépare une application en trois groupes de composants principaux : les modèles, les vues et les contrôleurs. Ce modèle permet d’effectuer la [séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). En utilisant ce modèle, les demandes de l’utilisateur sont acheminées vers un contrôleur qui a la responsabilité de fonctionner avec le modèle pour effectuer des actions de l’utilisateur et/ou de récupérer les résultats de requêtes. Le contrôleur choisit la vue à afficher à l’utilisateur et lui fournit toutes les données de modèle dont elle a besoin.

Le diagramme suivant montre les trois composants principaux et leurs relations entre eux :

![Modèle MVC](overview/_static/mvc.png)

Cette délimitation des responsabilités vous aide à mettre à l’échelle la complexité de l’application, car il est plus facile de coder, déboguer et tester une chose (modèle, vue ou contrôleur) qui a un seul travail. Il est plus difficile de mettre à jour, tester et déboguer du code dont les dépendances sont réparties sur deux de ces domaines ou plus. Par exemple, la logique de l’interface utilisateur a tendance à changer plus fréquemment que la logique métier. Si le code de présentation et la logique métier sont combinés en un seul objet, l’objet contenant la logique métier doit être modifié chaque fois que l’interface utilisateur change. Cela introduit souvent des erreurs et nécessite de retester la logique métier après chaque changement minimal de l’interface utilisateur.

> [!NOTE]
> La vue et le contrôleur dépendent tous deux du modèle. Toutefois, le modèle ne dépend ni de la vue ni du contrôleur. Il s’agit de l’un des principaux avantages de la séparation. Cette séparation permet de générer et de tester le modèle indépendamment de la présentation visuelle.

### <a name="model-responsibilities"></a>Responsabilités du modèle

Le modèle d’une application MVC représente l’état de l’application, ainsi que la logique métier ou les opérations à effectuer. La logique métier doit être encapsulée dans le modèle, ainsi que toute autre logique d’implémentation pour la persistance de l’état de l’application. En général, les vues fortement typées utilisent des types ViewModel conçus pour contenir les données à afficher sur cette vue. Le contrôleur crée et remplit ces instances de ViewModel à partir du modèle.

### <a name="view-responsibilities"></a>Responsabilités de la vue

Les vues sont responsables de la présentation du contenu via l’interface utilisateur. Ils utilisent le [ Razor moteur d’affichage](#razor-view-engine) pour incorporer le code .net dans le balisage HTML. Il doit exister une logique minimale dans les vues, et cette logique doit être liée à la présentation du contenu. Si vous avez besoin d’exécuter une grande partie de la logique dans les fichiers de vue pour afficher les données d’un modèle complexe, utilisez un [composant de vue](views/view-components.md), ViewModel ou un modèle de vue pour simplifier la vue.

### <a name="controller-responsibilities"></a>Responsabilités du contrôleur

Les contrôleurs sont des composants qui gèrent l’interaction avec l’utilisateur, fonctionnent avec le modèle et, au final, sélectionnent une vue à afficher. Dans une application MVC, la vue affiche uniquement des informations ; le contrôleur gère les entrées et interactions des utilisateurs, et y répond. Dans le modèle MVC, le contrôleur est le point d’entrée initial. Il est responsable de la sélection des types de modèle à utiliser et de la vue à afficher (ce qui explique son nom, car il contrôle la manière dont l’application répond à une requête donnée).

> [!NOTE]
> Vous devez éviter les excès de responsabilités pour ne pas rendre les contrôleurs trop complexes. Pour éviter que la logique du contrôleur ne devienne trop complexe, envoyez (push) la logique métier hors du contrôleur vers le modèle de domaine.

>[!TIP]
> Si vous constatez que le contrôleur effectue souvent les mêmes genres d’action, placez ces actions usuelles dans des [filtres](#filters).

## <a name="what-is-aspnet-core-mvc"></a>Nouveautés d’ASP.NET Core MVC

Le framework ASP.NET Core MVC est un framework de présentation léger, open source et hautement testable, optimisé pour ASP.NET Core.

ASP.NET Core MVC offre un fonctionnement basé sur des patterns pour créer des sites Web dynamiques qui permettent une séparation claire des préoccupations. Il vous donne un contrôle total sur le balisage, prend en charge les développements TDD et utilise les standards web les plus récents.

## <a name="features"></a>Fonctionnalités

ASP.NET Core MVC inclut les éléments suivants :

* [Routage](#routing)
* [Liaison de données](#model-binding)
* [Validation du modèle](#model-validation)
* [Injection de dépendances](../fundamentals/dependency-injection.md)
* [Filtres](#filters)
* [Régions](#areas)
* [API web](#web-apis)
* [Testabilité](#testability)
* [Razor moteur d’affichage](#razor-view-engine)
* [Vues fortement typées](#strongly-typed-views)
* [Tag Helpers](#tag-helpers)
* [Afficher les composants](#view-components)

### <a name="routing"></a>Routage

ASP.NET Core MVC est construit sur [le routage d'ASP.NET Core](../fundamentals/routing.md), un composant de mappage d’URL puissant qui vous permet de créer des applications ayant des URLs compréhensibles et découvrables. Cela vous permet de définir les patterns de noms d’URL de votre application qui fonctionnent bien pour l’optimisation des moteurs de recherche (SEO) et pour la génération de lien, sans tenir compte de la façon dont les fichiers sont organisés sur votre serveur web. Vous pouvez définir vos routes à l’aide d’une syntaxe pratique de modèle de routes (route template) qui prend en charge les contraintes de valeur, les valeurs par défaut et les valeurs facultatives de routes.

*Le routage basé sur les conventions* vous permet de définir globalement les formats d’URL acceptés par votre application, ainsi que le mappage de chacun de ces formats à une méthode d’action spécifique sur un contrôleur donné. Quand une requête entrante est reçue, le moteur de routage analyse l’URL et la fait correspondre à l’un des formats d’URL définis, puis il appelle la méthode d’action du contrôleur associé.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

Le *routage d’attributs* vous permet de spécifier des informations de routage en décorant vos contrôleurs et vos actions avec des attributs qui définissent les routages de votre application. Cela signifie que vos définitions de routage sont placées à côté du contrôleur et de l’action auxquels elles sont associées.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a>Liaison de données

La [liaison de modèle](models/model-binding.md) ASP.NET Core MVC convertit les données de requête client (les valeurs de formulaire, les données de routage, les paramètres de chaîne de requête, les en-têtes HTTP) en objets que le contrôleur peut traiter. Ainsi, la logique du contrôleur n’a pas besoin d’identifier les données de requête entrantes ; elle utilise simplement les données en tant que paramètres de ses méthodes d’action.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a>Validation du modèle

ASP.NET Core MVC prend en charge la [validation](models/validation.md) en décorant votre objet de modèle avec des attributs de validation d’annotation de données. Les attributs de validation sont vérifiés côté client avant que les valeurs ne soient postées sur le serveur, ainsi que sur le serveur avant l’appel de l’action du contrôleur.

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

Action du contrôleur :

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

Le framework gère la validation des données de requête à la fois sur le client et sur le serveur. La logique de validation spécifiée pour les types de modèle est ajoutée aux vues affichées en tant qu’annotations discrètes, et est appliquée dans le navigateur à l’aide de la [validation jQuery](https://jqueryvalidation.org/).

### <a name="dependency-injection"></a>Injection de dépendances

ASP.NET Core offre une prise en charge intégrée de l’[injection de dépendances](../fundamentals/dependency-injection.md). Dans ASP.NET Core MVC, les [contrôleurs](controllers/dependency-injection.md) peuvent demander les services nécessaires via leurs constructeurs, ce qui leur permet de suivre le [principe des dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

Votre application peut également utiliser l’[injection de dépendances dans les fichiers de vue](views/dependency-injection.md), à l’aide de la directive `@inject` :

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>Filtres

Les [filtres](controllers/filters.md) permettent aux développeurs d’intégrer des problèmes transversaux, par exemple la prise en charge des exceptions ou les autorisations. Les filtres permettent d’exécuter une logique de prétraitement et de posttraitement personnalisée pour les méthodes d’action. Vous pouvez les configurer pour qu’ils se lancent à certaines étapes du pipeline d’exécution d’une requête donnée. Vous pouvez appliquer les filtres aux contrôleurs ou aux actions en tant qu’attributs (ou vous pouvez les exécuter de manière globale). Plusieurs filtres (tels que `Authorize`) sont inclus dans le framework. `[Authorize]` est l’attribut qui est utilisé pour créer des filtres d’autorisation MVC.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>Régions

Les [zones](controllers/areas.md) permettent de partitionner une grande ASP.net Core application Web MVC en regroupements fonctionnels plus petits. Une zone est en réalité une structure MVC à l’intérieur d’une application. Dans un projet MVC, les composants logiques tels que les modèles, les contrôleurs et les vues sont conservés dans des dossiers différents et MVC utilise les conventions de nommage pour créer la relation entre ces composants. Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau. Par exemple, une application de commerce électronique avec plusieurs unités commerciales, telles que l’extraction, la facturation et la recherche, etc. Chacune de ces unités a ses propres affichages de composants logiques, contrôleurs et modèles.

### <a name="web-apis"></a>API Web

En plus d’être une excellente plateforme pour la création de sites web, ASP.NET Core MVC prend en charge la génération d’API web. Vous pouvez créer des services accessibles à un large éventail de clients, notamment les navigateurs et les appareils mobiles.

Le framework inclut la prise en charge de la négociation de contenu HTTP, ainsi que la prise en charge intégrée de la [mise en forme des données](xref:web-api/advanced/formatting) au format JSON ou XML. Écrivez des [formateurs personnalisés](xref:web-api/advanced/custom-formatters) pour ajouter la prise en charge de vos propres formats.

Utilisez la génération de lien pour activer la prise en charge de liens hypermédia. Activez facilement la prise en charge du [partage de ressources cross-origin (CORS)](https://www.w3.org/TR/cors/) afin que vos APIs Web puissent être partagées entre plusieurs applications Web.

### <a name="testability"></a>Testabilité

Le framework utilise les interfaces et l’injection de dépendances, ce qui le rend particulièrement adapté aux tests unitaires. De plus, le framework inclut des fonctionnalités (par exemple un fournisseur TestHost et InMemory pour Entity Framework) qui facilitent aussi les [tests d’intégration](xref:test/integration-tests). Découvrez en plus sur le [test de la logique du contrôleur](controllers/testing.md).

### <a name="no-locrazor-view-engine"></a>Razor moteur d’affichage

[ASP.net Core vues MVC](views/overview.md) utilisent le [ Razor moteur d’affichage](views/razor.md) pour restituer les vues. Razor est un langage de balisage de modèle fluide, expressif et compact pour la définition de vues à l’aide de code C# incorporé. Razor est utilisé pour générer dynamiquement du contenu Web sur le serveur. Vous pouvez mélanger sans problème du code serveur avec du contenu et du code côté client.

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

À l’aide du Razor moteur d’affichage, vous pouvez définir des [dispositions](views/layout.md), des [vues partielles](views/partial.md) et des sections remplaçables.

### <a name="strongly-typed-views"></a>Vues fortement typées

Razor les vues dans MVC peuvent être fortement typées en fonction de votre modèle. Les contrôleurs peuvent passer un modèle fortement typé à des vues, ce qui leur permet de disposer du contrôle de type et de la prise en charge d’IntelliSense.

Par exemple, la vue suivante affiche un modèle de type `IEnumerable<Product>` :

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Tag Helpers

Les [tag helpers](views/tag-helpers/intro.md) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers. Vous pouvez utiliser des Tag helpers pour définir des balises personnalisées (par exemple, `<environment>`) ou pour modifier le comportement de balises existantes (par exemple, `<label>`). Les Tag helpers associent des éléments spécifiques en fonction du nom de l’élément et des ses attributs. Ils fournissent les avantages de rendu côté serveur tout en conservant la possibilité d'éditer le HTML.

Il existe de nombreux Tag Helpers pour les tâches courantes (par exemple la création de formulaires ou de liens, le chargement de ressources, etc.) et bien d’autres encore, dans les dépôts GitHub publics et sous forme de packages NuGet. Les Tag helpers sont créés en c#, et ils ciblent des éléments HTML en fonction de la balise parente, du nom d’attribut ou du nom de l’élément. Par exemple, le Tag Helper Link intégré permet de créer un lien vers l’action `Login` de `AccountsController` :

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

Le `EnvironmentTagHelper` permet d’inclure différents scripts dans vos vues (par exemple bruts ou minimisés) en fonction de l’environnement d’exécution, Développement, Préproduction ou Production :

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

Tag helpers offre une expérience de développement HTML et un environnement IntelliSense élaboré pour la création de code HTML et de Razor balises. La plupart des Tag Helpers intégrés ciblent les éléments HTML existants et fournissent des attributs côté serveur pour l’élément.

### <a name="view-components"></a>Composants de vues

Les [composants de vues](views/view-components.md) vous permettent de compresser la logique de rendu et de la réutiliser dans l’application. Ils sont similaires aux [vues partielles](views/partial.md), mais avec une logique associée.

## <a name="compatibility-version"></a>Version de compatibilité

La méthode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permet à une application d’accepter ou de refuser les changements de comportement potentiellement cassants introduits dans ASP.NET Core MVC 2.1 ou version ultérieure.

Pour plus d'informations, consultez <xref:mvc/compatibility-version>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Bibliothèque de tests MyTested. AspNetCore. Mvc-Fluent pour ASP.net Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): bibliothèque de tests unitaires fortement typés, fournissant une interface Fluent pour tester les applications MVC et API Web. ( *Non géré ou pris en charge par Microsoft.* )
* <xref:blazor/components/prerendering-and-integration>
