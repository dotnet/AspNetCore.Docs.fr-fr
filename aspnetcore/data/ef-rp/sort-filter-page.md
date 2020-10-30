---
title: 'Partie 3 : Razor pages avec EF Core dans ASP.net Core-Trier, filtrer, pagination'
author: rick-anderson
description: Partie 3 des Razor pages et Entity Framework de la série de didacticiels.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 51a1e2a90259898262ac655b7a0e8a55d766f0c7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061039"
---
# <a name="part-3-no-locrazor-pages-with-ef-core-in-aspnet-core---sort-filter-paging"></a>Partie 3 : Razor pages avec EF Core dans ASP.net Core-Trier, filtrer, pagination

Par [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Ce tutoriel ajoute des fonctionnalités de tri, de filtrage et de pagination aux pages des étudiants.

L’illustration suivante présente une page complète. Les en-têtes de colonne sont des liens hypertexte permettant de trier la colonne. Cliquez de façon répétée sur un en-tête de colonne pour changer l’ordre de tri (croissant ou décroissant).

![Page d’index des étudiants](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Ajouter la fonctionnalité de tri

Remplacez le code de *Pages/Students/Index.cshtml.cs* par le code suivant pour ajouter la fonctionnalité de tri.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All)]

Le code précédent :

* Nécessite l’ajout de `using System;` .
* Ajoute des propriétés devant contenir les paramètres de tri.
* Remplace le nom de la propriété `Student` par `Students`.
* Remplace le code de la méthode `OnGetAsync`.

La méthode `OnGetAsync` reçoit un paramètre `sortOrder` à partir de la chaîne de requête dans l’URL. L’URL et la chaîne de requête sont générées par le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).

Le `sortOrder` paramètre a la valeur `Name` ou `Date` . Le `sortOrder` paramètre est éventuellement suivi `_desc` de pour spécifier l’ordre décroissant. L’ordre de tri par défaut est croissant.

Quand la page Index est demandée à partir du lien **Students** , il n’existe aucune chaîne de requête. Les étudiants sont affichés par nom de famille dans l’ordre croissant. L’ordre croissant par nom de famille est le `default` dans l' `switch` instruction. Quand l’utilisateur clique sur un lien d’en-tête de colonne, la valeur `sortOrder` appropriée est fournie dans la valeur de chaîne de requête.

`NameSort` et `DateSort` sont utilisés par la Razor page pour configurer les liens hypertexte d’en-tête de colonne avec les valeurs de chaîne de requête appropriées :

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

Le code utilise l' [opérateur conditionnel C# ?:](/dotnet/csharp/language-reference/operators/conditional-operator). L' `?:` opérateur est un opérateur ternaire qui prend trois opérandes. La première ligne spécifie que lorsque `sortOrder` a la valeur null ou est vide, `NameSort` a la valeur `name_desc` . Si `sortOrder` est * *_not_* _ null ou vide, `NameSort` a pour valeur une chaîne vide.

Ces deux instructions permettent à la page de définir les liens hypertexte d’en-tête de colonne comme suit :

| Ordre de tri actuel   | Lien hypertexte Nom de famille | Lien hypertexte Date |
|:--------------------:|:-------------------:|:--------------:|
| Nom de famille croissant  | descending          | ascending      |
| Nom de famille décroissant | ascending           | ascending      |
| Date croissante       | ascending           | descending     |
| Date décroissante      | ascending           | ascending      |

