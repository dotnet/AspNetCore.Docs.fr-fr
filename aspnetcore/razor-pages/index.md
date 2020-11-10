---
title: Présentation Razor des pages dans ASP.net Core
author: Rick-Anderson
description: Découvrez comment Razor les pages de ASP.net Core rendent le codage des scénarios orientés page plus facile et plus productif que l’utilisation de MVC.
monikerRange: '>= aspnetcore-2.0'
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
uid: razor-pages/index
ms.openlocfilehash: 89e06d00e9312a428c4e164b0dc60527fe12d904
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430872"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a>Présentation Razor des pages dans ASP.net Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/rynowak)

Razor Les pages peuvent rendre le codage des scénarios orientés page plus facile et plus productif que l’utilisation de contrôleurs et de vues.

Si vous cherchez un didacticiel qui utilise l’approche Model-View-Controller, consultez [Bien démarrer avec ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ce document fournit une introduction aux Razor pages. Il ne s’agit pas d’un didacticiel pas à pas. Si vous trouvez certaines des sections trop avancées, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start). Pour une vue d’ensemble d’ASP.NET Core, consultez [Introduction à ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Créer un Razor projet pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour obtenir des instructions détaillées sur la création d’un projet de pages, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) Razor .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez `dotnet new webapp` à partir de la ligne de commande.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Pour obtenir des instructions détaillées sur la création d’un projet de pages, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) Razor .

---

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages est activée dans *Startup.cs* :

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Considérez une page de base : <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Le code précédent ressemble beaucoup à un [ Razor fichier de vue](xref:tutorials/first-mvc-app/adding-view) utilisé dans une application ASP.net core avec des contrôleurs et des vues. Ce qui le rend différent est la [`@page`](xref:mvc/views/razor#page) directive. `@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur. `@page` doit être la première Razor directive sur une page. `@page` affecte le comportement d’autres [Razor](xref:mvc/views/razor) constructions. Razor Les noms de fichiers de pages ont un suffixe *. cshtml* .

Une page similaire, utilisant une classe `PageModel`, est illustrée dans les deux fichiers suivants. Le fichier *Pages/Index2.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Le modèle de page *Pages/Index2.cshtml.cs*  :

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Par Convention, le `PageModel` fichier de classe a le même nom que le Razor fichier d’échange avec *. cs* ajouté. Par exemple, la Razor page précédente est *pages/index2. cshtml*. Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.

Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers. Le tableau suivant indique un Razor chemin d’accès à la page et l’URL correspondante :

| Nom et chemin de fichier               | URL correspondante |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` ou `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` ou `/Store/Index` |

Remarques :

* Le runtime recherche les Razor fichiers de pages dans le dossier *pages* par défaut.
* `Index` est la page par défaut quand une URL n’inclut pas de page.

## <a name="write-a-basic-form"></a>Écrire un formulaire de base

Razor Les pages sont conçues pour rendre les modèles courants utilisés avec les navigateurs Web faciles à implémenter lors de la création d’une application. La [liaison de modèle](xref:mvc/models/model-binding), les [tag helpers](xref:mvc/views/tag-helpers/intro)et les applications auxiliaires HTML *fonctionnent tout simplement* avec les propriétés définies dans une classe de Razor page. Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :

Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).

La base de données en mémoire requiert le `Microsoft.EntityFrameworkCore.InMemory` package NuGet.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Le modèle de données :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Le contexte de la base de données :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Le fichier vue *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Le modèle de page *Pages/Create.cshtml.cs*  :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.

La classe `PageModel` permet de séparer la logique d’une page de sa présentation. Elle définit des gestionnaires de page pour les demandes envoyées à la page et les données utilisées pour l’afficher. Cette séparation permet :

* Gestion des dépendances de page via l' [injection de dépendances](xref:fundamentals/dependency-injection).
* [Effectuer des tests unitaires](xref:test/razor-pages-tests)

La page a une  *méthode de gestionnaire*`OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire). Les méthodes de gestionnaire pour tout verbe HTTP peuvent être ajoutées. Les gestionnaires les plus courants sont :

* `OnGet` pour initialiser l’état nécessaire pour la page. Dans le code précédent, la `OnGet` méthode affiche la page *CreateModel. cshtml* Razor .
* `OnPost` pour gérer les envois de formulaire.

Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones. Le code précédent est normal pour les Razor pages.

Si vous êtes familiarisé avec les applications ASP.NET à l’aide de contrôleurs et de vues :

* Le `OnPostAsync` Code de l’exemple précédent ressemble au code de contrôleur classique.
* La plupart des primitives MVC, telles que la [liaison de modèle](xref:mvc/models/model-binding), la [validation](xref:mvc/models/validation)et les résultats d’action, fonctionnent de la même manière avec les contrôleurs et les Razor pages. 

