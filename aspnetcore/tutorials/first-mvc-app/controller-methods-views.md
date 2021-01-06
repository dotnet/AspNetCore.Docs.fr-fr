---
title: Partie 6, méthodes et vues de contrôleur dans ASP.NET Core
author: rick-anderson
description: 'Partie 6 : ajouter un modèle à une application ASP.NET Core MVC'
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: b4850821317b6907452793ef09194844c90c0137
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93050769"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a>Partie 6, méthodes et vues de contrôleur dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Nous avons une bonne ébauche de l’application de films, mais sa présentation n’est pas idéale, par exemple, **ReleaseDate** devrait être écrit en deux mots.

![Vue d’index : Release Date apparaît en un seul mot (sans espace) et chaque date de sortie d’un film a comme heure « 12 AM »](working-with-sql/_static/m55.png)

Ouvrez le fichier *Models/Movie.cs*, puis ajoutez les lignes en surbrillance ci-dessous :

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Nous abordons [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) dans le prochain tutoriel. L’attribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) spécifie les éléments à afficher pour le nom d’un champ (dans le cas présent, « Release Date » au lieu de « ReleaseDate »). L’attribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) spécifie le type des données (Date). Les informations d’heures stockées dans le champ ne s’affichent donc pas.

L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` est nécessaire pour qu’Entity Framework Core puisse correctement mapper `Price` en devise dans la base de données. Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).

Accédez au contrôleur `Movies` et maintenez le pointeur de la souris sur un lien **Edit** pour afficher l’URL cible.

![Fenêtre de navigateur avec la souris sur le lien Edit et l’URL de lien https://localhost:5001/Movies/Edit/5 affichée](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

Les liens **Edit**, **Details** et **Delete** sont générés par le Tag Helper Anchor Core MVC dans le fichier *Views/Movies/Index.cshtml*.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers. Dans le code ci-dessus, le `AnchorTagHelper` génère dynamiquement la `href` valeur de l’attribut HTML à partir de la méthode d’action du contrôleur et de l’ID de l’itinéraire. Vous utilisez **afficher la source** à partir de votre navigateur favori ou utiliser les outils de développement pour examiner le balisage généré. Une partie du code HTML généré est affichée ci-dessous :

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Rappelez-vous le format du [routage](xref:mvc/controllers/routing) défini dans le fichier *Startup.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core traduit `https://localhost:5001/Movies/Edit/4` en une requête à la méthode d’action `Edit` du contrôleur `Movies` avec un paramètre `Id` de 4. (Les méthodes de contrôleur sont également appelées méthodes d’action.)

Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont l’une des nouvelles fonctionnalités les plus populaires dans ASP.NET Core. Pour plus d'informations, consultez [Ressources supplémentaires](#additional-resources).

<a name="get-post"></a>

Ouvrez le contrôleur `Movies` et examinez les deux méthodes d’action `Edit`. Le code suivant montre la `HTTP GET Edit` méthode, qui extrait le film et remplit le formulaire de modification généré par le fichier *Edit. cshtml* Razor .

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Le code suivant montre la méthode `HTTP POST Edit`, qui traite les valeurs de film publiées :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Le code suivant montre la méthode `HTTP POST Edit`, qui traite les valeurs de film publiées :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