La méthode utilise LINQ to Entities pour spécifier la colonne d’après laquelle effectuer le tri. Le code initialise un `IQueryable<Student>` avant l’instruction switch, et le modifie dans l’instruction switch :

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Lorsqu’une `IQueryable` est créée ou modifiée, aucune requête n’est envoyée à la base de données. La requête n’est pas exécutée tant que l’objet `IQueryable` n’a pas été converti en collection. Les `IQueryable` sont convertis en collection en appelant une méthode telle que `ToListAsync`. Ainsi, le code `IQueryable` génère une requête unique qui n’est pas exécutée avant l’instruction suivante :

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` peut contenir un grand nombre de colonnes triables. Pour connaître les autres méthodes permettant de coder cette fonctionnalité, consultez [Utiliser du code dynamique LINQ pour simplifier le code](xref:data/ef-mvc/advanced#dynamic-linq) dans la version MVC de cette série de tutoriels.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Ajouter des liens hypertexte d’en-tête de colonne à la page d’index des étudiants

Remplacez le code dans _Students/index.cshtml *, par le code suivant. Les modifications sont mises en surbrillance.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

Le code précédent :

* Ajoute des liens hypertexte aux en-têtes de colonne `LastName` et `EnrollmentDate`.
* Utilise les informations contenues dans `NameSort` et `DateSort` pour définir des liens hypertexte avec les valeurs d’ordre de tri actuelles.
* Remplace l’en-tête Index de la page par l’en-tête Students.
* Remplace `Model.Student` par `Model.Students`.

Pour vérifier que le tri fonctionne

* Exécutez l’application et sélectionnez l’onglet **Students** .
* Cliquez sur les en-têtes de colonne.

## <a name="add-filtering"></a>Ajouter la fonctionnalité de filtrage

Pour ajouter le filtrage à la page d’index des étudiants :

* Une zone de texte et un bouton Envoyer sont ajoutés à la Razor page. La zone de texte fournit une chaîne de recherche sur le prénom ou le nom de famille.
* Le modèle de page est mis à jour pour utiliser la valeur de zone de texte.

### <a name="update-the-ongetasync-method"></a>Mettre à jour la méthode OnGetAsync

Remplacez le code de *Students/Index.cshtml.cs* par le code suivant pour ajouter la fonctionnalité de filtrage :

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=17,22,26-30)]

Le code précédent :

* Ajoute le paramètre `searchString` à la méthode `OnGetAsync`, et enregistre la valeur de paramètre dans la propriété `CurrentFilter`. La valeur de chaîne de recherche est reçue à partir d’une zone de texte qui est ajoutée dans la section suivante.
* Ajoute une clause `Where` à l’instruction LINQ. La clause `Where` sélectionne uniquement les étudiants dont le prénom ou le nom de famille contient la chaîne de recherche. L’instruction LINQ est exécutée uniquement s’il y a une valeur à rechercher.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable et IEnumerable

Le code appelle la méthode <xref:System.Linq.Queryable.Where%2A> de l’objet `IQueryable`, et le filtre est traité sur le serveur. Dans certains scénarios, l’application peut appeler la méthode `Where` en tant que méthode d’extension sur une collection en mémoire. Par exemple, supposez que `_context.Students` passe de `DbSet` EF Core à une méthode de référentiel qui retourne une collection `IEnumerable`. Le résultat serait normalement le même, mais dans certains cas il peut être différent.

Par exemple, l’implémentation .NET Framework de `Contains` effectue par défaut une comparaison respectant la casse. Dans SQL Server, le respect de la casse de `Contains` est déterminé par le paramètre de classement de l’instance de SQL Server. Par défaut, SQL Server ne respecte pas la casse. Par défaut, SQLite est sensible à la casse. `ToUpper` peut être appelée pour que le test ne respecte pas la casse de manière explicite :

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

Le code précédent garantit que le filtre n’est pas sensible à la casse, même si la méthode `Where` est appelée sur un `IEnumerable` ou s’exécute sur SQLite.

Quand `Contains` est appelée sur une collection `IEnumerable`, l’implémentation .NET Core est utilisée. Quand `Contains` est appelée sur un objet `IQueryable`, l’implémentation de base de données est utilisée.

Pour des raisons de performances, il est généralement préférable d’appeler `Contains` sur un `IQueryable`. Avec `IQueryable`, le filtrage est effectué par le serveur de base de données. Si un `IEnumerable` est créé en premier, toutes les lignes doivent être retournées à partir du serveur de base de données.

Il existe un coût en matière de performances en cas d’appel à `ToUpper`. Le code `ToUpper` ajoute une fonction dans la clause WHERE de l’instruction TSQL SELECT. La fonction ajoutée empêche l’optimiseur d’utiliser un index. Étant donné que SQL est installé sans respect de la casse, il est préférable d’éviter l’appel à `ToUpper` quand il n’est pas nécessaire.

Pour plus d’informations, consultez [How to use case-insensitive query with Sqlite provider](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-no-locrazor-page"></a>Mettre à jour la Razor page

Remplacez le code dans *pages/Students/index. cshtml* pour ajouter un bouton de **recherche** .

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

Le code précédent utilise le  [Tag Helper](xref:mvc/views/tag-helpers/intro)`<form>` pour ajouter le bouton et la zone de texte de recherche. Par défaut, le Tag Helper `<form>` envoie les données de formulaire avec un POST. Avec POST, les paramètres sont passés dans le corps du message HTTP et non dans l’URL. Quand HTTP GET est utilisé, les données du formulaire sont transmises dans l’URL sous forme de chaînes de requête. La transmission des données avec des chaînes de requête permet aux utilisateurs d’ajouter l’URL aux favoris. Les [recommandations du W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) stipulent que GET doit être utilisé quand l’action ne produit pas de mise à jour.

Testez l’application :

* Sélectionnez l’onglet **Students** et entrez une chaîne de recherche. Si vous utilisez SQLite, le filtre n’est pas sensible à la casse seulement si vous avez implémenté le code `ToUpper` facultatif indiqué plus haut.

* Sélectionnez **Recherche** .

Notez que l’URL contient la chaîne de recherche. Exemple :

```browser-address-bar
https://localhost:5001/Students?SearchString=an
```

Si la page est dans les favoris, le favori contient l’URL de la page et la chaîne de requête `SearchString`. `method="get"` dans la balise `form` est ce qui a provoqué la génération de la chaîne de requête.

Actuellement, quand un lien de tri d’en-tête de colonne est sélectionné, la valeur du filtre de la zone **Search** est perdue. La valeur de filtre perdue est corrigée dans la section suivante.

## <a name="add-paging"></a>Ajouter la fonctionnalité de pagination

Dans cette section, nous allons créer une classe `PaginatedList` pour prendre en charge la pagination. La classe `PaginatedList` utilise des instructions `Skip` et `Take` pour filtrer les données sur le serveur au lieu de récupérer toutes les lignes de la table. L’illustration suivante montre les boutons de pagination.

![Page d’index des étudiants avec liens de changement de page](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Créer la classe PaginatedList

Dans le dossier du projet, créez `PaginatedList.cs` avec le code suivant :

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

La méthode `CreateAsync` dans le code précédent prend la taille de page et le numéro de page, et applique les instructions `Skip` et `Take` appropriées au `IQueryable`. Quand `ToListAsync` est appelée sur le `IQueryable`, elle retourne une liste contenant uniquement la page demandée. Les propriétés `HasPreviousPage` et `HasNextPage` sont utilisées pour activer ou désactiver les boutons de pagination **Previous** et **Next** .

La méthode `CreateAsync` est utilisée pour créer le `PaginatedList<T>`. Un constructeur ne peut pas créer l’objet `PaginatedList<T>`, car les constructeurs ne peuvent pas exécuter du code asynchrone.

### <a name="add-paging-to-the-pagemodel-class"></a>Ajouter la fonctionnalité de pagination à la classe PageModel

Remplacez le code de *Students/Index.cshtml.cs* pour ajouter la fonctionnalité de pagination.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=15-20,23-30,57-59)]

Le code précédent :

* Remplace le type `IList<Student>` de la propriété `Students` par le type `PaginatedList<Student>`.
* Ajoute l’index de page, le `sortOrder` actuel et le `currentFilter` à la signature de méthode `OnGetAsync`.
* Enregistre l’ordre de tri dans la `CurrentSort` propriété.
* Rétablit la valeur 1 pour l’index de la page lorsqu’il existe une nouvelle chaîne de recherche.
* Utilise la classe `PaginatedList` pour accéder aux entités d’étudiants.
* Définit `pageSize` sur 3. Une application réelle utilise la [configuration](xref:fundamentals/configuration/index) pour définir la valeur de taille de page.

Tous les paramètres reçus par `OnGetAsync` sont Null si :

* La page est appelée à partir du lien **Students** .
* L’utilisateur n’a pas cliqué sur un lien de pagination ou de tri.

Quand l’utilisateur clique sur un lien de pagination, la variable d’index de page contient le numéro de page à afficher.

La `CurrentSort` propriété fournit la Razor page avec l’ordre de tri actuel. L’ordre de tri actuel doit être inclus dans les liens de pagination afin de conserver l’ordre de tri lors de la pagination.

La `CurrentFilter` propriété fournit la Razor page avec la chaîne de filtre actuelle. La valeur `CurrentFilter` :

* Doit être incluse dans les liens de pagination afin de conserver les paramètres de filtre lors de la pagination.
* Doit être restaurée à la zone de texte quand la page est réaffichée.

Si la chaîne de recherche est modifiée pendant la pagination, la page est réinitialisée à 1. La page doit être réinitialisée à 1, car le nouveau filtre peut entraîner l’affichage de données différentes. Quand une valeur de recherche est entrée et que le bouton **Submit** est sélectionné :

  * La chaîne de recherche est changée.
  * Le paramètre `searchString` n’est pas null.

  La méthode `PaginatedList.CreateAsync` convertit la requête d’étudiant en une seule page d’étudiants dans un type de collection qui prend en charge la pagination. Cette seule page d’élèves est transmise à la Razor page.

  Les deux points d’interrogation situés après `pageIndex` dans l’appel `PaginatedList.CreateAsync` représentent l’[opérateur de fusion avec valeur Null](/dotnet/csharp/language-reference/operators/null-conditional-operator). L’opérateur de fusion de Null définit une valeur par défaut pour un type nullable. L’expression `pageIndex ?? 1` retourne la valeur de `pageIndex` si elle a une valeur ; sinon, elle retourne la valeur 1.

### <a name="add-paging-links-to-the-no-locrazor-page"></a>Ajouter des liens de pagination à la Razor page

Remplacez le code dans *students/index. cshtml* par le code suivant. Les modifications apparaissent en surbrillance :

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

Les liens d’en-tête de colonne utilisent la chaîne de requête pour passer la chaîne de recherche actuelle à la méthode `OnGetAsync` :

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Les boutons de changement de page sont affichés par des Tag Helpers :

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Exécutez l’application et accédez à la page des étudiants.

* Pour vérifier que la pagination fonctionne, cliquez sur les liens de pagination dans différents ordres de tri.
* Pour vérifier que la pagination fonctionne correctement avec le tri et le filtrage, entrez une chaîne de recherche et essayez de changer de page.

![Page d’index des étudiants avec liens de pagination](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Ajouter la fonctionnalité de regroupement

Cette section crée la page About (À propos) qui indique le nombre d’étudiants inscrits pour chaque date d’inscription. La mise à jour utilise le regroupement et comprend les étapes suivantes :

* Créez un modèle de vue pour les données utilisées par la page **about** .
* Mettre à jour la page About pour utiliser le modèle de vue.

### <a name="create-the-view-model"></a>Créer le modèle d’affichage

Créez un dossier *Models/SchoolViewModels* .

Créez *SchoolViewModels/EnrollmentDateGroup.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-no-locrazor-page"></a>Créer la Razor page

Créez un fichier *Pages/About.cshtml* avec le code suivant :

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Créer le modèle de page

Mettez à jour le fichier *Pages/About.cshtml.cs* avec le code suivant :

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

L’instruction LINQ regroupe les entités Student par date d’inscription, calcule le nombre d’entités dans chaque groupe et stocke les résultats dans une collection d’objets de modèle de vue `EnrollmentDateGroup`.

Exécutez l’application et accédez à la page About. Le nombre d’étudiants pour chaque date d’inscription s’affiche dans une table.

![Page About](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, l’application utilise des migrations pour mettre à jour le modèle de données.

> [!div class="step-by-step"]
> [Didacticiel précédent](xref:data/ef-rp/crud) 
>  [Didacticiel suivant](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans ce didacticiel, nous allons ajouter des fonctionnalités de tri, de filtrage, de regroupement et de pagination.

L’illustration suivante présente une page complète. Les en-têtes de colonne sont des liens hypertexte permettant de trier la colonne. Un clic sur un en-tête de colonne permet de changer l’ordre de tri (croissant ou décroissant).

![Page d’index des étudiants](sort-filter-page/_static/paging.png)

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez [l’application terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Ajouter le tri à la page Index

Ajoutez des chaînes au *students/index. cshtml. cs* `PageModel` pour contenir les paramètres de tri :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Mettez à jour le Student */index. cshtml. cs* `OnGetAsync` avec le code suivant :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Le code précédent reçoit un paramètre `sortOrder` à partir de la chaîne de requête dans l’URL. L’URL (y compris la chaîne de requête) est générée par le [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

Le paramètre `sortOrder` est « Name » ou « Date ». Le paramètre `sortOrder` peut être suivi de « _desc » pour spécifier l’ordre décroissant. L’ordre de tri par défaut est croissant.

Quand la page Index est demandée à partir du lien **Students** , il n’existe aucune chaîne de requête. Les étudiants sont affichés par nom de famille dans l’ordre croissant. Le tri croissant par nom de famille est la valeur par défaut dans l’instruction `switch`. Quand l’utilisateur clique sur un lien d’en-tête de colonne, la valeur `sortOrder` appropriée est fournie dans la valeur de chaîne de requête.

`NameSort` et `DateSort` sont utilisés par la Razor page pour configurer les liens hypertexte d’en-tête de colonne avec les valeurs de chaîne de requête appropriées :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Le code suivant contient [l’opérateur ?:](/dotnet/csharp/language-reference/operators/conditional-operator) conditionnel C# :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

La première ligne indique que quand `sortOrder` est null ou vide, `NameSort` prend la valeur « name_desc ». Si `sortOrder` n’est **pas** null ou vide, `NameSort` prend pour valeur une chaîne vide.

`?: operator` est également appelé opérateur ternaire.

Ces deux instructions permettent à la page de définir les liens hypertexte d’en-tête de colonne comme suit :

| Ordre de tri actuel | Lien hypertexte Nom de famille | Lien hypertexte Date |
|:--------------------:|:-------------------:|:--------------:|
| Nom de famille croissant | descending        | ascending      |
| Nom de famille décroissant | ascending           | ascending      |
| Date croissante       | ascending           | descending     |
| Date décroissante      | ascending           | ascending      |

La méthode utilise LINQ to Entities pour spécifier la colonne d’après laquelle effectuer le tri. Le code initialise un `IQueryable<Student>` avant l’instruction switch, et le modifie dans l’instruction switch :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Quand un `IQueryable` est créé ou modifié, aucune requête n’est envoyée à la base de données. La requête n’est pas exécutée tant que l’objet `IQueryable` n’a pas été converti en collection. Les `IQueryable` sont convertis en collection en appelant une méthode telle que `ToListAsync`. Ainsi, le code `IQueryable` génère une requête unique qui n’est pas exécutée avant l’instruction suivante :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` peut contenir un grand nombre de colonnes triables.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Ajouter des liens hypertexte d’en-tête de colonne à la page d’index des étudiants

