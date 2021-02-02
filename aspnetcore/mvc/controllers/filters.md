---
title: Filtres dans ASP.NET Core
author: Rick-Anderson
description: Découvrez comment les filtres fonctionnent et comment les utiliser dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: ee30ef89c5d7aeae83f23a81eb02235397c89ac2
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238321"
---
# <a name="filters-in-aspnet-core"></a>Filtres dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)

Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.

Les filtres intégrés gèrent notamment les tâches suivantes :

* Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).
* Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).

Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux. Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.  Les filtres évitent la duplication de code. Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.

Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues. Les filtres ne fonctionnent pas directement avec les [ Razor composants](xref:blazor/components/index). Un filtre peut uniquement affecter indirectement un composant lorsque :

* Le composant est incorporé dans une page ou une vue.
* La page ou le contrôleur/la vue utilise le filtre.

[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([comment télécharger](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Fonctionnement des filtres

Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*. Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.

![La demande est traitée par un autre intergiciel, un intergiciel (middleware) de routage, une sélection d’action et le pipeline d’appel d’action. Le traitement de la requête se poursuit via une sélection d’action, un intergiciel de routage et différents autres intergiciels avant de devenir une réponse envoyée au client.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Types de filtres

Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :

* Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête. Filtres d’autorisation court-circuiter le pipeline si la demande n’est pas autorisée.

* [Filtres de ressources](#resource-filters):

  * Exécuter après l’autorisation.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> exécute du code avant le reste du pipeline de filtre. Par exemple, `OnResourceExecuting` exécute le code avant la liaison de modèle.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> exécute du code une fois que le reste du pipeline est terminé.

* [Filtres d’action](#action-filters):

  * Exécutez le code juste avant et après l’appel d’une méthode d’action.
  * Peut modifier les arguments passés dans une action.
  * Peut modifier le résultat retourné par l’action.
  * Ne sont **pas** pris en charge dans les Razor pages.

* Les [filtres d’exception](#exception-filters) appliquent des stratégies globales aux exceptions non gérées qui se produisent avant l’écriture du corps de la réponse.

* Les [filtres de résultats](#result-filters) exécutent le code immédiatement avant et après l’exécution des résultats d’action. Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action. Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.

Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats. En sortie, la requête est traitée seulement par les filtres de résultats et les filtres de ressources avant de devenir une réponse envoyée au client.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implémentation

Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.

Les filtres synchrones exécutent le code avant et après leur étape de pipeline. Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> est appelé avant l’appel de la méthode d’action. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> est appelé après le retour de la méthode d’action.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Dans le code précédent, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) est une fonction utilitaire dans l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).

Les filtres asynchrones définissent une `On-Stage-ExecutionAsync` méthode. Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

Dans le code précédent, `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) qui exécute la méthode d’action.

### <a name="multiple-filter-stages"></a>Plusieurs étapes de filtres

Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe. Par exemple, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implémente :

* Synchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> et  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>
* Asynchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** . Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface. Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone. Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée. Lorsque vous utilisez des classes abstraites telles que <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , remplacez uniquement les méthodes synchrones ou les méthodes asynchrones pour chaque type de filtre.

### <a name="built-in-filter-attributes"></a>Attributs de filtre intégrés

ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser. Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus. Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Utilisez un outil tel que les [outils de développement du navigateur](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) pour examiner les en-têtes. Sous **en-têtes de réponse**, `author: Rick Anderson` est affiché.

Le code suivant implémente un `ActionFilterAttribute` qui :

* Lit le titre et le nom à partir du système de configuration. Contrairement à l’exemple précédent, le code suivant ne nécessite pas l’ajout de paramètres de filtre au code.
* Ajoute le titre et le nom à l’en-tête de réponse.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

Les options de configuration sont fournies par le [système de configuration](xref:fundamentals/configuration/index) à l’aide du modèle d' [options](xref:fundamentals/configuration/options). Par exemple, à partir du *appsettings.json* fichier :

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

Dans le `StartUp.ConfigureServices` :

* La `PositionOptions` classe est ajoutée au conteneur de services avec la `"Position"` zone de configuration.
* Le `MyActionFilterAttribute` est ajouté au conteneur de services.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

Le code suivant illustre la `PositionOptions` classe :

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

Le code suivant applique `MyActionFilterAttribute` à la `Index2` méthode :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

Sous **les en-têtes de réponse**, `author: Rick Anderson` et `Editor: Joe Smith` s’affiche lorsque le `Sample/Index2` point de terminaison est appelé.

Le code suivant applique le `MyActionFilterAttribute` et le `AddHeaderAttribute` à la Razor page :

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

Les filtres ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de page. Ils peuvent être appliqués au modèle de Razor page ou globalement.

Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.

Les attributs de filtre :

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Étendues de filtre et ordre d’exécution

Un filtre peut être ajouté au pipeline à l’une des trois *étendues* suivantes :

* Utilisation d’un attribut sur une action de contrôleur. Les attributs de filtre ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de pages.
* Utilisation d’un attribut sur un contrôleur ou une Razor page.
* Globalement pour tous les contrôleurs, actions et Razor pages, comme indiqué dans le code suivant :

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>Ordre d’exécution par défaut

Quand il existe plusieurs filtres pour une étape particulière du pipeline, l’étendue détermine l’ordre par défaut de l’exécution du filtre.  Les filtres globaux entourent les filtres de classe, qui à leur tour entourent les filtres de méthode.

En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*. La séquence de filtre :

* Le code *avant* des filtres globaux.
  * *Avant* le code du contrôleur et des Razor filtres de page.
    * Le code *avant* des filtres de méthodes d’action.
    * Le code *après* des filtres de méthodes d’action.
  * *Après* le code du contrôleur et des Razor filtres de page.
* Le code *après* des filtres globaux.
  
Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.

| Séquence | Étendue de filtre | Méthode de filtre |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Contrôleur ou Razor page| `OnActionExecuting` |
| 3 | Méthode | `OnActionExecuting` |
| 4 | Méthode | `OnActionExecuted` |
| 5 | Contrôleur ou Razor page | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

### <a name="controller-level-filters"></a>Filtres au niveau du contrôleur

Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Ces méthodes :

* Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.
* `OnActionExecuting` est appelé avant tous les filtres de l’action.
* `OnActionExecuted` est appelé après tous les filtres d’actions.
* `OnActionExecutionAsync` est appelé avant tous les filtres de l’action. Le code dans le filtre après `next` s’exécute après la méthode d’action.

Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.

`TestController` :

* Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.
* Remplace `OnActionExecuting` et `OnActionExecuted`.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

La navigation vers `https://localhost:5001/Test/FilterTest2` exécute le code suivant :

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` . Les filtres au niveau du contrôleur **ne peuvent pas** être configurés pour s’exécuter après les filtres appliqués aux méthodes. L’ordre est expliqué dans la section suivante.

Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Remplacement de l’ordre par défaut

Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. `IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution. Un filtre avec une valeur `Order` inférieure :

* Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.
* Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.

La `Order` propriété est définie avec un paramètre de constructeur :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

Examinez les deux filtres d’action dans le contrôleur suivant :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

Un filtre global est ajouté dans `StartUp.ConfigureServices` :

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

Les 3 filtres s’exécutent dans l’ordre suivant :

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent. Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens. Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut. Comme mentionné précédemment, les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` pour les filtres intégrés, Scope détermine l’ordre, à moins que `Order` ne soit défini sur une valeur différente de zéro.

Dans le code précédent, `MySampleActionFilter` a une portée globale pour qu’elle s’exécute avant `MyAction2FilterAttribute` , qui a une portée de contrôleur. Pour effectuer `MyAction2FilterAttribute` l’exécution en premier, définissez l’ordre sur `int.MinValue` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

Pour que le filtre global `MySampleActionFilter` s’exécute en premier, définissez `Order` sur `int.MinValue` :

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>Annulation et court-circuit

Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre. Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`. `ShortCircuitingResourceFilter` :

* S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).
* Court-circuite le reste du pipeline.

Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`. Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier. `ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=1,15)]

## <a name="dependency-injection"></a>Injection de dépendances

Les filtres peuvent être ajoutés par type ou par instance. Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête. Si un type est ajouté, il est activé par type. Un filtre activé par type signifie :

* Une instance est créée pour chaque requête.
* Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).

Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection). Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :

* Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués. 
* Il s’agit d’une limitation de la façon dont les attributs fonctionnent.

Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.

Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :

Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances. Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement. L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement. Enregistrement ajouté aux filtres :

* Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.
* Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure. Les filtres intégrés consignent les actions et les événements du Framework.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`. Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.

Le code suivant affiche le `AddHeaderResultServiceFilter` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :

* Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée. Le runtime ASP.NET Core ne garantit pas :

  * Qu’une seule instance du filtre sera créée.
  * Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.

* Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.

 L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` charge le type spécifié à partir de l’injection de dépendances.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances. Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :

* Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.  Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.
* `TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.

Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :
* Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée. Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.

* Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.

L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtres d’autorisations

Filtres d’autorisations :

* Sont les premiers filtres à être exécutés dans le pipeline de filtres.
* Contrôlent l’accès aux méthodes d’action.
* Ont une méthode avant, mais pas de méthode après.

Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée. Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé. Le filtre d'autorisations intégré :

* Appelle le système d’autorisation.
* N’autoriser les requêtes.

Ne levez **pas** d’exceptions dans les filtres d’autorisations :

* L’exception ne sera pas gérée.
* Les filtres d’exceptions ne gèreront pas l’exception.

Songez à émettre une stimulation quand un filtre d’autorisations se produit.

Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtres de ressources

Filtres de ressources :

* Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.
* Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.

Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline. Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.

Exemples de filtre de ressources :

* [Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :

  * Il empêche la liaison de données d’accéder aux données de formulaire.
  * Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.

## <a name="action-filters"></a>Filtres d’actions

Les filtres d’action ne s’appliquent **pas** aux Razor pages. Razor Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).

Filtres d'actions :

* Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* Leur exécution entoure l’exécution de méthodes d’action.

Le code suivant montre un exemple de filtre d’actions :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> -active la lecture des entrées dans une méthode d’action.
* <xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.

Levée d’une exception dans une méthode d’action :

* Empêche l’exécution des filtres suivants.
* Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception. Paramètre de cette propriété sur null :

  * Gère effectivement une exception.
  * `Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.

Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :

* Exécute tous les filtres d’actions suivants et la méthode d’action.
* Retourne `ActionExecutedContext`.

Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).

