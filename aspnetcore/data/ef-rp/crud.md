---
title: Partie 2, Razor pages avec EF Core dans ASP.net Core-CRUD
author: rick-anderson
description: Partie 2 de Razor pages et Entity Framework série de didacticiels.
ms.author: riande
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
uid: data/ef-rp/crud
ms.openlocfilehash: 4a48fb094888d51aa6f881c82e4f20ffbc84c8e2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901169"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a>Partie 2, Razor pages avec EF Core dans ASP.net Core-CRUD

Par [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) et [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

Dans ce didacticiel, nous allons examiner et personnaliser le code CRUD (créer, lire, mettre à jour, supprimer) généré automatiquement.

## <a name="no-repository"></a>Aucun référentiel

Certains développeurs utilisent une couche de service ou un modèle de référentiel pour créer une couche d’abstraction entre l’interface utilisateur ( Razor pages) et la couche d’accès aux données. Ce n’est pas le cas de ce tutoriel. Pour que ce tutoriel soit moins complexe et traite exclusivement d’EF Core, le code EF Core est directement ajouté aux classes de modèle de page. 

## <a name="update-the-details-page"></a>Mettre à jour la page Details

Le code généré automatiquement pour les pages Students n’inclut pas les données d’inscription (« enrollment »). Dans cette section, vous allez ajouter des inscriptions à la page Details.

### <a name="read-enrollments"></a>Lire les inscriptions

Pour afficher les données d’inscription d’un étudiant dans la page, vous devez les lire. Le code généré automatiquement dans *Pages/Students/Details.cshtml.cs* lit uniquement les données d’étudiant, sans les données d’inscription :

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Remplacez la méthode `OnGetAsync` par le code suivant pour lire les données d’inscription de l’étudiant sélectionné. Les modifications sont mises en surbrillance.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

Les méthodes [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) et [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) forcent le contexte à charger la propriété de navigation `Student.Enrollments` et, dans chaque inscription, la propriété de navigation `Enrollment.Course`. Ces méthodes sont examinées en détail dans le didacticiel [lire les données associées](xref:data/ef-rp/read-related-data) .

La méthode [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) améliore les performances dans les scénarios où les entités retournées ne sont pas mises à jour dans le contexte actuel. Le sujet `AsNoTracking` est abordé plus loin dans ce didacticiel.

### <a name="display-enrollments"></a>Afficher les inscriptions

Remplacez le code dans *Pages/Students/Details.cshtml* par le code suivant pour afficher une liste d’inscriptions. Les modifications sont mises en surbrillance.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Le code précédent effectue une itération sur les entités dans la propriété de navigation `Enrollments`. Pour chaque inscription, il affiche le titre du cours et le niveau. Le titre du cours est récupéré à partir de l’entité de cours qui est stockée dans la propriété de navigation `Course` de l’entité Enrollments.

Exécutez l’application, sélectionnez l’onglet **Students**, puis cliquez sur le lien **Details** pour un étudiant. La liste des cours et les notes de l’étudiant sélectionné s’affiche.

### <a name="ways-to-read-one-entity"></a>Méthodes pour lire une entité

Le code généré utilise [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) pour lire une entité. Cette méthode retourne la valeur Null si rien n’est trouvé ; sinon, elle retourne la première ligne trouvée qui répond aux critères de filtre de requête. `FirstOrDefaultAsync` est généralement un meilleur choix que les autres solutions suivantes :

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) – Lève une exception si plusieurs entités répondent au filtre de requête. Pour déterminer si plusieurs lignes peuvent être retournées par la requête, `SingleOrDefaultAsync` tente de récupérer plusieurs lignes. Ce travail supplémentaire est inutile si la requête ne peut retourner qu’une seule entité, comme quand elle effectue une recherche sur une clé unique.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) – Recherche une entité avec la clé primaire. Si une entité avec la clé primaire est suivie par le contexte, elle est retournée sans qu’aucune requête soit envoyée à la base de données. Cette méthode est optimisée pour la recherche d’une seule entité, mais vous ne pouvez pas appeler `Include` avec `FindAsync`.  Par conséquent, si des données associées sont nécessaires, `FirstOrDefaultAsync` est le meilleur choix.

### <a name="route-data-vs-query-string"></a>Données de route/chaîne de requête

L’URL de la page Details est `https://localhost:<port>/Students/Details?id=1`. La valeur de clé primaire de l’entité se trouve dans la chaîne de requête. Certains développeurs préfèrent passer la valeur de clé dans des données de route : `https://localhost:<port>/Students/Details/1`. Pour plus d'informations, consultez [Mettre à jour le code généré](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Mettre à jour la page Create

