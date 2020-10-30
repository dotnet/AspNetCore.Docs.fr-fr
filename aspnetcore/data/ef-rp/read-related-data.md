---
title: Partie 6, Razor pages avec EF Core dans ASP.net Core-lire les données associées
author: rick-anderson
description: Partie 6 de Razor pages et Entity Framework série de didacticiels.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061065"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>Partie 6, Razor pages avec EF Core dans ASP.net Core-lire les données associées

Par [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Ce tutoriel montre comment lire et afficher des données associées. Les données associées sont des données qu’EF Core charge dans des propriétés de navigation.

Les illustrations suivantes montrent les pages terminées pour ce didacticiel :

![Page d’index des cours](read-related-data/_static/courses-index30.png)

![Page d’index des formateurs](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Chargement hâtif, explicite et différé

EF Core peut charger des données associées dans les propriétés de navigation d’une entité de plusieurs manières :

* [Chargement hâtif](/ef/core/querying/related-data#eager-loading). Le chargement hâtif a lieu quand une requête pour un type d’entité charge également des entités associées. Quand une entité est lue, ses données associées sont récupérées. Cela génère en général une requête de jointure unique qui récupère toutes les données nécessaires. EF Core émet plusieurs requêtes pour certains types de chargement hâtif. Il peut s’avérer plus efficace d’émettre plusieurs requêtes plutôt qu’une seule très grande. Le chargement hâtif est spécifié avec les méthodes `Include` et `ThenInclude`.

  ![Exemple de chargement hâtif](read-related-data/_static/eager-loading.png)
 
  Le chargement hâtif envoie plusieurs requêtes quand une navigation dans la collection est incluse :

  * Une requête pour la requête principale 
  * Une requête pour chaque « périmètre » de collection dans l’arborescence de la charge.

* Requêtes distinctes avec `Load` : les données peuvent être récupérées dans des requêtes distinctes, et EF Core « corrige » les propriétés de navigation. Quand EF Core « corrige », cela signifie que les propriétés de navigation sont renseignées automatiquement. Les requêtes distinctes avec `Load` s’apparentent plus au chargement explicite qu’au chargement hâtif.

  ![Exemple de requêtes distinctes](read-related-data/_static/separate-queries.png)

  **Remarque :** EF Core résout automatiquement les propriétés de navigation vers d’autres entités précédemment chargées dans l’instance de contexte. Même si les données pour une propriété de navigation ne sont *pas* explicitement incluses, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.

* [Chargement explicite](/ef/core/querying/related-data#explicit-loading). Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées. Vous devez écrire du code pour récupérer les données associées en cas de besoin. En cas de chargement explicite avec des requêtes distinctes, plusieurs requêtes sont envoyées à la base de données. Avec le chargement explicite, le code spécifie les propriétés de navigation à charger. Utilisez la méthode `Load` pour effectuer le chargement explicite. Exemple :

  ![Exemple de chargement explicite](read-related-data/_static/explicit-loading.png)

* [Chargement différé](/ef/core/querying/related-data#lazy-loading). Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées. Lors du premier accès à une propriété de navigation, les données requises pour cette propriété de navigation sont récupérées automatiquement. Une requête est envoyée à la base de données chaque fois qu’une propriété de navigation fait pour la première fois l’objet d’un accès. Le chargement différé peut nuire aux performances, par exemple lorsque les développeurs utilisent des modèles N + 1, en chargeant un parent et en énumérant les enfants.

## <a name="create-course-pages"></a>Créer des pages Course

L’entité `Course` comprend une propriété de navigation qui contient l’entité `Department` associée.

![Course.Department](read-related-data/_static/dep-crs.png)

Pour afficher le nom du service (« department ») affecté pour un cours (« course ») :

* Chargez l’entité `Department` associée dans la propriété de navigation `Course.Department`.
* Obtenez le nom à partir de la propriété `Name` de l’entité `Department`.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Générer automatiquement des modèles de pages Course

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Suivez les instructions dans [Générer automatiquement des modèles de pages Student](xref:data/ef-rp/intro#scaffold-student-pages) avec les exceptions suivantes :

  * Créez un dossier *Pages/Courses* .
  * Utilisez `Course` pour la classe de modèle.
  * Utilisez la classe de contexte existante au lieu d’en créer une nouvelle.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Créez un dossier *Pages/Courses* .

* Exécutez la commande suivante pour générer automatiquement des modèles de pages Course.

  **Sur Windows :**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **Sur Linux ou macOS :**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* Ouvrez *Pages/Courses/Index.cshtml.cs* et examinez la méthode `OnGetAsync`. Le moteur de génération de modèles automatique a spécifié le chargement hâtif pour la propriété de navigation `Department`. La méthode `Include` spécifie le chargement hâtif.

* Exécutez l’application et sélectionnez le lien **Courses** . La colonne Department affiche le `DepartmentID`, ce qui n’est pas utile.

### <a name="display-the-department-name"></a>Afficher le nom du service (« department »)

Mettez à jour Pages/Courses/Index.cshtml.cs avec le code suivant :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Le code précédent remplace la propriété `Course` par `Courses` et ajoute `AsNoTracking`. `AsNoTracking` améliore les performances, car les entités retournées ne sont pas suivies. Les entités n’ont pas besoin d’être suivies, car elles ne sont pas mises à jour dans le contexte actuel.

Mettez à jour *Pages/Courses/Index.cshtml* avec le code suivant.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

Les modifications suivantes ont été apportées au code généré automatiquement :

* Le nom de propriété `Course` est changé en `Courses`.
* Ajout d’une colonne **Number** qui affiche la valeur de la propriété `CourseID`. Par défaut, les clés primaires ne sont pas générées automatiquement, car elles ne sont normalement pas significatives pour les utilisateurs finaux. Toutefois, dans le cas présent la clé primaire est significative.
* Modification de la colonne **Department** afin d’afficher le nom du département. Le code affiche la propriété `Name` de l’entité `Department` qui est chargée dans la propriété de navigation `Department` :

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Exécutez l’application et sélectionnez l’onglet **Courses** pour afficher la liste avec les noms des départements.

![Page d’index des cours](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Chargement de données associées avec Select

La méthode `OnGetAsync` charge les données associées avec la méthode `Include`. La méthode `Select` est autre solution qui charge uniquement les données associées nécessaires. Pour les éléments uniques, comme `Department.Name`, il utilise une jointure interne SQL. Pour les collections, il utilise un autre accès à la base de données, mais c’est aussi le cas de l’opérateur `Include` sur les collections.

Le code suivant charge les données associées avec la méthode `Select` :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

Le code précédent ne retourne aucun type d’entité, par conséquent aucun suivi n’est effectué. Pour plus d’informations sur le suivi EF, consultez [suivi et requêtes de No-Tracking](/ef/core/querying/tracking).

`CourseViewModel` :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Pour obtenir un exemple complet, consultez [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) et [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).

## <a name="create-instructor-pages"></a>Créer des pages Instructor

Cette section génère automatiquement des modèles de pages Instructor et ajoute les cours et les inscriptions associés à la page d’index des formateurs.

<a name="IP"></a>
![Page d’index des formateurs](read-related-data/_static/instructors-index30.png)

Cette page lit et affiche les données associées comme suit :

* La liste des formateurs affiche des données associées de l’entité `OfficeAssignment` (Office dans l’image précédente). Il existe une relation un-à-zéro-ou-un entre les entités `Instructor` et `OfficeAssignment`. Le chargement hâtif est utilisé pour les entités `OfficeAssignment`. Le chargement hâtif est généralement plus efficace quand les données associées doivent être affichées. Ici, les affectations de bureau pour les formateurs sont affichées.
* Quand l’utilisateur sélectionne un formateur, les entités `Course` associées sont affichées. Il existe une relation plusieurs-à-plusieurs entre les entités `Instructor` et `Course`. Le chargement hâtif est utilisé pour les entités `Course` et leurs entités `Department` associées. Dans le cas présent, des requêtes distinctes peuvent être plus efficaces, car seuls les cours du formateur sélectionné sont nécessaires. Cet exemple montre comment utiliser le chargement hâtif pour des propriétés de navigation dans des entités qui se trouvent dans des propriétés de navigation.
* Quand l’utilisateur sélectionne un cours, les données associées de l’entité `Enrollments` s’affichent. Dans l’image précédente, le nom et la note de l’étudiant sont affichés. Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`.

### <a name="create-a-view-model"></a>Création d'un modèle de vue

La page sur les formateurs affiche les données de trois tables différentes. Un modèle de vue comprenant trois propriétés représentant les trois tables est nécessaire.

Créez *SchoolViewModels/InstructorIndexData.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Générer automatiquement des modèles de pages Instructor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Suivez les instructions dans [Générer automatiquement des modèles de pages Student](xref:data/ef-rp/intro#scaffold-student-pages) avec les exceptions suivantes :

  * Créez un dossier *Pages/Instructors* .
  * Utilisez `Instructor` pour la classe de modèle.
  * Utilisez la classe de contexte existante au lieu d’en créer une nouvelle.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Créez un dossier *Pages/Instructors* .

* Exécutez la commande suivante pour générer automatiquement des modèles de pages Instructor.

  **Sur Windows :**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **Sur Linux ou macOS :**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Pour voir à quoi ressemble la page générée automatiquement avant de la mettre à jour, exécutez l’application et accédez à la page Instructors.

Mettez à jour *pages/Instructors/index. cshtml. cs* avec le code suivant :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

La méthode `OnGetAsync` accepte des données de route facultatives pour l’ID du formateur sélectionné.

Examinez la requête dans le fichier *Pages/Instructors/Index.cshtml.cs*  :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

Le code spécifie un chargement hâtif pour les propriétés de navigation suivantes :

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Notez la répétition des méthodes `Include` et `ThenInclude` pour `CourseAssignments` et `Course`. Cette répétition est nécessaire pour spécifier un chargement hâtif pour deux propriétés de navigation de l’entité `Course`.

Le code suivant s’exécute quand un formateur est sélectionné (`id != null`).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Le formateur sélectionné est récupéré à partir de la liste des formateurs dans le modèle d’affichage. La propriété `Courses` du modèle d’affichage est chargée avec les entités `Course` de la propriété de navigation `CourseAssignments` de ce formateur.

La méthode `Where` retourne une collection. Toutefois, dans ce cas, le filtre sélectionne une entité unique, de sorte que la `Single` méthode est appelée pour convertir la collection en une seule `Instructor` entité. L’entité `Instructor` fournit l’accès à la propriété `CourseAssignments`. `CourseAssignments` fournit l’accès aux entités `Course` associées.

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

La méthode `Single` est utilisée sur une collection quand la collection ne compte qu’un seul élément. La méthode `Single` lève une exception si la collection est vide ou s’il y a plusieurs éléments. Une alternative est `SingleOrDefault`, qui renvoie une valeur par défaut (Null dans ce cas) si la collection est vide.

Le code suivant renseigne la propriété `Enrollments` du modèle d’affichage quand un cours est sélectionné :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Mettre à jour la page d’index des formateurs

Mettez à jour *Pages/Instructors/Index.cshtml* avec le code suivant.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Le code précédent apporte les modifications suivantes :

* Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int?}"`. `"{id:int?}"` est un modèle de route. Le modèle de route change les chaînes de requête entières dans l’URL en données de route. Par exemple, si vous cliquez sur le lien **Select** pour un formateur avec seulement la directive `@page`, une URL comme celle-ci est générée :

  `https://localhost:5001/Instructors?id=2`

  Quand la directive de page est `@page "{id:int?}"`, l’URL est :

  `https://localhost:5001/Instructors/2`

* Ajoute une colonne **Office** qui affiche `item.OfficeAssignment.Location` seulement si `item.OfficeAssignment` n’a pas la valeur Null. Comme il s’agit d’une relation un-à-zéro-ou-un, il se peut qu’il n’y ait pas d’entité OfficeAssignment associée.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Ajoute une colonne **Courses** qui affiche les cours animés par chaque formateur. Pour plus d’informations sur cette syntaxe Razor, consultez [transition de ligne explicite](xref:mvc/views/razor#explicit-line-transition) .

* Ajoute du code qui ajoute dynamiquement `class="success"` à l’élément `tr` du formateur et du cours sélectionnés. Cela définit une couleur d’arrière-plan pour la ligne sélectionnée à l’aide d’une classe d’amorçage.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Ajoute un nouveau lien hypertexte libellé **Select** . Ce lien envoie l’ID du formateur sélectionné à la méthode `Index`, et définit une couleur d’arrière-plan.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Ajoute un tableau de cours pour l’instructeur sélectionné.

* Ajoute un tableau d’inscriptions d’étudiants pour le cours sélectionné.

Exécutez l’application et sélectionnez l’onglet **Instructors** . La page affiche le `Location` (Office) à partir de l' `OfficeAssignment` entité associée. Si `OfficeAssignment` a la valeur Null, une cellule de tableau vide est affichée.

Cliquez sur le lien **Select** pour un formateur. Le style de ligne change et les cours attribués à ce formateur s’affichent.

Sélectionnez un cours pour afficher la liste des étudiants inscrits et leurs notes.

![Page d’index des formateurs avec un formateur et un cours sélectionnés](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Utilisation de Single

La méthode `Single` peut passer la condition `Where` au lieu d’appeler la méthode `Where` séparément :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

L’utilisation de `Single` avec une condition Where est une question de préférence personnelle. Elle n’offre pas d’avantages par rapport à la méthode `Where`.

## <a name="explicit-loading"></a>Chargement explicite

Le code actuel spécifie le chargement hâtif pour `Enrollments` et `Students` :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Supposez que les utilisateurs souhaitent rarement voir les inscriptions à un cours. Dans ce cas, une optimisation consisterait à charger les données d’inscription uniquement si elles sont demandées. Dans cette section, la méthode `OnGetAsync` est mise à jour de façon à utiliser le chargement explicite de `Enrollments` et `Students`.

Mettez à jour *Pages/Instructors/Index.cshtml.cs* avec le code suivant.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Le code précédent supprime les appels de méthode *ThenInclude* pour les données sur les inscriptions et les étudiants. Si un cours est sélectionné, le code de chargement explicite récupère :

* Les entités `Enrollment` pour le cours sélectionné.
* Les entités `Student` pour chaque `Enrollment`.

Notez que le code précédent commente `.AsNoTracking()`. Les propriétés de navigation peuvent être chargées explicitement uniquement pour les entités suivies.

Tester l'application. Du point de vue d’un utilisateur, l’application se comporte de façon identique à la version précédente.

## <a name="next-steps"></a>Étapes suivantes

Le didacticiel suivant montre comment mettre à jour les données associées.

>[!div class="step-by-step"]
>[Didacticiel précédent](xref:data/ef-rp/complex-data-model) 
> [Didacticiel suivant](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans ce didacticiel, nous allons lire et afficher des données associées. Les données associées sont des données qu’EF Core charge dans des propriétés de navigation.

Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, [téléchargez ou affichez l’application terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [Instructions de téléchargement](xref:index#how-to-download-a-sample).

Les illustrations suivantes montrent les pages terminées pour ce didacticiel :

![Page d’index des cours](read-related-data/_static/courses-index.png)

![Page d’index des formateurs](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Chargement hâtif, explicite et différé des données associées

EF Core peut charger des données associées dans les propriétés de navigation d’une entité de plusieurs manières :

* [Chargement hâtif](/ef/core/querying/related-data#eager-loading). Le chargement hâtif a lieu quand une requête pour un type d’entité charge également des entités associées. Quand l’entité est lue, ses données associées sont récupérées. Cela génère en général une requête de jointure unique qui récupère toutes les données nécessaires. EF Core émet plusieurs requêtes pour certains types de chargement hâtif. L’émission de requêtes multiples peut être plus efficace que ce n’était le cas pour certaines requêtes dans EF6 où une seule requête était émise. Le chargement hâtif est spécifié avec les méthodes `Include` et `ThenInclude`.

  ![Exemple de chargement hâtif](read-related-data/_static/eager-loading.png)
 
  Le chargement hâtif envoie plusieurs requêtes quand une navigation dans la collection est incluse :

  * Une requête pour la requête principale 
  * Une requête pour chaque « périmètre » de collection dans l’arborescence de la charge.

* Requêtes distinctes avec `Load` : les données peuvent être récupérées dans des requêtes distinctes, et EF Core « corrige » les propriétés de navigation. Le terme « corrige » signifie qu’EF Core renseigne automatiquement les propriétés de navigation. Les requêtes distinctes avec `Load` s’apparentent plus au chargement explicite qu’au chargement hâtif.

  ![Exemple de requêtes distinctes](read-related-data/_static/separate-queries.png)

  Remarque : EF Core corrige automatiquement les propriétés de navigation vers d’autres entités qui étaient précédemment chargées dans l’instance de contexte. Même si les données pour une propriété de navigation ne sont *pas* explicitement incluses, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.

* [Chargement explicite](/ef/core/querying/related-data#explicit-loading). Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées. Vous devez écrire du code pour récupérer les données associées en cas de besoin. En cas de chargement explicite avec des requêtes distinctes, plusieurs requêtes sont envoyées à la base de données. Avec le chargement explicite, le code spécifie les propriétés de navigation à charger. Utilisez la méthode `Load` pour effectuer le chargement explicite. Exemple :

  ![Exemple de chargement explicite](read-related-data/_static/explicit-loading.png)

* [Chargement différé](/ef/core/querying/related-data#lazy-loading). [Le chargement différé a été ajouté à EF Core dans la version 2.1](/ef/core/querying/related-data#lazy-loading). Quand l’entité est lue pour la première fois, les données associées ne sont pas récupérées. Lors du premier accès à une propriété de navigation, les données requises pour cette propriété de navigation sont récupérées automatiquement. Une requête est envoyée à la base de données chaque fois qu’une propriété de navigation est sollicitée pour la première fois.

* L’opérateur `Select` charge uniquement les données associées nécessaires.

## <a name="create-a-course-page-that-displays-department-name"></a>Créer une page Course qui affiche le nom du département

L’entité Course comprend une propriété de navigation qui contient l’entité `Department`. L’entité `Department` contient le département auquel le cours est affecté.

Pour afficher le nom du département affecté dans une liste de cours

* Obtenez la propriété `Name` à partir de l’entité `Department`.
* L’entité `Department` provient de la propriété de navigation `Course.Department`.

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Génération automatique du modèle Course

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Suivez les instructions fournies dans [Générer automatiquement le modèle d’étudiant](xref:data/ef-rp/intro#scaffold-the-student-model) et utilisez `Course` pour la classe de modèle.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

La commande précédente génère automatiquement le modèle `Course`. Ouvrez le projet dans Visual Studio.

Ouvrez *Pages/Courses/Index.cshtml.cs* et examinez la méthode `OnGetAsync`. Le moteur de génération de modèles automatique a spécifié le chargement hâtif pour la propriété de navigation `Department`. La méthode `Include` spécifie le chargement hâtif.

Exécutez l’application et sélectionnez le lien **Courses** . La colonne Department affiche le `DepartmentID`, ce qui n’est pas utile.

Mettez à jour la méthode `OnGetAsync` avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Le code précédent ajoute `AsNoTracking`. `AsNoTracking` améliore les performances, car les entités retournées ne sont pas suivies. Les entités ne sont pas suivies, car elles ne sont pas mises à jour dans le contexte actuel.

Mettez à jour *Pages/Courses/Index.cshtml* avec le balisage en surbrillance suivant :

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Les modifications suivantes ont été apportées au code généré automatiquement :

* Changement de l’en-tête : Index a été remplacé par Courses.
* Ajout d’une colonne **Number** qui affiche la valeur de la propriété `CourseID`. Par défaut, les clés primaires ne sont pas générées automatiquement, car elles ne sont normalement pas significatives pour les utilisateurs finaux. Toutefois, dans le cas présent la clé primaire est significative.
* Modification de la colonne **Department** afin d’afficher le nom du département. Le code affiche la propriété `Name` de l’entité `Department` qui est chargée dans la propriété de navigation `Department` :

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Exécutez l’application et sélectionnez l’onglet **Courses** pour afficher la liste avec les noms des départements.

![Page d’index des cours](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Chargement de données associées avec Select

La méthode `OnGetAsync` charge les données associées avec la méthode `Include` :

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

L’opérateur `Select` charge uniquement les données associées nécessaires. Pour les éléments uniques, comme `Department.Name`, il utilise une jointure interne SQL. Pour les collections, il utilise un autre accès à la base de données, mais c’est aussi le cas de l’opérateur `Include` sur les collections.

Le code suivant charge les données associées avec la méthode `Select` :

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel` :

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Pour obtenir un exemple complet, consultez [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) et [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Créer une page Instructors qui affiche les cours et les inscriptions

Dans cette section, nous allons créer la page Instructors.

<a name="IP"></a>
![Page d’index des formateurs](read-related-data/_static/instructors-index.png)

Cette page lit et affiche les données associées comme suit :

* La liste des formateurs affiche des données associées de l’entité `OfficeAssignment` (Office dans l’image précédente). Il existe une relation un-à-zéro-ou-un entre les entités `Instructor` et `OfficeAssignment`. Le chargement hâtif est utilisé pour les entités `OfficeAssignment`. Le chargement hâtif est généralement plus efficace quand les données associées doivent être affichées. Ici, les affectations de bureau pour les formateurs sont affichées.
* Quand l’utilisateur sélectionne un formateur (Harui dans l’image précédente), les entités `Course` associées sont affichées. Il existe une relation plusieurs-à-plusieurs entre les entités `Instructor` et `Course`. Le chargement hâtif est utilisé pour les entités `Course` et leurs entités `Department` associées. Dans le cas présent, des requêtes distinctes peuvent être plus efficaces, car seuls les cours du formateur sélectionné sont nécessaires. Cet exemple montre comment utiliser le chargement hâtif pour des propriétés de navigation dans des entités qui se trouvent dans des propriétés de navigation.
* Quand l’utilisateur sélectionne un cours (Chemistry dans l’image précédente), les données associées de l’entité `Enrollments` sont affichées. Dans l’image précédente, le nom et la note de l’étudiant sont affichés. Il existe une relation un-à-plusieurs entre les entités `Course` et `Enrollment`.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Créer un modèle de vue pour la vue d’index des formateurs

La page sur les formateurs affiche les données de trois tables différentes. Nous allons créer un modèle d’affichage qui comprend les trois entités représentant les trois tables.

Dans le dossier *SchoolViewModels* , créez *InstructorIndexData.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Générer automatiquement le modèle Instructor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Suivez les instructions fournies dans [Générer automatiquement le modèle d’étudiant](xref:data/ef-rp/intro#scaffold-the-student-model) et utilisez `Instructor` pour la classe de modèle.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

La commande précédente génère automatiquement le modèle `Instructor`. 
Exécutez l’application et accédez à la page des formateurs.

Remplacez *Pages/Instructors/Index.cshtml.cs* par le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

La méthode `OnGetAsync` accepte des données de route facultatives pour l’ID du formateur sélectionné.

Examinez la requête dans le fichier *Pages/Instructors/Index.cshtml.cs*  :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

La requête comporte deux Include :

* `OfficeAssignment` : affiché dans l’[affichage Instructors](#IP).
* `CourseAssignments` : qui affiche les cours dispensés.

### <a name="update-the-instructors-index-page"></a>Mettre à jour la page d’index des formateurs

Mettez à jour *Pages/Instructors/Index.cshtml* avec le balisage suivant :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Le balisage précédent apporte les modifications suivantes :

* Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int?}"`. `"{id:int?}"` est un modèle de route. Le modèle de route change les chaînes de requête entières dans l’URL en données de route. Par exemple, si vous cliquez sur le lien **Select** pour un formateur avec seulement la directive `@page`, une URL comme celle-ci est générée :

  `http://localhost:1234/Instructors?id=2`

  Quand la directive de page est `@page "{id:int?}"`, l’URL précédente est :

  `http://localhost:1234/Instructors/2`

* Le titre de la page est **Instructors** .
* Il ajoute une colonne **Office** qui affiche `item.OfficeAssignment.Location` uniquement si `item.OfficeAssignment` n’est pas Null. Comme il s’agit d’une relation un-à-zéro-ou-un, il se peut qu’il n’y ait pas d’entité OfficeAssignment associée.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Vous avez ajouté une colonne **Courses** qui affiche les cours animés par chaque formateur. Pour plus d’informations sur cette syntaxe Razor, consultez [transition de ligne explicite](xref:mvc/views/razor#explicit-line-transition) .

* Vous avez ajouté un code qui ajoute dynamiquement `class="success"` à l’élément `tr` du formateur sélectionné. Cela définit une couleur d’arrière-plan pour la ligne sélectionnée à l’aide d’une classe d’amorçage.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Ajout d’un nouveau lien hypertexte libellé **Select** . Ce lien envoie l’ID du formateur sélectionné à la méthode `Index`, et définit une couleur d’arrière-plan.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Exécutez l’application et sélectionnez l’onglet **Instructors** . La page affiche le `Location` (Office) à partir de l' `OfficeAssignment` entité associée. Si OfficeAssignment` est Null, une cellule de table vide est affichée.

Cliquez sur le lien **Select** . Le style de ligne change.

### <a name="add-courses-taught-by-selected-instructor"></a>Ajouter les cours dispensés par le formateur sélectionné

Mettez à jour la méthode `OnGetAsync` dans *Pages/Instructors/Index.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Ajouter `public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Examinez la requête mise à jour :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

La requête précédente ajoute les entités `Department`.

Le code suivant s’exécute quand un formateur est sélectionné (`id != null`). Le formateur sélectionné est récupéré à partir de la liste des formateurs dans le modèle d’affichage. La propriété `Courses` du modèle d’affichage est chargée avec les entités `Course` de la propriété de navigation `CourseAssignments` de ce formateur.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

La méthode `Where` retourne une collection. Dans la méthode `Where` précédente, une seule entité `Instructor` est retournée. La méthode `Single` convertit la collection en une seule entité `Instructor`. L’entité `Instructor` fournit l’accès à la propriété `CourseAssignments`. `CourseAssignments` fournit l’accès aux entités `Course` associées.

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

La méthode `Single` est utilisée sur une collection quand la collection ne compte qu’un seul élément. La méthode `Single` lève une exception si la collection est vide ou s’il y a plusieurs éléments. Une alternative est `SingleOrDefault`, qui renvoie une valeur par défaut (Null dans ce cas) si la collection est vide. L’utilisation de `SingleOrDefault` sur une collection vide :

* Génère une exception (à cause de la tentative de trouver une propriété `Courses` sur une référence Null).
* Le message d’exception indique moins clairement la cause du problème.

Le code suivant renseigne la propriété `Enrollments` du modèle d’affichage quand un cours est sélectionné :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Ajoutez le balisage suivant à la fin de la page *pages/Instructors/index. cshtml* Razor :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Le balisage précédent affiche une liste de cours associés à un formateur quand un formateur est sélectionné.

Tester l'application. Cliquez sur un lien **Select** dans la page des formateurs.

### <a name="show-student-data"></a>Afficher les données sur les étudiants

Dans cette section, nous allons mettre à jour l’application afin d’afficher les données sur les étudiants pour le cours sélectionné.

Mettez à jour la requête dans la méthode `OnGetAsync` dans *Pages/Instructors/Index.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Mettez à jour *Pages/Instructors/Index.cshtml* . Ajoutez le balisage suivant à la fin du fichier :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Le balisage précédent affiche une liste des étudiants qui sont inscrits au cours sélectionné.

Actualisez la page et sélectionnez un formateur. Sélectionnez un cours pour afficher la liste des étudiants inscrits et leurs notes.

![Page d’index des formateurs avec un formateur et un cours sélectionnés](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Utilisation de Single

La méthode `Single` peut passer la condition `Where` au lieu d’appeler la méthode `Where` séparément :

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

L’approche précédente faisant appel à `Single` ne présente aucun avantage par rapport à l’utilisation de `Where`. Certains développeurs préfèrent le style d’approche `Single`.

## <a name="explicit-loading"></a>Chargement explicite

Le code actuel spécifie le chargement hâtif pour `Enrollments` et `Students` :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Supposez que les utilisateurs souhaitent rarement voir les inscriptions à un cours. Dans ce cas, une optimisation consisterait à charger les données d’inscription uniquement si elles sont demandées. Dans cette section, la méthode `OnGetAsync` est mise à jour de façon à utiliser le chargement explicite de `Enrollments` et `Students`.

Mettez à jour la méthode `OnGetAsync` avec le code suivant :

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Le code précédent supprime les appels de méthode *ThenInclude* pour les données sur les inscriptions et les étudiants. Si un cours est sélectionné, le code en surbrillance récupère :

* Les entités `Enrollment` pour le cours sélectionné.
* Les entités `Student` pour chaque `Enrollment`.

Notez que le code précédent commente `.AsNoTracking()`. Les propriétés de navigation peuvent être chargées explicitement uniquement pour les entités suivies.

Tester l'application. Du point de vue des utilisateurs, l’application se comporte comme la version précédente.

Le didacticiel suivant montre comment mettre à jour les données associées.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel (partie 1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Version YouTube de ce tutoriel (partie 2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Précédent](xref:data/ef-rp/complex-data-model) 
> [Suivant](xref:data/ef-rp/update-related-data)

::: moniker-end