L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.

Le filtre d’actions `OnActionExecuting` peut être utilisé pour :

* Valider l’état du modèle.
* Renvoyer une erreur si l’état n’est pas valide.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> Les contrôleurs annotés avec l' `[ApiController]` attribut valident automatiquement l’état du modèle et retournent une réponse 400. Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).

La méthode `OnActionExecuted` s’exécute après la méthode d’action :

* Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception. Le fait de définir `Exception` avec la valeur null :

  * Gère effectivement une exception.
  * `ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtres d’exceptions

Les filtres d’exceptions :

* Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.
* Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.

L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Le code suivant teste le filtre d’exception :

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

Les filtres d’exceptions :

* N’ont pas d’événements avant et après.
* Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.
* N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.

Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse. Ceci arrête la propagation de l’exception. Un filtre d’exceptions ne peut pas changer une exception en « réussite ». Seul un filtre d’actions peut le faire.

Les filtres d’exceptions :

* Conviennent pour intercepter des exceptions qui se produisent dans des actions.
* N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.

Privilégiez le middleware pour la gestion des exceptions. Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée. Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML. Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.

## <a name="result-filters"></a>Filtres de résultats

Filtres de résultats :

* Implémenter une interface :
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Leur exécution entoure l’exécution de résultats d’action.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter et IAsyncResultFilter

Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Le type de résultat à exécuter dépend de l’action. Une action qui retourne une vue comprend tout le traitement Razor dans le cadre du <xref:Microsoft.AspNetCore.Mvc.ViewResult> en cours d’exécution. Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat. En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).

Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action. Les filtres de résultats ne sont pas exécutés dans les cas suivants :

* Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.
* Un filtre d’exception gère une exception en générant un résultat d’action.

La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`. Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide. Levée d’une exception dans `IResultFilter.OnResultExecuting` :

