---
title: 'Didacticiel : gérer la concurrence-ASP.NET MVC avec EF Core'
description: Ce didacticiel montre comment gérer les conflits quand plusieurs utilisateurs mettent à jour la même entité en même temps.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
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
uid: data/ef-mvc/concurrency
ms.openlocfilehash: cc93069899953d04d1df4e79a282c349a163b06e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586772"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Didacticiel : gérer la concurrence-ASP.NET MVC avec EF Core

Dans les didacticiels précédents, vous avez découvert comment mettre à jour des données. Ce didacticiel montre comment gérer les conflits quand plusieurs utilisateurs mettent à jour la même entité en même temps.

Vous allez créer des pages web qui utilisent l’entité Department et gérer les erreurs d’accès concurrentiel. Les illustrations suivantes montrent les pages Edit et Delete, notamment certains messages qui sont affichés si un conflit d’accès concurrentiel se produit.

![Page Edit pour les départements](concurrency/_static/edit-error.png)

![Page Delete pour les départements](concurrency/_static/delete-error.png)

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * En savoir plus sur les conflits d’accès concurrentiel
> * Ajouter une propriété de suivi
> * Créer un contrôleur Departments et des vues
> * Mettre à jour la vue Index
> * Mettre à jour les méthodes de modification
> * Mettre à jour la vue Edit
> * Tester les conflits d'accès concurrentiel
> * Mettre à jour la page Delete
> * Mettre à jour les vues Details et Create

## <a name="prerequisites"></a>Prérequis

* [Mettre à jour les données associées](update-related-data.md)

## <a name="concurrency-conflicts"></a>Conflits d’accès concurrentiel

Un conflit d’accès concurrentiel se produit quand un utilisateur affiche les données d’une entité pour la modifier, puis qu’un autre utilisateur met à jour les données de la même entité avant que les modifications du premier utilisateur soient écrites dans la base de données. Si vous n’activez pas la détection de ces conflits, la personne qui met à jour la base de données en dernier remplace les modifications de l’autre utilisateur. Dans de nombreuses applications, ce risque est acceptable : s’il n’y a que quelques utilisateurs ou quelques mises à jour, ou s’il n’est pas réellement critique que des modifications soient remplacées, le coût de la programmation nécessaire à la gestion des accès concurrentiels peut être supérieur au bénéfice qu’elle apporte. Dans ce cas, vous ne devez pas configurer l’application pour gérer les conflits d’accès concurrentiel.

### <a name="pessimistic-concurrency-locking"></a>Accès concurrentiel pessimiste (verrouillage)

Si votre application doit éviter la perte accidentelle de données dans des scénarios d’accès concurrentiel, une manière de le faire consiste à utiliser des verrous de base de données. Ceci est appelé « accès concurrentiel pessimiste ». Par exemple, avant de lire une ligne d’une base de données, vous demandez un verrou pour lecture seule ou pour accès avec mise à jour. Si vous verrouillez une ligne pour accès avec mise à jour, aucun autre utilisateur n’est autorisé à verrouiller la ligne pour lecture seule ou pour accès avec mise à jour, car ils obtiendraient ainsi une copie de données qui sont en cours de modification. Si vous verrouillez une ligne pour accès en lecture seule, d’autres utilisateurs peuvent également la verrouiller pour accès en lecture seule, mais pas pour accès avec mise à jour.

La gestion des verrous présente des inconvénients. Elle peut être complexe à programmer. Elle nécessite des ressources de gestion de base de données importantes, et peut provoquer des problèmes de performances au fil de l’augmentation du nombre d’utilisateurs d’une application. Pour ces raisons, certains systèmes de gestion de base de données ne prennent pas en charge l’accès concurrentiel pessimiste. Entity Framework Core n’en fournit pas de prise en charge intégrée et ce didacticiel ne vous montre comment l’implémenter.

### <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

La solution alternative à l’accès concurrentiel pessimiste est l’accès concurrentiel optimiste. L’accès concurrentiel optimiste signifie autoriser la survenance des conflits d’accès concurrentiel, puis de réagir correctement quand ils surviennent. Par exemple, Jane consulte la page Department Edit et change le montant de « Budget » pour le département « English » en le passant de $350 000,00 à $0,00.

![Modification de la valeur de budget sur 0](concurrency/_static/change-budget.png)

Avant que Jane clique sur **Save**, John consulte la même page et change le champ Start Date de 01/09/2007 en 01/09/2013.

![Modification de la date de début sur 2013](concurrency/_static/change-date.png)