Remplacez le code dans *Students/Index.cshtml* par le code en surbrillance suivant :

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Le code précédent :

* Ajoute des liens hypertexte aux en-têtes de colonne `LastName` et `EnrollmentDate`.
* Utilise les informations contenues dans `NameSort` et `DateSort` pour définir des liens hypertexte avec les valeurs d’ordre de tri actuelles.

Pour vérifier que le tri fonctionne

* Exécutez l’application et sélectionnez l’onglet **Students** .
* Cliquez sur **Last Name** .
* Cliquez sur **Enrollment Date** .

Pour mieux comprendre le fonctionnement du code

* Dans *Students/Index.cshtml.cs* , définissez un point d’arrêt sur `switch (sortOrder)`.
* Ajoutez un espion pour `NameSort` et `DateSort`.
* Dans *Students/Index.cshtml* , définissez un point d’arrêt sur `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Effectuez un pas à pas détaillé dans le débogueur.

## <a name="add-a-search-box-to-the-students-index-page"></a>Ajouter une zone de recherche à la page d’index des étudiants

Pour ajouter le filtrage à la page d’index des étudiants :

* Une zone de texte et un bouton Envoyer sont ajoutés à la Razor page. La zone de texte fournit une chaîne de recherche sur le prénom ou le nom de famille.
* Le modèle de page est mis à jour pour utiliser la valeur de zone de texte.

### <a name="add-filtering-functionality-to-the-index-method"></a>Ajouter la fonctionnalité de filtrage à la méthode Index

Mettez à jour le Student */index. cshtml. cs* `OnGetAsync` avec le code suivant :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Le code précédent :

* Ajoute le paramètre `searchString` à la méthode `OnGetAsync`. La valeur de chaîne de recherche est reçue à partir d’une zone de texte qui est ajoutée dans la section suivante.
* A ajouté une clause `Where` à l’instruction LINQ. La clause `Where` sélectionne uniquement les étudiants dont le prénom ou le nom de famille contient la chaîne de recherche. L’instruction LINQ est exécutée uniquement s’il y a une valeur à rechercher.

Remarque : Le code précédent appelle la méthode `Where` sur un objet `IQueryable`, et le filtre est traité sur le serveur. Dans certains scénarios, l’application peut appeler la méthode `Where` en tant que méthode d’extension sur une collection en mémoire. Par exemple, supposez que `_context.Students` passe de `DbSet` EF Core à une méthode de référentiel qui retourne une collection `IEnumerable`. Le résultat serait normalement le même, mais dans certains cas il peut être différent.

Par exemple, l’implémentation .NET Framework de `Contains` effectue par défaut une comparaison respectant la casse. Dans SQL Server, le respect de la casse de `Contains` est déterminé par le paramètre de classement de l’instance de SQL Server. Par défaut, SQL Server ne respecte pas la casse. `ToUpper` peut être appelée pour que le test ne respecte pas la casse de manière explicite :

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Le code précédent garantit que les résultats ne respectent pas la casse si le code change et utilise `IEnumerable`. Quand `Contains` est appelée sur une collection `IEnumerable`, l’implémentation .NET Core est utilisée. Quand `Contains` est appelée sur un objet `IQueryable`, l’implémentation de base de données est utilisée. Retourner un `IEnumerable` à partir d’un référentiel peut entraîner une dégradation significative des performances :

1. Toutes les lignes sont retournées à partir du serveur de base de données.
1. Le filtre est appliqué à toutes les lignes retournées dans l’application.

Il existe un coût en matière de performances en cas d’appel à `ToUpper`. Le code `ToUpper` ajoute une fonction dans la clause WHERE de l’instruction TSQL SELECT. La fonction ajoutée empêche l’optimiseur d’utiliser un index. Étant donné que SQL est installé sans respect de la casse, il est préférable d’éviter l’appel à `ToUpper` quand il n’est pas nécessaire.

### <a name="add-a-search-box-to-the-student-index-page"></a>Ajouter une zone de recherche à la page d’index des étudiants

Dans *Pages/Students/Index.cshtml* , ajoutez le code en surbrillance suivant pour créer un bouton **Search** et le chrome assorti.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Le code précédent utilise le  [Tag Helper](xref:mvc/views/tag-helpers/intro)`<form>` pour ajouter le bouton et la zone de texte de recherche. Par défaut, le Tag Helper `<form>` envoie les données de formulaire avec un POST. Avec POST, les paramètres sont passés dans le corps du message HTTP et non dans l’URL. Quand HTTP GET est utilisé, les données du formulaire sont transmises dans l’URL sous forme de chaînes de requête. La transmission des données avec des chaînes de requête permet aux utilisateurs d’ajouter l’URL aux favoris. Les [recommandations du W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) stipulent que GET doit être utilisé quand l’action ne produit pas de mise à jour.

Testez l’application :

* Sélectionnez l’onglet **Students** et entrez une chaîne de recherche.
* Sélectionnez **Recherche** .

Notez que l’URL contient la chaîne de recherche.

```html
http://localhost:5000/Students?SearchString=an
```

Si la page est dans les favoris, le favori contient l’URL de la page et la chaîne de requête `SearchString`. `method="get"` dans la balise `form` est ce qui a provoqué la génération de la chaîne de requête.

Actuellement, quand un lien de tri d’en-tête de colonne est sélectionné, la valeur du filtre de la zone **Search** est perdue. La valeur de filtre perdue est corrigée dans la section suivante.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Ajouter la fonctionnalité de changement de page à la page d’index des étudiants

Dans cette section, nous allons créer une classe `PaginatedList` pour prendre en charge la pagination. La classe `PaginatedList` utilise des instructions `Skip` et `Take` pour filtrer les données sur le serveur au lieu de récupérer toutes les lignes de la table. L’illustration suivante montre les boutons de pagination.

![Page d’index des étudiants avec liens de changement de page](sort-filter-page/_static/paging.png)

Dans le dossier du projet, créez `PaginatedList.cs` avec le code suivant :

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

La méthode `CreateAsync` dans le code précédent prend la taille de page et le numéro de page, et applique les instructions `Skip` et `Take` appropriées au `IQueryable`. Quand `ToListAsync` est appelée sur le `IQueryable`, elle retourne une liste contenant uniquement la page demandée. Les propriétés `HasPreviousPage` et `HasNextPage` sont utilisées pour activer ou désactiver les boutons de pagination **Previous** et **Next** .

La méthode `CreateAsync` est utilisée pour créer le `PaginatedList<T>`. Un constructeur ne peut pas créer l’objet `PaginatedList<T>` ; les constructeurs ne peuvent pas exécuter du code asynchrone.

## <a name="add-paging-functionality-to-the-index-method"></a>Ajouter la fonctionnalité de pagination à la méthode Index

Dans *Students/Index.cshtml.cs* , mettez à jour le type de `Student` en remplaçant `IList<Student>` par `PaginatedList<Student>` :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Mettez à jour le Student */index. cshtml. cs* `OnGetAsync` avec le code suivant :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Le code précédent ajoute l’index de page, le `sortOrder` actuel et le `currentFilter` à la signature de méthode.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Tous les paramètres sont null quand :

* La page est appelée à partir du lien **Students** .
* L’utilisateur n’a pas cliqué sur un lien de pagination ou de tri.

Quand l’utilisateur clique sur un lien de pagination, la variable d’index de page contient le numéro de page à afficher.

`CurrentSort` fournit la Razor page avec l’ordre de tri actuel. L’ordre de tri actuel doit être inclus dans les liens de pagination afin de conserver l’ordre de tri lors de la pagination.

`CurrentFilter` fournit la Razor page avec la chaîne de filtre actuelle. La valeur `CurrentFilter` :

* Doit être incluse dans les liens de pagination afin de conserver les paramètres de filtre lors de la pagination.
* Doit être restaurée à la zone de texte quand la page est réaffichée.

Si la chaîne de recherche est modifiée pendant la pagination, la page est réinitialisée à 1. La page doit être réinitialisée à 1, car le nouveau filtre peut entraîner l’affichage de données différentes. Quand une valeur de recherche est entrée et que le bouton **Submit** est sélectionné :

* La chaîne de recherche est changée.
* Le paramètre `searchString` n’est pas null.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

La méthode `PaginatedList.CreateAsync` convertit la requête d’étudiant en une seule page d’étudiants dans un type de collection qui prend en charge la pagination. Cette seule page d’élèves est transmise à la Razor page.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Les deux points d’interrogation dans `PaginatedList.CreateAsync` représentent [l’opérateur de fusion de Null](/dotnet/csharp/language-reference/operators/null-conditional-operator). L’opérateur de fusion de Null définit une valeur par défaut pour un type nullable. L’expression `(pageIndex ?? 1)` signifie qu’il faut retourner la valeur de `pageIndex` s’il a une valeur. Si `pageIndex` n’a pas de valeur, il faut retourner 1.

## <a name="add-paging-links-to-the-student-no-locrazor-page"></a>Ajouter des liens de pagination à la page Student Razor

Mettez à jour le balisage dans *Students/Index.cshtml* . Les modifications apparaissent en surbrillance :

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Les liens d’en-tête de colonne utilisent la chaîne de requête pour passer la chaîne de recherche actuelle à la méthode `OnGetAsync` afin que l’utilisateur puisse trier dans les résultats du filtrage :

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Les boutons de changement de page sont affichés par des Tag Helpers :

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Exécutez l’application et accédez à la page des étudiants.

* Pour vérifier que la pagination fonctionne, cliquez sur les liens de pagination dans différents ordres de tri.
* Pour vérifier que la pagination fonctionne correctement avec le tri et le filtrage, entrez une chaîne de recherche et essayez de changer de page.

![Page d’index des étudiants avec liens de pagination](sort-filter-page/_static/paging.png)

Pour mieux comprendre le fonctionnement du code

* Dans *Students/Index.cshtml.cs* , définissez un point d’arrêt sur `switch (sortOrder)`.
* Ajoutez un espion pour `NameSort`, `DateSort`, `CurrentSort` et `Model.Student.PageIndex`.
* Dans *Students/Index.cshtml* , définissez un point d’arrêt sur `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Effectuez un pas à pas détaillé dans le débogueur.

