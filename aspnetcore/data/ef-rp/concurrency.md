---
title: Partie 8, Razor pages avec EF Core dans ASP.net Core-concurrence
author: rick-anderson
description: Partie 8 des Razor pages et Entity Framework de la série de didacticiels.
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053993"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a>Partie 8, Razor pages avec EF Core dans ASP.net Core-concurrence

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)et [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Ce didacticiel montre comment gérer les conflits quand plusieurs utilisateurs mettent à jour une entité en même temps.

## <a name="concurrency-conflicts"></a>Conflits d’accès concurrentiel

Un conflit d’accès concurrentiel se produit quand :

* Un utilisateur accède à la page de modification d’une entité.
* Un autre utilisateur met à jour la même entité avant que la modification du premier utilisateur soit écrite dans la base de données.

Si la détection de l’accès concurrentiel n’est pas activée, quiconque qui met à jour la base de données en dernier remplace les modifications de l’autre utilisateur. Si ce risque est acceptable, le coût de programmation de l’accès concurrentiel peut l’emporter sur l’avantage.

### <a name="pessimistic-concurrency-locking"></a>Accès concurrentiel pessimiste (verrouillage)

Une façon d’éviter les conflits d’accès concurrentiel consiste à utiliser des verrous de base de données. Ceci est appelé « accès concurrentiel pessimiste ». Avant de lire une ligne de base de données qu’elle entend mettre à jour, une application demande un verrou. Dès lors qu’une ligne est verrouillée pour l’accès aux mises à jour, aucun autre utilisateur n’est autorisé à verrouiller la ligne tant que le premier verrou n’est pas libéré.

La gestion des verrous présente des inconvénients. Elle peut être difficile à programmer et peut occasionner des problèmes de performances à mesure que le nombre d’utilisateurs augmente. Entity Framework Core n’assure pas de prise en charge intégrée pour celle-ci et ce tutoriel ne vous montre pas comment l’implémenter.

### <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

L’accès concurrentiel optimiste autorise la survenance des conflits d’accès concurrentiel, et réagit correctement quand ils surviennent. Par exemple, Jane consulte la page de modification de département et change le montant de « Budget » pour le département « English » en le faisant passer de 350 000,00 $ à 0,00 $.

![Modification de la valeur de budget sur 0](concurrency/_static/change-budget30.png)

Avant que Jane clique sur **Save**, John consulte la même page et change le champ Start Date de 01/09/2007 en 01/09/2013.

![Modification de la date de début sur 2013](concurrency/_static/change-date30.png)

Jane clique d’abord sur **Save** et voit sa modification prendre effet, puisque le navigateur affiche la page d’index avec un montant de budget égal à zéro.

John clique sur **Save** dans une page Edit qui affiche toujours un budget de 350 000,00 $. Ce qui se passe ensuite dépend de la façon dont vous gérez les conflits d’accès concurrentiel :

* Vous pouvez effectuer le suivi des propriétés modifiées par un utilisateur et mettre à jour seulement les colonnes correspondantes dans la base de données.

  Dans le scénario, aucune donnée ne serait perdue. Des propriétés différentes ont été mises à jour par les deux utilisateurs. La prochaine fois que quelqu’un consultera le département « English », il verra à la fois les modifications de Jane et de John. Cette méthode de mise à jour peut réduire le nombre de conflits susceptibles d’entraîner une perte de données. Cette approche présente quelques inconvénients :
 
  * Elle ne peut pas éviter une perte de données si des modifications concurrentes sont apportées à la même propriété.
  * Elle n’est généralement pas pratique dans une application web. Elle nécessite la tenue à jour d’un état significatif afin d’effectuer le suivi de toutes les valeurs récupérées et des nouvelles valeurs. La maintenance de grandes quantités d’état peut affecter les performances de l’application.
  * Elle peut augmenter la complexité de l’application par rapport à la détection de l’accès concurrentiel sur une entité.

* Vous pouvez laisser les modifications de John remplacer les modifications de Jane.

  La prochaine fois que quelqu’un consultera le département « English », il verra la date 01/09/2013 et la valeur 350 000,00 $ récupérée. Cette approche est un scénario *Priorité au client* ou *Priorité au dernier*. (Toutes les valeurs du client sont prioritaires par rapport à ce qui se trouve dans le magasin de données.) Si vous n’effectuez aucun codage pour la gestion de l’accès concurrentiel, le client WINS se produit automatiquement.

* Vous pouvez empêcher les modifications de John de faire l’objet d’une mise à jour dans la base de données. En règle générale, l’application :

  * affiche un message d’erreur ;
  * indique l’état actuel des données ;
  * autorise l’utilisateur à réappliquer les modifications.

  Il s’agit alors d’un scénario *Priorité au magasin*. (Les valeurs du magasin de données ont priorité sur les valeurs soumises par le client.) Vous implémentez le scénario de stockage WINS dans ce didacticiel. Cette méthode garantit qu’aucune modification n’est remplacée sans qu’un utilisateur soit averti.

## <a name="conflict-detection-in-ef-core"></a>Détection de conflits dans EF Core

EF Core lève des exceptions `DbConcurrencyException` quand il détecte des conflits. Le modèle de données doit être configuré pour activer la détection de conflits. Voici les options qui permettent d’activer la détection de conflits :

* Configurez EF Core de façon à inclure les valeurs d’origine des colonnes configurées en tant que [jetons d’accès concurrentiel](/ef/core/modeling/concurrency) dans la clause Where des commandes Update et Delete.

  Lorsque `SaveChanges` est appelé, la clause WHERE recherche les valeurs d’origine de toutes les propriétés annotées avec l' <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribut. L’instruction update ne trouve pas de ligne à mettre à jour si aucune propriété de jeton d’accès concurrentiel n’a changé depuis la première lecture de la ligne. EF Core interprète cela comme un conflit d’accès concurrentiel. Pour les tables de base de données qui comptent de nombreuses colonnes, cette approche peut aboutir à des clauses Where de très grande taille et peut nécessiter de grandes quantités d’états. Par conséquent, cette approche n’est généralement pas recommandée et n’est pas la méthode utilisée dans ce didacticiel.

* Dans la table de base de données, incluez une colonne de suivi qui peut être utilisée pour déterminer quand une ligne a été modifiée.

  Dans une base de données SQL Server, le type de données de la colonne de suivi est `rowversion`. La valeur de `rowversion` est un nombre séquentiel qui est incrémenté chaque fois que la ligne est mise à jour. Dans une commande Update ou Delete, la clause Where inclut la valeur d’origine de la colonne de suivi (le numéro de version de la ligne d’origine). Si la ligne mise à jour a été modifiée par un autre utilisateur, la valeur de la colonne `rowversion` est différente de la valeur d’origine. Dans ce cas, l’instruction Update ou Delete ne peut pas trouver la ligne à mettre à jour en raison de la clause Where. EF Core lève une exception d’accès concurrentiel quand aucune ligne n’est affectée par une commande Update ou Delete.

## <a name="add-a-tracking-property"></a>Ajouter une propriété de suivi

Dans *Models/Department.cs*, ajoutez une propriété de suivi nommée RowVersion :

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

L' <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribut est ce qui identifie la colonne comme une colonne de suivi d’accès concurrentiel. L’API Fluent est un autre moyen de spécifier la propriété de suivi :

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour une base de données SQL Server, l’attribut `[Timestamp]` d’une propriété d’entité définie en tant que tableau d’octets :

* Entraîne l’inclusion de la colonne dans les clauses Where des commandes DELETE et UPDATE.
* Définit le type de colonne dans la base de données sur [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).

La base de données génère un numéro de version de ligne séquentiel qui est incrémenté chaque fois que la ligne est mise à jour. Dans une commande `Update` ou `Delete`, la clause `Where` comprend la valeur de version de ligne récupérée. Si la ligne mise à jour a changé depuis sa récupération :

* La valeur de version de ligne actuelle ne correspond pas à la valeur récupérée.
* Les commandes `Update` ou `Delete` ne trouvent pas de ligne, car la clause `Where` recherche la valeur de version de ligne récupérée.
* Une `DbUpdateConcurrencyException` est levée.

Le code suivant montre une partie du T-SQL généré par EF Core quand le nom du département est mis à jour :

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Le code en surbrillance ci-dessus montre la clause `WHERE` contenant `RowVersion`. Si la base de données `RowVersion` n’est pas égale au paramètre `RowVersion` (`@p2`), aucune ligne n’est mise à jour.

Le code en surbrillance suivant montre le T-SQL qui vérifie qu’une seule ligne a été mise à jour :

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retourne le nombre de lignes affectées par la dernière instruction. Si aucune ligne n’est mise à jour, EF Core lève une exception `DbUpdateConcurrencyException`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pour une base de données SQLite, l’attribut `[Timestamp]` d’une propriété d’entité définie en tant que tableau d’octets :

* Entraîne l’inclusion de la colonne dans les clauses Where des commandes DELETE et UPDATE.
* Mappe à un type de colonne BLOB.

Les déclencheurs de base de données mettent à jour la colonne RowVersion avec un nouveau tableau d’octets aléatoire chaque fois qu’une ligne est mise à jour. Dans une commande `Update` ou `Delete`, la clause `Where` comprend la valeur récupérée de la colonne RowVersion. Si la ligne mise à jour a changé depuis sa récupération :

* La valeur de version de ligne actuelle ne correspond pas à la valeur récupérée.
* Les commandes `Update` ou `Delete` ne trouvent pas de ligne, car la clause `Where` recherche la valeur de version de ligne d’origine.
* Une `DbUpdateConcurrencyException` est levée.

---

### <a name="update-the-database"></a>Mettre à jour la base de données

L’ajout de la propriété `RowVersion` change le modèle de données, ce qui nécessite une migration.

Créez le projet. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Exécutez la commande suivante dans PMC :

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Exécutez la commande suivante dans un terminal :

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

Cette commande :

* Crée le fichier de migration *Migrations/{horodatage}_RowVersion.cs*.
* Mettent à jour le fichier *Migrations/SchoolContextModelSnapshot.cs*. La mise à jour ajoute le code en surbrillance suivant à la méthode `BuildModel` :

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Exécutez la commande suivante dans PMC :

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le fichier `Migrations/<timestamp>_RowVersion.cs` et ajoutez le code mis en surbrillance :

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Le code précédent :

  * Met à jour les lignes existantes avec des valeurs d’objet blob aléatoires.
  * Ajoute des déclencheurs de base de données qui définissent la colonne RowVersion sur une valeur d’objet blob aléatoire chaque fois qu’une ligne est mise à jour.

* Exécutez la commande suivante dans un terminal :

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Générer automatiquement des modèles de pages Department

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Suivez les instructions dans [Générer automatiquement des modèles de pages Student](xref:data/ef-rp/intro#scaffold-student-pages) avec les exceptions suivantes :

* Créez un dossier *Pages/Departments*.  
* Utilisez `Department` pour la classe de modèle.
  * Utilisez la classe de contexte existante au lieu d’en créer une nouvelle.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Créez un dossier *Pages/Departments*.

* Exécutez la commande suivante pour générer automatiquement des modèles de pages Department.

  **Sur Windows :**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **Sur Linux ou macOS :**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Créez le projet.

## <a name="update-the-index-page"></a>Mettre à jour la page Index

L’outil de génération de modèles automatique créé une colonne `RowVersion` pour la page Index, mais ce champ ne s’affiche pas dans une application de production. Dans ce tutoriel, le dernier octet de `RowVersion` est affiché pour montrer comment la gestion de l’accès concurrentiel fonctionne. Il n’est pas garanti que le dernier octet soit unique par lui-même.

Mettre à jour la page *Pages\Departments\Index.cshtml* :

* Remplacez Index par Departments.
* Modifiez le code contenant `RowVersion` pour afficher uniquement le dernier octet du tableau d’octets.
* Remplacez FirstMidName par FullName.

Le code suivant affiche la page mise à jour :

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Mettre à jour le modèle de page de modification

Mettez à jour *Pages\Departments\Edit.cshtml.cs* à l’aide du code suivant :

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

La <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> est mise à jour avec la `rowVersion` valeur de l’entité lorsqu’elle a été récupérée dans la `OnGet` méthode. EF Core génère une commande SQL UPDATE avec une clause WHERE contenant la valeur `RowVersion` d’origine. Si aucune ligne n’est affectée par la commande UPDATE (aucune ligne ne contient la valeur `RowVersion` d’origine), une exception `DbUpdateConcurrencyException` est levée.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

Dans le code en surbrillance précédent :

* La valeur de `Department.RowVersion` est celle qui se trouvait dans l’entité au moment où elle a été initialement récupérée dans la requête Get pour la page Edit. La valeur est fournie à la `OnPost` méthode par un champ masqué dans la Razor page qui affiche l’entité à modifier. La valeur du champ masqué est copiée dans `Department.RowVersion` par le classeur de modèles.
* `OriginalValue` est la valeur qu’utilisera EF Core dans la clause Where. Avant l’exécution de la ligne de code en surbrillance, `OriginalValue` a la valeur qui se trouvait dans la base de données au moment où `FirstOrDefaultAsync` été appelé dans cette méthode, laquelle risque d’être différente de celle qui figurait dans la page Edit.
* Le code en surbrillance garantit qu’EF Core utilise la valeur `RowVersion` d’origine de l’entité `Department` affichée dans la clause Where de l’instruction SQL UPDATE.

Quand une erreur d’accès concurrentiel se produit, le code en surbrillance suivant obtient les valeurs du client (valeurs publiées dans cette méthode) et les valeurs de la base de données.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Le code suivant ajoute un message d’erreur personnalisé pour chaque colonne dont les valeurs dans la base de données sont différentes de celles envoyées à `OnPostAsync` :

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Le code en surbrillance suivant affecte à `RowVersion` la nouvelle valeur récupérée à partir de la base de données. La prochaine fois que l’utilisateur cliquera sur **Save**, seules les erreurs d’accès concurrentiel qui se sont produites depuis le dernier affichage de la page Edit seront interceptées.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

L’instruction `ModelState.Remove` est nécessaire car `ModelState` contient l’ancienne valeur `RowVersion`. Dans la Razor page, la `ModelState` valeur d’un champ est prioritaire sur les valeurs de propriété du modèle lorsque les deux sont présentes.

### <a name="update-the-edit-page"></a>Mettre à jour la page Edit

Mettez à jour *Pages/Departments/Edit.cshtml* avec le code suivant :

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Le code précédent :

* Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int}"`.
* Ajoute une version de ligne masquée. `RowVersion` doit être ajouté de sorte que la publication (postback) lie la valeur.
* Affiche le dernier octet de `RowVersion` à des fins de débogage.
* Remplace `ViewData` par le `InstructorNameSL` fortement typé.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Tester les conflits d’accès concurrentiel avec la page Edit

Ouvrez deux instances de navigateur de la page Edit sur le département English :

* Exécutez l’application et sélectionnez Departments.
* Cliquez avec le bouton droit sur le lien hypertexte **Edit** correspondant au département English, puis sélectionnez **Open in new tab**.
* Sous le premier onglet, cliquez sur le lien hypertexte **Edit** correspondant au département English.

Les deux onglets de navigateur affichent les mêmes informations.

Changez le nom sous le premier onglet de navigateur, puis cliquez sur **Save**.

![Page 1 de modification de département après changement](concurrency/_static/edit-after-change-130.png)

Le navigateur affiche la page Index avec la valeur modifiée et un indicateur rowVersion mis à jour. Notez l’indicateur rowVersion mis à jour ; il est affiché sur la deuxième publication (postback) sous l’autre onglet.

Changez un champ différent sous le deuxième onglet du navigateur.

![Page Edit 2 du département après changement](concurrency/_static/edit-after-change-230.png)

Cliquez sur **Enregistrer**. Des messages d’erreur s’affichent pour tous les champs qui ne correspondent pas aux valeurs de la base de données :

![Message d’erreur de page de modification de département](concurrency/_static/edit-error30.png)

Cette fenêtre de navigateur n’avait pas l’intention de changer le champ Name. Copiez et collez la valeur actuelle (Languages) dans le champ Name. Tabulation. La validation côté client supprime le message d’erreur.

Cliquez à nouveau sur **Enregistrer**. La valeur que vous avez entrée sous le deuxième onglet du navigateur est enregistrée. Les valeurs enregistrées sont visibles dans la page Index.

## <a name="update-the-delete-page-model"></a>Mettre à jour le modèle de page de suppression

Mettez à jour *Pages/Departments/Delete.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

La page Delete détecte les conflits d’accès concurrentiel quand l’entité a changé après avoir été récupérée. `Department.RowVersion` est la version de ligne quand l’entité a été récupérée. Quand EF Core crée la commande SQL DELETE, il inclut une clause WHERE avec `RowVersion`. Si après l’exécution de la commande SQL DELETE aucune ligne n’est affectée :

* La valeur de `RowVersion` dans la commande SQL DELETE ne correspond pas à `RowVersion` dans la base de données.
* Une exception DbUpdateConcurrencyException est levée.
* `OnGetAsync` est appelée avec `concurrencyError`.

### <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Mettez à jour *Pages/Departments/Delete.cshtml* avec le code suivant :

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

Le code précédent apporte les modifications suivantes :

* Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int}"`.
* Il ajoute un message d’erreur.
* Il remplace FirstMidName par FullName dans le champ **Administrator**.
* Il change `RowVersion` pour afficher le dernier octet.
* Ajoute une version de ligne masquée. `RowVersion` doit être ajouté de sorte que la publication (postback) lie la valeur.