Jane clique la première sur **Save** et voit sa modification quand le navigateur revient à la page Index.

![Le budget est passé à zéro](concurrency/_static/budget-zero.png)

John clique à son tour sur **Save** sur une page Edit qui affiche toujours un budget de $350 000,00. Ce qui se passe ensuite est déterminé par la façon dont vous gérez les conflits d’accès concurrentiel.

Voici quelques-unes des options :

* Vous pouvez effectuer le suivi des propriétés modifiées par un utilisateur et mettre à jour seulement les colonnes correspondantes dans la base de données.

     Dans l’exemple de scénario, aucune donnée ne serait perdue, car des propriétés différentes ont été mises à jour par chacun des deux utilisateurs. La prochaine fois que quelqu’un examine le département « English », il voit à la fois les modifications de Jane et de John : une date de début au 01/09/2013 et un budget de zéro dollars. Cette méthode de mise à jour peut réduire le nombre de conflits qui peuvent entraîner des pertes de données, mais elle ne peut pas éviter la perte de données si des modifications concurrentes sont apportées à la même propriété d’une entité. Un tel fonctionnement d’Entity Framework dépend de la façon dont vous implémentez votre code de mise à jour. Il n’est pas souvent pratique dans une application web, car il peut nécessiter la gestion de grandes quantités d’états pour effectuer le suivi de toutes les valeurs de propriété d’origine d’une entité, ainsi que des nouvelles valeurs. La conservation d’un grand nombre d’États peut affecter les performances de l’application, car elle nécessite des ressources serveur ou elle doit être incluse dans la page Web elle-même (par exemple, dans les champs masqués) ou dans un cookie .

* Vous pouvez laisser les modifications de John remplacer les modifications de Jane.

     La prochaine fois que quelqu’un consultera le département « English », il verra la date du 01/09/2013 et la valeur $350 000,00 restaurée. Ceci s’appelle un scénario *Priorité au client* ou *Priorité au dernier entré* (Last in Wins). (Toutes les valeurs du client sont prioritaires par rapport à ce qui se trouve dans le magasin de données.) Comme indiqué dans la présentation de cette section, si vous n’effectuez aucun codage pour la gestion de l’accès concurrentiel, cela se produit automatiquement.

* Vous pouvez empêcher les modifications de John de faire l’objet d’une mise à jour dans la base de données.

     En règle générale, vous affichez un message d’erreur, vous lui montrez l’état actuel des données et vous lui permettez de réappliquer ses modifications s’il veut toujours les faire. Il s’agit alors d’un scénario *Priorité au magasin*. (Les valeurs du magasin de données ont priorité sur les valeurs soumises par le client.) Vous allez implémenter le scénario de stockage WINS dans ce didacticiel. Cette méthode garantit qu’aucune modification n’est remplacée sans qu’un utilisateur soit averti de ce qui se passe.

### <a name="detecting-concurrency-conflicts"></a>Détection des conflits d’accès concurrentiel

Vous pouvez résoudre les conflits en gérant les exceptions `DbConcurrencyException` levées par Entity Framework. Pour savoir quand lever ces exceptions, Entity Framework doit être en mesure de détecter les conflits. Par conséquent, vous devez configurer de façon appropriée la base de données et le modèle de données. Voici quelques options pour l’activation de la détection des conflits :

* Dans la table de base de données, incluez une colonne de suivi qui peut être utilisée pour déterminer quand une ligne a été modifiée. Vous pouvez ensuite configurer Entity Framework pour inclure cette colonne dans la clause WHERE des commandes SQL UPDATE et DELETE.

     Le type de données de la colonne de suivi est généralement `rowversion`. La valeur de `rowversion` est un nombre séquentiel qui est incrémenté chaque fois que la ligne est mise à jour. Dans une commande UPDATE ou DELETE, la clause WHERE inclut la valeur d’origine de la colonne de suivi (la version d’origine de la ligne). Si la ligne à mettre à jour a été changée par un autre utilisateur, la valeur de la colonne `rowversion` est différente de la valeur d’origine : l’instruction UPDATE ou DELETE ne peut donc pas trouver la ligne à mettre à jour en raison de la clause WHERE. Quand Entity Framework trouve qu’aucune ligne n’a été mise à jour par la commande UPDATE ou DELETE (c’est-à-dire quand le nombre de lignes affectées est égal à zéro), il interprète ceci comme étant un conflit d’accès concurrentiel.

