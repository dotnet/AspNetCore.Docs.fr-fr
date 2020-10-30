---
title: Partie 7, ajouter une recherche à une application ASP.NET Core MVC
author: rick-anderson
description: Partie 7 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 657072803f59feb99de8b31ddb3a6433d832aa30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059622"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a>Partie 7, ajouter une recherche à une application ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Dans cette section, vous ajoutez une fonctionnalité de recherche à la méthode d’action `Index` qui vous permet de rechercher des films par *genre* ou par *nom* .

Mettez à jour la méthode `Index` trouvée dans *Controllers/MoviesController.cs* avec le code suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

La première ligne de la méthode d’action `Index` crée une requête [LINQ](/dotnet/standard/using-linq) pour sélectionner les films :

```csharp
var movies = from m in _context.Movie
             select m;
```

La requête est *seulement* définie à ce stade, elle n’a **pas** été exécutée sur la base de données.

Si le paramètre `searchString` contient une chaîne, la requête de films est modifiée de façon à filtrer sur la valeur de la chaîne de recherche :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

Le code `s => s.Title.Contains()` ci-dessus est une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Les expressions lambda sont utilisées dans les requêtes [LINQ](/dotnet/standard/using-linq) basées sur une méthode en tant qu’arguments pour les méthodes d’opérateur de requête standard, comme la méthode [Where](/dotnet/api/system.linq.enumerable.where) ou `Contains` (utilisée dans le code ci-dessus). Les requêtes LINQ ne sont pas exécutées quand elles sont définies ou quand elles sont modifiées en appelant une méthode, comme `Where`, `Contains` ou `OrderBy`. Au lieu de cela, l’exécution de la requête est différée.  Cela signifie que l’évaluation d’une expression est retardée jusqu’à ce que sa valeur réalisée fasse l’objet d’une itération réelle ou que la méthode `ToListAsync` soit appelée. Pour plus d’informations sur l’exécution différée des requêtes, consultez [Exécution de requête](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Remarque : La méthode [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) est exécutée sur la base de données, et non pas dans le code C# ci-dessus. Le respect de la casse pour la requête dépend de la base de données et du classement. Sur SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) est mappé à [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), qui ne respecte pas la casse. Dans SQLite, avec le classement par défaut, elle respecte la casse.

Accédez à `/Movies/Index`. Ajoutez une chaîne de requête comme `?searchString=Ghost` à l’URL. Les films filtrés sont affichés.

![Vue Index](~/tutorials/first-mvc-app/search/_static/ghost.png)

Si vous changez la signature de la méthode `Index` pour y inclure un paramètre nommé `id`, le paramètre`id` correspondra à l’espace réservé facultatif `{id}` pour les routes par défaut définies dans *Startup.cs* .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Remplacez le paramètre par `id` et toutes les occurrences de `searchString` par `id`.

La méthode `Index` précédente :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

La méthode `Index` mise à jour avec le paramètre `id` :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Vous pouvez maintenant passer le titre de la recherche en tant que données de routage (un segment de l’URL) et non pas en tant que valeur de chaîne de requête.

![Vue Index avec le mot « ghost » ajouté à l’URL et une liste de films retournée contenant deux films, Ghostbusters et Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Cependant, vous ne pouvez pas attendre des utilisateurs qu’ils modifient l’URL à chaque fois qu’ils veulent rechercher un film. Vous allez donc maintenant ajouter des éléments d’interface utilisateur pour les aider à filtrer les films. Si vous avez changé la signature de la méthode `Index` pour tester comment passer le paramètre `ID` lié à une route, rétablissez-la de façon à ce qu’elle prenne un paramètre nommé `searchString` :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Ouvrez le fichier *Views/Movies/Index.cshtml* et ajoutez le balisage `<form>` mis en surbrillance ci-dessous :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

La balise HTML `<form>` utilise le [Tag Helper de formulaire](xref:mvc/views/working-with-forms), de façon que quand vous envoyez le formulaire, la chaîne de filtrage soit envoyée à l’action `Index` du contrôleur de films. Enregistrez vos modifications puis testez le filtre.

![Vue Index avec le mot « ghost » tapé dans la zone de texte de filtre des titres](~/tutorials/first-mvc-app/search/_static/filter.png)

Contrairement à ce que vous pourriez penser, une surcharge de `[HttpPost]` dans la méthode `Index` n’est pas nécessaire. Vous n’en avez pas besoin, car la méthode ne change pas l’état de l’application, elle filtre seulement les données.

Vous pourriez ajouter la méthode `[HttpPost] Index` suivante.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

Le paramètre `notUsed` est utilisé pour créer une surcharge pour la méthode `Index`. Nous parlons de ceci plus loin dans le didacticiel.

Si vous ajoutez cette méthode, le demandeur de l’action correspondrait à la méthode `[HttpPost] Index` et la méthode `[HttpPost] Index` s’exécuterait comme indiqué dans l’image ci-dessous.

![Fenêtre de navigateur avec la réponse de l’application de « From HttpPost Index: » avec filtrage sur « ghost »](~/tutorials/first-mvc-app/search/_static/fo.png)

Cependant, même si vous ajoutez cette version `[HttpPost]` de la méthode `Index`, il existe une limitation dans la façon dont tout ceci a été implémenté. Imaginez que vous voulez insérer un signet pour une recherche spécifique, ou que vous voulez envoyer un lien à vos amis sur lequel ils peuvent cliquer pour afficher la même liste filtrée de films. Notez que l’URL de la requête HTTP POST est identique à l’URL de la requête GET (localhost:{PORT}/Movies/Index) : il n’existe aucune information de recherche dans l’URL. Les informations de la chaîne de recherche sont envoyées au serveur en tant que [valeur d’un champ de formulaire](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Vous pouvez vérifier ceci avec les outils de développement du navigateur ou avec l’excellent [outil Fiddler](https://www.telerik.com/fiddler). L’illustration ci-dessous montre les outils de développement du navigateur Chrome :

![Onglet Réseau des outils de développement dans Microsoft Edge montrant un corps de demande avec une valeur « ghost » pour searchString](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Vous pouvez voir le paramètre de recherche et le jeton [XSRF](xref:security/anti-request-forgery) dans le corps de la demande. Notez que, comme indiqué dans le didacticiel précédent, le [Tag Helper de formulaire](xref:mvc/views/working-with-forms) génère un jeton [XSRF](xref:security/anti-request-forgery) anti-contrefaçon. Nous ne modifions pas les données : nous n’avons donc pas besoin de valider le jeton dans la méthode du contrôleur.

Comme le paramètre de recherche se trouve dans le corps de la demande et pas dans l’URL, vous ne pouvez pas capturer ces informations de recherche pour les insérer dans un signet ou les partager avec d’autres personnes. Corrigez ce problème en indiquant que la requête doit être `HTTP GET` trouvée dans le fichier *Views/Movies/Index.cshtml* .

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Maintenant, quand vous soumettez une recherche, l’URL contient la chaîne de requête de la recherche. La recherche accède également à la méthode d’action `HttpGet Index`, même si vous avez une méthode `HttpPost Index`.

![Fenêtre de navigateur montrant la chaîne de recherche « ghost » dans l’URL et les films retournés, Ghostbusters et Ghostbusters 2, qui contiennent le mot « ghost »](~/tutorials/first-mvc-app/search/_static/search_get.png)

La mise en forme suivante montre la modification apportée à la balise `form` :

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Ajouter la recherche par genre

Ajoutez la classe `MovieGenreViewModel` suivante au dossier *Models* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Le modèle de vue movie-genre contiendra :

* Une liste de films.
* Une `SelectList` contenant la liste des genres. Cela permet à l’utilisateur de sélectionner un genre dans la liste.
* `MovieGenre`, qui contient le genre sélectionné.
* `SearchString`, qui contient le texte que les utilisateurs entrent dans la zone de texte de recherche.

Remplacez la méthode `Index` dans `MoviesController.cs` par le code suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Le code suivant est une requête `LINQ` qui récupère tous les genres de la base de données.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

La `SelectList` de genres est créée en projetant les genres distincts (nous ne voulons pas que notre liste de sélection ait des genres en doublon).

Quand l’utilisateur recherche l’élément, la valeur de recherche est conservée dans la zone de recherche.

## <a name="add-search-by-genre-to-the-index-view"></a>Ajouter la recherche par genre à la vue Index

Mettez à jour `Index.cshtml` trouvé dans *Views/Movies/* comme suit :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Examinez l’expression lambda utilisée dans le HTML Helper suivant :

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

Dans le code précédent, le Helper HTML `DisplayNameFor` inspecte la propriété `Title` référencée dans l’expression lambda pour déterminer le nom d’affichage. Comme l’expression lambda est inspectée et non pas évaluée, vous ne recevez pas de violation d’accès quand `model`, `model.Movies` ou `model.Movies[0]` sont `null` ou vides. Quand l’expression lambda est évaluée (par exemple `@Html.DisplayFor(modelItem => item.Title)`), les valeurs des propriétés du modèle sont évaluées.

Testez l’application en effectuant une recherche par genre, par titre de film et selon ces deux critères :

![Fenêtre du navigateur montrant les résultats de https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Précédent](controller-methods-views.md) 
>  [Suivant](new-field.md)