## <a name="update-the-about-page-to-show-student-statistics"></a>Mettez à jour la page About pour afficher les statistiques sur les étudiants

Lors de cette étape, nous allons mettre à jour *Pages/About.cshtml* afin d’afficher le nombre d’étudiants qui se sont inscrits pour chaque date d’inscription. La mise à jour utilise le regroupement et comprend les étapes suivantes :

* Créer un modèle de vue pour les données utilisées par la page **About** .
* Mettre à jour la page About pour utiliser le modèle de vue.

### <a name="create-the-view-model"></a>Créer le modèle d’affichage

Créez un dossier *SchoolViewModels* dans le dossier *Models* .

Dans le dossier *SchoolViewModels* , ajoutez un *EnrollmentDateGroup.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Mettre à jour le modèle de page About

Les modèles web dans ASP.NET Core 2.2 n’incluent pas la page About. Si vous utilisez ASP.NET Core 2,2, créez la page about Razor .

Mettez à jour le fichier *Pages/About.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

L’instruction LINQ regroupe les entités Student par date d’inscription, calcule le nombre d’entités dans chaque groupe et stocke les résultats dans une collection d’objets de modèle de vue `EnrollmentDateGroup`.

### <a name="modify-the-about-no-locrazor-page"></a>Modifier la page à propos de Razor

Remplacez le code du fichier *Pages/About.cshtml* par le code suivant :

[!code-cshtml[](intro/samples/cu21/Pages/About.cshtml)]

Exécutez l’application et accédez à la page About. Le nombre d’étudiants pour chaque date d’inscription s’affiche dans une table.

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez [l’application terminée pour cette phase](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![Page About](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Débogage d’une source ASP.NET Core 2.x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Version YouTube de ce tutoriel](https://www.youtube.com/watch?v=MDs7PFpoMqI)

Dans le didacticiel suivant, l’application utilise des migrations pour mettre à jour le modèle de données.

> [!div class="step-by-step"]
> [Précédent](xref:data/ef-rp/crud) 
>  [Suivant](xref:data/ef-rp/migrations)

::: moniker-end