La méthode `OnPostAsync` précédente :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Le flux de base de `OnPostAsync` :

Vérifiez s’il y a des erreurs de validation.

* S’il n’y a aucune erreur, enregistrez les données et redirigez.
* S’il y a des erreurs, réaffichez la page avec des messages de validation. Dans de nombreux cas, les erreurs de validation seraient détectées sur le client et jamais envoyées au serveur.

Le fichier vue *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Le rendu HTML à partir de *pages/Create. cshtml* :

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

Dans le code précédent, la publication du formulaire :

* Avec des données valides :

  * La `OnPostAsync` méthode de gestionnaire appelle la <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> méthode d’assistance. `RedirectToPage` retourne une instance de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Est un résultat d’action.
    * Est semblable à `RedirectToAction` ou `RedirectToRoute` (utilisé dans les contrôleurs et les vues).
    * Est personnalisé pour les pages. Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`). `RedirectToPage` est détaillé dans la section [génération d’URL pour les pages](#url_gen) .

* Avec les erreurs de validation qui sont transmises au serveur :

  * La `OnPostAsync` méthode de gestionnaire appelle la <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> méthode d’assistance. `Page` retourne une instance de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`. `PageResult` est le type de retour par défaut pour une méthode de gestionnaire. Une méthode de gestionnaire qui retourne `void` restitue la page.
  * Dans l’exemple précédent, la publication du formulaire sans valeur entraîne le renvoi de la valeur false à [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) . Dans cet exemple, aucune erreur de validation n’est affichée sur le client. La gestion des erreurs de validation est traitée plus loin dans ce document.

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* Avec les erreurs de validation détectées par la validation côté client :

  * Les données ne sont **pas** publiées sur le serveur.
  * La validation côté client est expliquée plus loin dans ce document.

La `Customer` propriété utilise l' [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribut pour s’abonner à la liaison de modèle :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]` ne doit **pas** être utilisé sur les modèles contenant des propriétés qui ne doivent pas être modifiées par le client. Pour plus d’informations, consultez [survalidation](xref:data/ef-rp/crud#overposting).

Razor Les pages, par défaut, lient les propriétés uniquement avec des non- `GET` verbes. La liaison aux propriétés évite d’avoir à écrire du code pour convertir des données HTTP en type de modèle. Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name">`) et accepter l’entrée.

[!INCLUDE[](~/includes/bind-get.md)]

