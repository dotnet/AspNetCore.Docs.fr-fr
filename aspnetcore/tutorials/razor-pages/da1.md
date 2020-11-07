---
title: 'Partie 5 : mettre à jour les pages générées'
author: rick-anderson
description: Partie 5 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 09/20/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 7146c1955a578502a63578de4f1abce932cb8b32
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360603"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>Partie 5 : mettre à jour les pages générées dans une application ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

L’application de gestion des films générée est un bon début, mais la présentation n’est pas idéale. La **publication** doit être de deux mots, **Date de publication**.

![Application de gestion des films ouverte dans Chrome](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a>Mettre à jour le code généré

Ouvrez le fichier *Models/Movie.cs* , puis ajoutez les lignes affichées en surbrillance dans le code suivant :

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Dans le code précédent :

* L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` permet à Entity Framework Core de mapper correctement `Price` en devise dans la base de données. Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).
* L’attribut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) spécifie le nom complet d’un champ. Dans le code précédent, « date de publication » au lieu de « Released ».
* L’attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ). Les informations d’heure stockées dans le champ ne sont pas affichées.

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) est abordé dans le tutoriel suivant.

Accédez à *pages/films* et pointez sur un lien **modifier** pour afficher l’URL cible.

![Fenêtre de navigateur avec la souris sur le lien Edit et l’URL de lien https://localhost:1234/Movies/Edit/5 affichée](~/tutorials/razor-pages/da1/edit7.png)

Les liens **Edit** , **Details** et **Delete** Links sont générés par le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le fichier *pages/movies/ Index . cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.

Dans le code précédent, le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) génère dynamiquement la `href` valeur de l’attribut HTML à partir de la Razor page (l’itinéraire est relatif), et l’identificateur de l' `asp-page` itinéraire ( `asp-route-id` ). Pour plus d’informations, consultez [génération d’URL pour les pages](xref:razor-pages/index#url-generation-for-pages).

Utilisez **afficher la source** à partir d’un navigateur pour examiner le balisage généré. Une partie du code HTML généré est affichée ci-dessous :

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   Les liens générés dynamiquement passent l’ID de film avec une chaîne de requête. Par exemple, `?id=1` dans `https://localhost:5001/Movies/Details?id=1` .

### <a name="add-route-template"></a>Ajouter un modèle de route

Mettez à jour la modification, les détails et les Delete Razor pages pour utiliser le `{id:int}` modèle de routage. Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`. Exécuter l’application, puis affichez le code source.

Le code HTML généré ajoute l’ID à la partie de chemin de l’URL :

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Une requête à la page avec le `{id:int}` modèle de routage qui n’inclut **pas** l’entier retourne une erreur HTTP 404 (introuvable). Par exemple, `https://localhost:5001/Movies/Details` retourne une erreur 404. Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

```cshtml
@page "{id:int?}"
```

Tester le comportement de `@page "{id:int?}"` :

1. Définissez la directive de page dans *Pages/Movies/Details.cshtml* sur `@page "{id:int?}"`.
1. Définissez un point d’arrêt dans `public async Task<IActionResult> OnGetAsync(int? id)` , dans *pages/movies/details. cshtml. cs*.
1. Accédez à `https://localhost:5001/Movies/Details/`.

Avec la directive `@page "{id:int}"`, le point d’arrêt n’est jamais atteint. Le moteur de routage retourne l’erreur HTTP 404. À l’aide de `@page "{id:int?}"` , la `OnGetAsync` méthode retourne `NotFound` (http 404) :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>Passer en revue la gestion des exceptions d’accès concurrentiel

Passez en revue la méthode `OnPostAsync` dans le fichier *Pages/Movies/Edit.cshtml.cs*  :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Le code précédent détecte les exceptions d’accès concurrentiel lorsqu’un client supprime le film et que l’autre client publie les modifications apportées au film.

Pour tester le bloc `catch` :

1. Définissez un point d’arrêt sur `catch (DbUpdateConcurrencyException)` .
1. Sélectionnez **Edit** (Modifier) pour un film, apportez des modifications, mais ne cliquez pas sur **Save** (Enregistrer).
1. Dans une autre fenêtre de navigateur, sélectionnez le **Delete** lien du même film, puis supprimez le film.
1. Dans la fenêtre de navigateur précédente, postez les modifications apportées au film.

Dans le code destiné à la production, il est nécessaire de détecter les conflits d’accès concurrentiel. Pour plus d’informations, consultez [Gérer les conflits d’accès concurrentiel](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Validation de la publication et de la liaison

Examinez le fichier *Pages/Movies/Edit.cshtml.cs* : 

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Quand une requête HTTP est effectuée sur la page movies/Edit, par exemple `https://localhost:5001/Movies/Edit/3` :

* La méthode `OnGetAsync` extrait le film de la base de données et retourne la méthode `Page`.
* La `Page` méthode restitue la page *pages/movies/Edit. cshtml* Razor . Le fichier *pages/movies/Edit. cshtml* contient la directive Model `@model RazorPagesMovie.Pages.Movies.EditModel` , qui rend le modèle Movie disponible sur la page.
* Le formulaire d’édition s’affiche avec les valeurs relatives au film.

Quand la page Movies/Edit est postée :

* Les valeurs de formulaire affichées dans la page sont liées à la propriété `Movie`. L’attribut `[BindProperty]` active la [liaison de données](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* S’il existe des erreurs dans l’état du modèle, par exemple, `ReleaseDate` ne peut pas être convertie en date, le formulaire est à nouveau affiché avec les valeurs soumises.
* S’il n’y a aucune erreur de modèle, le film est enregistré.

Les méthodes HTTP d’extraction des Index Create pages, et Delete Razor suivent un modèle similaire. La méthode HTTP après `OnPostAsync` dans la Create Razor page suit un modèle similaire à la `OnPostAsync` méthode dans la page de modification Razor .

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : utiliser une base de données](xref:tutorials/razor-pages/sql) 
>  [Suivant : ajouter une recherche](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

L’application de gestion des films générée est un bon début, mais la présentation n’est pas idéale. La **publication** doit être de deux mots, **Date de publication**.

![Application de gestion des films ouverte dans Chrome](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a>Mettre à jour le code généré

Ouvrez le fichier *Models/Movie.cs* , puis ajoutez les lignes affichées en surbrillance dans le code suivant :

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` permet à Entity Framework Core de mapper correctement `Price` en devise dans la base de données. Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) est abordé dans le tutoriel suivant. L’attribut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) spécifie les éléments à afficher pour le nom d’un champ, dans le cas présent « date de publication » au lieu de « Released ». L’attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) spécifie le type des données ( `Date` ), de sorte que les informations d’heure stockées dans le champ ne sont pas affichées.

Accédez à Pages/Movies, puis placez le curseur sur un lien **Modifier** pour afficher l’URL cible.

![Fenêtre de navigateur avec la souris sur le lien Edit et l’URL de lien http://localhost:1234/Movies/Edit/5 affichée](~/tutorials/razor-pages/da1/edit7.png)

Les liens **Edit** , **Details** et **Delete** Links sont générés par le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le fichier *pages/movies/ Index . cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers. Dans le code précédent, le `AnchorTagHelper` génère dynamiquement la `href` valeur de l’attribut HTML à partir de la Razor page (l’itinéraire est relatif), le `asp-page` et l’ID de l’itinéraire ( `asp-route-id` ). Pour plus d’informations, consultez [Génération d’URL pour les pages](xref:razor-pages/index#url-generation-for-pages).

Utilisez **afficher la source** à partir d’un navigateur pour examiner le balisage généré. Une partie du code HTML généré est affichée ci-dessous :

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Les liens générés dynamiquement passent l’ID de film avec une chaîne de requête. Par exemple, `?id=1` dans  `https://localhost:5001/Movies/Details?id=1` .

Mettez à jour les pages Edition, détails et Delete Razor pages pour utiliser le modèle de routage « {ID : int} ». Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`. Exécuter l’application, puis affichez le code source. Le code HTML généré ajoute l’ID à la partie de chemin de l’URL :

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Une requête à la page avec le modèle d’itinéraire « {id:int} » qui n’inclut **pas** l’entier retourne une erreur HTTP 404 (introuvable). Par exemple, `https://localhost:5001/Movies/Details` retourne une erreur 404. Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Pour tester le comportement de `@page "{id:int?}"` :

* Définissez la directive de page dans *Pages/Movies/Details.cshtml* sur `@page "{id:int?}"`.
* Définissez un point d’arrêt dans `public async Task<IActionResult> OnGetAsync(int? id)` , dans *pages/movies/details. cshtml. cs*.
* Accédez à `https://localhost:5001/Movies/Details/`.

Avec la directive `@page "{id:int}"`, le point d’arrêt n’est jamais atteint. Le moteur de routage retourne l’erreur HTTP 404. À l’aide de `@page "{id:int?}"` , la `OnGetAsync` méthode retourne `NotFound` (http 404) :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>Passer en revue la gestion des exceptions d’accès concurrentiel

Passez en revue la méthode `OnPostAsync` dans le fichier *Pages/Movies/Edit.cshtml.cs*  :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Le code précédent détecte les exceptions d’accès concurrentiel quand un client supprime le film et que l’autre client envoie les modifications apportées au film.

Pour tester le bloc `catch` :

* Définissez un point d'arrêt sur `catch (DbUpdateConcurrencyException)`
* Sélectionnez **Edit** (Modifier) pour un film, apportez des modifications, mais ne cliquez pas sur **Save** (Enregistrer).
* Dans une autre fenêtre de navigateur, sélectionnez le **Delete** lien du même film, puis supprimez le film.
* Dans la fenêtre de navigateur précédente, postez les modifications apportées au film.

Dans le code destiné à la production, il est nécessaire de détecter les conflits d’accès concurrentiel. Pour plus d’informations, consultez [Gérer les conflits d’accès concurrentiel](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Validation de la publication et de la liaison

Examinez le fichier *Pages/Movies/Edit.cshtml.cs* : 

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Quand une requête HTTP est effectuée sur la page movies/Edit, par exemple `https://localhost:5001/Movies/Edit/3` :

* La méthode `OnGetAsync` extrait le film de la base de données et retourne la méthode `Page`. 
* La `Page` méthode restitue la page *pages/movies/Edit. cshtml* Razor . Le fichier *pages/movies/Edit. cshtml* contient la directive Model `@model RazorPagesMovie.Pages.Movies.EditModel` , qui rend le modèle Movie disponible sur la page.
* Le formulaire d’édition s’affiche avec les valeurs relatives au film.

Quand la page Movies/Edit est postée :

* Les valeurs de formulaire affichées dans la page sont liées à la propriété `Movie`. L’attribut `[BindProperty]` active la [liaison de données](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* S’il existe des erreurs dans l’état du modèle, par exemple, `ReleaseDate` ne peut pas être convertie en date, le formulaire s’affiche avec les valeurs soumises.
* S’il n’y a aucune erreur de modèle, le film est enregistré.

Les méthodes HTTP d’extraction des Index Create pages, et Delete Razor suivent un modèle similaire. La méthode HTTP après `OnPostAsync` dans la Create Razor page suit un modèle similaire à la `OnPostAsync` méthode dans la page de modification Razor .

La fonction de recherche est ajoutée dans le prochain didacticiel.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Précédent : utiliser une base de données](xref:tutorials/razor-pages/sql) 
>  [Suivant : ajouter une recherche](xref:tutorials/razor-pages/search)

::: moniker-end