Le code `OnPostAsync` généré automatiquement pour la page Create est vulnérable aux [sur-publications](#overposting). Remplacez la méthode `OnPostAsync` dans *Pages/Students/Create.cshtml.cs* par le code suivant.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Le code précédent crée un objet Student, puis utilise des champs de formulaire publiés pour mettre à jour les propriétés de l’objet Student. La méthode [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) :

* Utilise les valeurs de formulaire publiées de la propriété [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) de [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).
* Met à jour uniquement les propriétés listées (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).
* Recherche les champs de formulaire dotés d’un préfixe « Student ». Par exemple : `Student.FirstMidName`. Il ne respecte pas la casse.
* Utilise le système de [liaison de modèles](xref:mvc/models/model-binding) pour convertir les valeurs de formulaire de chaînes en types dans le modèle `Student`. Par exemple, `EnrollmentDate` est converti en `DateTime` .

Exécutez l’application, puis créez une entité Student pour tester la page Create.

## <a name="overposting"></a>Sur-publication

L’utilisation de `TryUpdateModel` pour mettre à jour des champs avec des valeurs publiées est une bonne pratique de sécurité, car cela empêche la sur-publication. Par exemple, supposez que l’entité Student comprend une propriété `Secret` que cette page web ne doit pas mettre à jour ou ajouter :

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Même si l’application n’a pas de `Secret` champ dans la page de création ou de mise à jour Razor , un pirate peut définir la `Secret` valeur par survalidation. Un pirate pourrait utiliser un outil tel que Fiddler, ou écrire du JavaScript, pour publier une valeur de formulaire `Secret`. Le code d’origine ne limite pas les champs que le classeur de modèles utilise quand il crée une instance de Student.

La valeur spécifiée par le pirate pour le champ de formulaire `Secret`, quelle qu’elle soit, est mise à jour dans la base de données. L’illustration suivante montre l’outil Fiddler ajoutant le `Secret` champ, avec la valeur « surpost », aux valeurs de formulaire publiées.

![Fiddler ajoutant un champ Secret](../ef-mvc/crud/_static/fiddler.png)

La valeur « OverPost » est ajoutée avec succès à la propriété `Secret` de la ligne insérée. Cela se produit même si le concepteur de l’application n’avait jamais prévu que la propriété `Secret` serait définie avec la page Create.

### <a name="view-model"></a>Afficher le modèle

Les modèles d’affichage fournissent une alternative pour empêcher la sur-publication.

Le modèle d’application est souvent appelé modèle de domaine. En règle générale, le modèle de domaine contient toutes les propriétés requises par l’entité correspondante dans la base de données. Le modèle de vue contient uniquement les propriétés nécessaires à la page de l’interface utilisateur, par exemple, la page créer.

Outre le modèle de vue, certaines applications utilisent un modèle de liaison ou un modèle d’entrée pour passer des données entre la Razor classe de modèle de page pages et le navigateur. 

Considérez le modèle d’affichage `StudentVM` suivant :

[!code-csharp[Main](intro/samples/cu50/ViewModels/StudentVM.cs?name=snippet)]

Le code suivant utilise le modèle d’affichage `StudentVM` pour créer un étudiant :

[!code-csharp[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml.cs?name=snippet)]

La méthode [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) définit les valeurs de cet objet en lisant les valeurs d’un autre objet [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues). `SetValues` utilise la correspondance de nom de propriété. Type de modèle de vue :

* N’a pas besoin d’être lié au type de modèle.
* Doit avoir des propriétés qui correspondent.

L’utilisation `StudentVM` de requiert l’utilisation de la page Create `StudentVM` au lieu de `Student` :

[!code-cshtml[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml)]

## <a name="update-the-edit-page"></a>Mettre à jour la page Edit

Dans *Pages/Students/Edit.cshtml.cs*, remplacez les méthodes `OnGetAsync` et `OnPostAsync` par le code suivant.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Les modifications de code sont semblables à celles de la page Create, à quelques exceptions près :

* `FirstOrDefaultAsync` a été remplacée par [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). Quand vous n’êtes pas tenu d’inclure des données associées, `FindAsync` est plus efficace.
* `OnPostAsync` contient un paramètre `id`.
* Plutôt que de créer un étudiant vide, l’étudiant actuel est récupéré à partir de la base de données.

Exécutez l’application et testez-la en créant et modifiant un étudiant.

## <a name="entity-states"></a>États des entités

Le contexte de base de données effectue un suivi pour déterminer si les entités en mémoire sont synchronisées avec les lignes correspondantes de la base de données. Ces informations de suivi déterminent ce qui se passe quand [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) est appelé. Par exemple, quand une nouvelle entité est passée à la méthode [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync), l’état de cette entité est défini sur [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Lorsque `SaveChangesAsync` est appelé, le contexte de base de données émet une `INSERT` commande SQL.

Une entité peut être dans l’un des [États suivants](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: L’entité n’existe pas encore dans la base de données. La `SaveChanges` méthode émet une `INSERT` instruction.

* `Unchanged` : Aucune modification ne doit être enregistrée avec cette entité. Une entité est dans cet état quand elle est lue à partir de la base de données.

* `Modified` : Tout ou une partie des valeurs de propriété de l’entité ont été modifiées. La `SaveChanges` méthode émet une `UPDATE` instruction.

* `Deleted` : L’entité a été marquée pour suppression. La `SaveChanges` méthode émet une `DELETE` instruction.

* `Detached`: L’entité n’est pas suivie par le contexte de base de données.

Dans une application de bureau, les changements d’état sont généralement définis automatiquement. Une entité est lue, des modifications sont apportées et l’état d’entité passe automatiquement à `Modified`. `SaveChanges`L’appel de génère une `UPDATE` instruction SQL qui met à jour uniquement les propriétés modifiées.

Dans une application web, le `DbContext` qui lit une entité et affiche les données est supprimé après le rendu d’une page. Quand la méthode `OnPostAsync` d’une page est appelée, une nouvelle requête web est faite avec une nouvelle instance du `DbContext`. La relecture de l’entité dans ce nouveau contexte simule le traitement de bureau.

## <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Dans cette section, un message d’erreur personnalisé est implémenté lorsque l’appel à `SaveChanges` échoue.

Remplacez le code dans *Pages/Students/Delete.cshtml.cs* par le code suivant. Les modifications apparaissent en surbrillance :

[!code-csharp[Main](intro/samples/cu50/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=12-14,22,30-33,45-99)]

Le code précédent ajoute le paramètre facultatif `saveChangesError` à la signature de méthode `OnGetAsync`. `saveChangesError` indique si la méthode a été appelée après un échec de suppression de l’objet Student. L’opération de suppression peut échouer en raison de problèmes réseau temporaires. Vous avez plus de chances de rencontrer des erreurs réseau temporaires quand la base de données est dans le cloud. Le `saveChangesError` paramètre est `false` lorsque la page `OnGetAsync` de suppression est appelée à partir de l’interface utilisateur. Lorsque `OnGetAsync` est appelé par, `OnPostAsync` car l’opération de suppression a échoué, le `saveChangesError` paramètre est `true` .

La méthode `OnPostAsync` récupère l’entité sélectionnée, puis appelle la méthode [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) pour définir l’état de l’entité sur `Deleted`. Lorsque `SaveChanges` la méthode est appelée, une `DELETE` commande SQL est générée. Si `Remove` échoue :

* L’exception de la base de données est interceptée.
* La méthode `OnGetAsync` des pages est appelée avec `saveChangesError=true`.

Ajoutez un message d’erreur à *pages/élèves/Delete. cshtml*:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Exécutez l’application et supprimez un étudiant pour tester la page Delete.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Didacticiel précédent](xref:data/ef-rp/intro) 
>  [Didacticiel suivant](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Dans ce didacticiel, nous allons examiner et personnaliser le code CRUD (créer, lire, mettre à jour, supprimer) généré automatiquement.

## <a name="no-repository"></a>Aucun référentiel

Certains développeurs utilisent une couche de service ou un modèle de référentiel pour créer une couche d’abstraction entre l’interface utilisateur ( Razor pages) et la couche d’accès aux données. Ce n’est pas le cas de ce tutoriel. Pour que ce tutoriel soit moins complexe et traite exclusivement d’EF Core, le code EF Core est directement ajouté aux classes de modèle de page. 

## <a name="update-the-details-page"></a>Mettre à jour la page Details

Le code généré automatiquement pour les pages Students n’inclut pas les données d’inscription (« enrollment »). Dans cette section, les inscriptions sont ajoutées à la page de détails.

### <a name="read-enrollments"></a>Lire les inscriptions

Pour afficher les données d’inscription d’un étudiant sur la page, vous devez lire les données d’inscription. Le code généré automatiquement dans *Pages/Students/Details.cshtml.cs* lit uniquement les données d’étudiant, sans les données d’inscription :

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Remplacez la méthode `OnGetAsync` par le code suivant pour lire les données d’inscription de l’étudiant sélectionné. Les modifications sont mises en surbrillance.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

Les méthodes [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) et [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) forcent le contexte à charger la propriété de navigation `Student.Enrollments` et, dans chaque inscription, la propriété de navigation `Enrollment.Course`. Ces méthodes sont examinées en détail dans le tutoriel [Lecture de données associées](xref:data/ef-rp/read-related-data).

La méthode [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) améliore les performances dans les scénarios où les entités retournées ne sont pas mises à jour dans le contexte actuel. Le sujet `AsNoTracking` est abordé plus loin dans ce didacticiel.

### <a name="display-enrollments"></a>Afficher les inscriptions

Remplacez le code dans *Pages/Students/Details.cshtml* par le code suivant pour afficher une liste d’inscriptions. Les modifications sont mises en surbrillance.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Le code précédent effectue une itération sur les entités dans la propriété de navigation `Enrollments`. Pour chaque inscription, il affiche le titre du cours et le niveau. Le titre du cours est récupéré à partir de l’entité de cours qui est stockée dans la propriété de navigation `Course` de l’entité Enrollments.

Exécutez l’application, sélectionnez l’onglet **Students**, puis cliquez sur le lien **Details** pour un étudiant. La liste des cours et les notes de l’étudiant sélectionné s’affiche.

### <a name="ways-to-read-one-entity"></a>Méthodes pour lire une entité

Le code généré utilise [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) pour lire une entité. Cette méthode retourne la valeur Null si rien n’est trouvé ; sinon, elle retourne la première ligne trouvée qui répond aux critères de filtre de requête. `FirstOrDefaultAsync` est généralement un meilleur choix que les autres solutions suivantes :

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) – Lève une exception si plusieurs entités répondent au filtre de requête. Pour déterminer si plusieurs lignes peuvent être retournées par la requête, `SingleOrDefaultAsync` tente de récupérer plusieurs lignes. Ce travail supplémentaire est inutile si la requête ne peut retourner qu’une seule entité, comme quand elle effectue une recherche sur une clé unique.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) – Recherche une entité avec la clé primaire. Si une entité avec la clé primaire est suivie par le contexte, elle est retournée sans qu’aucune requête soit envoyée à la base de données. Cette méthode est optimisée pour la recherche d’une seule entité, mais vous ne pouvez pas appeler `Include` avec `FindAsync`.  Par conséquent, si des données associées sont nécessaires, `FirstOrDefaultAsync` est le meilleur choix.

### <a name="route-data-vs-query-string"></a>Données de route/chaîne de requête

L’URL de la page Details est `https://localhost:<port>/Students/Details?id=1`. La valeur de clé primaire de l’entité se trouve dans la chaîne de requête. Certains développeurs préfèrent passer la valeur de clé dans des données de route : `https://localhost:<port>/Students/Details/1`. Pour plus d'informations, consultez [Mettre à jour le code généré](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Mettre à jour la page Create

Le code `OnPostAsync` généré automatiquement pour la page Create est vulnérable aux [sur-publications](#overposting). Remplacez la méthode `OnPostAsync` dans *Pages/Students/Create.cshtml.cs* par le code suivant.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Le code précédent crée un objet Student, puis utilise des champs de formulaire publiés pour mettre à jour les propriétés de l’objet Student. La méthode [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) :

* Utilise les valeurs de formulaire publiées de la propriété [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) de [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).
* Met à jour uniquement les propriétés listées (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).
* Recherche les champs de formulaire dotés d’un préfixe « Student ». Par exemple : `Student.FirstMidName`. Il ne respecte pas la casse.
* Utilise le système de [liaison de modèles](xref:mvc/models/model-binding) pour convertir les valeurs de formulaire de chaînes en types dans le modèle `Student`. Par exemple, `EnrollmentDate` doit être converti en DateTime.

Exécutez l’application, puis créez une entité Student pour tester la page Create.

## <a name="overposting"></a>Sur-publication

L’utilisation de `TryUpdateModel` pour mettre à jour des champs avec des valeurs publiées est une bonne pratique de sécurité, car cela empêche la sur-publication. Par exemple, supposez que l’entité Student comprend une propriété `Secret` que cette page web ne doit pas mettre à jour ou ajouter :

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Même si l’application n’a pas de `Secret` champ dans la page de création ou de mise à jour Razor , un pirate peut définir la `Secret` valeur par survalidation. Un pirate pourrait utiliser un outil tel que Fiddler, ou écrire du JavaScript, pour publier une valeur de formulaire `Secret`. Le code d’origine ne limite pas les champs que le classeur de modèles utilise quand il crée une instance de Student.

La valeur spécifiée par le pirate pour le champ de formulaire `Secret`, quelle qu’elle soit, est mise à jour dans la base de données. L’illustration suivante montre l’outil Fiddler en train d’ajouter le champ `Secret` (avec la valeur « OverPost ») aux valeurs du formulaire envoyé.

![Fiddler ajoutant un champ Secret](../ef-mvc/crud/_static/fiddler.png)

La valeur « OverPost » est ajoutée avec succès à la propriété `Secret` de la ligne insérée. Cela se produit même si le concepteur de l’application n’avait jamais prévu que la propriété `Secret` serait définie avec la page Create.

### <a name="view-model"></a>Afficher le modèle

Les modèles d’affichage fournissent une alternative pour empêcher la sur-publication.

Le modèle d’application est souvent appelé modèle de domaine. En règle générale, le modèle de domaine contient toutes les propriétés requises par l’entité correspondante dans la base de données. Le modèle de vue contient uniquement les propriétés nécessaires à l’interface utilisateur pour laquelle il est utilisé (par exemple, la page Create).

Outre le modèle de vue, certaines applications utilisent un modèle de liaison ou un modèle d’entrée pour passer des données entre la Razor classe de modèle de page pages et le navigateur. 

Considérez le modèle d’affichage `Student` suivant :

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

Le code suivant utilise le modèle d’affichage `StudentVM` pour créer un étudiant :

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

La méthode [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) définit les valeurs de cet objet en lisant les valeurs d’un autre objet [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues). `SetValues` utilise la correspondance de nom de propriété. Le type de modèle d’affichage ne doit pas nécessairement être lié au type de modèle. Il doit simplement avoir des propriétés qui correspondent.

L’utilisation de `StudentVM` exige que [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) soit mis à jour pour utiliser `StudentVM` plutôt que `Student`.

## <a name="update-the-edit-page"></a>Mettre à jour la page Edit

Dans *Pages/Students/Edit.cshtml.cs*, remplacez les méthodes `OnGetAsync` et `OnPostAsync` par le code suivant.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Les modifications de code sont semblables à celles de la page Create, à quelques exceptions près :

* `FirstOrDefaultAsync` a été remplacée par [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). Lorsque les données associées incluses ne sont pas nécessaires, `FindAsync` est plus efficace.
* `OnPostAsync` contient un paramètre `id`.
* Plutôt que de créer un étudiant vide, l’étudiant actuel est récupéré à partir de la base de données.

Exécutez l’application et testez-la en créant et modifiant un étudiant.

## <a name="entity-states"></a>États des entités

Le contexte de base de données effectue un suivi pour déterminer si les entités en mémoire sont synchronisées avec les lignes correspondantes de la base de données. Ces informations de suivi déterminent ce qui se passe quand [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) est appelé. Par exemple, quand une nouvelle entité est passée à la méthode [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync), l’état de cette entité est défini sur [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Quand `SaveChangesAsync` est appelé, le contexte de base de données émet une commande SQL INSERT.

Une entité peut être dans l’un des [États suivants](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: L’entité n’existe pas encore dans la base de données. La méthode `SaveChanges` émet une instruction INSERT.

* `Unchanged` : Aucune modification ne doit être enregistrée avec cette entité. Une entité est dans cet état quand elle est lue à partir de la base de données.

* `Modified` : Tout ou une partie des valeurs de propriété de l’entité ont été modifiées. La méthode `SaveChanges` émet une instruction UPDATE.

* `Deleted` : L’entité a été marquée pour suppression. La méthode `SaveChanges` émet une instruction DELETE.

* `Detached`: L’entité n’est pas suivie par le contexte de base de données.

Dans une application de bureau, les changements d’état sont généralement définis automatiquement. Une entité est lue, des modifications sont apportées et l’état d’entité passe automatiquement à `Modified`. L’appel de `SaveChanges` génère une instruction SQL UPDATE qui met à jour uniquement les propriétés modifiées.

Dans une application web, le `DbContext` qui lit une entité et affiche les données est supprimé après le rendu d’une page. Quand la méthode `OnPostAsync` d’une page est appelée, une nouvelle requête web est faite avec une nouvelle instance du `DbContext`. La relecture de l’entité dans ce nouveau contexte simule le traitement de bureau.

## <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Dans cette section, vous allez implémenter un message d’erreur personnalisé quand l’appel à `SaveChanges` échoue.

Remplacez le code dans *Pages/Students/Delete.cshtml.cs* par le code suivant. Les modifications (autres que le nettoyage des instructions `using`) sont mises en surbrillance.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

Le code précédent ajoute le paramètre facultatif `saveChangesError` à la signature de méthode `OnGetAsync`. `saveChangesError` indique si la méthode a été appelée après un échec de suppression de l’objet Student. L’opération de suppression peut échouer en raison de problèmes réseau temporaires. Vous avez plus de chances de rencontrer des erreurs réseau temporaires quand la base de données est dans le cloud. Le paramètre `saveChangesError` a la valeur false quand la méthode `OnGetAsync` de la page Delete est appelée à partir de l’interface utilisateur. Quand `OnGetAsync` est appelée par `OnPostAsync` (car l’opération de suppression a échoué), le paramètre `saveChangesError` a la valeur true.

La méthode `OnPostAsync` récupère l’entité sélectionnée, puis appelle la méthode [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) pour définir l’état de l’entité sur `Deleted`. Lorsque `SaveChanges` est appelée, une commande SQL DELETE est générée. Si `Remove` échoue :

* L’exception de la base de données est interceptée.
* La méthode de la page de suppression `OnGetAsync` est appelée avec `saveChangesError=true` .

Ajoutez un message d’erreur à la Razor page de suppression (*pages/élèves/Delete. cshtml*) :

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Exécutez l’application et supprimez un étudiant pour tester la page Delete.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Didacticiel précédent](xref:data/ef-rp/intro) 
>  [Didacticiel suivant](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans ce didacticiel, nous allons examiner et personnaliser le code CRUD (créer, lire, mettre à jour, supprimer) généré automatiquement.

Pour que ces tutoriels soient moins complexes et traitent exclusivement d’EF Core, nous avons utilisé le code EF Core dans les modèles de page. Certains développeurs utilisent une couche de service ou un modèle de référentiel dans pour créer une couche d’abstraction entre l’interface utilisateur ( Razor pages) et la couche d’accès aux données.

Dans ce didacticiel, les pages créer, modifier, supprimer et Détails du Razor dossier *students* sont examinées.

Le code généré automatiquement utilise le modèle suivant pour les pages Create, Edit et Delete :

* Obtenir et afficher les données demandées avec la méthode HTTP GET `OnGetAsync`.
* Enregistrer les modifications dans les données avec la méthode HTTP POST `OnPostAsync`.

Les pages Index et Details obtiennent et affichent les données demandées avec la méthode HTTP GET `OnGetAsync`.

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync et FirstOrDefaultAsync

Le code généré utilise [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), qui est généralement préféré à [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

 `FirstOrDefaultAsync` est plus efficace que `SingleOrDefaultAsync` pour récupérer une entité :

* Sauf si le code doit vérifier que la requête ne retourne pas plusieurs entités.
* `SingleOrDefaultAsync` récupère davantage de données et effectue un travail inutile.
* `SingleOrDefaultAsync` lève une exception si plusieurs entités correspondent à la partie filtre.
* `FirstOrDefaultAsync` ne lève pas d’exception si plusieurs entités correspondent à la partie filtre.

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

Dans une grande partie du code généré automatiquement, vous pouvez utiliser [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) à la place de `FirstOrDefaultAsync`.

`FindAsync`:

* Recherche une entité avec la clé primaire. Si une entité avec la clé primaire est suivie par le contexte, elle est retournée sans qu’une requête soit envoyée à la base de données.
* Est simple et concise.
* Est optimisée pour rechercher une entité unique.
* Peut avoir des avantages de performances dans certaines situations, mais c’est rarement le cas pour les applications web standard.
* Utilise implicitement [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) au lieu de [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

Toutefois, si vous voulez exécuter `Include` sur d’autres entités, `FindAsync` n’est plus approprié. Cela signifie que vous devez peut-être abandonner `FindAsync` et passer à une requête à mesure que votre application progresse.

## <a name="customize-the-details-page"></a>Personnaliser la page Details

Accédez à la page `Pages/Students`. Les liens **Edit**, **Details**, et **Delete** sont générés par le [Tag helper anchor](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dans le fichier *Pages/Student/Index.cshtml*.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Exécutez l’application et sélectionnez un lien **Details**. L’URL est au format `http://localhost:5000/Students/Details?id=2`. L’ID d’étudiant est transmis à l’aide d’une chaîne de requête (`?id=2`).

Mettez à jour les pages de modification, de détails et Razor de suppression pour utiliser le `"{id:int}"` modèle de routage. Remplacez la directive de chacune de ces pages (`@page "{id:int}"`) par `@page`.

Une requête à la page avec le modèle de route « {id:int} » qui n’inclut **pas** une valeur de route entière retourne une erreur HTTP 404 (introuvable). Par exemple, `http://localhost:5000/Students/Details` retourne une erreur 404. Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Exécutez l’application, cliquez sur un lien Détails et vérifiez que l’URL passe l’ID en tant que données de route (`http://localhost:5000/Students/Details/2`).

Ne changez pas `@page` en `@page "{id:int}"` globalement, cela casserait les liens vers les pages Home et Create.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Ajouter des données associées

Le code généré automatiquement pour la page Index des étudiants n’inclut pas la propriété `Enrollments`. Dans cette section, le contenu de la collection `Enrollments` s’affiche dans la page Details.

Le méthode `OnGetAsync` de *Pages/Students/Details.cshtml.cs* utilise la méthode `FirstOrDefaultAsync` pour récupérer une seule entité `Student`. Ajoutez le code en surbrillance suivant :

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

Les méthodes [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) et [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) forcent le contexte à charger la propriété de navigation `Student.Enrollments` et, dans chaque inscription, la propriété de navigation `Enrollment.Course`. Ces méthodes sont examinées en détail dans le tutoriel sur la lecture des données associées.

La méthode [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) améliore les performances dans les scénarios où les entités retournées ne sont pas mises à jour dans le contexte actuel. Le sujet `AsNoTracking` est abordé plus loin dans ce didacticiel.

### <a name="display-related-enrollments-on-the-details-page"></a>Afficher les inscriptions associées sur la page Details

Ouvrez *Pages/Students/Details.cshtml*. Ajoutez le code en surbrillance suivant pour afficher la liste des inscriptions :

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Si la mise en retrait du code est incorrecte, une fois que le code est collé, appuyez sur CTRL + K + D pour résoudre ce problème.

Le code précédent effectue une itération sur les entités dans la propriété de navigation `Enrollments`. Pour chaque inscription, il affiche le titre du cours et le niveau. Le titre du cours est récupéré à partir de l’entité de cours qui est stockée dans la propriété de navigation `Course` de l’entité Enrollments.

Exécutez l’application, sélectionnez l’onglet **Students**, puis cliquez sur le lien **Details** pour un étudiant. La liste des cours et les notes de l’étudiant sélectionné s’affiche.

## <a name="update-the-create-page"></a>Mettre à jour la page Create

Mettez à jour la méthode `OnPostAsync` dans *Pages/Students/Create.cshtml.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Examinez le code [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) :

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

Dans le code précédent, `TryUpdateModelAsync<Student>` tente de mettre à jour l’objet `emptyStudent` en utilisant les valeurs de formulaire publiées à partir de la propriété [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) dans le [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel). `TryUpdateModelAsync` met uniquement à jour les propriétés répertoriées (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).

Dans l’exemple précédent :

* Le deuxième argument (`"student", // Prefix`) est le préfixe utilisé pour rechercher des valeurs. Il ne respecte pas la casse.
* Les valeurs de formulaire publiées sont converties vers les types dans le modèle `Student` à l’aide de la [liaison de modèle](xref:mvc/models/model-binding).

<a id="overpost"></a>

### <a name="overposting"></a>Sur-publication

L’utilisation de `TryUpdateModel` pour mettre à jour des champs avec des valeurs publiées est une bonne pratique de sécurité, car cela empêche la sur-publication. Par exemple, supposez que l’entité Student comprend une propriété `Secret` que cette page web ne doit pas mettre à jour ou ajouter :

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Même si l’application n’a pas de `Secret` champ dans la page de création/mise à jour Razor , un pirate peut définir la `Secret` valeur par la survalidation. Un pirate pourrait utiliser un outil tel que Fiddler, ou écrire du JavaScript, pour publier une valeur de formulaire `Secret`. Le code d’origine ne limite pas les champs que le classeur de modèles utilise quand il crée une instance de Student.

La valeur spécifiée par le pirate pour le champ de formulaire `Secret`, quelle qu’elle soit, est mise à jour dans la base de données. L’illustration suivante montre l’outil Fiddler en train d’ajouter le champ `Secret` (avec la valeur « OverPost ») aux valeurs du formulaire envoyé.

![Fiddler ajoutant un champ Secret](../ef-mvc/crud/_static/fiddler.png)

La valeur « OverPost » est ajoutée avec succès à la propriété `Secret` de la ligne insérée. Le concepteur de l’application n’a jamais prévu que la propriété `Secret` soit définie avec la page Create.

<a name="vm"></a>

### <a name="view-model"></a>Afficher le modèle

Un modèle d’affichage contient généralement un sous-ensemble des propriétés incluses dans le modèle utilisé par l’application. Le modèle d’application est souvent appelé modèle de domaine. En règle générale, le modèle de domaine contient toutes les propriétés requises par l’entité correspondante dans la base de données. Le modèle d’affichage contient uniquement les propriétés nécessaires pour la couche d’interface utilisateur (par exemple, la page Create). Outre le modèle de vue, certaines applications utilisent un modèle de liaison ou un modèle d’entrée pour passer des données entre la Razor classe de modèle de page pages et le navigateur. Considérez le modèle d’affichage `Student` suivant :

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Les modèles d’affichage fournissent une alternative pour empêcher la sur-publication. Le modèle d’affichage contient uniquement les propriétés à afficher ou à mettre à jour.

Le code suivant utilise le modèle d’affichage `StudentVM` pour créer un étudiant :

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

La méthode [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) définit les valeurs de cet objet en lisant les valeurs d’un autre objet [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues). `SetValues` utilise la correspondance de nom de propriété. Le type de modèle d’affichage ne doit pas nécessairement être lié au type de modèle. Il doit simplement avoir des propriétés qui correspondent.

L’utilisation de `StudentVM` exige que [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) soit mis à jour pour utiliser `StudentVM` plutôt que `Student`.

Dans les Razor pages, la `PageModel` classe dérivée est le modèle de vue.

## <a name="update-the-edit-page"></a>Mettre à jour la page Edit

Mettez à jour le modèle de page pour la page Edit. Les modifications principales apparaissent en surbrillance :

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Les modifications de code sont semblables à celles de la page Create, à quelques exceptions près :

* `OnPostAsync` a un paramètre facultatif `id`.
* L’étudiant actuel est récupéré à partir de la base de données (on ne crée pas un étudiant vide).
* `FirstOrDefaultAsync` a été remplacée par [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). `FindAsync` est un bon choix lors de la sélection d’une entité à partir de la clé primaire. Pour plus d’informations, consultez [FindAsync](#FindAsync).

### <a name="test-the-edit-and-create-pages"></a>Tester les pages Edit et Create

Créez et modifiez quelques entités d’étudiants.

## <a name="entity-states"></a>États des entités

Le contexte de base de données effectue un suivi afin de savoir si les entités en mémoire sont synchronisées avec les lignes correspondantes dans la base de données. Les informations de synchronisation du contexte de base de données déterminent ce qui se produit quand [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) est appelé. Par exemple, quand une nouvelle entité est passée à la méthode [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync), l’état de cette entité est défini sur [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Quand `SaveChangesAsync` est appelée, le contexte de base de données émet une commande SQL INSERT.

Une entité peut être dans l’un des [États suivants](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added` : L’entité n’existe pas encore dans la base de données. La méthode `SaveChanges` émet une instruction INSERT.

* `Unchanged` : Aucune modification ne doit être enregistrée avec cette entité. Une entité est dans cet état quand elle est lue à partir de la base de données.

* `Modified` : Tout ou une partie des valeurs de propriété de l’entité ont été modifiées. La méthode `SaveChanges` émet une instruction UPDATE.

* `Deleted` : L’entité a été marquée pour suppression. La méthode `SaveChanges` émet une instruction DELETE.

* `Detached` : L’entité n’est pas suivie par le contexte de base de données.

Dans une application de bureau, les changements d’état sont généralement définis automatiquement. Une entité est lue, des modifications sont apportées et l’état d’entité passe automatiquement à `Modified`. L’appel de `SaveChanges` génère une instruction SQL UPDATE qui met à jour uniquement les propriétés modifiées.

Dans une application web, le `DbContext` qui lit une entité et affiche les données est supprimé après le rendu d’une page. Quand la méthode `OnPostAsync` d’une page est appelée, une nouvelle requête web est faite avec une nouvelle instance du `DbContext`. La relecture de l’entité dans ce nouveau contexte simule le traitement de bureau.

## <a name="update-the-delete-page"></a>Mettre à jour la page Delete

Dans cette section, nous ajoutons du code pour implémenter un message d’erreur personnalisé quand l’appel à `SaveChanges` échoue. Ajoutez une chaîne contenant les messages d’erreur possibles :

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Remplacez la méthode `OnGetAsync` par le code suivant :

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Le code précédent contient le paramètre facultatif `saveChangesError`. `saveChangesError` indique si la méthode a été appelée après un échec de suppression de l’objet Student. L’opération de suppression peut échouer en raison de problèmes réseau temporaires. Les erreurs réseau temporaires sont probablement dans le cloud. `saveChangesError` a la valeur false quand la méthode `OnGetAsync` de la page Delete est appelée à partir de l’interface utilisateur. Quand `OnGetAsync` est appelée par `OnPostAsync` (car l’opération de suppression a échoué), le paramètre `saveChangesError` a la valeur true.

### <a name="the-delete-pages-onpostasync-method"></a>La méthode OnPostAsync des pages Delete

Remplacez `OnPostAsync` par le code suivant :

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Le code précédent récupère l’entité sélectionnée, puis appelle la méthode [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) pour définir l’état de l’entité sur `Deleted`. Lorsque `SaveChanges` est appelée, une commande SQL DELETE est générée. Si `Remove` échoue :

* L’exception de la base de données est interceptée.
* La méthode `OnGetAsync` des pages est appelée avec `saveChangesError=true`.

### <a name="update-the-delete-no-locrazor-page"></a>Mettre à jour la page de suppression Razor

Ajoutez le message d’erreur en surbrillance suivant à la page de suppression Razor .
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Testez la suppression.

## <a name="common-errors"></a>Erreurs courantes

Students/Index ou d’autres liens ne fonctionnent pas :

Vérifiez que la Razor page contient la `@page` directive correcte. Par exemple, la page Students/index ne Razor doit **pas** contenir de modèle de routage :

```cshtml
@page "{id:int}"
```

Chaque Razor page doit inclure la `@page` directive.



## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [Précédent](xref:data/ef-rp/intro) 
>  [Suivant](xref:data/ef-rp/sort-filter-page)

::: moniker-end