Examen du fichier de vue *pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* Dans le code précédent, le [tag Helper d’entrée](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` lie l’élément HTML `<input>` à l' `Customer.Name` expression de modèle.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) rend les tag helpers disponibles.

### <a name="the-home-page"></a>La page d’accueil

*Index. cshtml* est la page d’hébergement :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

La classe `PageModel` associée ( *Index.cshtml.cs* ) :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Le fichier *index. cshtml* contient le balisage suivant :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

Le `<a /a>` [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a utilisé l' `asp-route-{value}` attribut pour générer un lien vers la page de modification. Le lien contient des données d’itinéraire avec l’ID de contact. Par exemple : `https://localhost:5001/Edit/1`. Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.

Le fichier *index. cshtml* contient un balisage pour créer un bouton Supprimer pour chaque contact client :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

HTML rendu :

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Lorsque le bouton supprimer est rendu en HTML, son [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) comprend des paramètres pour :

* ID du contact client, spécifié par l' `asp-route-id` attribut.
* `handler`, Spécifié par l' `asp-page-handler` attribut.

Quand le bouton est sélectionné, une demande `POST` de forumaire est envoyée au serveur. Par convention, le nom de la méthode de gestionnaire est sélectionné en fonction de la valeur du paramètre `handler` conformément au schéma `OnPost[handler]Async`.

Étant donné que le `handler` est `delete` dans cet exemple, la méthode de gestionnaire `OnPostDeleteAsync` est utilisée pour traiter la demande `POST`. Si `asp-page-handler` est défini sur une autre valeur, comme `remove`, une méthode de gestionnaire avec le nom `OnPostRemoveAsync` est sélectionnée.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

La méthode `OnPostDeleteAsync` :

* Obtient le `id` de la chaîne de requête.
* Interroge la base de données pour le contact client avec `FindAsync`.
* Si le contact client est trouvé, il est supprimé et la base de données est mise à jour.
* Appelle <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pour rediriger vers la page Index racine (`/Index`).

### <a name="the-editcshtml-file"></a>Le fichier Edit. cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

La première ligne contient la directive `@page "{id:int}"`. La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`. Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable). Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Le fichier *Edit.cshtml.cs* :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Validation

Règles de validation :

* Sont spécifiés de façon déclarative dans la classe de modèle.
* Sont appliquées partout dans l’application.

L' <xref:System.ComponentModel.DataAnnotations> espace de noms fournit un jeu d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou une propriété. DataAnnotations contient également des attributs de mise en forme tels [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) que l’aide à la mise en forme et ne fournissent aucune validation.

Prenons le `Customer` modèle :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

À l’aide du fichier de vue *Create. cshtml* suivant :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Le code précédent :

* Comprend des scripts de validation jQuery et jQuery.
* Utilise le `<div />` et les `<span />` [aide pour les balises](xref:mvc/views/tag-helpers/intro) pour activer :

  * Validation côté client.
  * Rendu des erreurs de validation.

* Génère le code HTML suivant :

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

La publication de la valeur créer un formulaire sans nom affiche le message d’erreur « le champ nom est obligatoire. » sur le formulaire. Si JavaScript est activé sur le client, le navigateur affiche l’erreur sans la publier sur le serveur.

L' `[StringLength(10)]` attribut génère `data-val-length-max="10"` sur le rendu HTML. `data-val-length-max` empêche les navigateurs d’entrer une valeur supérieure à la longueur maximale spécifiée. Si un outil tel que [Fiddler](https://www.telerik.com/fiddler) est utilisé pour modifier et relire la publication :

* Avec le nom plus long que 10.
* Le message d’erreur « le nom du champ doit être une chaîne d’une longueur maximale de 10 ». » est renvoyé.

Prenons le `Movie` modèle suivant :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Les attributs de validation spécifient le comportement à appliquer sur les propriétés de modèle auxquelles ils sont appliqués :

* Les `Required` `MinimumLength` attributs et indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer un espace blanc pour satisfaire cette validation.
* L’attribut `RegularExpression` sert à limiter les caractères pouvant être entrés. Dans le code précédent, « Genre » :

  * Doit utiliser seulement des lettres.
  * La première lettre doit être une majuscule. Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.

* L’expression `RegularExpression` « Rating » :

  * Nécessite que le premier caractère soit une lettre majuscule.
  * Autorise les caractères spéciaux et les nombres dans les espaces suivants. « PG-13 » est valide pour une évaluation, mais échoue pour un « Genre ».

* L’attribut `Range` limite une valeur à une plage spécifiée.
* L' `StringLength` attribut définit la longueur maximale d’une propriété de type chaîne et, éventuellement, sa longueur minimale.
* Les types valeur (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `[Required]`.

La page créer du modèle affiche des `Movie` Erreurs avec des valeurs non valides :

![Formulaire de vue Movie avec plusieurs erreurs de validation jQuery côté client](~/tutorials/razor-pages/validation/_static/val.png)

Pour plus d’informations, consultez :

* [Ajouter la validation à l’application vidéo](xref:tutorials/razor-pages/validation)
* [Validation de modèle dans ASP.net Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Gérer les requêtes HEAD avec un gestionnaire OnGet de secours

`HEAD` les requêtes permettent de récupérer les en-têtes pour une ressource spécifique. Contrairement aux requêtes `GET`, les requêtes `HEAD` ne retournent pas un corps de réponse.

En règle générale, un gestionnaire `OnHead` est créé et appelé pour les requêtes `HEAD` :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Les pages reviennent à appeler le `OnGet` Gestionnaire si aucun `OnHead` gestionnaire n’est défini.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF et Razor pages

Razor Les pages sont protégées par la validation anti- [contrefaçon](xref:security/anti-request-forgery). Le [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injecte des jetons anti-contrefaçon dans des éléments de formulaire HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Utilisation de dispositions, de partiels, de modèles et d’aide pour les balises avec des Razor pages

Les pages fonctionnent avec toutes les fonctionnalités du Razor moteur d’affichage. Les mises en page, les partiels, les modèles, les tag helpers, *_ViewStart. cshtml* et *_ViewImports. cshtml* fonctionnent de la même façon que pour les Razor vues conventionnelles.

Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.

Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/Shared/_Layout.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

La [disposition](xref:mvc/views/layout):

* Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).
* Importe des structures HTML telles que JavaScript et les feuilles de style.
* Le contenu de la Razor page est rendu où `@RenderBody()` est appelé.

Pour plus d’informations, consultez [page disposition](xref:mvc/views/layout).

La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

La disposition est dans le dossier *Pages/Shared*. Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active. Une mise en page dans le dossier *pages/Shared* peut être utilisée à partir de n’importe quelle Razor page sous le dossier *pages* .

Le fichier de disposition doit être placé dans le dossier *Pages/Shared*.

Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*. *Views/Shared* est un modèle de vues MVC. Razor Les pages sont conçues pour s’appuyer sur la hiérarchie des dossiers, et non sur les conventions de chemin.

L’affichage de la recherche à partir d’une Razor page comprend le dossier *pages* . Les mises en page, les modèles et les partiels utilisés avec les contrôleurs MVC et les Razor vues conventionnelles *fonctionnent simplement*.

Ajoutez un fichier *Pages/_ViewImports.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` est expliqué plus loin dans le didacticiel. La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](xref:mvc/views/tag-helpers/builtin-th/Index) dans toutes les pages du dossier *Pages*.