* Configurez Entity Framework de façon à inclure les valeurs d’origine de chaque colonne dans la table de la clause WHERE des commandes UPDATE et DELETE.

     Comme dans la première option, si quelque chose dans la ligne a changé depuis la première lecture de la ligne, la clause WHERE ne retourne pas de ligne à mettre à jour, ce qui est interprété par Entity Framework comme un conflit d’accès concurrentiel. Pour les tables de base de données qui ont beaucoup de colonnes, cette approche peut aboutir à des clauses WHERE de très grande taille et nécessiter la gestion de grandes quantités d’états. Comme indiqué précédemment, la gestion de grandes quantités d’états peut affecter les performances de l’application. Par conséquent, cette approche n’est généralement pas recommandée et n’est pas la méthode utilisée dans ce didacticiel.

     Si vous ne voulez pas implémenter cette approche de l’accès concurrentiel, vous devez marquer toutes les propriétés qui ne sont pas des clés primaires de l’entité dont vous voulez suivre les accès concurrentiels en leur ajoutant l’attribut `ConcurrencyCheck`. Cette modification permet à Entity Framework d’inclure toutes les colonnes dans la clause SQL WHERE des instructions UPDATE et DELETE.

Dans le reste de ce didacticiel, vous ajoutez une propriété de suivi `rowversion` à l’entité Department, vous créez un contrôleur et des vues, et vous testez pour vérifier que tout fonctionne correctement.

## <a name="add-a-tracking-property"></a>Ajouter une propriété de suivi

Dans *Models/Department.cs*, ajoutez une propriété de suivi nommée RowVersion :

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

L’attribut `Timestamp` spécifie que cette colonne sera incluse dans la clause WHERE des commandes UPDATE et DELETE envoyées à la base de données. L’attribut est nommé `Timestamp`, car les versions précédentes de SQL Server utilisaient un type de données SQL `timestamp` avant son remplacement par le type SQL `rowversion`. Le type .NET pour `rowversion` est un tableau d’octets.

Si vous préférez utiliser l’API actuelle, vous pouvez utiliser la méthode `IsConcurrencyToken` (dans *Data/SchoolContext.cs*) pour spécifier la propriété de suivi, comme indiqué dans l’exemple suivant :

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

En ajoutant une propriété, vous avez changé le modèle de base de données et vous devez donc effectuer une autre migration.

Enregistrez vos modifications et générez le projet, puis entrez les commandes suivantes dans la fenêtre Commande :

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Créer un contrôleur Departments et des vues

Générez automatiquement un modèle de contrôleur Departments et des vues, comme vous l’avez fait précédemment pour les étudiants, les cours et les enseignants.

![Générer automatiquement un modèle Department](concurrency/_static/add-departments-controller.png)

Dans le fichier *DepartmentsController.cs*, changez les quatre occurrences de « FirstMidName » en « FullName », de façon que les listes déroulantes de l’administrateur du département contiennent le nom complet de l’enseignant et non pas simplement son nom de famille.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Mettre à jour la vue Index

Le moteur de génération de modèles automatique a créé une colonne RowVersion pour la vue Index, mais ce champ ne doit pas être affiché.

Remplacez le code dans *Students/Index.cshtml* par le code suivant.

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Ceci change l’en-tête en « Departments », supprime la colonne RowVersion et montre à l’administrateur le nom complet au lieu du prénom.

## <a name="update-edit-methods"></a>Mettre à jour les méthodes de modification

Dans la méthode HttpGet `Edit` et la méthode `Details`, ajoutez `AsNoTracking`. Dans la méthode HttpGet `Edit`, ajoutez un chargement hâtif pour l’administrateur.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

Remplacez le code existant pour la méthode HttpPost `Edit` méthode par le code suivant :

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Le code commence par essayer de lire le département à mettre à jour. Si la méthode `FirstOrDefaultAsync` retourne null, c’est que le département a été supprimé par un autre utilisateur. Dans ce cas, le code utilise les valeurs du formulaire envoyé pour créer une entité Department de façon que la page Edit puisse être réaffichée avec un message d’erreur. Vous pouvez aussi ne pas recréer l’entité Department si vous affichez seulement un message d’erreur sans réafficher les champs du département.

La vue stocke la valeur d’origine de `RowVersion` dans un champ masqué, et cette méthode reçoit cette valeur dans le paramètre `rowVersion`. Avant d’appeler `SaveChanges`, vous devez placer la valeur d’origine de la propriété `RowVersion` dans la collection `OriginalValues` pour l’entité.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Ensuite, quand Entity Framework crée une commande SQL UPDATE, cette commande inclut une clause WHERE qui recherche une ligne contenant la valeur d’origine de `RowVersion`. Si aucune ligne n’est affectée par la commande UPDATE (aucune ligne ne contient la valeur `RowVersion` d’origine), Entity Framework lève une exception `DbUpdateConcurrencyException`.

