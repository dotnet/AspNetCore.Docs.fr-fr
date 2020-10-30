---
title: 'Didacticiel : implémenter la fonctionnalité CRUD-ASP.NET MVC avec EF Core'
description: Dans ce didacticiel, vous allez examiner et personnaliser le code CRUD (créer, lire, mettre à jour, supprimer) que la génération de modèles automatique MVC a créé automatiquement pour vous dans des contrôleurs et des vues.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
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
uid: data/ef-mvc/crud
ms.openlocfilehash: 043fe513f370cf63637733b66ca195e7887faab0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054292"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Didacticiel : implémenter la fonctionnalité CRUD-ASP.NET MVC avec EF Core

Dans le didacticiel précédent, vous avez créé une application MVC qui stocke et affiche les données en utilisant Entity Framework et SQL Server LocalDB. Dans ce didacticiel, vous allez examiner et personnaliser le code CRUD (créer, lire, mettre à jour, supprimer) que la génération de modèles automatique MVC a créé automatiquement pour vous dans des contrôleurs et des vues.

> [!NOTE]
> Il est courant d’implémenter le modèle de référentiel pour créer une couche d’abstraction entre votre contrôleur et la couche d’accès aux données. Pour conserver ces didacticiels simples et orientés vers l’apprentissage de l’utilisation d’Entity Framework proprement dit, ils n’utilisent pas de référentiels. Pour plus d’informations sur les référentiels avec EF, consultez [le dernier didacticiel de cette série](advanced.md).

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Personnaliser la page Details
> * Mettre à jour la page Create
> * Mettre à jour la page Edit
> * Mettre à jour la page Delete
> * Fermer les connexions de base de données

## <a name="prerequisites"></a>Prérequis

* [Bien démarrer avec EF Core et ASP.NET Core MVC](intro.md)

## <a name="customize-the-details-page"></a>Personnaliser la page Details

Le code du modèle généré automatiquement pour la page Index des étudiants exclut la propriété `Enrollments`, car elle contient une collection. Dans la page **Details** , vous affichez le contenu de la collection dans un tableau HTML.

Dans *Controllers/StudentsController.cs* , la méthode d’action pour la vue Details utilise la méthode `SingleOrDefaultAsync` pour récupérer une seule entité `Student`. Ajoutez du code qui appelle `Include`. Les méthodes `ThenInclude` et `AsNoTracking`, comme indiqué dans le code en surbrillance suivant.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

Les méthodes `Include` et `ThenInclude` font que le contexte charge la propriété de navigation `Student.Enrollments` et dans chaque inscription, la propriété de navigation `Enrollment.Course`.  Vous découvrirez plus d’informations sur ces méthodes dans le tutoriel sur [la lecture des données associées](read-related-data.md).

La méthode `AsNoTracking` améliore les performances dans les scénarios où les entités retournées ne sont pas mises à jour pendant la durée de vie du contexte actif. Vous pouvez découvrir plus d’informations sur `AsNoTracking` à la fin de ce didacticiel.

### <a name="route-data"></a>Données de routage

La valeur de clé qui est passée à la méthode `Details` provient des *données de route* . Les données de route sont des données que le classeur de modèles a trouvées dans un segment de l’URL. Par exemple, la route par défaut spécifie les segments contrôleur, action et ID :

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

Dans l’URL suivante, la route par défaut mappe Instructor en tant que contrôleur, Index en tant qu’action et 1 en tant qu’ID ; il s’agit des valeurs des données de route.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

La dernière partie de l’URL (« ?courseID=2021 ») est une valeur de chaîne de requête. Le classeur de modèles passe aussi la valeur d’ID au paramètre `id` de la méthode `Index` si vous le passez en tant que valeur de chaîne de requête :

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

Dans la page index, les URL de lien hypertexte sont créées par des instructions tag Helper dans la Razor vue. Dans le Razor code suivant, le `id` paramètre correspond à l’itinéraire par défaut. par conséquent, `id` est ajouté aux données d’itinéraire.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Ceci génère le code HTML suivant quand `item.ID` vaut 6 :

```html
<a href="/Students/Edit/6">Edit</a>
```

Dans le Razor code suivant, `studentID` ne correspond pas à un paramètre de l’itinéraire par défaut. il est donc ajouté en tant que chaîne de requête.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Ceci génère le code HTML suivant quand `item.ID` vaut 6 :

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Pour plus d’informations sur les Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.