<a name="namespace"></a>

La `@namespace` directive est définie sur une page :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La `@namespace` directive définit l’espace de noms de la page. La directive `@model` n’a pas besoin d’inclure l’espace de noms.

Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml* , l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`. Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.

Par exemple, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

L’espace de noms généré pour la page *pages/Customers/Edit. cshtml* Razor est le même que la `PageModel` classe.

`@namespace`*fonctionne également avec les Razor vues conventionnelles.*

Examinez le fichier de vue *pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Le fichier de vue *pages/Create. cshtml* mis à jour avec *_ViewImports. cshtml* et le fichier de disposition précédent :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

Dans le code précédent, le *_ViewImports. cshtml* a importé l’espace de noms et les tag helpers. Le fichier de disposition a importé les fichiers JavaScript.

Le [ Razor projet de démarrage pages](#rpvs17) contient les *pages/_ValidationScriptsPartial. cshtml* , qui raccorde la validation côté client.

Pour plus d'informations sur les affichages partiels, consultez <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Génération d’URL pour les pages

La page `Create`, illustrée précédemment, utilise `RedirectToPage` :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

L’application a la structure de fichiers/dossiers suivante :

* */Pages*

  * *Index.cshtml*
  * *Privacy. cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Les pages *pages/Customers/Create. cshtml* et *pages/Customers/Edit. cshtml* redirigent vers *pages/Customers/index. cshtml* après réussite. La chaîne `./Index` est un nom de page relatif utilisé pour accéder à la page précédente. Elle est utilisée pour générer des URL dans la page *pages/Customers/index. cshtml* . Par exemple :

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Le nom de page absolu `/Index` est utilisé pour générer des URL dans la page *pages/index. cshtml* . Par exemple :

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Le nom de la page est le chemin de la page à partir du dossier racine */Pages* avec un `/` devant (par exemple, `/Index`). Les exemples de génération d’URL précédents offrent des options améliorées et des fonctionnalités fonctionnelles par rapport au codage en dur d’une URL. La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.

La génération d’URL pour les pages prend en charge les noms relatifs. Le tableau suivant indique quelle page d’index est sélectionnée à l’aide `RedirectToPage` de différents paramètres dans *pages/Customers/Create. cshtml*.

| RedirectToPage(x)| Page |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")` et `RedirectToPage("../Index")` sont des *noms relatifs*. Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.

La liaison de nom relatif est utile lors de la création de sites avec une structure complexe. Lorsque des noms relatifs sont utilisés pour établir une liaison entre les pages d’un dossier :

* Le changement de nom d’un dossier n’interrompt pas les liens relatifs.
* Les liens ne sont pas rompus, car ils n’incluent pas le nom du dossier.

Pour rediriger vers une page située dans une autre [Zone](xref:mvc/controllers/areas), spécifiez la zone :

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Pour plus d’informations, consultez <xref:mvc/controllers/areas> et <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Attribut ViewData

Les données peuvent être passées à une page avec <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> . Les valeurs des propriétés avec l' `[ViewData]` attribut sont stockées et chargées à partir de <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .

Dans l’exemple suivant, le `AboutModel` applique l' `[ViewData]` attribut à la `Title` propriété :

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Dans la page À propos de, accédez à la propriété `Title` en tant que propriété de modèle :

```cshtml
<h1>@Model.Title</h1>
```

Dans la disposition, le titre est lu à partir du dictionnaire ViewData :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core expose <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Cette propriété stocke les données jusqu’à ce qu’elles soient lues. Vous pouvez utiliser les méthodes <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> et <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> pour examiner les données sans suppression. `TempData` est utile pour la redirection, quand des données sont nécessaires pour plusieurs requêtes.

Le code suivant définit la valeur de `Message` à l’aide de `TempData` :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Le modèle de page *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Pour plus d’informations, consultez [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Plusieurs gestionnaires par page

La page suivante génère un balisage pour deux gestionnaires en utilisant le Tag Helper `asp-page-handler` :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente. L’attribut `asp-page-handler` est un complément de `asp-page`. `asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page. `asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.

Le modèle de page :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Le code précédent utilise des *méthodes de gestionnaire nommées*. Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant). Dans l’exemple précédent, les méthodes de page sont OnPost **JoinList** Async et OnPost **JoinListUC** Async. Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Routes personnalisées

Utilisez la directive `@page` pour :

* Spécifier une route personnalisée vers une page. Par exemple, la route vers la page À propos peut être définie sur `/Some/Other/Path` avec `@page "/Some/Other/Path"`.
* Ajouter des segments à la route par défaut d’une page. Par exemple, un segment « item » peut être ajouté à la route par défaut d’une page avec `@page "item"`.
* Ajouter des paramètres à la route par défaut d’une page. Par exemple, un paramètre d’ID, `id`, peut être nécessaire pour une page avec `@page "{id}"`.