### <a name="test-concurrency-conflicts"></a>Tester les conflits d'accès concurrentiel

Créez un département test.

Ouvrez deux instances de navigateur de la page Delete sur le département test :

* Exécutez l’application et sélectionnez Departments.
* Cliquez avec le bouton droit sur le lien hypertexte **Delete** correspondant au département test, puis sélectionnez **Open in new tab**.
* Cliquez sur le lien hypertexte **Edit** correspondant au département test.

Les deux onglets de navigateur affichent les mêmes informations.

Changez le budget sous le premier onglet de navigateur, puis cliquez sur **Save**.

Le navigateur affiche la page Index avec la valeur modifiée et un indicateur rowVersion mis à jour. Notez l’indicateur rowVersion mis à jour ; il est affiché sur la deuxième publication (postback) sous l’autre onglet.

Supprimez le service test du deuxième onglet. Une erreur d’accès concurrentiel s’affiche avec les valeurs actuelles de la base de données. Le fait de cliquer sur **supprimer** supprime l’entité, sauf si `RowVersion` a été mis à jour.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Concurrency Tokens in EF Core (Jetons d’accès concurrentiel dans EF Core)](/ef/core/modeling/concurrency)
* [Gestion de l’accès concurrentiel dans EF Core](/ef/core/saving/concurrency)
* [Débogage d’une source ASP.NET Core 2.x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel est le dernier de la série. Des rubriques supplémentaires sont abordées dans la [version MVC de cette série de tutoriels](xref:data/ef-mvc/index).

> [!div class="step-by-step"]
> [Tutoriel précédent](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ce didacticiel montre comment gérer les conflits quand plusieurs utilisateurs mettent à jour une entité en même temps. Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, [téléchargez ou affichez l’application terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [Instructions de téléchargement](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>Conflits d’accès concurrentiel

Un conflit d’accès concurrentiel se produit quand :

* Un utilisateur accède à la page de modification d’une entité.
* Un autre utilisateur met à jour la même entité avant que la modification du premier utilisateur soit écrite dans la base de données.

Si la détection d’accès concurrentiel n’est pas activée, quand des mises à jour simultanées se produisent :

* La dernière mise à jour est prioritaire. Autrement dit, les dernières valeurs mises à jour sont enregistrées dans la base de données.
* La première des mises à jour en cours est perdue.

### <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

L’accès concurrentiel optimiste autorise la survenance des conflits d’accès concurrentiel, et réagit correctement quand ils surviennent. Par exemple, Jane consulte la page de modification de département et change le montant de « Budget » pour le département « English » en le faisant passer de 350 000,00 $ à 0,00 $.

![Modification de la valeur de budget sur 0](concurrency/_static/change-budget.png)

Avant que Jane clique sur **Save**, John consulte la même page et change le champ Start Date de 01/09/2007 en 01/09/2013.

![Modification de la date de début sur 2013](concurrency/_static/change-date.png)

Jane clique la première sur **Save** et voit sa modification quand le navigateur revient à la page Index.

![Le budget est passé à zéro](concurrency/_static/budget-zero.png)

John clique sur **Save** dans une page Edit qui affiche toujours un budget de 350 000,00 $. Ce qui se passe ensuite est déterminé par la façon dont vous gérez les conflits d’accès concurrentiel.

L’accès concurrentiel optimiste comprend les options suivantes :

* Vous pouvez effectuer le suivi des propriétés modifiées par un utilisateur et mettre à jour seulement les colonnes correspondantes dans la base de données.

  Dans le scénario, aucune donnée ne serait perdue. Des propriétés différentes ont été mises à jour par les deux utilisateurs. La prochaine fois que quelqu’un consultera le département « English », il verra à la fois les modifications de Jane et de John. Cette méthode de mise à jour peut réduire le nombre de conflits susceptibles d’entraîner une perte de données. Cette approche a les caractéristiques suivantes :
 
  * Elle ne peut pas éviter une perte de données si des modifications concurrentes sont apportées à la même propriété.
  * Elle n’est généralement pas pratique dans une application web. Elle nécessite la tenue à jour d’un état significatif afin d’effectuer le suivi de toutes les valeurs récupérées et des nouvelles valeurs. La maintenance de grandes quantités d’état peut affecter les performances de l’application.
  * Elle peut augmenter la complexité de l’application par rapport à la détection de l’accès concurrentiel sur une entité.

* Vous pouvez laisser les modifications de John remplacer les modifications de Jane.

  La prochaine fois que quelqu’un consultera le département « English », il verra la date 01/09/2013 et la valeur 350 000,00 $ récupérée. Cette approche est un scénario *Priorité au client* ou *Priorité au dernier*. (Toutes les valeurs du client sont prioritaires par rapport à ce qui se trouve dans le magasin de données.) Si vous n’effectuez aucun codage pour la gestion de l’accès concurrentiel, le client WINS se produit automatiquement.

* Vous pouvez empêcher les modifications de John d’être mises à jour dans la base de données. En règle générale, l’application :

  * affiche un message d’erreur ;
  * indique l’état actuel des données ;
  * autorise l’utilisateur à réappliquer les modifications.

  Il s’agit alors d’un scénario *Priorité au magasin*. (Les valeurs du magasin de données ont priorité sur les valeurs soumises par le client.) Vous implémentez le scénario de stockage WINS dans ce didacticiel. Cette méthode garantit qu’aucune modification n’est remplacée sans qu’un utilisateur soit averti.

## <a name="handling-concurrency"></a>Gestion de l’accès concurrentiel 

Quand une propriété est configurée en tant que [jeton d’accès concurrentiel](/ef/core/modeling/concurrency) :

* EF Core vérifie que cette propriété n’a pas été modifiée après avoir été récupérée. La vérification a lieu quand [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) ou [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) est appelée.
* Si la propriété a été modifiée après avoir été récupérée, une [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) est levée. 

Le modèle de données et de la base de données doivent être configurés pour prendre en charge la levée de `DbUpdateConcurrencyException`.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Détection des conflits d’accès concurrentiel sur une propriété

Les conflits d’accès concurrentiel peuvent être détectés au niveau de la propriété avec l’attribut [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute). L’attribut peut être appliqué à plusieurs propriétés sur le modèle. Pour plus d’informations, consultez [Data Annotations-ConcurrencyCheck (Annotations de données-ConcurrencyCheck)](/ef/core/modeling/concurrency#data-annotations).

Nous n’utilisons pas l’attribut `[ConcurrencyCheck]` dans ce didacticiel.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Détection des conflits d’accès concurrentiel sur une ligne

Pour détecter les conflits d’accès concurrentiel, une colonne de suivi [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) est ajoutée au modèle.  `rowversion` :

* est propre à SQL Server. D’autres bases de données peuvent ne pas fournir une fonctionnalité similaire.
* Sert à déterminer qu’une entité n’a pas été modifiée depuis qu’elle a été récupérée à partir de la base de données. 

La base de données génère un numéro `rowversion` séquentiel qui est incrémenté chaque fois que la ligne est mise à jour. Dans une commande `Update` ou `Delete`, la clause `Where` comprend la valeur récupérée de `rowversion`. Si la ligne mise à jour a changé :

* `rowversion` ne correspond pas à la valeur récupérée.
* Les commandes `Update` ou `Delete` ne trouvent pas de ligne, car la clause `Where` comprend la valeur `rowversion` récupérée.
* Une `DbUpdateConcurrencyException` est levée.

Dans EF Core, quand aucune ligne n’a été mise à jour par une commande `Update` ou `Delete`, une exception d’accès concurrentiel est levée.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Ajouter une propriété de suivi à l’entité Department

Dans *Models/Department.cs*, ajoutez une propriété de suivi nommée RowVersion :

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

L’attribut [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) spécifie que cette colonne est incluse dans la clause `Where` des commandes `Update` et `Delete`. L’attribut se nomme `Timestamp`, car les versions précédentes de SQL Server utilisaient un type de données SQL `timestamp` avant son remplacement par le type SQL `rowversion`.

L’API Fluent peut également spécifier la propriété de suivi :

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Le code suivant montre une partie du T-SQL généré par EF Core quand le nom du département est mis à jour :

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Le code en surbrillance ci-dessus montre la clause `WHERE` contenant `RowVersion`. Si la `RowVersion` de la base de données n’est pas égale au paramètre `RowVersion` (`@p2`), aucune ligne n’est mise à jour.

Le code en surbrillance suivant montre le T-SQL qui vérifie qu’une seule ligne a été mise à jour :

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retourne le nombre de lignes affectées par la dernière instruction. Si aucune ligne n’est mise à jour, EF Core lève une `DbUpdateConcurrencyException`.

Vous pouvez voir le T-SQL généré par EF Core dans la fenêtre Sortie de Visual Studio.

### <a name="update-the-db"></a>Mettre à jour la base de données

L’ajout de la propriété `RowVersion` change le modèle de base de données, ce qui nécessite une migration.

Créez le projet. Entrez ce qui suit dans une fenêtre de commande :

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Les commandes précédentes :

* Ajoutent le fichier de migration *Migrations/{horodatage}_RowVersion.cs*.
* Mettent à jour le fichier *Migrations/SchoolContextModelSnapshot.cs*. La mise à jour ajoute le code en surbrillance suivant à la méthode `BuildModel` :

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Exécutent des migrations pour mettre à jour la base de données.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Générer automatiquement le modèle Departments

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Suivez les instructions fournies dans [Générer automatiquement le modèle d’étudiant](xref:data/ef-rp/intro#scaffold-student-pages) et utilisez `Department` pour la classe de modèle.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

La commande précédente génère automatiquement le modèle `Department`. Ouvrez le projet dans Visual Studio.

Créez le projet.

### <a name="update-the-departments-index-page"></a>Mettre à jour la page d’index des départements

Le moteur de génération de modèles automatique créé une colonne `RowVersion` pour la page Index, mais ce champ ne doit pas être affiché. Dans ce didacticiel, le dernier octet de `RowVersion` est affiché afin d’aider à mieux comprendre l’accès concurrentiel. Il n’est pas garanti que le dernier octet soit unique. Une application réelle n’afficherait pas `RowVersion` ou le dernier octet de `RowVersion`.

Mettez à jour la page Index :

* Remplacez Index par Departments.
* Remplacez le balisage contenant `RowVersion` par le dernier octet de `RowVersion`.
* Remplacez FirstMidName par FullName.

Le balisage suivant montre la page mise à jour :

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Mettre à jour le modèle de page de modification

Mettez à jour *Pages\Departments\Edit.cshtml.cs* à l’aide du code suivant :

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Pour détecter un problème d’accès concurrentiel, [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) est mise à jour avec la valeur `rowVersion` de l’entité récupérée. EF Core génère une commande SQL UPDATE avec une clause WHERE contenant la valeur `RowVersion` d’origine. Si aucune ligne n’est affectée par la commande UPDATE (aucune ligne ne contient la valeur `RowVersion` d’origine), une exception `DbUpdateConcurrencyException` est levée.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

Dans le code précédent, `Department.RowVersion` est la valeur quand l’entité a été récupérée. `OriginalValue` est la valeur présente dans la base de données quand `FirstOrDefaultAsync` a été appelée dans cette méthode.

Le code suivant obtient les valeurs du client (celles envoyées à cette méthode) et les valeurs de la base de données :

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Le code suivant ajoute un message d’erreur personnalisé pour chaque colonne dont les valeurs dans la base de données sont différentes de celles envoyées à `OnPostAsync` :

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Le code en surbrillance suivant affecte à `RowVersion` la nouvelle valeur récupérée à partir de la base de données. La prochaine fois que l’utilisateur cliquera sur **Save**, seules les erreurs d’accès concurrentiel qui se sont produites depuis le dernier affichage de la page Edit seront interceptées.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

L’instruction `ModelState.Remove` est nécessaire car `ModelState` contient l’ancienne valeur `RowVersion`. Dans la Razor page, la `ModelState` valeur d’un champ est prioritaire sur les valeurs de propriété du modèle lorsque les deux sont présentes.

## <a name="update-the-edit-page"></a>Mettre à jour la page Edit

Mettez à jour *Pages/Departments/Edit.cshtml* avec le balisage suivant :

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Le balisage précédent :

* Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int}"`.
* Ajoute une version de ligne masquée. `RowVersion` doit être ajouté afin que la publication lie la valeur.
* Affiche le dernier octet de `RowVersion` à des fins de débogage.
* Remplace `ViewData` par le `InstructorNameSL` fortement typé.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Tester les conflits d’accès concurrentiel avec la page Edit

Ouvrez deux instances de navigateur de la page Edit sur le département English :

* Exécutez l’application et sélectionnez Departments.
* Cliquez avec le bouton droit sur le lien hypertexte **Edit** correspondant au département English, puis sélectionnez **Open in new tab**.
* Sous le premier onglet, cliquez sur le lien hypertexte **Edit** correspondant au département English.

Les deux onglets de navigateur affichent les mêmes informations.

Changez le nom sous le premier onglet de navigateur, puis cliquez sur **Save**.

![Page 1 de modification de département après changement](concurrency/_static/edit-after-change-1.png)

Le navigateur affiche la page Index avec la valeur modifiée et un indicateur rowVersion mis à jour. Notez l’indicateur rowVersion mis à jour ; il est affiché sur la deuxième publication (postback) sous l’autre onglet.

Changez un champ différent sous le deuxième onglet du navigateur.

![Page Edit 2 du département après changement](concurrency/_static/edit-after-change-2.png)

Cliquez sur **Enregistrer**. Des messages d’erreur s’affichent pour tous les champs qui ne correspondent pas aux valeurs de la base de données :

![Message d’erreur de page de modification de département](concurrency/_static/edit-error.png)

Cette fenêtre de navigateur n’avait pas l’intention de changer le champ Name. Copiez et collez la valeur actuelle (Languages) dans le champ Name. Tabulation. La validation côté client supprime le message d’erreur.

![Message d’erreur de page de modification de département](concurrency/_static/cv.png)

Cliquez à nouveau sur **Enregistrer**. La valeur que vous avez entrée sous le deuxième onglet du navigateur est enregistrée. Les valeurs enregistrées sont visibles dans la page Index.

## <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Mettez à jour le modèle de page de suppression avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

La page Delete détecte les conflits d’accès concurrentiel quand l’entité a changé après avoir été récupérée. `Department.RowVersion` est la version de ligne quand l’entité a été récupérée. Quand EF Core crée la commande SQL DELETE, il inclut une clause WHERE avec `RowVersion`. Si après l’exécution de la commande SQL DELETE aucune ligne n’est affectée :

* La valeur de `RowVersion` dans la commande SQL DELETE ne correspond pas à `RowVersion` dans la base de données.
* Une exception DbUpdateConcurrencyException est levée.
* `OnGetAsync` est appelée avec `concurrencyError`.

### <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Mettez à jour *Pages/Departments/Delete.cshtml* avec le code suivant :

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Le code précédent apporte les modifications suivantes :

* Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int}"`.
* Il ajoute un message d’erreur.
* Il remplace FirstMidName par FullName dans le champ **Administrator**.
* Il change `RowVersion` pour afficher le dernier octet.
* Ajoute une version de ligne masquée. `RowVersion` doit être ajouté afin que la publication lie la valeur.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Tester les conflits d’accès concurrentiel avec la page Delete

Créez un département test.

Ouvrez deux instances de navigateur de la page Delete sur le département test :

* Exécutez l’application et sélectionnez Departments.
* Cliquez avec le bouton droit sur le lien hypertexte **Delete** correspondant au département test, puis sélectionnez **Open in new tab**.
* Cliquez sur le lien hypertexte **Edit** correspondant au département test.

Les deux onglets de navigateur affichent les mêmes informations.

Changez le budget sous le premier onglet de navigateur, puis cliquez sur **Save**.

Le navigateur affiche la page Index avec la valeur modifiée et un indicateur rowVersion mis à jour. Notez l’indicateur rowVersion mis à jour ; il est affiché sur la deuxième publication (postback) sous l’autre onglet.

Supprimez le service test du deuxième onglet. Une erreur d’accès concurrentiel s’affiche avec les valeurs actuelles de la base de données. Le fait de cliquer sur **supprimer** supprime l’entité, sauf si `RowVersion` a été mis à jour.

Pour découvrir comment hériter d’un modèle de données, consultez [Héritage](xref:data/ef-mvc/inheritance).

### <a name="additional-resources"></a>Ressources supplémentaires

* [Concurrency Tokens in EF Core (Jetons d’accès concurrentiel dans EF Core)](/ef/core/modeling/concurrency)
* [Gestion de l’accès concurrentiel dans EF Core](/ef/core/saving/concurrency)
* [Version YouTube de ce tutoriel(gestion des conflits d’accès concurrentiel)](https://youtu.be/EosxHTFgYps)
* [Version YouTube de ce tutoriel(Partie 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Version YouTube de ce tutoriel(Partie 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Précédent](xref:data/ef-rp/update-related-data)

::: moniker-end