Le code du bloc catch pour cette exception obtient l’entité Department affectée qui a les valeurs mises à jour de la propriété `Entries` sur l’objet d’exception.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

La collection `Entries` n’a qu’un objet `EntityEntry`.  Vous pouvez utiliser cet objet pour obtenir les nouvelles valeurs entrées par l’utilisateur et les valeurs actuelles de la base de données.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Le code ajoute un message d’erreur personnalisé pour chaque colonne dont les valeurs dans la base de données diffèrent de ce que l’utilisateur a entré dans la page Edit (un seul champ est montré ici par souci de concision).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Enfin, le code affecte la nouvelle valeur récupérée auprès de la base de données à `RowVersion` pour `departmentToUpdate`. Cette nouvelle valeur de `RowVersion` est stockée dans le champ masqué quand la page Edit est réaffichée et, la prochaine fois que l’utilisateur clique sur **Save**, seules les erreurs d’accès concurrentiel qui se produisent depuis le réaffichage de la page Edit sont interceptées.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

L’instruction `ModelState.Remove` est nécessaire car `ModelState` contient l’ancienne valeur `RowVersion`. Dans la vue, la valeur `ModelState` d’un champ est prioritaire par rapport aux valeurs de propriétés du modèle quand les deux sont présentes.

## <a name="update-edit-view"></a>Mettre à jour la vue Edit

Dans *Views/Departments/Edit.cshtml*, faites les modifications suivantes :

* Ajoutez un champ masqué pour enregistrer la valeur de la propriété `RowVersion`, immédiatement après le champ masqué pour la propriété `DepartmentID`.

* Ajoutez une option « Select Administrator » à la liste déroulante.

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Tester les conflits d'accès concurrentiel

Exécutez l’application et accédez à la page Index des départements. Cliquez avec le bouton droit sur le lien hypertexte **Edit** pour le département « English », sélectionnez **Ouvrir dans un nouvel onglet**, puis cliquez sur le lien hypertexte **Edit** pour le département « English ». Les deux onglets du navigateur affichent maintenant les mêmes informations.

Changez un champ sous le premier onglet du navigateur, puis cliquez sur **Save**.

![Page 1 de modification de département après changement](concurrency/_static/edit-after-change-1.png)

Le navigateur affiche la page Index avec la valeur modifiée.

Changez un champ sous le deuxième onglet du navigateur.

![Page Edit 2 du département après changement](concurrency/_static/edit-after-change-2.png)

Cliquez sur **Enregistrer**. Vous voyez un message d’erreur :

![Message d’erreur de page de modification de département](concurrency/_static/edit-error.png)

Cliquez à nouveau sur **Enregistrer**. La valeur que vous avez entrée sous le deuxième onglet du navigateur est enregistrée. Vous voyez les valeurs enregistrées quand la page Index apparaît.

## <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Pour la page Delete, Entity Framework détecte les conflits d’accès concurrentiel provoqués par un autre utilisateur qui modifie le service de façon similaire. Quand la méthode HttpGet `Delete` affiche la vue de confirmation, la vue inclut la version d’origine de `RowVersion` dans un champ masqué. Cette valeur est ensuite disponible pour la méthode HttpPost `Delete` qui est appelée quand l’utilisateur confirme la suppression. Quand Entity Framework crée la commande SQL DELETE, il inclut une clause WHERE avec la valeur d’origine de `RowVersion`. Si la commande a pour résultat qu’aucune ligne n’est affectée (ce qui signifie que la ligne a été modifiée après l’affichage de la page de confirmation de la suppression), une exception d’accès concurrentiel est levée et la méthode HttpGet `Delete` est appelée avec un indicateur d’erreur défini sur true pour réafficher la page de confirmation avec un message d’erreur. Il est également possible qu’aucune ligne ne soit affectée en raison du fait que la ligne a été supprimée par un autre utilisateur : dans ce cas, aucun message d’erreur n’est donc affiché.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Mettre à jour les méthodes Delete dans le contrôleur Departments

Dans *DepartmentsController.cs*, remplacez la méthode HttpGet `Delete` par le code suivant :

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