Un chemin relatif racine désigné par un tilde (`~`) au début du chemin est pris en charge. Par exemple, `@page "~/Some/Other/Path"` est identique à `@page "/Some/Other/Path"`.

Si vous n’aimez pas la chaîne `?handler=JoinList` de requête dans l’URL, modifiez l’itinéraire pour placer le nom du gestionnaire dans la partie chemin d’accès de l’URL. L’itinéraire peut être personnalisé en ajoutant un modèle de routage entre guillemets doubles après la `@page` directive.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH/JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.

## <a name="advanced-configuration-and-settings"></a>Configuration et paramètres avancés

La configuration et les paramètres des sections suivantes ne sont pas requis par la plupart des applications.

Pour configurer des options avancées, utilisez la surcharge qui est configurée <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> pour définir le répertoire racine pour les pages ou ajouter des conventions de modèle d’application pour les pages. Pour plus d’informations sur les conventions, consultez [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization).

Pour précompiler des vues, consultez [ Razor afficher la compilation](xref:mvc/views/view-compilation).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Spécifier que Razor les pages se trouvent à la racine du contenu

Par défaut, Razor les pages sont enracinées dans le répertoire */pages* Ajouter <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> pour spécifier que vos Razor pages se trouvent à la [racine du contenu](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) de l’application :

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Spécifier que Razor les pages se trouvent dans un répertoire racine personnalisé

Ajouter <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> pour spécifier que Razor les pages se trouvent dans un répertoire racine personnalisé dans l’application (fournissez un chemin d’accès relatif) :

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* Consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start), qui s’appuie sur cette introduction.
* [Autoriser l’attribut et les Razor pages](xref:security/authorization/simple#aarp)
* [Télécharger ou afficher l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/rynowak)

Razor Pages est un nouvel aspect de ASP.NET Core MVC qui rend le codage des scénarios orientés page plus facile et plus productif.

Si vous cherchez un didacticiel qui utilise l’approche Model-View-Controller, consultez [Bien démarrer avec ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ce document fournit une introduction aux Razor pages. Il ne s’agit pas d’un didacticiel pas à pas. Si vous trouvez certaines des sections trop avancées, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start). Pour une vue d’ensemble d’ASP.NET Core, consultez [Introduction à ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Créer un Razor projet pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour obtenir des instructions détaillées sur la création d’un projet de pages, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) Razor .

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Exécutez `dotnet new webapp` à partir de la ligne de commande.

Ouvrez le fichier *.csproj* généré à partir de Visual Studio pour Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez `dotnet new webapp` à partir de la ligne de commande.

---

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages est activée dans *Startup.cs* :

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Considérez une page de base : <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Le code précédent ressemble beaucoup à un [ Razor fichier de vue](xref:tutorials/first-mvc-app/adding-view) utilisé dans une application ASP.net core avec des contrôleurs et des vues. Ce qui le rend différent est la directive `@page`. `@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur. `@page` doit être la première Razor directive sur une page. `@page` affecte le comportement d’autres Razor constructions.

Une page similaire, utilisant une classe `PageModel`, est illustrée dans les deux fichiers suivants. Le fichier *Pages/Index2.cshtml* :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Le modèle de page *Pages/Index2.cshtml.cs*  :

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Par Convention, le `PageModel` fichier de classe a le même nom que le Razor fichier d’échange avec *. cs* ajouté. Par exemple, la Razor page précédente est *pages/index2. cshtml*. Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.

Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers. Le tableau suivant indique un Razor chemin d’accès à la page et l’URL correspondante :

| Nom et chemin de fichier               | URL correspondante |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` ou `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` ou `/Store/Index` |

Remarques :

* Le runtime recherche les Razor fichiers de pages dans le dossier *pages* par défaut.
* `Index` est la page par défaut quand une URL n’inclut pas de page.

## <a name="write-a-basic-form"></a>Écrire un formulaire de base

Razor Les pages sont conçues pour rendre les modèles courants utilisés avec les navigateurs Web faciles à implémenter lors de la création d’une application. La [liaison de modèle](xref:mvc/models/model-binding), les [tag helpers](xref:mvc/views/tag-helpers/intro)et les applications auxiliaires HTML *fonctionnent tout simplement* avec les propriétés définies dans une classe de Razor page. Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :

Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Le modèle de données :

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Le contexte de la base de données :

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Le fichier vue *Pages/Create.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Le modèle de page *Pages/Create.cshtml.cs*  :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.

La classe `PageModel` permet de séparer la logique d’une page de sa présentation. Elle définit des gestionnaires de page pour les demandes envoyées à la page et les données utilisées pour l’afficher. Cette séparation permet :

* Gestion des dépendances de page via l' [injection de dépendances](xref:fundamentals/dependency-injection).
* [Test unitaire](xref:test/razor-pages-tests) des pages.

La page a une  *méthode de gestionnaire*`OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire). Vous pouvez ajouter des méthodes de gestionnaire pour n’importe quel verbe HTTP. Les gestionnaires les plus courants sont :

* `OnGet` pour initialiser l’état nécessaire pour la page. Exemple [OnGet](#OnGet).
* `OnPost` pour gérer les envois de formulaire.

Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones. Le code précédent est normal pour les Razor pages.

Si vous êtes familiarisé avec les applications ASP.NET à l’aide de contrôleurs et de vues :

* Le `OnPostAsync` Code de l’exemple précédent ressemble au code de contrôleur classique.
* La plupart des primitives MVC, telles que la [liaison de modèle](xref:mvc/models/model-binding), la [validation](xref:mvc/models/validation), la [validation](xref:mvc/models/validation)et les résultats d’action, sont partagées.

La méthode `OnPostAsync` précédente :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Le flux de base de `OnPostAsync` :

Vérifiez s’il y a des erreurs de validation.

* S’il n’y a aucune erreur, enregistrez les données et redirigez.
* S’il y a des erreurs, réaffichez la page avec des messages de validation. La validation côté client est identique à celle des applications ASP.NET Core MVC traditionnelles. Dans de nombreux cas, les erreurs de validation seraient détectées sur le client et jamais envoyées au serveur.

Quand les données sont entrées correctement, la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `RedirectToPage` pour retourner une instance de `RedirectToPageResult`. `RedirectToPage` est un nouveau résultat d’action, semblable à `RedirectToAction` ou `RedirectToRoute`, mais personnalisé pour les pages. Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`). `RedirectToPage` est détaillé dans la section [génération d’URL pour les pages](#url_gen) .

Quand le formulaire envoyé comporte des erreurs de validation (qui sont passées au serveur), la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `Page`. `Page` retourne une instance de `PageResult`. Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`. `PageResult` est le type de retour par défaut pour une méthode de gestionnaire. Une méthode de gestionnaire qui retourne `void` restitue la page.

La propriété `Customer` utilise l’attribut `[BindProperty]` pour accepter la liaison de modèle.

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Les pages, par défaut, lient les propriétés uniquement avec des non- `GET` verbes. La liaison à des propriétés peut réduire la quantité de code à écrire. Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name">`) et accepter l’entrée.