* Empêche l’exécution du résultat de l’action et des filtres suivants.
* Est traité comme un échec au lieu d’un résultat réussi.

Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client. Si la réponse a déjà été envoyée au client, elle ne peut pas être modifiée.

`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.

`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception. La définition `Exception` de la valeur null gère efficacement une exception et empêche la levée de l’exception plus tard dans le pipeline. Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats. Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.

Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action. Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe. La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter

Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action. Cela comprend les résultats d’action produits par :

* Filtres d’autorisation et filtres de ressources qui court-circuitent.
* Filtres d’exception.

Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres. Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`. Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée. La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.

`IFilterFactory.IsReusable`:

* Est un indicateur par la fabrique que l’instance de filtre créée par la fabrique peut être réutilisée en dehors de la portée de la demande dans laquelle elle a été créée.
* ***Not** _ doit être utilisé avec un filtre qui dépend de services dont la durée de vie n’est pas le singleton.

Le runtime ASP.NET Core ne garantit pas :

_ Qu’une seule instance du filtre sera créée.
* Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.

> [!WARNING] 
> Configurez uniquement <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> pour retourner `true` si la source des filtres n’est pas ambiguë, si les filtres sont sans État, et si les filtres peuvent être utilisés en toute sécurité sur plusieurs requêtes http. Par exemple, ne renvoyez pas de filtres à partir de DI qui sont enregistrés comme délimités ou transitoires si `IFilterFactory.IsReusable` retourne `true` .

Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Le filtre est appliqué dans le code suivant :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

Testez le code précédent en exécutant l' [exemple Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):

* Appeler les outils de développement F12.
* Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.

Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :

* **Auteur :**`Rick Anderson`
* **globaladdheader :** `Result filter added to MvcOptions.Filters`
* **interne :**`My header`

Le code précédent crée l’en-tête de réponse **interne :** `My header`.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implémenté sur un attribut

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :

* Ne nécessitent pas le passage de paramètres.
* Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.

L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Utilisation d’un intergiciel dans le pipeline de filtres

Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline. Toutefois, les filtres diffèrent des intergiciels (middleware) en ce sens qu’ils font partie du runtime, ce qui signifie qu’ils ont accès au contexte et aux constructions.

Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres. Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.

## <a name="next-actions"></a>Actions suivantes

* Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).
* Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)

Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.

Les filtres intégrés gèrent notamment les tâches suivantes :

* Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).
* Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).

Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux. Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.  Les filtres évitent la duplication de code. Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.

Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.

[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([comment télécharger](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Fonctionnement des filtres

Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.  Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.

![La requête est traitée via un autre intergiciel, un intergiciel de routage, une sélection d’action et le pipeline d’appels d’action ASP.NET Core. Le traitement de la requête se poursuit via une sélection d’action, un intergiciel de routage et différents autres intergiciels avant de devenir une réponse envoyée au client.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Types de filtres

Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :

* Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête. Les filtres d’autorisations peuvent court-circuiter le pipeline si la requête n’est pas autorisée.

* [Filtres de ressources](#resource-filters):

  * Exécuter après l’autorisation.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> peut exécuter du code avant le reste du pipeline de filtres. Par exemple, `OnResourceExecuting` peut exécuter du code avant la liaison de données.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> peut exécuter du code une fois que le reste du pipeline terminé.

* Les [filtres d’actions](#action-filters) peuvent exécuter du code juste avant et juste après l’appel d’une méthode d’action individuelle. Ils peuvent être utilisés pour manipuler les arguments passés à une action et le résultat retourné depuis l’action. Les filtres d’action **ne sont pas** pris en charge dans les Razor pages.

* Les [filtres d’exceptions](#exception-filters) sont utilisés pour appliquer des stratégies globales à des exceptions non gérées qui se produisent avant que des éléments aient été écrits dans le corps de la réponse.

* Les [filtres de résultats](#result-filters) peuvent exécuter du code juste avant et juste après l’exécution des résultats d’une action individuelle. Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action. Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.

Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats. En sortie, la requête est traitée seulement par les filtres de résultats et les filtres de ressources avant de devenir une réponse envoyée au client.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implémentation

Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.

Les filtres synchrones peuvent exécuter du code avant (`On-Stage-Executing`) et après (`On-Stage-Executed`) leur étape de pipeline. Par exemple, `OnActionExecuting` est appelé avant l’appel de la méthode d’action. `OnActionExecuted` est appelé après le retour de la méthode d’action.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Les filtres asynchrones définissent une méthode `On-Stage-ExecutionAsync` :

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

Dans le code précédent, le `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) qui exécute la méthode d’action.  Chacune des méthodes `On-Stage-ExecutionAsync` prennent un `FilterType-ExecutionDelegate` qui exécute l’étape de pipeline du filtre.

