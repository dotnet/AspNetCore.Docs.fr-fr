---
title: Tester la logique des contrôleurs dans ASP.NET Core
author: ardalis
description: Découvrez plus d’informations sur le test de la logique des contrôleurs dans ASP.NET Core avec Moq et xUnit.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: mvc/controllers/testing
ms.openlocfilehash: 0f4fcceefdef946a5f29633c069cbb64ec345a3d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586954"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>Logique du contrôleur de test unitaire dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

Les [tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) impliquent le test d’une partie d’une application de façon isolée par rapport à son infrastructure et à ses dépendances. Lors du test de la logique d’un contrôleur, seuls les contenus d’une action sont testés, et non pas le comportement de ses dépendances ou du framework lui-même.

## <a name="unit-testing-controllers"></a>Contrôleurs de test unitaire

Configurez des tests unitaires d’actions de contrôleur pour qu’ils s’attachent uniquement au comportement du contrôleur. Un test unitaire de contrôleur évite des scénarios, tels que les [filtres](xref:mvc/controllers/filters), le [routage](xref:fundamentals/routing) et la [liaison de données](xref:mvc/models/model-binding). Les tests couvrant les interactions entre les composants qui répondent collectivement à une requête sont gérés par des *tests d’intégration*. Pour plus d’informations sur les tests d’intégration, consultez <xref:test/integration-tests>.

Si vous écrivez des routes et des filtres personnalisés, testez-les de manière isolée avec des tests unitaires plutôt que dans le cadre de tests exécutés sur une action de contrôleur spécifique.

Pour illustrer les tests unitaires de contrôleur, examinez de plus près le contrôleur suivant dans l’exemple d’application. 

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Le contrôleur Home affiche une liste de sessions de brainstorming et permet la création de nouvelles sessions avec une requête POST :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Le contrôleur précédent :