### <a name="add-enrollments-to-the-details-view"></a>Ajouter des inscriptions à la vue Details

Ouvrez *Views/Students/Details.cshtml* . Chaque champ est affiché avec les helpers `DisplayNameFor` et `DisplayFor`, comme montré dans l’exemple suivant :

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Après le dernier champ et immédiatement avant la balise de fermeture `</dl>`, ajoutez le code suivant pour afficher une liste d’inscriptions :

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Si l’indentation du code est incorrecte une fois le code collé, appuyez sur Ctrl-K-D pour la corriger.

Ce code parcourt en boucle les entités dans la propriété de navigation `Enrollments`. Pour chaque inscription, il affiche le titre du cours et le niveau. Le titre du cours est récupéré à partir de l’entité de cours qui est stockée dans la propriété de navigation `Course` de l’entité Enrollments.

Exécutez l’application, sélectionnez l’onglet **Students** , puis cliquez sur le lien **Details** pour un étudiant. Vous voyez la liste des cours et des notes de l’étudiant sélectionné :

![Page Details pour les étudiants](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Mettre à jour la page Create

Dans *StudentsController.cs* , modifiez la méthode HttpPost `Create` en ajoutant un bloc try-catch et en supprimant l’ID de l’attribut `Bind`.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Ce code ajoute l’entité Student créée par le classeur de modèles ASP.NET Core MVC au jeu d’entités Students, puis enregistre les modifications dans la base de données. (Le classeur de modèles référence la fonctionnalité d’ASP.NET Core MVC qui facilite l’utilisation des données envoyées par un formulaire ; un classeur de modèles convertit les valeurs de formulaire envoyées en types CLR et les passe à la méthode d’action dans des paramètres. Dans ce cas, le classeur de modèles instancie une entité Student pour vous avec des valeurs de propriété provenant de la collection Form.)

Vous avez supprimé `ID` de l’attribut `Bind`, car ID est la valeur de clé primaire définie automatiquement par SQL Server lors de l’insertion de la ligne. L’entrée de l’utilisateur ne définit pas la valeur de l’ID.

À part l’attribut `Bind`, le bloc try-catch est la seule modification que vous avez apportée au code du modèle généré automatiquement. Si une exception qui dérive de `DbUpdateException` est interceptée lors de l’enregistrement des modifications, un message d’erreur générique est affiché. Les exceptions `DbUpdateException` sont parfois dues à quelque chose d’externe à l’application et non pas à une erreur de programmation : il est donc conseillé à l’utilisateur de réessayer. Bien que ceci ne soit pas implémenté dans cet exemple, une application destinée à la production doit consigner l’exception. Pour plus d’informations, consultez la section **Journal pour obtenir un aperçu** de [Surveillance et télémétrie (génération d’applications Cloud du monde réel avec Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).

L’attribut `ValidateAntiForgeryToken` aide à éviter les attaques par falsification de requête intersites (CSRF, Cross-Site Request Forgery). Le jeton est automatiquement injecté dans la vue par le [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) et est inclus quand le formulaire est envoyé par l’utilisateur. Le jeton est validé par l’attribut `ValidateAntiForgeryToken`. Pour plus d'informations, consultez <xref:security/anti-request-forgery>.

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Remarque sur la sécurité concernant la survalidation

L’attribut `Bind` inclus dans le code du modèle généré automatiquement sur la méthode `Create` est un moyen de protéger contre la survalidation dans les scénarios de création. Par exemple, supposons que l’entité Student comprend une propriété `Secret` et que vous ne voulez pas que cette page web définisse sa valeur.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

Même si vous n’avez pas de champ `Secret` dans la page web, un hacker pourrait utiliser un outil comme Fiddler, ou écrire du JavaScript, pour envoyer une valeur de formulaire pour `Secret`. Sans l’attribut `Bind` limitant les champs utilisés par le classeur de modèles quand il crée une instance de Student, le classeur de modèles choisit la valeur de formulaire pour `Secret` et l’utilise pour créer l’instance de l’entité Student. Ensuite, la valeur spécifiée par le hacker pour le champ de formulaire `Secret`, quelle qu’elle soit, est mise à jour dans la base de données. L’illustration suivante montre l’outil Fiddler en train d’ajouter le champ `Secret` (avec la valeur « OverPost ») aux valeurs du formulaire envoyé.

![Fiddler ajoutant un champ Secret](crud/_static/fiddler.png)

La valeur « OverPost » serait correctement ajoutée à la propriété `Secret` de la ligne insérée, même si vous n’aviez jamais prévu que la page web puisse définir cette propriété.

Vous pouvez empêcher la survalidation dans les scénarios de modification en lisant d’abord l’entité à partir de la base de données, puis en appelant `TryUpdateModel`, en passant une liste des propriétés explicitement autorisées. Il s’agit de la méthode utilisée dans ces didacticiels.

Une autre façon d’empêcher la survalidation qui est préférée par de nombreux développeurs consiste à utiliser les afficher des modèles de vues au lieu de classes d’entités avec la liaison de modèle. Incluez seulement les propriétés que vous voulez mettre à jour dans le modèle de vue. Une fois le classeur de modèles MVC a terminé, copiez les propriétés du modèle de vue vers l’instance de l’entité, en utilisant si vous le souhaitez un outil comme AutoMapper. Utilisez `_context.Entry` sur l’instance de l’entité pour définir son état sur `Unchanged`, puis définissez `Property("PropertyName").IsModified` sur true sur chaque propriété d’entité qui est incluse dans le modèle de vue. Cette méthode fonctionne à la fois dans les scénarios de modification et de création.

### <a name="test-the-create-page"></a>Tester la page Create

Le code dans *views/Students/Create. cshtml* utilise `label` , `input` , et `span` (pour les messages de validation) tag helpers pour chaque champ.

Exécutez l’application, sélectionnez l’onglet **Students** , puis cliquez sur **Create New** .

Entrez des noms et une date. Si votre navigateur vous le permet, essayez d’entrer une date non valide. (Certains navigateurs vous obligent à utiliser un sélecteur de dates.) Cliquez ensuite sur **créer** pour afficher le message d’erreur.

![Erreur de validation de date](crud/_static/date-error.png)

Il s’agit de la validation côté serveur que vous obtenez par défaut ; dans un didacticiel suivant, vous verrez comment ajouter des attributs qui génèrent du code également pour la validation côté client. Le code en surbrillance suivant montre la vérification de validation du modèle dans la méthode `Create`.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Changez la date en une valeur valide, puis cliquez sur **Create** pour voir apparaître le nouvel étudiant dans la page **Index** .

## <a name="update-the-edit-page"></a>Mettre à jour la page Edit

Dans *StudentController.cs* , la méthode HttpGet `Edit` (celle sans l’attribut `HttpPost`) utilise la méthode `SingleOrDefaultAsync` pour récupérer l’entité Student sélectionnée, comme vous l’avez vu dans la méthode `Details`. Vous n’avez pas besoin de modifier cette méthode.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Code HttpPost Edit recommandé : lire et mettre à jour

Remplacez la méthode d’action HttpPost Edit par le code suivant.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Ces modifications implémentent une bonne pratique de sécurité pour empêcher la survalidation. Le générateur de modèles automatique a généré un attribut `Bind` et a ajouté l’entité créée par le classeur de modèles au jeu d’entités avec un indicateur `Modified`. Ce code n’est pas recommandé dans de nombreux scénarios, car l’attribut `Bind` efface toutes les données préexistantes dans les champs non répertoriés dans le paramètre `Include`.

Le nouveau code lit l’entité existante et appelle `TryUpdateModel` pour mettre à jour les champs dans l’entité récupérée [en fonction de l’entrée d’utilisateur dans les données du formulaire envoyé](xref:mvc/models/model-binding). Le suivi automatique des modifications d’Entity Framework définit l’indicateur `Modified` sur les champs qui sont modifiés via une entrée dans le formulaire. Quand la méthode `SaveChanges` est appelée, Entity Framework crée des instructions SQL pour mettre à jour la ligne de la base de données. Les conflits d’accès concurrentiel sont ignorés, et seules les colonnes de table qui ont été mises à jour par l’utilisateur sont mises à jour dans la base de données. (Un didacticiel suivant montre comment gérer les conflits d’accès concurrentiel.)

Comme meilleure pratique pour empêcher la survalidation, les champs que vous souhaitez mettre à jour par la page de **modification** sont déclarés dans les `TryUpdateModel` paramètres. (La chaîne vide qui précède la liste de champs dans la liste de paramètres correspond à un préfixe à utiliser avec les noms de champs de formulaire.) Actuellement, il n’y a aucun champ supplémentaire que vous protégez, mais la liste des champs que vous souhaitez lier au classeur de modèles garantit que si vous ajoutez des champs au modèle de données à l’avenir, ils sont automatiquement protégés jusqu’à ce que vous les ajoutiez explicitement ici.

À la suite de ces modifications, la signature de méthode de la méthode HttpPost `Edit` est la même que celle de la méthode HttpGet `Edit` ; par conséquent, vous avez renommé la méthode `EditPost`.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Autre possibilité pour le code HttpPost Edit : Créer et attacher

Le code de HttpPost Edit recommandé garantit que seules les colonnes modifiées sont mises à jour et conserve les données dans les propriétés que vous ne voulez pas inclure pour la liaison de modèle. Cependant, l’approche « lecture en premier » nécessite une lecture supplémentaire de la base de données et peut aboutir à un code plus complexe pour la gestion des conflits d’accès concurrentiel. Une alternative consiste à attacher une entité créée par le classeur de modèles au contexte EF et à la marquer comme étant modifiée. (Ne mettez pas à jour votre projet avec ce code, il figure ici seulement pour illustrer une approche facultative.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Vous pouvez utiliser cette approche quand l’interface utilisateur de la page web inclut tous les champs de l’entité et peut les mettre à jour.

Le code du modèle généré automatiquement utilise l’approche « créer et attacher », mais il intercepte seulement les exceptions `DbUpdateConcurrencyException` et retourne des codes d’erreur 404.  L’exemple suivant intercepte toutes les exceptions de mise à jour de la base de données et affiche un message d’erreur.

### <a name="entity-states"></a>États des entités

Le contexte de base de données effectue le suivi de la synchronisation ou non des entités en mémoire avec leurs lignes correspondantes dans la base de données, et ces informations déterminent ce qui se passe quand vous appelez la méthode `SaveChanges`. Par exemple, quand vous passez une nouvelle entité à la méthode `Add`, l’état de cette entité est défini sur `Added`. Ensuite, quand vous appelez la méthode `SaveChanges`, le contexte de base de données émet une commande SQL INSERT.

L’état d’une entité peut être l’un des suivants :

* `Added`. L’entité n’existe pas encore dans la base de données. La méthode `SaveChanges` émet une instruction INSERT.

* `Unchanged`. La méthode `SaveChanges` ne doit rien faire avec cette entité. Quand vous lisez une entité dans la base de données, l’entité a d’abord cet état.

* `Modified`. Tout ou partie des valeurs de propriété de l’entité ont été modifiées. La méthode `SaveChanges` émet une instruction UPDATE.

* `Deleted`. L’entité a été marquée pour suppression. La méthode `SaveChanges` émet une instruction DELETE.

* `Detached`. L’entité n’est pas suivie par le contexte de base de données.

Dans une application de poste de travail, les changements d’état sont généralement définis automatiquement. Vous lisez une entité et vous apportez des modifications à certaines de ses valeurs de propriété. Son état passe alors automatiquement à `Modified`. Quand vous appelez `SaveChanges`, Entity Framework génère une instruction SQL UPDATE qui met à jour seulement les propriétés que vous avez modifiées.

Dans une application web, le `DbContext` qui lit initialement une entité et affiche ses données pour permettre leur modification est supprimé après le rendu d’une page. Quand la méthode d’action HttpPost `Edit` est appelée, une nouvelle requête web est effectuée et vous disposez d’une nouvelle instance de `DbContext`. Si vous relisez l’entité dans ce nouveau contexte, vous simulez le traitement du poste de travail.

Mais si vous ne voulez pas effectuer l’opération de lecture supplémentaire, vous devez utiliser l’objet entité créé par le classeur de modèles.  Le moyen le plus simple consiste à définir l’état de l’entité en Modified, comme cela est fait dans l’alternative pour le code HttpPost Edit illustrée précédemment. Ensuite, quand vous appelez `SaveChanges`, Entity Framework met à jour toutes les colonnes de la ligne de la base de données, car le contexte n’a aucun moyen de savoir quelles propriétés vous avez modifiées.

Si vous voulez éviter l’approche « lecture en premier », mais que vous voulez aussi que l’instruction SQL UPDATE mette à jour seulement les champs que l’utilisateur a réellement changés, le code est plus complexe. Vous devez enregistrer les valeurs d’origine d’une façon ou d’une autre (par exemple en utilisant des champs masqués) afin qu’ils soient disponibles quand la méthode HttpPost `Edit` est appelée. Vous pouvez ensuite créer une entité Student en utilisant les valeurs d’origine, appeler la méthode `Attach` avec cette version d’origine de l’entité, mettre à jour les valeurs de l’entité avec les nouvelles valeurs, puis appeler `SaveChanges`.

### <a name="test-the-edit-page"></a>Tester la page Edit

Exécutez l’application, sélectionnez l’onglet **Students** , puis cliquez sur un lien hypertexte **Edit** .

![Page de modification des étudiants](crud/_static/student-edit.png)

Changez quelques données et cliquez sur **Save** . La page **Index** s’ouvre et affiche les données modifiées.

## <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Dans *StudentController.cs* , le modèle de code pour la méthode HttpGet `Delete` utilise la méthode `SingleOrDefaultAsync` pour récupérer l’entité Student sélectionnée, comme vous l’avez vu dans les méthodes Details et Edit. Cependant, pour implémenter un message d’erreur personnalisé quand l’appel à `SaveChanges` échoue, vous devez ajouter des fonctionnalités à cette méthode et à sa vue correspondante.

Comme vous l’avez vu pour les opérations de mise à jour et de création, les opérations de suppression nécessitent deux méthodes d’action. La méthode qui est appelée en réponse à une demande GET affiche une vue qui permet à l’utilisateur d’approuver ou d’annuler l’opération de suppression. Si l’utilisateur l’approuve, une demande POST est créée. Quand cela se produit, la méthode HttpPost `Delete` est appelée, puis cette méthode effectue ensuite l’opération de suppression.

Vous allez ajouter un bloc try-catch à la méthode HttpPost `Delete` pour gérer les erreurs qui peuvent se produire quand la base de données est mise à jour. Si une erreur se produit, la méthode HttpPost Delete appelle la méthode HttpGet Delete, en lui passant un paramètre qui indique qu’une erreur s’est produite. La méthode HttpGet Delete réaffiche ensuite la page de confirmation, ainsi que le message d’erreur, donnant à l’utilisateur la possibilité d’annuler ou de recommencer.

Remplacez la méthode d’action HttpGet `Delete` par le code suivant, qui gère le signalement des erreurs.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Ce code accepte un paramètre facultatif qui indique si la méthode a été appelée après une erreur d’enregistrement des modifications. Ce paramètre a la valeur false quand la méthode HttpGet `Delete` est appelée sans une erreur antérieure. Quand elle est appelée par la méthode HttpPost `Delete` en réponse à une erreur de mise à jour de la base de données, le paramètre a la valeur true et un message d’erreur est passé à la vue.

### <a name="the-read-first-approach-to-httppost-delete"></a>L’approche « lecture en premier » pour HttpPost Delete

Remplacez la méthode d’action HttpPost `Delete` (nommée `DeleteConfirmed`) par le code suivant, qui effectue l’opération de suppression réelle et intercepte les erreurs de mise à jour de la base de données.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

Ce code récupère l’entité sélectionnée, puis appelle la méthode `Remove` pour définir l’état de l’entité sur `Deleted`. Lorsque `SaveChanges` est appelée, une commande SQL DELETE est générée.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>L’approche « créer et attacher » pour HttpPost Delete

Si l’amélioration des performances dans une application traitant des volumes importants est une priorité, vous pouvez éviter une requête SQL inutile en instanciant une entité de Student en utilisant seulement la valeur de la clé primaire, puis en définissant l’état de l’entité sur `Deleted`. C’est tout ce dont a besoin Entity Framework pour pouvoir supprimer l’entité. (Ne placez pas de ce code dans votre projet ; il figure ici seulement pour illustrer une solution alternative.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Si l’entité a des données connexes qui doivent également être supprimées, vérifiez que la suppression en cascade est configurée dans la base de données. Avec cette approche pour la suppression de l’entité, EF peut ne pas réaliser que des entités connexes doivent être supprimées.

### <a name="update-the-delete-view"></a>Mettre à jour la vue Delete

Dans *Views/Student/Delete.cshtml* , ajoutez un message d’erreur entre le titre h2 et le titre h3, comme indiqué dans l’exemple suivant :

[!code-cshtml[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Exécutez l’application, sélectionnez l’onglet **Students** , puis cliquez sur un lien hypertexte **Delete** :

![Page de confirmation de la suppression](crud/_static/student-delete.png)

Cliquez sur **Supprimer** . La page Index s’affiche sans l’étudiant supprimé. (Vous verrez un exemple du code de gestion des erreurs en action dans le didacticiel sur l’accès concurrentiel.)

## <a name="close-database-connections"></a>Fermer les connexions de base de données

Pour libérer les ressources détenues par une connexion de base de données, l’instance du contexte doit être supprimée dès que possible quand vous en avez terminé avec celle-ci. [L’injection de dépendances](../../fundamentals/dependency-injection.md) intégrée d’ASP.NET Core prend en charge cette tâche pour vous.

Dans *Startup.cs* , vous appelez la [méthode d’extension AddDbContext](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) pour provisionner la classe `DbContext` dans le conteneur d’injection de dépendances d’ASP.NET Core. Cette méthode définit par défaut la durée de vie du service sur `Scoped`. `Scoped` signifie que la durée de vie de l’objet de contexte coïncide avec la durée de vie de la demande web, et que la méthode `Dispose` sera appelée automatiquement à la fin de la requête web.

## <a name="handle-transactions"></a>Gérer les transactions

Par défaut, Entity Framework implémente implicitement les transactions. Dans les scénarios où vous apportez des modifications à plusieurs lignes ou plusieurs tables, puis que appelez `SaveChanges`, Entity Framework garantit automatiquement que soit toutes vos modifications réussissent soit elles échouent toutes. Si certaines modifications sont effectuées en premier puis qu’une erreur se produit, ces modifications sont automatiquement annulées. Pour les scénarios où vous avez besoin de plus de contrôle, par exemple si vous voulez inclure des opérations effectuées en dehors d’Entity Framework dans une transaction, consultez [Transactions](/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Pas de suivi des requêtes

Quand un contexte de base de données récupère des lignes de table et crée des objets entité qui les représentent, par défaut, il effectue le suivi du fait que les entités en mémoire sont ou non synchronisées avec ce qui se trouve dans la base de données. Les données en mémoire agissent comme un cache et sont utilisées quand vous mettez à jour une entité. Cette mise en cache est souvent inutile dans une application web, car les instances de contexte ont généralement une durée de vie courte (une instance est créée puis supprimée pour chaque requête) et le contexte qui lit une entité est généralement supprimé avant que cette entité soit réutilisée.

Vous pouvez désactiver le suivi des objets entité en mémoire en appelant la méthode `AsNoTracking`. Voici des scénarios classiques où vous voulez procéder ainsi :

* Pendant la durée de vie du contexte, vous n’avez besoin de mettre à jour aucune entité et il n’est pas nécessaire qu’EF [charge automatiquement les propriétés de navigation avec les entités récupérées par des requêtes distinctes](read-related-data.md). Ces conditions sont souvent rencontrées dans les méthodes d’action HttpGet d’un contrôleur.

* Vous exécutez une requête qui récupère un gros volume de données, et seule une petite partie des données retournées sont mises à jour. Il peut être plus efficace de désactiver le suivi pour la requête retournant un gros volume de données et d’exécuter une requête plus tard pour les quelques entités qui doivent être mises à jour.

* Vous voulez attacher une entité pour pouvoir la mettre à jour, mais vous avez auparavant récupéré la même entité à d’autre fins. Comme l’entité est déjà suivie par le contexte de base de données, vous ne pouvez pas attacher l’entité que vous voulez modifier. Une façon de gérer cette situation est d’appeler `AsNoTracking` sur la requête précédente.

Pour plus d’informations, consultez [suivi et non-suivi](/ef/core/querying/tracking).

## <a name="get-the-code"></a>Obtenir le code

[Télécharger ou afficher l’application complète.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Personnaliser la page Details
> * Mettre à jour la page Create
> * Mettre à jour la page Edit
> * Mettre à jour la page Delete
> * Connexions de base de données fermées

Passez au tutoriel suivant pour découvrir comment développer les fonctionnalités de la page **Index** en ajoutant le tri, le filtrage et la pagination.

> [!div class="nextstepaction"]
> [Suivant : tri, filtrage et pagination](sort-filter-page.md)