[!INCLUDE[](~/includes/bind-get.md)]

La page d’accueil ( *Index.cshtml* ) :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

La classe `PageModel` associée ( *Index.cshtml.cs* ) :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Le fichier *Index.cshtml* contient le balisage suivant pour créer un lien d’édition pour chaque contact :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

Le `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a utilisé l' `asp-route-{value}` attribut pour générer un lien vers la page de modification. Le lien contient des données d’itinéraire avec l’ID de contact. Par exemple : `https://localhost:5001/Edit/1`. Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers. Les Tag Helpers sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Le fichier *Pages/Edit.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

La première ligne contient la directive `@page "{id:int}"`. La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`. Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable). Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Le fichier *Pages/Edit.cshtml.cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Le fichier *Index.cshtml* contient également le balisage pour créer un bouton Supprimer pour chaque contact client :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Lorsque le bouton Supprimer est rendu en HTML, son `formaction` comprend des paramètres pour :

* L’ID du contact client spécifié par l’attribut `asp-route-id`.
* Le `handler` spécifié par l’attribut `asp-page-handler`.

Voici un exemple de bouton Supprimer rendu avec un ID de contact client de `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Quand le bouton est sélectionné, une demande `POST` de forumaire est envoyée au serveur. Par convention, le nom de la méthode de gestionnaire est sélectionné en fonction de la valeur du paramètre `handler` conformément au schéma `OnPost[handler]Async`.

Étant donné que le `handler` est `delete` dans cet exemple, la méthode de gestionnaire `OnPostDeleteAsync` est utilisée pour traiter la demande `POST`. Si `asp-page-handler` est défini sur une autre valeur, comme `remove`, une méthode de gestionnaire avec le nom `OnPostRemoveAsync` est sélectionnée. Le code suivant illustre le `OnPostDeleteAsync` Gestionnaire :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

La méthode `OnPostDeleteAsync` :

* Accepte l’`id` de la chaîne de requête. Si la directive de la page *index. cshtml* contenait la contrainte `"{id:int?}"` de routage, `id` provient des données d’itinéraire. Les données d’itinéraire pour `id` sont spécifiées dans l’URI, par exemple `https://localhost:5001/Customers/2` .
* Interroge la base de données pour le contact client avec `FindAsync`.
* Si le contact client est trouvé, il est supprimé de la liste des contacts client. La base de données est mise à jour.
* Appelle `RedirectToPage` pour rediriger vers la page Index racine (`/Index`).

