---
title: 'Partie 5 : mettre à jour les pages générées dans une application ASP.NET Core'
author: rick-anderson
description: Partie 5 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 12/20/2018
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 7d25dae67c928fa659654ce4ab34cfdad08b5300
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060064"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>Partie 5 : mettre à jour les pages générées dans une application ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

L’application de gestion des films générée est un bon début, mais la présentation n’est pas idéale. **ReleaseDate** doit être **Release Date** (en deux mots).

![Application de gestion des films ouverte dans Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Mettre à jour le code généré

Ouvrez le fichier *Models/Movie.cs* , puis ajoutez les lignes affichées en surbrillance dans le code suivant :

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` permet à Entity Framework Core de mapper correctement `Price` en devise dans la base de données. Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) est abordé dans le tutoriel suivant. L’attribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) spécifie les éléments à afficher pour le nom d’un champ (dans le cas présent, « Release Date » au lieu de « ReleaseDate »). L’attribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) spécifie le type des données (Date). Les informations d’heures stockées dans le champ ne s’affichent donc pas.

Accédez à Pages/Movies, puis placez le curseur sur un lien **Modifier** pour afficher l’URL cible.

![Fenêtre de navigateur avec la souris sur le lien Edit et l’URL de lien http://localhost:1234/Movies/Edit/5 affichée](~/tutorials/razor-pages/da1/edit7.png)

Les liens **Edit** , **Details** , et **Delete** sont générés par le [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le fichier *Pages/Movies/Index.cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers. Dans le code précédent, le `AnchorTagHelper` génère dynamiquement la `href` valeur de l’attribut HTML à partir de la Razor page (l’itinéraire est relatif), le `asp-page` et l’ID de l’itinéraire ( `asp-route-id` ). Pour plus d’informations, consultez [Génération d’URL pour les pages](xref:razor-pages/index#url-generation-for-pages).

Utilisez **Afficher la Source** dans votre navigateur favori pour examiner le balisage généré. Une partie du code HTML généré est affichée ci-dessous :

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Les liens générés dynamiquement passent l’ID de film avec une chaîne de requête (par exemple `?id=1` dans `https://localhost:5001/Movies/Details?id=1`).

### <a name="add-route-template"></a>Ajouter un modèle de route

Mettez à jour les pages de modification, de détails et Razor de suppression pour utiliser le modèle de routage « {ID : int} ». Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`. Exécuter l’application, puis affichez le code source. Le code HTML généré ajoute l’ID à la partie de chemin de l’URL :

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Une requête à la page avec le modèle d’itinéraire « {id:int} » qui n’inclut **pas** l’entier retourne une erreur HTTP 404 (introuvable). Par exemple, `http://localhost:5000/Movies/Details` retourne une erreur 404. Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Pour tester le comportement de `@page "{id:int?}"` :

* Définissez la directive de page dans *Pages/Movies/Details.cshtml* sur `@page "{id:int?}"`.
* Définissez un point d’arrêt dans `public async Task<IActionResult> OnGetAsync(int? id)` (dans *Pages/Movies/Details.cshtml.cs* ).
* Accédez à `https://localhost:5001/Movies/Details/`.

Avec la directive `@page "{id:int}"`, le point d’arrêt n’est jamais atteint. Le moteur de routage retourne l’erreur HTTP 404. Avec `@page "{id:int?}"`, la méthode `OnGetAsync` retourne `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Passer en revue la gestion des exceptions d’accès concurrentiel

Passez en revue la méthode `OnPostAsync` dans le fichier *Pages/Movies/Edit.cshtml.cs*  :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Le code précédent détecte les exceptions d’accès concurrentiel quand un client supprime le film et que l’autre client envoie les modifications apportées au film.

Pour tester le bloc `catch` :

* Définissez un point d'arrêt sur `catch (DbUpdateConcurrencyException)`
* Sélectionnez **Edit** (Modifier) pour un film, apportez des modifications, mais ne cliquez pas sur **Save** (Enregistrer).
* Dans une autre fenêtre de navigateur, sélectionnez le lien **Delete** du même film, puis supprimez le film.
* Dans la fenêtre de navigateur précédente, postez les modifications apportées au film.

Dans le code destiné à la production, il est nécessaire de détecter les conflits d’accès concurrentiel. Pour plus d’informations, consultez [Gérer les conflits d’accès concurrentiel](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Validation de la publication et de la liaison

Examinez le fichier *Pages/Movies/Edit.cshtml.cs* : 

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Quand une requête HTTP GET est effectuée sur la page Movies/Edit (par exemple, `http://localhost:5000/Movies/Edit/2`) :

* La méthode `OnGetAsync` extrait le film de la base de données et retourne la méthode `Page`.
* La `Page` méthode restitue la page *pages/movies/Edit. cshtml* Razor . Le fichier *Pages/Movies/Edit.cshtml* contient la directive de modèle (`@model RazorPagesMovie.Pages.Movies.EditModel`), ce qui rend le modèle Movie disponible sur la page.
* Le formulaire d’édition s’affiche avec les valeurs relatives au film.

Quand la page Movies/Edit est postée :

* Les valeurs de formulaire affichées dans la page sont liées à la propriété `Movie`. L’attribut `[BindProperty]` active la [liaison de données](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* S’il y a des erreurs dans l’état du modèle (par exemple, si `ReleaseDate` ne peut pas être converti en date), le formulaire est à nouveau affiché avec les valeurs soumises.
* S’il n’y a aucune erreur de modèle, le film est enregistré.

Les méthodes HTTP d’extraction des pages index, Create et Delete Razor suivent un modèle similaire. La méthode HTTP après `OnPostAsync` dans la Razor page créer suit un modèle similaire à la `OnPostAsync` méthode dans la Razor page Modifier.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : utilisation d’une base de données](xref:tutorials/razor-pages/sql) 
>  [Suivant : ajouter une recherche](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

L’application de gestion des films générée est un bon début, mais la présentation n’est pas idéale. **ReleaseDate** doit être **Release Date** (en deux mots).

![Application de gestion des films ouverte dans Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Mettre à jour le code généré

Ouvrez le fichier *Models/Movie.cs* , puis ajoutez les lignes affichées en surbrillance dans le code suivant :

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` permet à Entity Framework Core de mapper correctement `Price` en devise dans la base de données. Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) est abordé dans le tutoriel suivant. L’attribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) spécifie les éléments à afficher pour le nom d’un champ (dans le cas présent, « Release Date » au lieu de « ReleaseDate »). L’attribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) spécifie le type des données (Date). Les informations d’heures stockées dans le champ ne s’affichent donc pas.

Accédez à Pages/Movies, puis placez le curseur sur un lien **Modifier** pour afficher l’URL cible.

![Fenêtre de navigateur avec la souris sur le lien Edit et l’URL de lien http://localhost:1234/Movies/Edit/5 affichée](~/tutorials/razor-pages/da1/edit7.png)

Les liens **Edit** , **Details** , et **Delete** sont générés par le [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le fichier *Pages/Movies/Index.cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers. Dans le code précédent, le `AnchorTagHelper` génère dynamiquement la `href` valeur de l’attribut HTML à partir de la Razor page (l’itinéraire est relatif), le `asp-page` et l’ID de l’itinéraire ( `asp-route-id` ). Pour plus d’informations, consultez [Génération d’URL pour les pages](xref:razor-pages/index#url-generation-for-pages).

Utilisez **Afficher la Source** dans votre navigateur favori pour examiner le balisage généré. Une partie du code HTML généré est affichée ci-dessous :

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Les liens générés dynamiquement passent l’ID de film avec une chaîne de requête (par exemple `?id=1` dans `https://localhost:5001/Movies/Details?id=1`).

Mettez à jour les pages de modification, de détails et Razor de suppression pour utiliser le modèle de routage « {ID : int} ». Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`. Exécuter l’application, puis affichez le code source. Le code HTML généré ajoute l’ID à la partie de chemin de l’URL :

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Une requête à la page avec le modèle d’itinéraire « {id:int} » qui n’inclut **pas** l’entier retourne une erreur HTTP 404 (introuvable). Par exemple, `http://localhost:5000/Movies/Details` retourne une erreur 404. Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Pour tester le comportement de `@page "{id:int?}"` :

* Définissez la directive de page dans *Pages/Movies/Details.cshtml* sur `@page "{id:int?}"`.
* Définissez un point d’arrêt dans `public async Task<IActionResult> OnGetAsync(int? id)` (dans *Pages/Movies/Details.cshtml.cs* ).
* Accédez à `https://localhost:5001/Movies/Details/`.

Avec la directive `@page "{id:int}"`, le point d’arrêt n’est jamais atteint. Le moteur de routage retourne l’erreur HTTP 404. Avec `@page "{id:int?}"`, la méthode `OnGetAsync` retourne `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Passer en revue la gestion des exceptions d’accès concurrentiel

Passez en revue la méthode `OnPostAsync` dans le fichier *Pages/Movies/Edit.cshtml.cs*  :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Le code précédent détecte les exceptions d’accès concurrentiel quand un client supprime le film et que l’autre client envoie les modifications apportées au film.

Pour tester le bloc `catch` :

* Définissez un point d'arrêt sur `catch (DbUpdateConcurrencyException)`
* Sélectionnez **Edit** (Modifier) pour un film, apportez des modifications, mais ne cliquez pas sur **Save** (Enregistrer).
* Dans une autre fenêtre de navigateur, sélectionnez le lien **Delete** du même film, puis supprimez le film.
* Dans la fenêtre de navigateur précédente, postez les modifications apportées au film.

Dans le code destiné à la production, il est nécessaire de détecter les conflits d’accès concurrentiel. Pour plus d’informations, consultez [Gérer les conflits d’accès concurrentiel](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Validation de la publication et de la liaison

Examinez le fichier *Pages/Movies/Edit.cshtml.cs* : 

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Quand une requête HTTP GET est effectuée sur la page Movies/Edit (par exemple, `http://localhost:5000/Movies/Edit/2`) :

* La méthode `OnGetAsync` extrait le film de la base de données et retourne la méthode `Page`. 
* La `Page` méthode restitue la page *pages/movies/Edit. cshtml* Razor . Le fichier *Pages/Movies/Edit.cshtml* contient la directive de modèle (`@model RazorPagesMovie.Pages.Movies.EditModel`), ce qui rend le modèle Movie disponible sur la page.
* Le formulaire d’édition s’affiche avec les valeurs relatives au film.

Quand la page Movies/Edit est postée :

* Les valeurs de formulaire affichées dans la page sont liées à la propriété `Movie`. L’attribut `[BindProperty]` active la [liaison de données](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* S’il existe des erreurs dans l’état du modèle (par exemple, si `ReleaseDate` ne peut pas être converti en date), le formulaire est affiché avec les valeurs soumises.
* S’il n’y a aucune erreur de modèle, le film est enregistré.

Les méthodes HTTP d’extraction des pages index, Create et Delete Razor suivent un modèle similaire. La méthode HTTP après `OnPostAsync` dans la Razor page créer suit un modèle similaire à la `OnPostAsync` méthode dans la Razor page Modifier.

La fonction de recherche est ajoutée dans le prochain didacticiel.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Précédent : utilisation d’une base de données](xref:tutorials/razor-pages/sql) 
>  [Suivant : ajouter une recherche](xref:tutorials/razor-pages/search)

::: moniker-end