### <a name="multiple-filter-stages"></a>Plusieurs étapes de filtres

Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe. Par exemple, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implémente `IActionFilter`, `IResultFilter` et leurs équivalents asynchrones.

Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** . Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface. Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone. Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée. Quand vous utilisez des classes abstraites comme <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, vous devez remplacer seulement les méthodes synchrones ou la méthode asynchrone pour chaque type de filtre.

### <a name="built-in-filter-attributes"></a>Attributs de filtre intégrés

ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser. Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus. Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.

Les attributs de filtre :

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Étendues de filtre et ordre d’exécution

Un filtre peut être ajouté au pipeline à l’une des trois *étendues* suivantes :

* À l’aide d’un attribut sur une action.
* À l’aide d’un attribut sur un contrôleur.
* Dans le monde entier pour tous les contrôleurs et actions, comme indiqué dans le code suivant :

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

Le code précédent ajoute trois filtres globalement à l’aide de la collection [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).

### <a name="default-order-of-execution"></a>Ordre d’exécution par défaut

Lorsqu’il existe plusieurs filtres *du même type*, l’étendue détermine l’ordre par défaut de l’exécution du filtre.  Filtres globaux-filtres de classe surround. Les filtres de classe entourent les filtres de méthode.

En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*. La séquence de filtre :

* Le code *avant* des filtres globaux.
  * Le code *avant* des filtres du contrôleur.
    * Le code *avant* des filtres de méthodes d’action.
    * Le code *après* des filtres de méthodes d’action.
  * Le code *après* des filtres du contrôleur.