## <a name="mark-page-properties-as-required"></a>Marquer les propriétés de page comme Required

Les propriétés d’un `PageModel` peuvent être marquées avec l’attribut [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) :

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Pour plus d’informations, consultez [Validation de modèle](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Gérer les requêtes HEAD avec un gestionnaire OnGet de secours

Les requêtes `HEAD` vous permettent de récupérer les en-têtes pour une ressource spécifique. Contrairement aux requêtes `GET`, les requêtes `HEAD` ne retournent pas un corps de réponse.

En règle générale, un gestionnaire `OnHead` est créé et appelé pour les requêtes `HEAD` : 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

Dans ASP.NET Core 2,1 ou version ultérieure, Razor les pages reviennent à appeler le `OnGet` Gestionnaire si aucun `OnHead` gestionnaire n’est défini. Ce comportement est activé par l’appel à [SetCompatibilityVersion](xref:mvc/compatibility-version) dans `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Les modèles par défaut génèrent l'appel `SetCompatibilityVersion` dans ASP.NET Core 2.1 et 2.2. `SetCompatibilityVersion` affecte efficacement Razor à l’option pages la `AllowMappingHeadRequestsToGetHandler` valeur `true` .

Au lieu d’adhérer à tous les comportements avec `SetCompatibilityVersion`, vous pouvez adhérer explicitement à des comportements *spécifiques*. Le code suivant adhère à l’autorisation de mappage des requêtes `HEAD` au gestionnaire `OnGet` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF et Razor pages

Vous n’avez aucun code à écrire pour la [validation anti-contrefaçon](xref:security/anti-request-forgery). La génération et la validation des jetons anti-contrefaçon sont automatiquement incluses dans les Razor pages.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Utilisation de dispositions, de partiels, de modèles et d’aide pour les balises avec des Razor pages

Les pages fonctionnent avec toutes les fonctionnalités du Razor moteur d’affichage. Les mises en page, les partiels, les modèles, les tag helpers, *_ViewStart. cshtml* , *_ViewImports. cshtml* fonctionnent de la même façon que pour les Razor vues conventionnelles.

Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.

Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/Shared/_Layout.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

La [disposition](xref:mvc/views/layout):

* Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).
* Importe des structures HTML telles que JavaScript et les feuilles de style.

Pour plus d’informations, consultez [Page de disposition](xref:mvc/views/layout).

La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

La disposition est dans le dossier *Pages/Shared*. Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active. Une mise en page dans le dossier *pages/Shared* peut être utilisée à partir de n’importe quelle Razor page sous le dossier *pages* .

Le fichier de disposition doit être placé dans le dossier *Pages/Shared*.

Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*. *Views/Shared* est un modèle de vues MVC. Razor Les pages sont conçues pour s’appuyer sur la hiérarchie des dossiers, et non sur les conventions de chemin.

L’affichage de la recherche à partir d’une Razor page comprend le dossier *pages* . Les mises en page, les modèles et les partiels que vous utilisez avec les contrôleurs MVC et les Razor vues conventionnelles *fonctionnent simplement*.

Ajoutez un fichier *Pages/_ViewImports.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` est expliqué plus loin dans le didacticiel. La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](xref:mvc/views/tag-helpers/builtin-th/Index) dans toutes les pages du dossier *Pages*.

<a name="namespace"></a>

Quand la directive `@namespace` est utilisée explicitement sur une page :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La directive définit l’espace de noms pour la page. La directive `@model` n’a pas besoin d’inclure l’espace de noms.

Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml* , l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`. Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.

Par exemple, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

L’espace de noms généré pour la page *pages/Customers/Edit. cshtml* Razor est le même que la `PageModel` classe.

`@namespace`*fonctionne également avec les Razor vues conventionnelles.*

Le fichier de vue *Pages/Create.cshtml* d’origine :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Le fichier vue *Pages/Create.cshtml* mis à jour :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

Le [ Razor projet de démarrage pages](#rpvs17) contient les *pages/_ValidationScriptsPartial. cshtml* , qui raccorde la validation côté client.

Pour plus d'informations sur les affichages partiels, consultez <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Génération d’URL pour les pages

La page `Create`, illustrée précédemment, utilise `RedirectToPage` :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

L’application a la structure de fichiers/dossiers suivante :

* */Pages*

  * *Index.cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Une fois l’opération réussie, les pages *Pages/Customers/Create.cshtml* et *Pages/Customers/Edit.cshtml* redirigent vers *Pages/Index.cshtml*. La chaîne `/Index` fait partie de l’URI pour accéder à la page précédente. La chaîne `/Index` peut être utilisée pour générer l’URI de la page *Pages/Index.cshtml*. Par exemple :

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Le nom de la page est le chemin de la page à partir du dossier racine */Pages* avec un `/` devant (par exemple, `/Index`). Les exemples de génération d’URL précédents offrent des options améliorées et des capacités fonctionnelles sur le codage en dur d’une URL. La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.

La génération d’URL pour les pages prend en charge les noms relatifs. Le tableau suivant montre la page Index sélectionnée avec différents paramètres `RedirectToPage` à partir de *Pages/Customers/Create.cshtml* :

| RedirectToPage(x)| Page |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` et `RedirectToPage("../Index")`  sont des *noms relatifs*. Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

