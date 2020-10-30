---
title: Gérer les requêtes avec des contrôleurs dans ASP.NET Core MVC
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/actions
ms.openlocfilehash: a9319e74d0213b178c2a71be69a0332270d9446c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061455"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>Gérer les requêtes avec des contrôleurs dans ASP.NET Core MVC

Par [Steve Smith](https://ardalis.com/) et [Scott Addie](https://github.com/scottaddie)

Les contrôleurs, les actions et les résultats des actions sont une part fondamentale dans la façon dont les développeurs créent des applications avec ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>Qu’est-ce qu’un contrôleur ?

Un contrôleur est utilisé pour définir et regrouper un ensemble d’actions. Une action (ou *méthode d’action* ) est une méthode sur un contrôleur qui gère les demandes. Les contrôleurs regroupent de façon logique des actions similaires. Cette agrégation des actions permet l’application collective de jeux de règles communs, comme le routage, la mise en cache et les autorisations. Les demandes sont mappées à des actions via un [routage](xref:mvc/controllers/routing).

Par convention, les classes de contrôleur :

* Résident dans le dossier des *contrôleurs* de niveau racine du projet.
* Héritez de `Microsoft.AspNetCore.Mvc.Controller`.

Un contrôleur est une classe instanciable dans laquelle au moins une des conditions suivantes est vraie :

* Le nom de la classe est suivi d’un suffixe `Controller` .
* La classe hérite d’une classe dont le nom est suivi d’un suffixe `Controller` .
* L' `[Controller]` attribut est appliqué à la classe.

Une classe de contrôleur ne doit pas avoir d’attribut `[NonController]` associé.

Les contrôleurs doivent suivre le [principe de dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies). Il existe deux approches pour implémenter ce principe. Si plusieurs actions de contrôleur nécessitent le même service, envisagez d’utiliser [l’injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) pour demander ces dépendances. Si le service est nécessaire pour une seule méthode d’action, envisagez d’utiliser [l’injection d’action](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) pour demander la dépendance.

Dans le **M** odèle- **V** ue- **C** ontrôleur, un contrôleur est responsable du traitement initial de la demande et de l’instanciation du modèle. En règle générale, les décisions métier doivent être prises dans le modèle.

Le contrôleur prend le résultat du traitement du modèle (le cas échéant) et retourne la vue appropriée et les données associées à cette vue, ou bien le résultat de l’appel d’API. Pour en savoir plus, consultez [Vue d’ensemble d’ASP.NET Core MVC](xref:mvc/overview) et [Bien démarrer avec ASP.NET Core MVC et Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Le contrôleur est une abstraction *au niveau de l’interface utilisateur* . Ses responsabilités sont de garantir que les données de la demande sont valides et de choisir la vue (ou le résultat d’API) à retourner. Dans les applications bien construites, il n’inclut pas directement l’accès aux données ni la logique métier. Au lieu de cela, le contrôleur délègue à des services la gestion de ces responsabilités.

## <a name="defining-actions"></a>Définition des actions

Les méthodes publiques sur un contrôleur, à l’exception de celles avec l' `[NonAction]` attribut, sont des actions. Les paramètres sur les actions sont liés aux données des demandes et sont validés avec la [liaison de modèle](xref:mvc/models/model-binding). La validation du modèle est effectuée pour tout ce qui est lié au modèle. La valeur de la propriété `ModelState.IsValid` indique si la liaison de modèle et la validation ont réussi.

Les méthodes d’action doivent contenir la logique nécessaire pour mapper une demande à un problème métier. Les problèmes métier doivent généralement être représentés comme des services auxquels le contrôleur accède via [l’injection de dépendances](xref:mvc/controllers/dependency-injection). Les actions mappent ensuite le résultat de l’action métier à un état de l’application.

Les actions peuvent retourner des valeurs de n’importe quel type, mais elles retournent souvent une instance de `IActionResult` (ou de `Task<IActionResult>` pour les méthodes asynchrones) qui produit une réponse. La méthode d’action est responsable du choix du *type de réponse* . Le résultat de l’action *constitue la réponse* .

### <a name="controller-helper-methods"></a>Méthodes helper des contrôleurs

Les contrôleurs héritent généralement de la classe [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), bien que ce ne soit pas obligatoire. Le fait de dériver de `Controller` fournit l’accès à trois catégories de méthodes helper :

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. les méthodes conduisent à un corps de réponse vide

Aucune en-tête de réponse HTTP `Content-Type` n’est présente, étant donné que le corps de la réponse n’a pas de contenu à décrire.

Il existe deux types de résultats dans cette catégorie : Redirection et Code d’état HTTP.

* **Code d’état HTTP**

    Ce type retourne un code d’état HTTP. `BadRequest`, `NotFound` et `Ok` sont des méthodes helper de ce type. Par exemple, `return BadRequest();` produit un code d’état 400 quand elle est exécutée. Quand des méthodes comme `BadRequest`, `NotFound` et `Ok` sont surchargées, elles ne sont plus qualifiées comme répondeurs de code d’état HTTP, étant donné que la négociation du contenu est en cours.

* **Rediriger**

    Ce type retourne une redirection vers une action ou une destination (avec `Redirect`, `LocalRedirect`, `RedirectToAction` ou `RedirectToRoute`). Par exemple, `return RedirectToAction("Complete", new {id = 123});` redirige vers `Complete`, en passant un objet anonyme.

    Le type de résultat Redirection diffère du type Code d’état HTTP principalement par l’ajout d’un en-tête de réponse HTTP `Location`.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. les méthodes conduisent à un corps de réponse non vide avec un type de contenu prédéfini

La plupart des méthodes helper de cette catégorie incluent une propriété `ContentType`, qui vous permet de définir l’en-tête de réponse `Content-Type` pour décrire le corps de la réponse.

Il existe deux types de résultats dans cette catégorie : [Vue](xref:mvc/views/overview) et [Réponse mise en forme](xref:web-api/advanced/formatting).

* **Visualiser**

    Ce type retourne une vue qui utilise un modèle pour rendre le HTML. Par exemple, `return View(customer);` passe un modèle à la vue pour la liaison de données.

* **Réponse mise en forme**

    Ce type retourne un format JSON ou un format d’échange de données similaire pour représenter un objet d’une manière spécifique. Par exemple, `return Json(customer);` sérialise l’objet fourni au format JSON.
    
    `File` et `PhysicalFile` sont des méthodes courantes de ce type. Par exemple, `return PhysicalFile(customerFilePath, "text/xml");` retourne [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. les méthodes qui aboutissent à un corps de réponse non vide mis en forme dans un type de contenu négocié avec le client

Cette catégorie est plus connue sous le nom de **Négociation de contenu** . La [Négociation de contenu](xref:web-api/advanced/formatting#content-negotiation) s’applique chaque fois qu’une action retourne un type [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) ou quelque chose d’autre qu’une implémentation de [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult). Une action qui retourne une implémentation autre que `IActionResult` (par exemple `object`) retourne également une Réponse mise en forme.

`BadRequest`, `CreatedAtRoute` et `Ok` sont des méthodes helper de ce type. `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);` et `return Ok(value);` sont des exemples respectifs de ces méthodes. Notez que `BadRequest` et `Ok` effectuent une négociation de contenu seulement quand ils reçoivent une valeur ; si aucune valeur ne leur est passée, ils délivrent à la place des types de résultats Code d’état HTTP. La méthode `CreatedAtRoute` effectue quant à elle toujours une négociation de contenu, car ses surcharges nécessitent toutes qu’une valeur soit passée.

### <a name="cross-cutting-concerns"></a>Problèmes transversaux

En règle générale, les applications partagent des parties de leur flux de travail. C’est par exemple le cas d’une application qui exige une authentification pour l’accès au panier d’achat ou qui met en cache les données de certaines pages. Pour exécuter la logique avant ou après une méthode d’action, utilisez un *filtre* . L’utilisation de [Filtres](xref:mvc/controllers/filters) sur les problèmes transversaux peut réduire la duplication.

La plupart des attributs des filtres, comme `[Authorize]`, peuvent être appliqués au niveau du contrôleur ou de l’action, selon le niveau de granularité souhaité.

La gestion des erreurs et la mise en cache des réponses sont souvent des problèmes transversaux :
* [Gérer les erreurs](xref:mvc/controllers/filters#exception-filters)
* [Mise en cache des réponses](xref:performance/caching/response)

De nombreux problèmes transversaux peuvent être gérés en utilisant des filtres ou un [intergiciel (middleware)](xref:fundamentals/middleware/index) personnalisé.