La méthode accepte un paramètre facultatif qui indique si la page est réaffichée après une erreur d’accès concurrentiel. Si cet indicateur a la valeur true et que le département spécifié n’existe plus, c’est qu’il a été supprimé par un autre utilisateur. Dans ce cas, le code redirige vers la page Index.  Si cet indicateur a la valeur true et que le département existe, c’est qu’il a été modifié par un autre utilisateur. Dans ce cas, le code envoie un message d’erreur à la vue en utilisant `ViewData`.

Remplacez le code de la méthode HttpPost `Delete` (nommée `DeleteConfirmed`) par le code suivant :

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

Dans le code du modèle généré automatiquement que vous venez de remplacer, cette méthode n’acceptait qu’un seul ID d’enregistrement :

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Vous avez changé ce paramètre en une instance d’entité Department créée par le classeur de modèles. Ceci permet à Entity Framework d’accéder à la valeur de la propriété RowVersion en plus de la clé d’enregistrement.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Vous avez également changé le nom de la méthode d’action de `DeleteConfirmed` en `Delete`. Le code du modèle généré automatiquement utilisait le nom `DeleteConfirmed` pour donner à la méthode HttpPost une signature unique. (Le CLR exige que les méthodes surchargées aient des paramètres de méthode différents.) Maintenant que les signatures sont uniques, vous pouvez respecter la convention MVC et utiliser le même nom pour les méthodes de suppression HttpPost et HttpGet.

Si le département est déjà supprimé, la méthode `AnyAsync` retourne la valeur false et l’application revient simplement à la méthode Index.

Si une erreur d’accès concurrentiel est interceptée, le code réaffiche la page de confirmation de suppression et fournit un indicateur indiquant qu’elle doit afficher un message d’erreur d’accès concurrentiel.

### <a name="update-the-delete-view"></a>Mettre à jour la vue Delete

Dans *Views/Departments/Delete.cshtml*, remplacez le code du modèle généré automatiquement par le code suivant, qui ajoute un champ de message d’erreur et des champs masqués pour les propriétés DepartmentID et RowVersion. Les modifications sont mises en surbrillance.

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Ceci apporte les modifications suivantes :

* Ajoute un message d’erreur entre les titres `h2` et `h3`.

* Il remplace FirstMidName par FullName dans le champ **Administrator**.

* Supprime le champ RowVersion.

* Ajoute un champ masqué pour la propriété `RowVersion`.

Exécutez l’application et accédez à la page Index des départements. Cliquez avec le bouton droit sur le lien hypertexte **Delete** pour le département « English », sélectionnez **Ouvrir dans un nouvel onglet** puis, sous le premier onglet, cliquez sur le lien hypertexte **Edit** pour le département « English ».

Dans la première fenêtre, changez une des valeurs, puis cliquez sur **Save** :

![Page Edit pour les départements après modification avant la suppression](concurrency/_static/edit-after-change-for-delete.png)

Sous le deuxième onglet, cliquez sur **Delete**. Vous voyez le message d’erreur d’accès concurrentiel et les valeurs du département sont actualisées avec ce qui est actuellement dans la base de données.

![Page de confirmation de suppression du département avec erreur d’accès concurrentiel](concurrency/_static/delete-error.png)

Si vous recliquez sur **Delete**, vous êtes redirigé vers la page Index, qui montre que le département a été supprimé.

## <a name="update-details-and-create-views"></a>Mettre à jour les vues Details et Create

Vous pouvez éventuellement nettoyer le code du modèle généré automatiquement dans les vues Details et Create.

Remplacez le code de *Views/Departments/Details.cshtml* pour supprimer la colonne RowVersion et afficher le nom complet de l’administrateur.

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Remplacez le code de *Views/Departments/Create.cshtml* pour ajouter une option de sélection à la liste déroulante.

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Obtenir le code

[Télécharger ou afficher l’application complète.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ressources supplémentaires

 Pour plus d’informations sur la gestion de l’accès concurrentiel dans EF Core, consultez [Conflits d’accès concurrentiel](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Découvrez les conflits d’accès concurrentiel
> * Propriété de suivi ajoutée
> * Créez un contrôleur Departments et des vues
> * Vue Index mise à jour
> * Méthodes de modification mises à jour
> * Vue Edit mise à jour
> * Conflits d’accès concurrentiel testés
> * Mettre à jour la page Delete
> * Vues Details et Create mises à jour

Passez au tutoriel suivant pour découvrir comment implémenter l’héritage table par hiérarchie pour les entités Instructor et Student.

> [!div class="nextstepaction"]
> [Étape suivante : implémenter l’héritage TPH (table par hiérarchie)](inheritance.md)
