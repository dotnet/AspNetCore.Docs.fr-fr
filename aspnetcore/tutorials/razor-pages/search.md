---
title: Partie 6, ajouter une recherche
author: rick-anderson
description: Partie 6 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 00c1be2704d92c7d4f868e6eaa346bd8e9901dbf
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360837"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>Partie 6, ajouter une recherche aux Razor Pages ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Dans les sections suivantes, la recherche de films par *genre* ou par *nom* est ajoutée.

Ajoutez l’instruction et les propriétés en surbrillance suivantes à *pages/movies/ Index . cshtml.cs* :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

Dans le code précédent :

* `SearchString`: Contient le texte que les utilisateurs entrent dans la zone de texte Rechercher. `SearchString` a l' [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribut. `[BindProperty]` lie les valeurs de formulaire et les chaînes de requête avec le même nom que la propriété. `[BindProperty(SupportsGet = true)]` est requis pour la liaison sur les requêtes HTTP d’extraction.
* `Genres`: Contient la liste des genres. `Genres` permet à l’utilisateur de sélectionner un genre dans la liste. `SelectList` nécessite `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: Contient le genre spécifique que l’utilisateur sélectionne. Par exemple, « Ouest ».
* `Genres` et `MovieGenre` sont utilisés plus loin dans ce tutoriel.

[!INCLUDE[](~/includes/bind-get.md)]

Mettez à jour la Index méthode de la page `OnGetAsync` avec le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

La première ligne de la méthode `OnGetAsync` crée une requête [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pour sélectionner les films :

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

La requête est *seulement* définie à ce stade, elle n’a **pas** été exécutée sur la base de données.

Si la propriété `SearchString` n’est pas nulle ou vide, la requête sur les films est modifiée de façon à filtrer sur la chaîne de recherche :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Le code `s => s.Title.Contains()` est une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Les expressions lambda sont utilisées dans les requêtes [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basées sur une méthode en tant qu’arguments pour les méthodes d’opérateur de requête standard, telles que la méthode [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` . Les requêtes LINQ ne sont pas exécutées quand elles sont définies ou quand elles sont modifiées en appelant une méthode, telle que `Where` , `Contains` ou `OrderBy` . Au lieu de cela, l’exécution de la requête est différée. L’évaluation d’une expression est retardée jusqu’à ce que sa valeur réalisée soit itérée ou que la `ToListAsync` méthode soit appelée. Pour plus d’informations, consultez [Exécution de requête](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

> [!NOTE]
> La méthode [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) est exécutée sur la base de données, et non pas dans le code C#. Le respect de la casse pour la requête dépend de la base de données et du classement. Sur SQL Server, `Contains` est mappée à [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), qui ne respecte pas la casse. Dans SQLite, avec le classement par défaut, elle respecte la casse.

Accédez à la page films et ajoutez une chaîne de requête telle que `?searchString=Ghost` à l’URL. Par exemple, `https://localhost:5001/Movies?searchString=Ghost`. Les films filtrés sont affichés.

![::: No-Loc (index) ::: View](search/_static/ghost.png)

Si le modèle de routage suivant est ajouté à la Index page, la chaîne de recherche peut être transmise en tant que segment d’URL. Par exemple, `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

La contrainte d’itinéraire précédente permet de rechercher le titre comme données d’itinéraire (un segment d’URL) et non comme valeur de chaîne de requête.  Le `?` dans `"{searchString?}"` signifie qu’il s’agit d’un paramètre d’itinéraire facultatif.

![::: No-Loc (index) ::: View avec le mot fantôme ajouté à l’URL et une liste de films retournée de deux films, Ghostbusters et Ghostbusters 2](search/_static/g2.png)

Le runtime ASP.NET Core utilise la [liaison de modèle](xref:mvc/models/model-binding) pour définir la valeur de la propriété `SearchString` à partir de la chaîne de requête (`?searchString=Ghost`) ou des données de la route (`https://localhost:5001/Movies/Ghost`). La liaison de modèle est *_sans_* respect de la casse.

Toutefois, les utilisateurs ne sont pas censés modifier l’URL pour rechercher un film. Dans cette étape, une interface utilisateur est ajoutée pour filtrer les films. Si vous avez ajouté la contrainte d’itinéraire `"{searchString?}"`, supprimez-la.

Ouvrez le fichier _Pages/movies/ Index . cshtml * et ajoutez le balisage mis en surbrillance dans le code suivant :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

La balise HTML `<form>` utilise les [Tag Helpers](xref:mvc/views/tag-helpers/intro) suivants :

* [Tag Helper Form](xref:mvc/views/working-with-forms#the-form-tag-helper). Lorsque le formulaire est envoyé, la chaîne de filtrage est envoyée à la page *pages/ Index movies/* page via la chaîne de requête.
* [Tag Helper Input](xref:mvc/views/working-with-forms#the-input-tag-helper)

Enregistrez les modifications apportées, puis testez le filtre.

![::: No-Loc (index) ::: View avec le mot fantôme tapé dans la zone de texte filtre de titre](search/_static/filter.png)

## <a name="search-by-genre"></a>Rechercher par genre

Mettez à jour la Index méthode de la page `OnGetAsync` avec le code suivant :

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Le code suivant est une requête LINQ qui récupère tous les genres dans la base de données.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

La liste `SelectList` de genres est créée en projetant des différents genres.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Ajouter la recherche par genre à la Razor page

1. Mettez à jour le *Index . cshtml* [ `<form>` élément] ( https://developer.mozilla.org/docs/Web/HTML/Element/form) mis en surbrillance dans le balisage suivant :

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. Testez l’application en effectuant une recherche par genre, par titre de film et selon ces deux critères.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : mettre à jour les pages](xref:tutorials/razor-pages/da1) 
>  [Suivant : ajouter un nouveau champ](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans les sections suivantes, la recherche de films par *genre* ou par *nom* est ajoutée.

Ajoutez les propriétés en surbrillance suivantes à *pages/movies/ Index . cshtml.cs* :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: Contient le texte que les utilisateurs entrent dans la zone de texte Rechercher. `SearchString` a l' [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribut. `[BindProperty]` lie les valeurs de formulaire et les chaînes de requête avec le même nom que la propriété. `[BindProperty(SupportsGet = true)]` est requis pour la liaison sur les requêtes HTTP d’extraction.
* `Genres`: Contient la liste des genres. `Genres` permet à l’utilisateur de sélectionner un genre dans la liste. `SelectList` nécessite `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: Contient le genre spécifique que l’utilisateur sélectionne. Par exemple, « Ouest ».
* `Genres` et `MovieGenre` sont utilisés plus loin dans ce tutoriel.

[!INCLUDE[](~/includes/bind-get.md)]

Mettez à jour la Index méthode de la page `OnGetAsync` avec le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

La première ligne de la méthode `OnGetAsync` crée une requête [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pour sélectionner les films :

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

La requête est *seulement* définie à ce stade, elle n’a **pas** été exécutée sur la base de données.

Si la propriété `SearchString` n’est pas nulle ou vide, la requête sur les films est modifiée de façon à filtrer sur la chaîne de recherche :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Le code `s => s.Title.Contains()` est une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Les expressions lambda sont utilisées dans les requêtes [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) basées sur une méthode en tant qu’arguments pour les méthodes d’opérateur de requête standard, telles que la méthode [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` . Les requêtes LINQ ne sont pas exécutées quand elles sont définies ou quand elles sont modifiées en appelant une méthode, telle que `Where` , `Contains`  ou `OrderBy` . Au lieu de cela, l’exécution de la requête est différée. L’évaluation d’une expression est retardée jusqu’à ce que sa valeur réalisée soit itérée ou que la `ToListAsync` méthode soit appelée. Pour plus d’informations, consultez [Exécution de requête](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

**Remarque :** La méthode [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) est exécutée sur la base de données, et non pas dans le code C#. Le respect de la casse pour la requête dépend de la base de données et du classement. Sur SQL Server, `Contains` est mappée à [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), qui ne respecte pas la casse. Dans SQLite, avec le classement par défaut, elle respecte la casse.

Accédez à la page films et ajoutez une chaîne de requête telle que `?searchString=Ghost` à l’URL. Par exemple, `https://localhost:5001/Movies?searchString=Ghost`. Les films filtrés sont affichés.

![::: No-Loc (index) ::: View](search/_static/ghost.png)

Si le modèle de routage suivant est ajouté à la Index page, la chaîne de recherche peut être transmise en tant que segment d’URL. Par exemple, `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

La contrainte d’itinéraire précédente permet de rechercher le titre comme données d’itinéraire (un segment d’URL) et non comme valeur de chaîne de requête.  Le `?` dans `"{searchString?}"` signifie qu’il s’agit d’un paramètre d’itinéraire facultatif.

![::: No-Loc (index) ::: View avec le mot fantôme ajouté à l’URL et une liste de films retournée de deux films, Ghostbusters et Ghostbusters 2](search/_static/g2.png)

Le runtime ASP.NET Core utilise la [liaison de modèle](xref:mvc/models/model-binding) pour définir la valeur de la propriété `SearchString` à partir de la chaîne de requête (`?searchString=Ghost`) ou des données de la route (`https://localhost:5001/Movies/Ghost`). La liaison de modèle est *_sans_* respect de la casse.

Toutefois, les utilisateurs ne sont pas censés modifier l’URL pour rechercher un film. Dans cette étape, une interface utilisateur est ajoutée pour filtrer les films. Si vous avez ajouté la contrainte d’itinéraire `"{searchString?}"`, supprimez-la.

Ouvrez le fichier _Pages/movies/ Index . cshtml * et ajoutez le `<form>` balisage mis en surbrillance dans le code suivant :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

La balise HTML `<form>` utilise les [Tag Helpers](xref:mvc/views/tag-helpers/intro) suivants :

* [Tag Helper Form](xref:mvc/views/working-with-forms#the-form-tag-helper). Lorsque le formulaire est envoyé, la chaîne de filtrage est envoyée à la page *pages/ Index movies/* page via la chaîne de requête.
* [Tag Helper Input](xref:mvc/views/working-with-forms#the-input-tag-helper)

Enregistrez les modifications apportées, puis testez le filtre.

![::: No-Loc (index) ::: View avec le mot fantôme tapé dans la zone de texte filtre de titre](search/_static/filter.png)

## <a name="search-by-genre"></a>Rechercher par genre

Mettez à jour la méthode `OnGetAsync` avec le code suivant :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Le code suivant est une requête LINQ qui récupère tous les genres dans la base de données.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

La liste `SelectList` de genres est créée en projetant des différents genres.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Ajouter la recherche par genre à la Razor page

Mettez à jour le *Index . cshtml* [ `<form>` élément] ( https://developer.mozilla.org/docs/Web/HTML/Element/form) mis en surbrillance dans le balisage suivant :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Testez l’application en effectuant une recherche par genre, par titre de film et selon ces deux critères.
Le code précédent utilise le [tag Helper Select](xref:mvc/views/working-with-forms#the-select-tag-helper) et option tag Helper.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Précédent : mettre à jour les pages](xref:tutorials/razor-pages/da1) 
>  [Suivant : ajouter un nouveau champ](xref:tutorials/razor-pages/new-field)

::: moniker-end