* Le code *après* des filtres globaux.
  
Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.

| Séquence | Étendue de filtre | Méthode de filtre |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Contrôleur | `OnActionExecuting` |
| 3 | Méthode | `OnActionExecuting` |
| 4 | Méthode | `OnActionExecuted` |
| 5 | Contrôleur | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

Cette séquence montre que :

* Le filtre de méthode est imbriqué dans le filtre de contrôleur.
* Le filtre de contrôleur est imbriqué dans le filtre global.

### <a name="controller-and-no-locrazor-page-level-filters"></a>Filtres au niveau du contrôleur et de la Razor page

Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Ces méthodes :

* Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.
* `OnActionExecuting` est appelé avant tous les filtres de l’action.
* `OnActionExecuted` est appelé après tous les filtres d’actions.
* `OnActionExecutionAsync` est appelé avant tous les filtres de l’action. Le code dans le filtre après `next` s’exécute après la méthode d’action.

Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.

`TestController` :

* Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.
* Remplace `OnActionExecuting` et `OnActionExecuted`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

La navigation vers `https://localhost:5001/Test/FilterTest2` exécute le code suivant :

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Remplacement de l’ordre par défaut

Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. `IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution. Un filtre avec une valeur `Order` inférieure :

* Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.
* Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.

La propriété `Order` peut être définie avec un paramètre de constructeur :

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Prenez en compte les mêmes 3 filtres d’actions indiqués dans l’exemple précédent. Si la propriété `Order` du contrôleur et les filtres globaux sont définis sur 1 et 2 respectivement, l’ordre d’exécution est inversé.

| Séquence | Étendue de filtre | Propriété`Order` | Méthode de filtre |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Méthode | 0 | `OnActionExecuting` |
| 2 | Contrôleur | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | Contrôleur | 1  | `OnActionExecuted` |
| 6 | Méthode | 0  | `OnActionExecuted` |

La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent. Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens. Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut. Pour les filtres intégrés, l’étendue détermine l’ordre, sauf si `Order` est défini sur une valeur différente de zéro.

## <a name="cancellation-and-short-circuiting"></a>Annulation et court-circuit

Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre. Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`. `ShortCircuitingResourceFilter` :

* S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).
* Court-circuite le reste du pipeline.

Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`. Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier. `ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Injection de dépendances

Les filtres peuvent être ajoutés par type ou par instance. Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête. Si un type est ajouté, il est activé par type. Un filtre activé par type signifie :

* Une instance est créée pour chaque requête.
* Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).

Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection). Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :

* Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués. 
* Il s’agit d’une limitation de la façon dont les attributs fonctionnent.

Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.

Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :

Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances. Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement. L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement. Enregistrement ajouté aux filtres :

* Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.
* Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure. Les filtres intégrés enregistrent des actions et des événements d’infrastructure.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`. Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.

Le code suivant affiche le `AddHeaderResultServiceFilter` :

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :

* Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée. Le runtime ASP.NET Core ne garantit pas :

  * Qu’une seule instance du filtre sera créée.
  * Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.

* Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.

 L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` charge le type spécifié à partir de l’injection de dépendances.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances. Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :

* Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.  Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.
* `TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.

Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :
* Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée. Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.

* Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.

L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtres d’autorisations

Filtres d’autorisations :

* Sont les premiers filtres à être exécutés dans le pipeline de filtres.
* Contrôlent l’accès aux méthodes d’action.
* Ont une méthode avant, mais pas de méthode après.

Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée. Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé. Le filtre d'autorisations intégré :

* Appelle le système d’autorisation.
* N’autoriser les requêtes.

Ne levez **pas** d’exceptions dans les filtres d’autorisations :

* L’exception ne sera pas gérée.
* Les filtres d’exceptions ne gèreront pas l’exception.

Songez à émettre une stimulation quand un filtre d’autorisations se produit.

Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtres de ressources

Filtres de ressources :

* Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.
* Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.

Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline. Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.

Exemples de filtre de ressources :

* [Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :

  * Il empêche la liaison de données d’accéder aux données de formulaire.
  * Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.

## <a name="action-filters"></a>Filtres d’actions

> [!IMPORTANT]
> Les filtres d’action ne s’appliquent **pas** aux Razor pages. Razor Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).

Filtres d'actions :

* Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* Leur exécution entoure l’exécution de méthodes d’action.

Le code suivant montre un exemple de filtre d’actions :

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> : permet de lire les entrées vers une méthode d’action.
* <xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.

Levée d’une exception dans une méthode d’action :

* Empêche l’exécution des filtres suivants.
* Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception. Paramètre de cette propriété sur null :

  * Gère effectivement une exception.
  * `Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.

Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :

* Exécute tous les filtres d’actions suivants et la méthode d’action.
* Retourne `ActionExecutedContext`.

Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).

L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.

Le filtre d’actions `OnActionExecuting` peut être utilisé pour :

* Valider l’état du modèle.
* Renvoyer une erreur si l’état n’est pas valide.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

La méthode `OnActionExecuted` s’exécute après la méthode d’action :

* Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception. Le fait de définir `Exception` avec la valeur null :

  * Gère effectivement une exception.
  * `ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtres d’exceptions

Les filtres d’exceptions :

* Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>. 
* Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.

L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Les filtres d’exceptions :

* N’ont pas d’événements avant et après.
* Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.
* N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.

Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse. Ceci arrête la propagation de l’exception. Un filtre d’exceptions ne peut pas changer une exception en « réussite ». Seul un filtre d’actions peut le faire.

Les filtres d’exceptions :

* Conviennent pour intercepter des exceptions qui se produisent dans des actions.
* N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.

Privilégiez le middleware pour la gestion des exceptions. Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée. Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML. Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.

## <a name="result-filters"></a>Filtres de résultats

Filtres de résultats :

* Implémenter une interface :
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Leur exécution entoure l’exécution de résultats d’action.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter et IAsyncResultFilter

Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Le type de résultat à exécuter dépend de l’action. Une action retournant un affichage inclut tous les traitements Razor dans le cadre de la <xref:Microsoft.AspNetCore.Mvc.ViewResult> à exécuter. Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat. En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).

Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action. Les filtres de résultats ne sont pas exécutés dans les cas suivants :

* Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.
* Un filtre d’exception gère une exception en générant un résultat d’action.

La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`. Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide. La levée d’une exception dans `IResultFilter.OnResultExecuting` :

* Empêche l’exécution du résultat d’action et des filtres suivants.
* Est traitée comme une erreur et non comme une réussite.

Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client. Si la réponse a déjà été envoyée au client, elle ne peut plus être modifiée.

`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.

`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception. Le paramètre de `Exception` sur Null gère effectivement une exception et évite à l’exception d’être à nouveau levée par ASP.NET Core plus loin dans le pipeline. Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats. Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.

Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action. Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe. La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter

Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action. Cela comprend les résultats d’action produits par :

* Filtres d’autorisation et filtres de ressources qui court-circuitent.
* Filtres d’exception.

Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres. Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`. Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée. La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.

Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Le code précédent peut être testé en exécutant l’[échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) :

* Appeler les outils de développement F12.
* Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.

Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :

* **Auteur :**`Joe Smith`
* **globaladdheader :** `Result filter added to MvcOptions.Filters`
* **interne :**`My header`

Le code précédent crée l’en-tête de réponse **interne :** `My header`.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implémenté sur un attribut

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :

* Ne nécessitent pas le passage de paramètres.
* Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.

L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Utilisation d’un intergiciel dans le pipeline de filtres

Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline. Les filtres diffèrent cependant d’un intergiciel dans la mesure où ils font partie du runtime ASP.NET Core, ce qui signifie qu’ils ont accès au contexte et aux constructions ASP.NET Core.

Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres. Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.

## <a name="next-actions"></a>Actions suivantes

* Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).
* Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

::: moniker-end