L’attribut `[Bind]` est l’un des moyens qui permettent d’assurer une protection contre la [sur-publication](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Vous devez inclure dans l’attribut `[Bind]` uniquement les propriétés que vous souhaitez modifier. Pour plus d’informations, consultez [Protéger votre contrôleur contre la sur-publication](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). Les [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) fournissent une alternative pour empêcher la sur-publication.

Notez que la deuxième méthode d’action `Edit` est précédée de l’attribut `[HttpPost]`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

L’attribut `HttpPost` indique que cette méthode `Edit` peut être appelée *uniquement* pour les requêtes `POST`. Vous pouvez appliquer l’attribut `[HttpGet]` à la première méthode Edit, mais cela n’est pas nécessaire car `[HttpGet]` est la valeur par défaut.

L’attribut `ValidateAntiForgeryToken` est utilisé pour [lutter contre la falsification de requête](xref:security/anti-request-forgery). Il est associé à un jeton anti-contrefaçon généré dans le fichier de la vue Edit (*Views/Movies/Edit.cshtml*). Le fichier de la vue Edit génère le jeton anti-contrefaçon avec le [Tag Helper Form](xref:mvc/views/working-with-forms).

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

Le [Tag Helper Form](xref:mvc/views/working-with-forms) génère un jeton anti-contrefaçon masqué qui doit correspondre au jeton anti-contrefaçon généré par `[ValidateAntiForgeryToken]` dans la méthode `Edit` du contrôleur Movies. Pour plus d’informations, consultez <xref:security/anti-request-forgery>.

La méthode `HttpGet Edit` prend le paramètre `ID` du film, recherche le film à l’aide de la méthode Entity Framework `FindAsync`, et retourne le film sélectionné à la vue Edit. Si un film est introuvable, l’erreur `NotFound` (HTTP 404) est retournée.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Quand le système de génération de modèles automatique a créé la vue Edit, il a examiné la classe `Movie` et a créé le code pour restituer les éléments `<label>` et `<input>` de chaque propriété de la classe. L’exemple suivant montre la vue Edit qui a été générée par le système de génération de modèles automatique de Visual Studio :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Notez que le modèle de vue comporte une instruction `@model MvcMovie.Models.Movie` en haut du fichier. `@model MvcMovie.Models.Movie` indique que la vue s’attend à ce que le modèle pour le modèle de vue soit de type `Movie`.

Le code de génération de modèles automatique utilise plusieurs méthodes Tag Helper afin de rationaliser le balisage HTML. Le [tag Helper étiquette](xref:mvc/views/working-with-forms) affiche le nom du champ (« title », « Released », « genre » ou « Price »). Le [Tag Helper Input](xref:mvc/views/working-with-forms) affiche l’élément `<input>` HTML. Le [Tag Helper Validation](xref:mvc/views/working-with-forms) affiche les messages de validation associés à cette propriété.

Exécutez l’application et accédez à l’URL `/Movies`. Cliquez sur un lien **modifier** . Dans le navigateur, affichez la source de la page. Le code HTML généré pour l’élément `<form>` est indiqué ci-dessous.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

Les éléments `<input>` sont dans un élément `HTML <form>` dont l’attribut `action` est défini de façon à publier à l’URL `/Movies/Edit/id`. Les données du formulaire sont publiées au serveur en cas de clic sur le bouton `Save`. La dernière ligne avant l’élément `</form>` de fermeture montre le jeton [XSRF](xref:security/anti-request-forgery) masqué généré par le [Tag Helper Form](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Traitement de la requête POST

Le code suivant montre la version `[HttpPost]` de la méthode d’action `Edit`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

L’attribut `[ValidateAntiForgeryToken]` valide le jeton [XSRF](xref:security/anti-request-forgery) masqué généré par le générateur de jetons anti-contrefaçon dans le [Tag Helper Form](xref:mvc/views/working-with-forms)

Le système de [liaison de modèle](xref:mvc/models/model-binding) prend les valeurs de formulaire publiées et crée un objet `Movie` qui est passé en tant que paramètre `movie`. La `ModelState.IsValid` propriété vérifie que les données envoyées dans le formulaire peuvent être utilisées pour modifier (modifier ou mettre à jour) un `Movie` objet. Si les données sont valides, elles sont enregistrées. Les données de film mises à jour (modifiées) sont enregistrées dans la base de données en appelant la méthode `SaveChangesAsync` du contexte de base de données. Après avoir enregistré les données, le code redirige l’utilisateur vers la méthode d’action `Index` de la classe `MoviesController`, qui affiche la collection de films, avec notamment les modifications qui viennent d’être apportées.

Avant que le formulaire soit publié sur le serveur, la validation côté client vérifie les règles de validation sur les champs. En cas d’erreur de validation, un message d’erreur s’affiche et le formulaire n’est pas publié. Si JavaScript est désactivé, aucune validation côté client n’est effectuée, mais le serveur détecte les valeurs publiées qui ne sont pas valides, et les valeurs de formulaire sont réaffichées avec des messages d’erreur. Plus loin dans ce didacticiel, nous examinerons la [Validation du modèle](xref:mvc/models/validation) plus en détail. Le [Tag Helper Validation](xref:mvc/views/working-with-forms) dans le modèle de vue *Views/Movies/Edit.cshtml* se charge de l’affichage des messages d’erreur appropriés.

![Vue Edit : une exception pour une valeur Price incorrecte « abc » indique que le champ Price doit être un nombre. Une exception pour une valeur Release Date incorrecte « xyz » indique que vous devez entrer une date valide.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Toutes les méthodes `HttpGet` du contrôleur Movies suivent un modèle similaire. Elles reçoivent un objet de film (ou une liste d’objets, dans le cas de `Index`) et passent l’objet (modèle) à la vue. La méthode `Create` passe un objet de film vide à la vue `Create`. Toutes les méthodes qui créent, modifient, suppriment ou changent d’une quelconque manière des données le font dans la surcharge `[HttpPost]` de la méthode. Modifier des données dans une méthode `HTTP GET` présente un risque pour la sécurité. La modification des données dans une méthode `HTTP GET` enfreint également les bonnes pratiques HTTP et le modèle architectural [REST](http://rest.elkstein.org/), qui spécifie que les requêtes GET ne doivent pas changer l’état de votre application. En d’autres termes, une opération GET doit être sûre, ne doit avoir aucun effet secondaire et ne doit pas modifier vos données persistantes.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Globalisation et localisation](xref:fundamentals/localization)
* [Introduction aux Tag Helpers](xref:mvc/views/tag-helpers/intro)
* [Créer des Tag Helpers](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* Protéger votre contrôleur contre la [sur-publication](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Tag Helper Form](xref:mvc/views/working-with-forms)
* [Tag Helper Input](xref:mvc/views/working-with-forms)
* [Tag Helper Label](xref:mvc/views/working-with-forms)
* [Tag Helper Select](xref:mvc/views/working-with-forms)
* [Tag Helper Validation](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Précédent](working-with-sql.md) 
>  [Suivant](search.md)  