La liaison de nom relatif est utile lors de la création de sites avec une structure complexe. Si vous utilisez des noms relatifs pour établir une liaison entre les pages d’un dossier, vous pouvez renommer ce dossier. Tous les liens fonctionneront encore (car ils n’incluent pas le nom du dossier).

Pour rediriger vers une page située dans une autre [Zone](xref:mvc/controllers/areas), spécifiez la zone :

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Pour plus d'informations, consultez <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Attribut ViewData

Les données peuvent être passées à une page avec [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Les propriétés sur les contrôleurs ou Razor les modèles de page avec l' `[ViewData]` attribut ont leurs valeurs stockées et chargées à partir du [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

Dans l’exemple suivant, le `AboutModel` contient une `Title` propriété marquée avec `[ViewData]` . La propriété `Title` a pour valeur le titre de la page À propos de :

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Dans la page À propos de, accédez à la propriété `Title` en tant que propriété de modèle :

```cshtml
<h1>@Model.Title</h1>
```

Dans la disposition, le titre est lu à partir du dictionnaire ViewData :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core expose la propriété [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) sur un [contrôleur](/dotnet/api/microsoft.aspnetcore.mvc.controller). Cette propriété stocke les données jusqu’à ce qu’elles soient lues. Vous pouvez utiliser les méthodes `Keep` et `Peek` pour examiner les données sans suppression. `TempData` est utile pour la redirection, quand des données sont nécessaires pour plusieurs requêtes.

Le code suivant définit la valeur de `Message` à l’aide de `TempData` :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Le modèle de page *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Pour plus d’informations, consultez [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Plusieurs gestionnaires par page

La page suivante génère un balisage pour deux gestionnaires en utilisant le Tag Helper `asp-page-handler` :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente. L’attribut `asp-page-handler` est un complément de `asp-page`. `asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page. `asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.

Le modèle de page :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Le code précédent utilise des *méthodes de gestionnaire nommées*. Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant). Dans l’exemple précédent, les méthodes de page sont OnPost **JoinList** Async et OnPost **JoinListUC** Async. Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Routes personnalisées

Utilisez la directive `@page` pour :

* Spécifier une route personnalisée vers une page. Par exemple, la route vers la page À propos peut être définie sur `/Some/Other/Path` avec `@page "/Some/Other/Path"`.
* Ajouter des segments à la route par défaut d’une page. Par exemple, un segment « item » peut être ajouté à la route par défaut d’une page avec `@page "item"`.
* Ajouter des paramètres à la route par défaut d’une page. Par exemple, un paramètre d’ID, `id`, peut être nécessaire pour une page avec `@page "{id}"`.

Un chemin relatif racine désigné par un tilde (`~`) au début du chemin est pris en charge. Par exemple, `@page "~/Some/Other/Path"` est identique à `@page "/Some/Other/Path"`.

Si vous n’aimez pas la chaîne `?handler=JoinList` de requête dans l’URL, modifiez l’itinéraire pour placer le nom du gestionnaire dans la partie chemin d’accès de l’URL. L’itinéraire peut être personnalisé en ajoutant un modèle de routage entre guillemets doubles après la `@page` directive.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH/JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.

## <a name="configuration-and-settings"></a>Configuration et paramètres

Pour configurer les options avancées, utilisez la méthode d’extension `AddRazorPagesOptions` sur le générateur MVC :

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Actuellement, vous pouvez utiliser `RazorPagesOptions` pour définir le répertoire racine pour les pages, ou ajouter des conventions de modèle d’application pour les pages. Nous permettrons à l’avenir une plus grande extensibilité en ce sens.

Pour précompiler des vues, consultez [ Razor afficher la compilation](xref:mvc/views/view-compilation) .

[Téléchargez ou affichez des exemples de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start), qui s’appuie sur cette introduction.

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Spécifier que Razor les pages se trouvent à la racine du contenu

Par défaut, Razor les pages sont enracinées dans le répertoire */pages* Ajoutez [avec Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) à [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) pour spécifier que vos Razor pages se trouvent à la [racine du contenu](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) de l’application :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Spécifier que Razor les pages se trouvent dans un répertoire racine personnalisé

Ajoutez [avec Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) à [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) pour spécifier que vos Razor pages se trouvent dans un répertoire racine personnalisé dans l’application (fournissez un chemin d’accès relatif) :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Autoriser l’attribut et les Razor pages](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