* Suit le [Principe des dépendances explicites](/dotnet/architecture/modern-web-apps-azure/architectural-principles#explicit-dependencies).
* Attend que [l’injection de dépendance](xref:fundamentals/dependency-injection) fournisse une instance de `IBrainstormSessionRepository`.
* Peut être testé avec un service `IBrainstormSessionRepository` fictif au moyen d’un framework d’objets fictifs, tel que [Moq](https://www.nuget.org/packages/Moq/). Un *objet fictif* est un objet fabriqué avec un ensemble prédéterminé de comportements de propriété et de méthode utilisés pour les tests. Pour plus d’informations, consultez [Introduction aux tests d’intégration](xref:test/integration-tests#introduction-to-integration-tests).

La méthode `HTTP GET Index` n’a pas de boucle ni de branchement, et elle appelle seulement une méthode. Le test unitaire pour cette action :

* Simule le service `IBrainstormSessionRepository` à l’aide de la méthode `GetTestSessions`. `GetTestSessions` crée deux sessions de brainstorming fictives avec des dates et des noms de session.
* Exécute la méthode `Index`.
* Fait des assertions sur le résultat retourné par la méthode :
  * Un <xref:Microsoft.AspNetCore.Mvc.ViewResult> est retourné.
  * Le [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) est un `StormSessionViewModel`.
  * Deux sessions de brainstorming sont stockées dans le `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Le test de la méthode `HTTP POST Index` du contrôleur Home vérifie que :

* Quand [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) a la valeur `false` , la méthode d’action retourne une *demande 400 erronée* <xref:Microsoft.AspNetCore.Mvc.ViewResult> avec les données appropriées.
* Lorsque `ModelState.IsValid` est `true` :
  * La méthode `Add` sur le dépôt est appelée.
  * Un <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> est retourné avec les arguments corrects.

Un état de modèle non valide est testé en ajoutant des erreurs avec <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, comme le montre le premier test ci-dessous :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Lorsque [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) n’est pas valide, le même `ViewResult` est retourné, comme pour une requête GET. Le test ne tente pas de passer un modèle non valide. Le passage d’un modèle non valide n’est pas une approche admise, car la liaison de modèle n’est pas en cours d’exécution (même si un [test d’intégration](xref:test/integration-tests) utilise bien la liaison de modèle). Dans ce cas, la liaison de modèle n’est pas testée. Ces tests unitaires testent seulement le code dans la méthode d’action.

Le second test vérifie cela quand le `ModelState` est valide :

* Un nouveau `BrainstormSession` est ajouté (par le biais du référentiel).
* La méthode retourne un `RedirectToActionResult` avec les propriétés attendues.

Les appels fictifs qui ne sont pas appelés sont normalement ignorés, mais l’appel de `Verifiable` à la fin de l’appel de configuration autorise la validation fictive dans le test. Ceci est effectué avec l’appel à `mockRepo.Verify`, qui échoue au test si la méthode attendue n’a pas été appelée.

> [!NOTE]
> La bibliothèque Moq utilisée dans cet exemple permet la combinaison d’éléments fictifs vérifiables (ou « stricts ») avec des éléments fictifs non vérifiables (également appelés stubs ou éléments « lâches »). Découvrez plus d’informations sur la [personnalisation des éléments fictifs avec Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

Dans l’exemple d’application, [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) affiche des informations relatives à une session de brainstorming. Le contrôleur inclut la logique pour traiter des valeurs `id` non valides (il existe deux scénarios `return` dans l’exemple suivant pour couvrir ces scénarios). La dernière instruction `return` retourne un nouveau `StormSessionViewModel` à la vue (*Controllers/SessionController.cs*) :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Les tests unitaires comportent un test pour chaque scénario `return` dans l’action `Index` du contrôleur Session :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

En passant au contrôleur Ideas, l’application expose des fonctionnalités, comme une API web sur la route `api/ideas` :

* Une liste d’idées (`IdeaDTO`) associées à une session de brainstorming est retournée par la méthode `ForSession`.
* La méthode `Create` ajoute de nouvelles idées à une session.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Évitez de retourner des entités de domaine métier directement via des appels d’API. Les entités de domaine :

* Incluent souvent plus de données que ce dont a besoin le client.
* Couplent inutilement le modèle de domaine interne de l’application à l’API exposée publiquement.

Le mappage entre des entités de domaine et les types retournés au client peut être effectué :

* Manuellement avec un `Select` LINQ, comme l’utilise l’exemple d’application. Pour plus d’informations, consultez [LINQ (Language-Integrated Query)](/dotnet/standard/using-linq).
* Automatiquement avec une bibliothèque, telle que [AutoMapper](https://github.com/AutoMapper/AutoMapper).

Ensuite, l’exemple d’application décrit des tests unitaires pour les méthodes d’API `Create` et `ForSession` du contrôleur Ideas.

L’exemple d’application contient deux tests `ForSession`. Le premier test détermine si `ForSession` retourne un <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Non trouvé) pour une session non valide :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

La deuxième test `ForSession` détermine si `ForSession` retourne une liste d’idées de session (`<List<IdeaDTO>>`) pour une session valide. Les vérifications contrôlent également la première idée pour confirmer que sa propriété `Name` est correcte :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Pour tester le comportement de la méthode `Create` quand le `ModelState` n’est pas valide, l’exemple d’application ajoute une erreur de modèle au contrôleur dans le cadre de ce test. N’essayez pas de tester la validation de modèle ou la liaison de modèle dans des tests unitaires, testez simplement le comportement de votre méthode d’action quand elle est confrontée à un `ModelState` non valide :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Le deuxième test de `Create` dépend du retour de `null` par le dépôt, le dépôt fictif est donc configuré pour retourner `null`. Il est inutile de créer une base de données de test (dans la mémoire ou ailleurs) et de construire une requête qui retourne ce résultat. Le test peut être effectué en une seule instruction, comme le montre l’exemple de code :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Le troisième test `Create`, `Create_ReturnsNewlyCreatedIdeaForSession`, vérifie que la méthode `UpdateAsync` du dépôt est appelée. L’élément fictif est appelé avec `Verifiable`, puis la méthode `Verify` du dépôt fictif est appelée pour confirmer que la méthode vérifiable est exécutée. La garantie que la méthode `UpdateAsync` a enregistré les données ne relève pas de la responsabilité d’un test unitaire ; cette vérification peut être effectuée au moyen d’un test d’intégration.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>ActionResult de test\<T>

Dans ASP.net Core 2,1 ou version ultérieure [, \<T> ActionResult](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ) vous permet de retourner un type dérivant de `ActionResult` ou qui retourne un type spécifique.

L’exemple d’application comprend une méthode qui retourne un `List<IdeaDTO>` pour un `id` de session donné. Si l’`id` de session n’existe pas, le contrôleur retourne <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Deux tests du contrôleur `ForSessionActionResult` sont inclus dans `ApiIdeasControllerTests`.

Le premier test confirme que le contrôleur retourne un `ActionResult`, et non une liste inexistante d’idées pour un `id` de session inexistant :

* Le type `ActionResult` est `ActionResult<List<IdeaDTO>>`.
* Le <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> est un <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Pour un `id` de session valide, le deuxième test confirme que la méthode retourne :

* Un `ActionResult` avec un type `List<IdeaDTO>`.
* [ActionResult \<T> . La valeur](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) est un `List<IdeaDTO>` type.
* Le premier élément dans la liste est une idée valide correspondant à l’idée stockée dans la session fictive (obtenu en appelant `GetTestSession`).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

L’exemple d’application comporte également une méthode pour créer un nouveau `Idea` pour une session donnée. Le contrôleur retourne :

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> pour un modèle non valide.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> si la session n’existe pas.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> lorsque la session est mise à jour avec la nouvelle idée.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Trois tests de `CreateActionResult` sont inclus dans `ApiIdeasControllerTests`.

Le premier test confirme qu’un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> est retourné pour un modèle non valide.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Le deuxième test vérifie qu’un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> est retourné si la session n’existe pas.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Pour un `id` de session valide, le dernier test confirme que :

* La méthode retourne un `ActionResult` avec un type `BrainstormSession`.
* [ActionResult \<T> . Le résultat](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) est <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult` est analogue à une réponse *Créée 201* avec un en-tête `Location`.
* [ActionResult \<T> . La valeur](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) est un `BrainstormSession` type.
* L’appel fictif pour mettre à jour la session, `UpdateAsync(testSession)`, a été appelé. L’appel de la méthode `Verifiable` est contrôlé en exécutant `mockRepo.Verify()` dans les assertions.
* Deux objets `Idea` sont retournés pour la session.
* Le dernier élément (`Idea` ajouté par l’appel fictif à `UpdateAsync`) correspond à `newIdea` ajouté à la session dans le test.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Les [contrôleurs](xref:mvc/controllers/actions) jouent un rôle essentiel dans une application ASP.NET Core MVC. En tant que tel, vous devez être sûr qu’ils se comportent comme prévu. Les tests automatisés peuvent détecter des erreurs avant le déploiement de l’application dans un environnement de production.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Tests unitaires de la logique de contrôleur

Les [tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) impliquent le test d’une partie d’une application de façon isolée par rapport à son infrastructure et à ses dépendances. Lors du test de la logique d’un contrôleur, seuls les contenus d’une action sont testés, et non pas le comportement de ses dépendances ou du framework lui-même.

Configurez des tests unitaires d’actions de contrôleur pour qu’ils s’attachent uniquement au comportement du contrôleur. Un test unitaire de contrôleur évite des scénarios, tels que les [filtres](xref:mvc/controllers/filters), le [routage](xref:fundamentals/routing) et la [liaison de données](xref:mvc/models/model-binding). Les tests couvrant les interactions entre les composants qui répondent collectivement à une requête sont gérés par des *tests d’intégration*. Pour plus d’informations sur les tests d’intégration, consultez <xref:test/integration-tests>.

Si vous écrivez des routes et des filtres personnalisés, testez-les de manière isolée avec des tests unitaires plutôt que dans le cadre de tests exécutés sur une action de contrôleur spécifique.

Pour illustrer les tests unitaires de contrôleur, examinez de plus près le contrôleur suivant dans l’exemple d’application. Le contrôleur Home affiche une liste de sessions de brainstorming et permet la création de nouvelles sessions avec une requête POST :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Le contrôleur précédent :

* Suit le [Principe des dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Attend que [l’injection de dépendance](xref:fundamentals/dependency-injection) fournisse une instance de `IBrainstormSessionRepository`.
* Peut être testé avec un service `IBrainstormSessionRepository` fictif au moyen d’un framework d’objets fictifs, tel que [Moq](https://www.nuget.org/packages/Moq/). Un *objet fictif* est un objet fabriqué avec un ensemble prédéterminé de comportements de propriété et de méthode utilisés pour les tests. Pour plus d’informations, consultez [Introduction aux tests d’intégration](xref:test/integration-tests#introduction-to-integration-tests).

La méthode `HTTP GET Index` n’a pas de boucle ni de branchement, et elle appelle seulement une méthode. Le test unitaire pour cette action :

* Simule le service `IBrainstormSessionRepository` à l’aide de la méthode `GetTestSessions`. `GetTestSessions` crée deux sessions de brainstorming fictives avec des dates et des noms de session.
* Exécute la méthode `Index`.
* Fait des assertions sur le résultat retourné par la méthode :
  * Un <xref:Microsoft.AspNetCore.Mvc.ViewResult> est retourné.
  * Le [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) est un `StormSessionViewModel`.
  * Deux sessions de brainstorming sont stockées dans le `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Le test de la méthode `HTTP POST Index` du contrôleur Home vérifie que :

* Quand [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) a la valeur `false` , la méthode d’action retourne une *demande 400 erronée* <xref:Microsoft.AspNetCore.Mvc.ViewResult> avec les données appropriées.
* Lorsque `ModelState.IsValid` est `true` :
  * La méthode `Add` sur le dépôt est appelée.
  * Un <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> est retourné avec les arguments corrects.

Un état de modèle non valide est testé en ajoutant des erreurs avec <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, comme le montre le premier test ci-dessous :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Lorsque [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) n’est pas valide, le même `ViewResult` est retourné, comme pour une requête GET. Le test ne tente pas de passer un modèle non valide. Le passage d’un modèle non valide n’est pas une approche admise, car la liaison de modèle n’est pas en cours d’exécution (même si un [test d’intégration](xref:test/integration-tests) utilise bien la liaison de modèle). Dans ce cas, la liaison de modèle n’est pas testée. Ces tests unitaires testent seulement le code dans la méthode d’action.

Le second test vérifie cela quand le `ModelState` est valide :

* Un nouveau `BrainstormSession` est ajouté (par le biais du référentiel).
* La méthode retourne un `RedirectToActionResult` avec les propriétés attendues.

Les appels fictifs qui ne sont pas appelés sont normalement ignorés, mais l’appel de `Verifiable` à la fin de l’appel de configuration autorise la validation fictive dans le test. Ceci est effectué avec l’appel à `mockRepo.Verify`, qui échoue au test si la méthode attendue n’a pas été appelée.

> [!NOTE]
> La bibliothèque Moq utilisée dans cet exemple permet la combinaison d’éléments fictifs vérifiables (ou « stricts ») avec des éléments fictifs non vérifiables (également appelés stubs ou éléments « lâches »). Découvrez plus d’informations sur la [personnalisation des éléments fictifs avec Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

Dans l’exemple d’application, [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) affiche des informations relatives à une session de brainstorming. Le contrôleur inclut la logique pour traiter des valeurs `id` non valides (il existe deux scénarios `return` dans l’exemple suivant pour couvrir ces scénarios). La dernière instruction `return` retourne un nouveau `StormSessionViewModel` à la vue (*Controllers/SessionController.cs*) :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Les tests unitaires comportent un test pour chaque scénario `return` dans l’action `Index` du contrôleur Session :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

En passant au contrôleur Ideas, l’application expose des fonctionnalités, comme une API web sur la route `api/ideas` :

* Une liste d’idées (`IdeaDTO`) associées à une session de brainstorming est retournée par la méthode `ForSession`.
* La méthode `Create` ajoute de nouvelles idées à une session.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Évitez de retourner des entités de domaine métier directement via des appels d’API. Les entités de domaine :

* Incluent souvent plus de données que ce dont a besoin le client.
* Couplent inutilement le modèle de domaine interne de l’application à l’API exposée publiquement.

Le mappage entre des entités de domaine et les types retournés au client peut être effectué :

* Manuellement avec un `Select` LINQ, comme l’utilise l’exemple d’application. Pour plus d’informations, consultez [LINQ (Language-Integrated Query)](/dotnet/standard/using-linq).
* Automatiquement avec une bibliothèque, telle que [AutoMapper](https://github.com/AutoMapper/AutoMapper).

Ensuite, l’exemple d’application décrit des tests unitaires pour les méthodes d’API `Create` et `ForSession` du contrôleur Ideas.

L’exemple d’application contient deux tests `ForSession`. Le premier test détermine si `ForSession` retourne un <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Non trouvé) pour une session non valide :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

La deuxième test `ForSession` détermine si `ForSession` retourne une liste d’idées de session (`<List<IdeaDTO>>`) pour une session valide. Les vérifications contrôlent également la première idée pour confirmer que sa propriété `Name` est correcte :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Pour tester le comportement de la méthode `Create` quand le `ModelState` n’est pas valide, l’exemple d’application ajoute une erreur de modèle au contrôleur dans le cadre de ce test. N’essayez pas de tester la validation de modèle ou la liaison de modèle dans des tests unitaires, testez simplement le comportement de votre méthode d’action quand elle est confrontée à un `ModelState` non valide :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Le deuxième test de `Create` dépend du retour de `null` par le dépôt, le dépôt fictif est donc configuré pour retourner `null`. Il est inutile de créer une base de données de test (dans la mémoire ou ailleurs) et de construire une requête qui retourne ce résultat. Le test peut être effectué en une seule instruction, comme le montre l’exemple de code :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Le troisième test `Create`, `Create_ReturnsNewlyCreatedIdeaForSession`, vérifie que la méthode `UpdateAsync` du dépôt est appelée. L’élément fictif est appelé avec `Verifiable`, puis la méthode `Verify` du dépôt fictif est appelée pour confirmer que la méthode vérifiable est exécutée. La garantie que la méthode `UpdateAsync` a enregistré les données ne relève pas de la responsabilité d’un test unitaire ; cette vérification peut être effectuée au moyen d’un test d’intégration.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>ActionResult de test\<T>

Dans ASP.net Core 2,1 ou version ultérieure [, \<T> ActionResult](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ) vous permet de retourner un type dérivant de `ActionResult` ou qui retourne un type spécifique.

L’exemple d’application comprend une méthode qui retourne un `List<IdeaDTO>` pour un `id` de session donné. Si l’`id` de session n’existe pas, le contrôleur retourne <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Deux tests du contrôleur `ForSessionActionResult` sont inclus dans `ApiIdeasControllerTests`.

Le premier test confirme que le contrôleur retourne un `ActionResult`, et non une liste inexistante d’idées pour un `id` de session inexistant :

* Le type `ActionResult` est `ActionResult<List<IdeaDTO>>`.
* Le <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> est un <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Pour un `id` de session valide, le deuxième test confirme que la méthode retourne :

* Un `ActionResult` avec un type `List<IdeaDTO>`.
* [ActionResult \<T> . La valeur](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) est un `List<IdeaDTO>` type.
* Le premier élément dans la liste est une idée valide correspondant à l’idée stockée dans la session fictive (obtenu en appelant `GetTestSession`).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

L’exemple d’application comporte également une méthode pour créer un nouveau `Idea` pour une session donnée. Le contrôleur retourne :

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> pour un modèle non valide.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> si la session n’existe pas.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> lorsque la session est mise à jour avec la nouvelle idée.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Trois tests de `CreateActionResult` sont inclus dans `ApiIdeasControllerTests`.

Le premier test confirme qu’un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> est retourné pour un modèle non valide.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Le deuxième test vérifie qu’un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> est retourné si la session n’existe pas.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Pour un `id` de session valide, le dernier test confirme que :

* La méthode retourne un `ActionResult` avec un type `BrainstormSession`.
* [ActionResult \<T> . Le résultat](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) est <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult` est analogue à une réponse *Créée 201* avec un en-tête `Location`.
* [ActionResult \<T> . La valeur](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) est un `BrainstormSession` type.
* L’appel fictif pour mettre à jour la session, `UpdateAsync(testSession)`, a été appelé. L’appel de la méthode `Verifiable` est contrôlé en exécutant `mockRepo.Verify()` dans les assertions.
* Deux objets `Idea` sont retournés pour la session.
* Le dernier élément (`Idea` ajouté par l’appel fictif à `UpdateAsync`) correspond à `newIdea` ajouté à la session dans le test.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/integration-tests>
* [Créer et exécuter des tests unitaires avec Visual Studio](/visualstudio/test/unit-test-your-code)
* [Bibliothèque de tests MyTested. AspNetCore. Mvc-Fluent pour ASP.net Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): bibliothèque de tests unitaires fortement typés, fournissant une interface Fluent pour tester les applications MVC et API Web. (*Non géré ou pris en charge par Microsoft.*)
* [JustMockLite](https://github.com/telerik/JustMockLite): un Framework fictif pour les développeurs .net. (*Non géré ou pris en charge par Microsoft.*)

