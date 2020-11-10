---
title: Routage vers les actions du contrôleur dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core MVC utilise le middleware (intergiciel) de routage pour mettre en correspondance les URL des requêtes entrantes et les mapper à des actions.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: 59ad373cefaa12370aa7c02a367125c7a94f59a6
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422598"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Routage vers les actions du contrôleur dans ASP.NET Core

Par [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)et [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Les contrôleurs de ASP.NET Core utilisent l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) de routage pour faire correspondre les URL des demandes entrantes et les mapper aux [actions](#action).  Itinéraires des modèles :

* Sont définis dans le code de démarrage ou les attributs.
* Décrivez comment les chemins d’URL sont mis en correspondance avec les [actions](#action).
* Sont utilisées pour générer des URL pour les liens. Les liens générés sont généralement retournés dans les réponses.

Les actions sont soit [de façon conventionnelle,](#cr) soit [routées par attribut](#ar). Le fait de placer un itinéraire sur le contrôleur ou l' [action](#action) le rend positionné par attribut. Pour plus d’informations, consultez [Routage mixte](#routing-mixed-ref-label).

Ce document :

* Explique les interactions entre MVC et le routage :
  * Comment les applications MVC classiques utilisent les fonctionnalités de routage.
  * Couvre les deux :
    * Le [routage conventionnel](#cr) est généralement utilisé avec les contrôleurs et les vues.
    * *Routage d’attribut* utilisé avec les API REST. Si vous êtes principalement intéressé par le routage des API REST, passez à la section relative à l' [acheminement des attributs pour les API REST](#ar) .
  * Consultez [routage](xref:fundamentals/routing) pour plus d’informations sur le routage avancé.
* Fait référence au système de routage par défaut ajouté dans ASP.NET Core 3,0, appelé routage de point de terminaison. Il est possible d’utiliser des contrôleurs avec la version précédente du routage pour des raisons de compatibilité. Pour obtenir des instructions, consultez le [Guide de migration 2.2-3.0](xref:migration/22-to-30) . Reportez-vous à la [version 2,2 de ce document](xref:mvc/controllers/routing?view=aspnetcore-2.2) pour obtenir des documents de référence sur le système de routage hérité.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Configurer l’itinéraire conventionnel

`Startup.Configure` a généralement un code similaire à ce qui suit lors de l’utilisation du [routage conventionnel](#crd):

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

À l’intérieur de l’appel à <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> est utilisé pour créer un itinéraire unique. L’itinéraire unique est nommé `default` route. La plupart des applications avec contrôleurs et vues utilisent un modèle de routage similaire à l' `default` itinéraire. Les API REST doivent utiliser le [routage d’attributs](#ar).

Le modèle de routage `"{controller=Home}/{action=Index}/{id?}"` :

* Correspond à un chemin d’URL comme `/Products/Details/5`
* Extrait les valeurs d’itinéraire `{ controller = Products, action = Details, id = 5 }` en tokenant le chemin d’accès. L’extraction des valeurs de route aboutit à une correspondance si l’application possède un contrôleur nommé `ProductsController` et une `Details` action :

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5` le modèle lie la valeur de `id = 5` pour définir le `id` paramètre sur `5` . Pour plus d’informations, consultez [liaison de modèle](xref:mvc/models/model-binding) .
* `{controller=Home}` définit `Home` comme valeur par défaut `controller` .
* `{action=Index}` définit `Index` comme valeur par défaut `action` .
*  Le `?` caractère dans `{id?}` définit `id` comme étant facultatif.
  * Les paramètres de route par défaut et facultatifs n’ont pas besoin d’être présents dans le chemin d’URL pour qu’une correspondance soit établie. Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).
* Correspond au chemin d’accès de l’URL `/` .
* Produit les valeurs de route `{ controller = Home, action = Index }` .

Les valeurs pour `controller` et `action` utilisent les valeurs par défaut. `id` ne produit pas de valeur, car il n’existe pas de segment correspondant dans le chemin d’accès de l’URL. `/` correspond uniquement s’il existe une `HomeController` `Index` action et :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

À l’aide de la définition de contrôleur et du modèle de routage précédents, l' `HomeController.Index` action est exécutée pour les chemins d’URL suivants :

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Le chemin d’accès de l’URL `/` utilise les `Home` contrôleurs et l’action du modèle de routage par défaut `Index` . Le chemin d’accès de l’URL `/Home` utilise l’action par défaut du modèle de routage `Index` .

La méthode pratique <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> :

```csharp
endpoints.MapDefaultControllerRoute();
```

Remplace

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Le routage est configuré à l’aide de et de l' <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> intergiciel (middleware). Pour utiliser des contrôleurs :
>
> * Appelez <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> à l’intérieur `UseEndpoints` pour mapper les contrôleurs [routés d’attribut](#ar) .
> * Appelez <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> ou <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> pour mapper les contrôleurs routés de façon [conventionnelle](#cr) et les contrôleurs [routés d’attribut](#ar) .

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Routage conventionnel

Le routage conventionnel est utilisé avec les contrôleurs et les vues. La route `default` :

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

est un exemple de *routage conventionnel*. Il s’agit du *routage conventionnel* , car il établit une *Convention* pour les chemins d’URL :

* Le premier segment de chemin d’accès, `{controller=Home}` , correspond au nom du contrôleur.
* Le deuxième segment, `{action=Index}` , correspond au nom de l' [action](#action) .
* Le troisième segment, `{id?}` est utilisé pour un facultatif `id` . Le `?` dans le `{id?}` rend facultatif. `id` est utilisé pour mapper à une entité de modèle.

À l’aide de cet `default` itinéraire, le chemin d’accès de l’URL :

* `/Products/List` correspond à l' `ProductsController.List` action.
* `/Blog/Article/17` est mappé à `BlogController.Article` et en général, le modèle lie le `id` paramètre à 17.

Ce mappage :

* Est basé **uniquement** sur les noms de contrôleur et d' [action](#action) .
* N’est pas basé sur des espaces de noms, des emplacements de fichiers sources ou des paramètres de méthode.

L’utilisation du routage conventionnel avec l’itinéraire par défaut permet de créer l’application sans avoir à trouver un nouveau modèle d’URL pour chaque action. Pour une application avec des actions de style [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , avec cohérence des URL entre les contrôleurs :

* Permet de simplifier le code.
* Rend l’interface utilisateur plus prévisible.

> [!WARNING]
> Le `id` dans le code précédent est défini comme facultatif par le modèle de routage. Les actions peuvent s’exécuter sans l’ID facultatif fourni dans le cadre de l’URL. En règle générale, lorsque `id` est omis de l’URL :
>
> * `id` a la valeur `0` par liaison de modèle.
> * Aucune entité n’a été trouvée dans la correspondance de base de données `id == 0` .
>
> Le [routage d’attributs](#ar) fournit un contrôle affiné pour rendre l’ID requis pour certaines actions et non pour d’autres. Par Convention, la documentation comprend des paramètres facultatifs tels que le `id` moment où ils sont susceptibles d’apparaître dans une utilisation correcte.

La plupart des applications doivent choisir un schéma de routage de base et descriptif pour que les URL soient lisibles et explicites. La route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` :

* Prend en charge un schéma de routage de base et descriptif.
* Est un point de départ pratique pour les applications basées sur une interface utilisateur.
* Est le seul modèle de routage nécessaire pour de nombreuses applications d’interface utilisateur Web. Pour les applications d’interface utilisateur Web plus volumineuses, un autre itinéraire utilisant des [zones](#areas) est souvent le plus nécessaire.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> et <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :

* Assigner automatiquement une valeur de **commande** à leurs points de terminaison en fonction de l’ordre dans lequel ils sont appelés.

Routage des points de terminaison dans ASP.NET Core 3,0 et versions ultérieures :

* N’a pas de concept d’itinéraires.
* Ne fournit pas de garantie de classement pour l’exécution de l’extensibilité, tous les points de terminaison sont traités à la fois.

Activez la [journalisation](xref:fundamentals/logging/index) pour voir comment les implémentations de routage intégrées, comme <xref:Microsoft.AspNetCore.Routing.Route>, établissent des correspondances avec les requêtes.

Le [routage des attributs](#ar) est expliqué plus loin dans ce document.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Plusieurs itinéraires conventionnels

Vous pouvez ajouter plusieurs [itinéraires conventionnels](#cr) dans `UseEndpoints` en ajoutant des appels à <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> et <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> . Cela permet de définir plusieurs conventions ou d’ajouter des itinéraires conventionnels dédiés à une [action](#action)spécifique, par exemple :

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

L' `blog` itinéraire dans le code précédent est un **itinéraire conventionnel dédié**. Il s’agit d’un itinéraire conventionnel dédié, car :

* Il utilise le [routage conventionnel](#cr).
* Elle est dédiée à une [action](#action)spécifique.

Étant donné que `controller` et `action` n’apparaissent pas dans le modèle de routage `"blog/{*article}"` en tant que paramètres :

* Ils peuvent uniquement avoir les valeurs par défaut `{ controller = "Blog", action = "Article" }` .
* Cet itinéraire est toujours mappé à l’action `BlogController.Article` .

`/Blog`, `/Blog/Article` et `/Blog/{any-string}` sont les seuls chemins d’URL qui correspondent à l’itinéraire du blog.

L’exemple précédent :

* `blog` l’itinéraire a une priorité plus élevée pour les correspondances que l' `default` itinéraire, car il est d’abord ajouté.
* Est un exemple de routage de style [Slug](https://developer.mozilla.org/docs/Glossary/Slug) dans lequel il est courant d’avoir un nom d’article dans le cadre de l’URL.

> [!WARNING]
> Dans ASP.NET Core 3,0 et versions ultérieures, le routage ne suit pas :
> * Définissez un concept appelé *itinéraire*. `UseRouting` Ajoute la correspondance d’itinéraire au pipeline de l’intergiciel (middleware). L' `UseRouting` intergiciel examine l’ensemble des points de terminaison définis dans l’application et sélectionne la meilleure correspondance de point de terminaison en fonction de la demande.
> * Fournir des garanties sur l’ordre d’exécution de l’extensibilité comme <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> ou <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .
>
>Consultez [routage](xref:fundamentals/routing) pour obtenir des documents de référence sur le routage.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Ordre de routage conventionnel

Le routage conventionnel correspond uniquement à une combinaison d’action et de contrôleur définie par l’application. Cela vise à simplifier les cas où les itinéraires conventionnels se chevauchent.
L’ajout d’itinéraires à l’aide de <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> et <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> assigne automatiquement une valeur de commande à leurs points de terminaison en fonction de l’ordre dans lequel ils sont appelés. Les correspondances d’un itinéraire qui apparaît précédemment ont une priorité plus élevée. Le routage conventionnel est dépendant de l’ordre. En général, les itinéraires avec des zones doivent être placés plus tôt, car ils sont plus spécifiques que les itinéraires sans zone. Les [itinéraires conventionnels dédiés](#dcr) avec des paramètres d’itinéraire Catch-All comme `{*article}` peuvent rendre une route trop [gourmande](xref:fundamentals/routing#greedy), ce qui signifie qu’elle correspond aux URL que vous avez prévues pour être mises en correspondance par d’autres itinéraires. Mettez les itinéraires gourmands plus tard dans la table de routage pour empêcher les correspondances gourmandes.

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Résolution des actions ambiguës

Lorsque deux points de terminaison correspondent au routage, le routage doit effectuer l’une des opérations suivantes :

* Choisissez le meilleur candidat.
* Levée d'une exception.

Par exemple :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Le contrôleur précédent définit deux actions qui correspondent :

* Chemin de l’URL `/Products33/Edit/17`
* Acheminer les données `{ controller = Products33, action = Edit, id = 17 }` .

Il s’agit d’un modèle classique pour les contrôleurs MVC :

* `Edit(int)` affiche un formulaire pour modifier un produit.
* `Edit(int, Product)` traite le formulaire publié.

Pour résoudre le routage correct :

* `Edit(int, Product)` est sélectionné lorsque la requête est HTTP `POST` .
* `Edit(int)` est sélectionné lorsque le [verbe http](#verb) est autre chose. `Edit(int)` est généralement appelé via `GET` .

Le <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , est fourni au routage afin qu’il puisse choisir en fonction de la méthode http de la requête. Le `HttpPostAttribute` fournit `Edit(int, Product)` une meilleure correspondance que `Edit(int)` .

Il est important de comprendre le rôle des attributs comme `HttpPostAttribute` . Des attributs similaires sont définis pour d’autres [verbes HTTP](#verb). Dans le cadre d’un [routage conventionnel](#cr), il est courant que des actions utilisent le même nom d’action lorsqu’elles font partie d’un formulaire d’affichage, envoyer un flux de travail de formulaire. Par exemple, consultez [examiner les deux méthodes d’action de modification](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Si le routage ne peut pas choisir un meilleur candidat, une <xref:System.Reflection.AmbiguousMatchException> exception est levée et répertorie les différents points de terminaison correspondants.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Noms de routes conventionnels

Les chaînes  `"blog"` et `"default"` dans les exemples suivants sont des noms de route conventionnels :

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Les noms de routes donnent un nom logique à l’itinéraire. L’itinéraire nommé peut être utilisé pour la génération d’URL. L’utilisation d’un itinéraire nommé simplifie la création d’URL lorsque l’ordonnancement des itinéraires peut compliquer la génération d’URL. Les noms de routes doivent être uniques à l’ensemble de l’application.

Noms des itinéraires :

* N’ont aucun impact sur la correspondance d’URL ou la gestion des demandes.
* Sont utilisés uniquement pour la génération d’URL.

Le concept de nom d’itinéraire est représenté dans le routage en tant que [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). Nom de l' **itinéraire** et **nom du point de terminaison** :

* Sont interchangeables.
* Celui qui est utilisé dans la documentation et le code dépend de l’API qui est décrite.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Routage d’attribut pour les API REST

Les API REST doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources où les opérations sont représentées par des [verbes HTTP](#verb).

Le routage par attributs utilise un ensemble d’attributs pour mapper les actions directement aux modèles de routes. Le `StartUp.Configure` code suivant est courant pour une API REST et est utilisé dans l’exemple suivant :

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

Dans le code précédent, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> est appelé dans `UseEndpoints` pour mapper les contrôleurs routés d’attribut.

Dans l’exemple suivant :

* La `Configure` méthode précédente est utilisée.
* `HomeController` correspond à un ensemble d’URL similaires à ce que la route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` correspond.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

L' `HomeController.Index` action est exécutée pour tous les chemins d’accès d’URL `/` ,, `/Home` `/Home/Index` ou `/Home/Index/3` .

Cet exemple met en évidence une différence de programmation clé entre le routage d’attributs et le [routage conventionnel](#cr). Le routage des attributs requiert plus d’entrée pour spécifier un itinéraire. L’itinéraire par défaut conventionnel gère les routes de façon plus succincte. Toutefois, le routage d’attributs permet et requiert un contrôle précis des modèles de routage qui s’appliquent à chaque [action](#action).

Avec le routage d’attributs, les noms de contrôleur et d’action ne jouent aucun rôle dans lequel l’action est mise en correspondance, sauf si le [remplacement de jeton](#routing-token-replacement-templates-ref-label) est utilisé. L’exemple suivant correspond aux mêmes URL que l’exemple précédent :

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Le code suivant utilise le remplacement de jeton pour `action` et `controller` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Le code suivant s’applique `[Route("[controller]/[action]")]` au contrôleur :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Dans le code précédent, les `Index` modèles de méthode doivent précéder `/` ou `~/` atteindre les modèles de routage. Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur.

Pour plus d’informations sur la sélection d’un modèle de routage, consultez [priorité des modèles de routage](xref:fundamentals/routing#rtp) .

## <a name="reserved-routing-names"></a>Noms de routage réservés

Les mots clés suivants sont des noms de paramètres d’itinéraire réservés lors de l’utilisation de contrôleurs ou de Razor pages :

* `action`
* `area`
* `controller`
* `handler`
* `page`

L’utilisation de `page` comme paramètre d’itinéraire avec routage d’attribut est une erreur courante. Cela entraîne un comportement incohérent et confus avec la génération d’URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Les noms de paramètres spéciaux sont utilisés par la génération d’URL pour déterminer si une opération de génération d’URL fait référence à une Razor page ou à un contrôleur.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Modèles de verbe HTTP

ASP.NET Core a les modèles de verbe HTTP suivants :

* [HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Modèles de routage

ASP.NET Core contient les modèles d’itinéraire suivants :

* Tous les [modèles de verbe http](#verb) sont des modèles de routage.
* [Itinéraire](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Routage d’attribut avec attributs de verbe http

Prenons le contrôleur suivant :

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

Dans le code précédent :

* Chaque action contient l' `[HttpGet]` attribut, qui limite la correspondance aux requêtes http obtient uniquement.
* L' `GetProduct` action comprend le `"{id}"` modèle. par conséquent, `id` est ajouté au `"api/[controller]"` modèle sur le contrôleur. Le modèle de méthode est `"api/[controller]/"{id}""` . Par conséquent, cette action correspond uniquement aux demandes d’extraction pour le formulaire `/api/test2/xyz` ,, `/api/test2/123` `/api/test2/{any string}` , etc.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* L' `GetIntProduct` action contient le `"int/{id:int}")` modèle. La `:int` partie du modèle limite les `id` valeurs d’itinéraire aux chaînes qui peuvent être converties en un entier. Une demande d’accès à `/api/test2/int/abc` :
  * Ne correspond pas à cette action.
  * Retourne une erreur [404 introuvable](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* L' `GetInt2Product` action contient `{id}` dans le modèle, mais ne contraint pas les `id` valeurs qui peuvent être converties en entier. Une demande d’accès à `/api/test2/int2/abc` :
  * Correspond à cet itinéraire.
  * La liaison de modèle ne peut pas être convertie `abc` en entier. Le `id` paramètre de la méthode est un entier.
  * Retourne une [demande 400 incorrecte](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , car la liaison de modèle n’a pas pu être convertie `abc` en entier.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Le routage d’attribut peut utiliser des <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributs tels que <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> et <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> . Tous les attributs de [verbe http](#verb) acceptent un modèle d’itinéraire. L’exemple suivant montre deux actions qui correspondent au même modèle de routage :

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Utilisation du chemin d’accès de l’URL `/products3` :

* L' `MyProductsController.ListProducts` action s’exécute lorsque le [verbe http](#verb) est `GET` .
* L' `MyProductsController.CreateProduct` action s’exécute lorsque le [verbe http](#verb) est `POST` .

Lors de la création d’une API REST, il est rare que vous deviez utiliser `[Route(...)]` sur une méthode d’action, car l’action accepte toutes les méthodes http. Il est préférable d’utiliser l’attribut de [verbe http](#verb) plus spécifique pour préciser ce que votre API prend en charge. Les clients des API REST doivent normalement connaître les chemins et les verbes HTTP qui correspondent à des opérations logiques spécifiques.

Les API REST doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources où les opérations sont représentées par des verbes HTTP. Cela signifie que de nombreuses opérations, par exemple, obtenir et poster sur la même ressource logique, utilisent la même URL. Le routage d’attributs fournit le niveau de contrôle nécessaire pour concevoir avec soin la disposition des points de terminaison publics d’une API.

Dans la mesure où une route d’attribut s’applique à une action spécifique, il est facile de placer les paramètres nécessaires dans la définition du modèle de route. Dans l’exemple suivant, `id` est obligatoire dans le cadre du chemin d’accès de l’URL :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

L' `Products2ApiController.GetProduct(int)` action :

* Est exécuté avec un chemin d’URL comme `/products2/3`
* N’est pas exécuté avec le chemin d’accès de l’URL `/products2` .

L’attribut [[Consommed]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permet à une action de limiter les types de contenu de demande pris en charge. Pour plus d’informations, consultez [définir les types de contenu de demande pris en charge avec l’attribut consomme](xref:web-api/index#consumes).

 Consultez [Routage](xref:fundamentals/routing) pour obtenir une description complète des modèles de routes et des options associées.

Pour plus d’informations sur `[ApiController]` , consultez [attribut ApiController](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Nom de l’itinéraire

Le code suivant définit un nom de route`Products_List` :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Les noms de routes peuvent être utilisés pour générer une URL basée sur une route spécifique. Noms des itinéraires :

* N’ont aucun impact sur le comportement de correspondance d’URL du routage.
* Sont utilisés uniquement pour la génération d’URL.

Les noms de routes doivent être unique à l’échelle de l’application.

Comparez le code précédent avec l’itinéraire par défaut conventionnel, qui définit le `id` paramètre comme facultatif ( `{id?}` ). La possibilité de spécifier avec précision les API présente des avantages, tels que l’autorisation `/products` et la `/products/5` distribution à des actions différentes.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Combinaison d’itinéraires d’attributs

Pour rendre le routage par attributs moins répétitif, les attributs de route sont combinés avec des attributs de route sur les actions individuelles. Les modèles de routes définis sur le contrôleur sont ajoutés à des modèles de routes sur les actions. Placer un attribut de route sur le contrôleur a pour effet que **toutes** les actions du contrôleur utilisent le routage par attributs.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

Dans l’exemple précédent :

* Le chemin d’accès de l’URL `/products` peut correspondre `ProductsApi.ListProducts`
* Le chemin d’accès de l’URL `/products/5` peut correspondre `ProductsApi.GetProduct(int)` .

Ces deux actions correspondent uniquement à HTTP `GET` , car elles sont marquées avec l' `[HttpGet]` attribut.

Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur. L’exemple suivant correspond à un ensemble de chemins d’URL similaires à l’itinéraire par défaut.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

Le tableau suivant décrit les `[Route]` attributs dans le code précédent :

| Attribut               | Est combiné avec `[Route("Home")]` | Définit le modèle de routage |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Oui | `"Home"` |
| `[Route("Index")]` | Oui | `"Home/Index"` |
| `[Route("/")]` | **Non** | `""` |
| `[Route("About")]` | Oui | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Ordre de routage des attributs

Le routage génère une arborescence et met en correspondance tous les points de terminaison simultanément :

* Les entrées d’itinéraire se comportent comme si elles étaient placées dans un ordre idéal.
* Les itinéraires les plus spécifiques ont une chance de s’exécuter avant les itinéraires plus généraux.

Par exemple, un itinéraire d’attribut comme `blog/search/{topic}` est plus spécifique qu’un itinéraire d’attribut comme `blog/{*article}` . L' `blog/search/{topic}` itinéraire a une priorité plus élevée, par défaut, car il est plus spécifique. En utilisant le [routage conventionnel](#cr), le développeur est chargé de placer les routes dans l’ordre souhaité.

Les routes d’attribut peuvent configurer une commande à l’aide de la <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> propriété. Tous les [attributs d’itinéraire](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) fournis par l’infrastructure incluent `Order` . Les routes sont traitées selon un ordre croissant de la propriété `Order`. L’ordre par défaut est `0`. La définition d’un itinéraire à l’aide de `Order = -1` s’exécute avant les itinéraires qui ne définissent pas de commande. La définition d’un itinéraire à l’aide de `Order = 1` s’exécute après l’ordonnancement par défaut.

**Évitez** de dépendre de `Order` . Si l’espace URL d’une application exige que les valeurs d’ordre explicites soient routées correctement, il est probable que les clients soient également déroutants. En général, le routage des attributs sélectionne l’itinéraire correct avec la correspondance d’URL. Si l’ordre par défaut utilisé pour la génération d’URL ne fonctionne pas, l’utilisation d’un nom d’itinéraire comme remplacement est généralement plus simple que l’application de la `Order` propriété.

Considérez les deux contrôleurs suivants qui définissent la correspondance d’itinéraire `/home` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

`/home`La demande avec le code précédent lève une exception semblable à la suivante :

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

`Order`L’ajout de à l’un des attributs de route résout l’ambiguïté :

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Avec le code précédent, `/home` exécute le `HomeController.Index` point de terminaison. Pour accéder à la `MyDemoController.MyIndex` requête, `/home/MyIndex` . **Remarque**  :

* Le code précédent est un exemple ou une conception de routage médiocre. Il a été utilisé pour illustrer la `Order` propriété.
* La `Order` propriété ne résout que l’ambiguïté, ce modèle ne peut pas être mis en correspondance. Il serait préférable de supprimer le `[Route("Home")]` modèle.

Pour plus d’informations sur l’ordre des itinéraires avec les pages [ Razor , consultez conventions des applications et des itinéraires](xref:razor-pages/razor-pages-conventions#route-order) de routage Razor .

Dans certains cas, une erreur HTTP 500 est retournée avec des itinéraires ambigus. Utilisez la [journalisation](xref:fundamentals/logging/index) pour voir quels points de terminaison ont provoqué le `AmbiguousMatchException` .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Remplacement de jetons dans les modèles de routage [contrôleur], [action], [zone]

Pour plus de commodité, les itinéraires d’attributs prennent en charge le remplacement de jeton pour les paramètres d’itinéraire réservés en plaçant un jeton dans l’un des éléments suivants :

* Crochets : `[]`
* Accolades : `{}`

Les jetons `[action]` , `[area]` et `[controller]` sont remplacés par les valeurs du nom d’action, du nom de la zone et du nom de contrôleur de l’action dans laquelle l’itinéraire est défini :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Dans le code précédent :

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Correspond `/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Correspond `/Products0/Edit/{id}`

Le remplacement des jetons se produit à la dernière étape de la création des routes d’attribut. L’exemple précédent se comporte de la même façon que le code suivant :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Les routes d’attribut peuvent aussi être combinées avec l’héritage. Il s’agit d’une puissante combinaison avec le remplacement des jetons. Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`génère un nom d’itinéraire unique pour chaque action :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 génère un nom de route unique pour chaque action.

Pour faire correspondre le délimiteur littéral de remplacement de jetons `[` ou `]`, placez-le en échappement en répétant le caractère (`[[` ou `]]`).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons

Le remplacement des jetons peut être personnalisé à l’aide d’un transformateur de paramètre. Un transformateur de paramètre implémente <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> et transforme la valeur des paramètres. Par exemple, un `SlugifyParameterTransformer` transformateur de paramètre personnalisé remplace la `SubscriptionManagement` valeur d’itinéraire par `subscription-management` :

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> est une convention de modèle d’application qui :

* Applique un transformateur de paramètre à toutes les routes d’attribut dans une application.
* Personnalise les valeurs de jeton de route d’attribut quand elles sont remplacées.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

La `ListAll` méthode précédente correspond à `/subscription-management/list-all` .

`RouteTokenTransformerConvention` est inscrit en tant qu’option dans `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Consultez la [documentation Web MDN sur Slug](https://developer.mozilla.org/docs/Glossary/Slug) pour connaître la définition de Slug.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Plusieurs itinéraires d’attributs

Le routage par attributs prend en charge la définition de plusieurs routes pour atteindre la même action. L’utilisation la plus courante de ceci est d’imiter le comportement de la route conventionnelle par défaut, comme le montre l’exemple suivant :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Le fait de placer plusieurs attributs de route sur le contrôleur signifie que chacun d’eux est combiné avec chacun des attributs de route sur les méthodes d’action :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Toutes les contraintes d’itinéraire [http Verb](#verb) implémentent `IActionConstraint` .

Quand plusieurs attributs d’itinéraire implémentent <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> sont placés sur une action :

* Chaque contrainte d’action est associée au modèle de routage appliqué au contrôleur.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

L’utilisation de plusieurs itinéraires sur des actions peut paraître utile et puissante, mais il est préférable de conserver l’espace d’URL de base et bien défini. Utilisez plusieurs itinéraires sur des actions **uniquement** lorsque cela est nécessaire, par exemple pour prendre en charge les clients existants.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Spécification facultative de paramètres, de valeurs par défaut et de contraintes pour les routes d’attribut

Les routes d’attribut prennent en charge la même syntaxe inline que les routes conventionnelles pour spécifier des paramètres, des valeurs par défaut et des contraintes facultatifs.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

Dans le code précédent, `[HttpPost("product/{id:int}")]` applique une contrainte d’itinéraire. L' `ProductsController.ShowProduct` action est mise en correspondance uniquement par les chemins d’accès d’URL tels que `/product/3` . La partie de modèle `{id:int}` de routage limite ce segment à des entiers uniquement.

Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Attributs d’itinéraire personnalisés à l’aide de IRouteTemplateProvider

Tous les [attributs d’itinéraire](#rt) implémentent <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> . Le runtime ASP.NET Core :

* Recherche des attributs sur les classes de contrôleur et les méthodes d’action au démarrage de l’application.
* Utilise les attributs qui implémentent `IRouteTemplateProvider` pour générer le jeu initial d’itinéraires.

Implémentez `IRouteTemplateProvider` pour définir des attributs de routage personnalisés. Chaque `IRouteTemplateProvider` vous permet de définir une route avec un modèle, un nom et un ordre de route personnalisés :

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

La `Get` méthode précédente retourne `Order = 2, Template = api/MyTestApi` .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Utiliser le modèle d’application pour personnaliser les itinéraires d’attributs

Le modèle d’application :

* Est un modèle objet créé au démarrage.
* Contient toutes les métadonnées utilisées par ASP.NET Core pour router et exécuter les actions dans une application.

Le modèle d’application comprend toutes les données collectées à partir des attributs d’itinéraire. Les données des attributs de route sont fournies par l' `IRouteTemplateProvider` implémentation de. Utilisées

* Peut être écrit pour modifier le modèle d’application afin de personnaliser le comportement du routage.
* Sont lus au démarrage de l’application.

Cette section présente un exemple simple de personnalisation du routage à l’aide du modèle d’application. Le code suivant permet aux itinéraires de s’aligner à peu près avec la structure de dossiers du projet.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Le code suivant empêche l' `namespace` application de la Convention aux contrôleurs qui sont des attributs routés :

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Par exemple, le contrôleur suivant n’utilise pas `NamespaceRoutingConvention` :

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

La méthode `NamespaceRoutingConvention.Apply` :

* Ne fait rien si le contrôleur est un attribut routé.
* Définit le modèle de contrôleurs en fonction de `namespace` , avec la base `namespace` supprimée.

Le `NamespaceRoutingConvention` peut être appliqué dans `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Considérons, par exemple, le contrôleur suivant :

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Dans le code précédent :

* La base `namespace` est `My.Application` .
* Le nom complet du contrôleur précédent est `My.Application.Admin.Controllers.UsersController` .
* `NamespaceRoutingConvention`Définit le modèle de contrôleurs sur `Admin/Controllers/Users/[action]/{id?` .

`NamespaceRoutingConvention`Peut également être appliqué en tant qu’attribut sur un contrôleur :

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Routage mixte : routage conventionnel et routage par attributs

Les applications ASP.NET Core peuvent combiner l’utilisation du routage conventionnel et du routage des attributs. Il est courant d’utiliser des routes conventionnelles pour les contrôleurs délivrant des pages HTML pour les navigateurs, et le routage par attributs pour les contrôleurs délivrant des API REST.

Les actions sont routées de façon conventionnelle ou routées par attribut. Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ». Les actions qui définissent des routes d’attribut ne sont pas accessibles via les routes conventionnelles et vice versa. **Tout** attribut de route sur le contrôleur effectue **toutes les** actions dans l’attribut de contrôleur routé.

Le routage des attributs et le routage conventionnel utilisent le même moteur de routage.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Génération d’URL et valeurs ambiantes

Les applications peuvent utiliser les fonctionnalités de génération d’URL de routage pour générer des liens URL vers des actions. La génération d’URL élimine le codage en dur des URL, ce qui rend le code plus robuste et plus facile à gérer. Cette section se concentre sur les fonctionnalités de génération d’URL fournies par MVC et couvre uniquement les notions de base du fonctionnement de la génération d’URL. Pour une description détaillée de la génération d’URL, consultez [Routage](xref:fundamentals/routing).

L' <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface est l’élément sous-jacent de l’infrastructure entre MVC et le routage pour la génération d’URL. Une instance de `IUrlHelper` est disponible via la `Url` propriété dans les contrôleurs, les vues et les composants de vue.

Dans l’exemple suivant, l' `IUrlHelper` interface est utilisée par le biais de la `Controller.Url` propriété pour générer une URL vers une autre action.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Si l’application utilise l’itinéraire conventionnel par défaut, la valeur de la `url` variable est la chaîne de chemin d’URL `/UrlGeneration/Destination` . Ce chemin d’URL est créé par le routage en combinant :

* Valeurs d’itinéraire de la requête actuelle, qui sont appelées **valeurs ambiantes**.
* Valeurs passées à `Url.Action` et substituant ces valeurs dans le modèle de routage :

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

La valeur de chaque paramètre de route du modèle de route est remplacée en établissant une correspondance avec les valeurs et les valeurs ambiantes. Un paramètre d’itinéraire qui n’a pas de valeur peut :

* Utilisez une valeur par défaut s’il en a une.
* Être ignoré s’il est facultatif. Par exemple, `id` à partir du modèle de routage `{controller}/{action}/{id?}` .

La génération d’URL échoue si un paramètre d’itinéraire requis n’a pas de valeur correspondante. Si la génération d’URL échoue pour une route, la route suivante est essayée, ceci jusqu’à ce que toutes les routes aient été essayées ou qu’une correspondance soit trouvée.

L’exemple précédent de `Url.Action` suppose un [routage conventionnel](#cr). La génération d’URL fonctionne de la même manière avec le [routage des attributs](#ar), bien que les concepts soient différents. Avec routage conventionnel :

* Les valeurs de route sont utilisées pour développer un modèle.
* Les valeurs de route pour `controller` et `action` apparaissent généralement dans ce modèle. Cela fonctionne parce que les URL mises en correspondance par le routage adhèrent à une convention.

L’exemple suivant utilise le routage d’attributs :

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

L' `Source` action dans le code précédent génère `custom/url/to/destination` .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> a été ajouté dans ASP.NET Core 3,0 comme alternative à `IUrlHelper` . `LinkGenerator` offre des fonctionnalités similaires mais plus flexibles. Chaque méthode sur `IUrlHelper` a également une famille de méthodes correspondante `LinkGenerator` .

### <a name="generating-urls-by-action-name"></a>Génération des URL par nom d’action

[URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)et toutes les surcharges associées sont toutes conçues pour générer le point de terminaison cible en spécifiant un nom de contrôleur et un nom d’action.

Lors de l’utilisation `Url.Action` de, les valeurs d’itinéraire actuelles pour `controller` et `action` sont fournies par le Runtime :

* La valeur de `controller` et `action` font partie des valeurs et valeurs [ambiantes](#ambient) . La méthode `Url.Action` utilise toujours les valeurs actuelles de `action` et `controller` et génère un chemin d’accès d’URL qui achemine vers l’action actuelle.

Le routage tente d’utiliser les valeurs des valeurs ambiantes pour renseigner les informations qui n’ont pas été fournies lors de la génération d’une URL. Prenons l’exemple d’un itinéraire `{a}/{b}/{c}/{d}` avec des valeurs ambiantes `{ a = Alice, b = Bob, c = Carol, d = David }` :

* Le routage dispose de suffisamment d’informations pour générer une URL sans aucune valeur supplémentaire.
* Le routage a suffisamment d’informations, car tous les paramètres d’itinéraire ont une valeur.

Si la valeur `{ d = Donovan }` est ajoutée :

* La valeur `{ d = David }` est ignorée.
* Le chemin d’URL généré est `Alice/Bob/Carol/Donovan` .

**Avertissement** : les chemins d’accès d’URL sont hiérarchiques. Dans l’exemple précédent, si la valeur `{ c = Cheryl }` est ajoutée :

* Les deux valeurs `{ c = Carol, d = David }` sont ignorées.
* Il n’y a plus de valeur pour `d` et la génération d’URL échoue.
* Les valeurs souhaitées de `c` et `d` doivent être spécifiées pour générer une URL.  

Vous pouvez vous attendre à rencontrer ce problème avec l’itinéraire par défaut `{controller}/{action}/{id?}` . Ce problème est rare dans la pratique, car `Url.Action` spécifie toujours explicitement une `controller` `action` valeur et.

Plusieurs surcharges d' [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) acceptent un objet de valeurs d’itinéraire pour fournir des valeurs pour les paramètres de routage autres que `controller` et `action` . L’objet de valeurs d’itinéraire est fréquemment utilisé avec `id` . Par exemple, `Url.Action("Buy", "Products", new { id = 17 })`. Objet de valeurs d’itinéraire :

* Par Convention, est généralement un objet de type anonyme.
* Il peut s’agir d’un `IDictionary<>` ou d’un [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Toutes les valeurs de route supplémentaires qui ne correspondent pas aux paramètres de route sont placées dans la chaîne de requête.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Le code précédent génère `/Products/Buy/17?color=red` .

Le code suivant génère une URL absolue :

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Pour créer une URL absolue, utilisez l’une des options suivantes :

* Surcharge qui accepte un `protocol` . Par exemple, le code précédent.
* [LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), qui génère des URI absolus par défaut.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Générer des URL par itinéraire

Le code précédent a démontré la génération d’une URL en passant le nom du contrôleur et de l’action. `IUrlHelper` fournit également la famille de méthodes [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) . Ces méthodes sont similaires à [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), mais elles ne copient pas les valeurs actuelles de `action` et `controller` vers les valeurs de route. L’utilisation la plus courante de `Url.RouteUrl` :

* Spécifie un nom d’itinéraire pour générer l’URL.
* Ne spécifie généralement pas un nom de contrôleur ou d’action.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Le Razor fichier suivant génère un lien HTML vers `Destination_Route` :

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a>Générer des URL en HTML et Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> fournit les <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> méthodes [html. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) et [html. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) pour générer `<form>` des `<a>` éléments et respectivement. Ces méthodes utilisent la méthode [URL. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) pour générer une URL et elles acceptent des arguments similaires. Les pendants de `Url.RouteUrl` pour `HtmlHelper` sont `Html.BeginRouteForm` et `Html.RouteLink`, qui ont des fonctionnalités similaires.

Les TagHelpers génèrent des URL via le TagHelper `form` et le TagHelper `<a>`. Ils utilisent tous les deux `IUrlHelper` pour leur implémentation. Pour plus d’informations, consultez [tag Helpers in Forms](xref:mvc/views/working-with-forms) .

Dans les vues, `IUrlHelper` est disponible via la propriété `Url` pour toute génération d’URL ad hoc non couverte par ce qui figure ci-dessus.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Génération d’URL dans les résultats d’action

Les exemples précédents ont montré l’utilisation `IUrlHelper` de dans un contrôleur. L’utilisation la plus courante dans un contrôleur consiste à générer une URL dans le cadre d’un résultat d’action.

Les classes de base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> et <xref:Microsoft.AspNetCore.Mvc.Controller> fournissent des méthodes pratiques pour les résultats d’action qui référencent une autre action. Une utilisation classique consiste à effectuer une redirection après avoir accepté l’entrée d’utilisateur :

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Les méthodes de fabrique des résultats d’action, telles que <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> et, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> suivent un modèle similaire aux méthodes sur `IUrlHelper` .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Cas spécial pour les routes conventionnelles dédiées

Le [routage conventionnel](#cr) peut utiliser un type spécial de définition d’itinéraire appelé [itinéraire conventionnel dédié](#dcr). Dans l’exemple suivant, l’itinéraire nommé `blog` est une route conventionnelle dédiée :

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

À l’aide des définitions d’itinéraire précédentes, `Url.Action("Index", "Home")` génère le chemin d’accès de l’URL `/` à l’aide de l' `default` itinéraire, mais pourquoi ? Vous pouvez deviner que les valeurs de route `{ controller = Home, action = Index }` seraient suffisantes pour générer une URL avec `blog`, et que le résultat serait `/blog?action=Index&controller=Home`.

Les [itinéraires conventionnels dédiés](#dcr) s’appuient sur un comportement spécial des valeurs par défaut qui n’ont pas de paramètre de routage correspondant qui empêche l’itinéraire d’être trop [gourmand](xref:fundamentals/routing#greedy) en génération d’URL. Dans ce cas, les valeurs par défaut sont `{ controller = Blog, action = Article }`, et ni `controller` ni `action` n’apparaissent comme paramètre de route. Quand le routage effectue une génération d’URL, les valeurs fournies doivent correspondre aux valeurs par défaut. La génération d’URL avec `blog` échoue car les valeurs `{ controller = Home, action = Index }` ne correspondent pas `{ controller = Blog, action = Article }` . Le routage essaye alors d’utiliser `default`, ce qui réussit.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Zones (Areas)

Les [zones](xref:mvc/controllers/areas) sont une fonctionnalité MVC utilisée pour organiser les fonctionnalités associées dans un groupe sous la forme d’un groupe distinct :

* Espace de noms de routage pour les actions du contrôleur.
* Structure de dossiers pour les vues.

L’utilisation de zones permet à une application d’avoir plusieurs contrôleurs portant le même nom, à condition qu’ils aient des zones différentes. L’utilisation de zones crée une hiérarchie qui permet le routage par ajout d’un autre paramètre de route, `area`, à `controller` et à `action`. Cette section explique comment le routage interagit avec les zones. Pour plus d’informations sur la façon dont les zones sont utilisées avec les vues, consultez [zones](xref:mvc/controllers/areas) .

L’exemple suivant configure MVC pour utiliser l’itinéraire conventionnel par défaut et un `area` itinéraire pour un `area` nommé `Blog` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Dans le code précédent, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> est appelé pour créer le `"blog_route"` . Le deuxième paramètre, `"Blog"` , est le nom de la zone.

En cas de correspondance avec un chemin d’URL comme `/Manage/Users/AddUser` , l' `"blog_route"` itinéraire génère les valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }` . La `area` valeur de route est produite par une valeur par défaut pour `area` . L’itinéraire créé par `MapAreaControllerRoute` est équivalent à ce qui suit :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` crée une route avec à la fois une valeur par défaut et une contrainte pour `area` en utilisant le nom de la zone fournie, dans ce cas `Blog`. La valeur par défaut garantit que la route produit toujours `{ area = Blog, ... }`, et la contrainte nécessite la valeur `{ area = Blog, ... }` pour la génération d’URL.

Le routage conventionnel est dépendant de l’ordre. En général, les itinéraires avec des zones doivent être placés plus tôt, car ils sont plus spécifiques que les itinéraires sans zone.

À l’aide de l’exemple précédent, les valeurs d’itinéraire `{ area = Blog, controller = Users, action = AddUser }` correspondent à l’action suivante :

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

L’attribut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) désigne un contrôleur dans le cadre d’une zone. Ce contrôleur est dans la `Blog` zone. Les contrôleurs sans `[Area]` attribut ne sont pas membres d’une zone et ne correspondent **pas** lorsque la valeur de routage `area` est fournie par le routage. Dans l’exemple suivant, seul le premier contrôleur répertorié peut correspondre aux valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

L’espace de noms de chaque contrôleur est indiqué ici par exhaustivité. Si les contrôleurs précédents utilisent le même espace de noms, une erreur de compilateur est générée. Les espaces de noms de classe n’ont pas d’effet sur le routage de MVC.

Les deux premiers contrôleurs sont membres de zones, et ils sont trouvés en correspondance seulement quand le nom de leur zone respective est fourni par la valeur de route `area`. Le troisième contrôleur n’est membre d’aucune zone et peut être trouvé en correspondance seulement quand aucune valeur pour `area` n’est fournie par le routage.

<a name="aa"></a>

En termes de mise en correspondance avec *aucune valeur* , l’absence de la valeur `area` est identique à une valeur null ou de chaîne vide pour `area`.

Lors de l’exécution d’une action à l’intérieur d’une zone, la valeur de route pour `area` est disponible en tant que [valeur ambiante](#ambient) pour le routage à utiliser pour la génération d’URL. Cela signifie que par défaut, les zones agissent *par attraction* pour la génération d’URL, comme le montre l’exemple suivant.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Le code suivant génère une URL vers `/Zebra/Users/AddUser` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Définition de l’action

Les méthodes publiques sur un contrôleur, à l’exception de celles avec l’attribut non- [action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , sont des actions.

## <a name="sample-code"></a>Exemple de code

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* [Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC utilise [l’intergiciel](xref:fundamentals/middleware/index) de routage pour mettre en correspondance les URL des requêtes entrantes et les mapper à des actions. Les routes sont définies dans le code de démarrage ou dans des attributs. Les routes décrivent comment les chemins des URL doivent être mis en correspondance avec les actions. Les routes sont également utilisées pour générer des URL (pour les liens) envoyés dans les réponses.

Les actions sont routées de façon conventionnelle ou routées par attribut. Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ». Pour plus d’informations, consultez [Routage mixte](#routing-mixed-ref-label).

Ce document explique les interactions entre MVC et le routage, et comment les applications MVC classiques utilisent les fonctionnalités du routage. Pour plus d’informations sur le routage avancé, consultez [Routage](xref:fundamentals/routing).

## <a name="setting-up-routing-middleware"></a>Configuration de l’intergiciel de routage

Dans votre méthode *Configure* , vous pouvez voir un code similaire à celui-ci :

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

À l’intérieur de l’appel à `UseMvc`, `MapRoute` est utilisé pour créer une route, nous allons appeler route `default`. La plupart des applications MVC utilisent une route avec un modèle similaire à la route `default`.

Le modèle de route `"{controller=Home}/{action=Index}/{id?}"` peut correspondre à un chemin d’URL comme `/Products/Details/5` et il extrait les valeurs `{ controller = Products, action = Details, id = 5 }` de la route en décomposant le chemin en jetons. MVC tente de trouver un contrôleur nommé `ProductsController` et d’exécuter l’action `Details` :

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Notez que dans cet exemple, la liaison de modèle utilise la valeur de `id = 5` pour définir le paramètre `id` sur `5` lors de l’appel de cette action. Pour plus d’informations, consultez [Liaison de modèle](../models/model-binding.md).

Utilisation de la route `default` :

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Le modèle de route :

* `{controller=Home}` définit `Home` comme `controller` par défaut.

* `{action=Index}` définit `Index` comme `action` par défaut.

* `{id?}` définit `id` comme étant facultatif.

Les paramètres de route par défaut et facultatifs n’ont pas besoin d’être présents dans le chemin d’URL pour qu’une correspondance soit établie. Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).

`"{controller=Home}/{action=Index}/{id?}"` peut correspondre au chemin d’URL `/` et produit les valeurs de route `{ controller = Home, action = Index }`. Les valeurs de `controller` et `action` utilisent les valeurs par défaut, et `id` ne produit pas de valeur, car il n’existe pas de segment correspondant dans le chemin d’URL. MVC utilisez ces valeurs de route pour sélectionner `HomeController` et l’action `Index` :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Avec cette définition de contrôleur et ce modèle de route, l’action `HomeController.Index` est exécutée pour tous les chemins d’URL suivants :

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

La méthode pratique `UseMvcWithDefaultRoute` :

```csharp
app.UseMvcWithDefaultRoute();
```

Peut être utilisée pour remplacer :

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` et `UseMvcWithDefaultRoute` ajoutent une instance de `RouterMiddleware` au pipeline de l’intergiciel. MVC n’interagit pas directement avec l’intergiciel et utilise le routage pour gérer les requêtes. MVC est connecté aux routes via une instance de `MvcRouteHandler`. Le code de `UseMvc` est similaire à ceci :

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` ne définit directement aucune route, il ajoute un espace réservé à la collection de routes pour la route `attribute`. La surcharge `UseMvc(Action<IRouteBuilder>)` vous permet d’ajouter vos propres routes et prend également en charge le routage par attribut.  `UseMvc` et toutes ses variantes ajoutent un espace réservé pour l’attribut route-attribute Routing est toujours disponible, quelle que soit la façon dont vous configurez `UseMvc` . `UseMvcWithDefaultRoute` définit une route par défaut et prend en charge le routage par attribut. La section [Routage par attribut](#attribute-routing-ref-label) comprend plus de détails sur le routage par attribut.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Routage conventionnel

La route `default` :

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Le code précédent est un exemple de routage conventionnel. Ce style est appelé routage conventionnel, car il établit une *Convention* pour les chemins d’URL :

* Le premier segment de chemin d’accès correspond au nom du contrôleur.
* Le deuxième correspond au nom de l’action.
* Le troisième segment est utilisé pour un facultatif `id` . `id` correspond à une entité de modèle.

En utilisant cette route `default`, le chemin d’URL `/Products/List` mappe à l’action `ProductsController.List`, et `/Blog/Article/17` mappe à `BlogController.Article`. Ce mappage est basé **uniquement** sur les noms de contrôleur et d’action ; il n’est pas basé sur les espaces de noms, les emplacements des fichiers sources ou les paramètres de méthode.

> [!TIP]
> L’utilisation du routage conventionnel avec la route par défaut vous permet de créer rapidement l’application sans avoir à élaborer un nouveau modèle d’URL pour chaque action que vous définissez. Pour une application avec des actions de style CRUD, le fait de disposer d’une cohérence pour les URL entre vos contrôleurs peut simplifier votre code et rendre votre interface utilisateur plus prévisible.

> [!WARNING]
> `id` est défini comme étant facultatif par le modèle de route, ce qui signifie que vos actions peuvent s’exécuter sans l’ID fourni dans le cadre de l’URL. En général, si `id` est omis de l’URL, il est défini sur `0` par la liaison de modèle : aucune entité correspondant à `id == 0` n’est donc trouvée dans la base de données. Le routage par attribut vous donne un contrôle précis pour rendre le code obligatoire pour certaines actions et pas pour d’autres. Par convention, la documentation inclut des paramètres facultatifs comme `id` quand ils sont susceptibles d’apparaître dans une utilisation correcte.

## <a name="multiple-routes"></a>Routes multiples

Vous pouvez ajouter plusieurs routes dans `UseMvc` en ajoutant plusieurs appels à `MapRoute`. Ceci vous permet de définir plusieurs conventions ou d’ajouter des routes conventionnelles qui sont dédiées à une action spécifique, comme :

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

La route `blog` est ici une *route conventionnelle dédiée* , ce qui signifie qu’elle utilise le système de routage conventionnel, mais qu’elle est dédiée à une action spécifique. Comme `controller` et `action` n’apparaissent pas dans le modèle de route en tant que paramètres, ils peuvent seulement avoir les valeurs par défaut et par conséquent, cette route sera toujours mappée à l’action `BlogController.Article`.

Les routes de la collection de routes sont ordonnées et elles sont traitées dans l’ordre où elles sont ajoutées. Ainsi, dans cet exemple, la route `blog` est essayée avant la route `default`.

> [!NOTE]
> Les *itinéraires conventionnels dédiés* utilisent souvent des paramètres de routage de type **« catch-all »** comme `{*article}` pour capturer la partie restante du chemin d’URL. Ceci peut rendre une route « trop globale », ce qui signifie qu’elle correspond à des URL qui devaient être mises en correspondance par d’autres routes. Pour résoudre ce problème, placez les routes « globales » plus loin dans la table de routage.

### <a name="fallback"></a>Secours

Dans le cadre du traitement des requêtes, MVC vérifie que les valeurs de route peuvent être utilisées pour rechercher un contrôleur et une action dans votre application. Si les valeurs de route ne correspondent pas à une action, la route n’est pas considérée comme une correspondance, et la route suivante est essayée. Ceci est appelé *processus de repli* et est conçu pour simplifier les cas où des routes conventionnelles se chevauchent.

### <a name="disambiguating-actions"></a>Résolution des ambiguïtés pour les actions

Quand deux actions correspondent via le routage, MVC doit résoudre l’ambiguïté pour choisir le « meilleur » candidat ou sinon lever une exception. Par exemple :

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Ce contrôleur définit deux actions qui correspondraient au chemin d’URL `/Products/Edit/17` et les données de routage `{ controller = Products, action = Edit, id = 17 }`. Il s’agit d’un modèle classique pour les contrôleurs MVC, où `Edit(int)` montre un formulaire pour modifier un produit, et où `Edit(int, Product)` traite le formulaire envoyé. Pour rendre cela possible, MVC doit choisir `Edit(int, Product)` quand la requête est `POST` HTTP, et `Edit(int)` quand le verbe HTTP est autre chose.

`HttpPostAttribute` (`[HttpPost]`) est une implémentation de `IActionConstraint` qui permet la sélection de l’action seulement quand le verbe HTTP est `POST`. La présence d’une `IActionConstraint` fait de `Edit(int, Product)` une correspondance « meilleure » que `Edit(int)`, de sorte que `Edit(int, Product)` est essayé en premier.

Vous devez écrire des implémentations personnalisées de `IActionConstraint` seulement dans des scénarios spécifiques, mais il est important de comprendre le rôle d’attributs comme `HttpPostAttribute` ; des attributs similaires sont définis pour les autres verbes HTTP. Dans le routage conventionnel, il est courant que des actions utilisent un même nom d’action quand elles font partie d’un flux de travail `show form -> submit form`. L’avantage de ce modèle devient plus évident après la lecture de la section [Présentation d’IActionConstraint](#understanding-iactionconstraint).

Si plusieurs routes correspondent et que MVC ne peut pas trouver une « meilleure » route, elle lève une `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Noms des routes

Les chaînes `"blog"` et `"default"` dans les exemples suivants sont des noms de routes :

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Les noms de routes donnent aux routes des noms logiques : le nom de route peut ainsi être utilisé pour la génération des URL. Ceci simplifie considérablement la création d’URL quand l’ordonnancement des routes peut rendre compliquée la génération des URL. Les noms de routes doivent être unique à l’échelle de l’application.

Les noms de routes n’ont pas d’impact sur la correspondance des URL ni sur le traitement des requêtes ; ils sont utilisés seulement pour la génération d’URL. La section [Routage](xref:fundamentals/routing) contient des informations plus détaillées sur la génération d’URL, notamment la génération d’URL dans les helpers spécifiques à MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Routage par attributs

Le routage par attributs utilise un ensemble d’attributs pour mapper les actions directement aux modèles de routes. Dans l’exemple suivant, `app.UseMvc();` est utilisé dans la méthode `Configure` et aucune route n’est passée. `HomeController` va trouver des correspondances avec un ensemble d’URL similaires aux correspondances qui seraient trouvées par la route par défaut `{controller=Home}/{action=Index}/{id?}`:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

L’action `HomeController.Index()` sera exécutée pour tous les chemins d’URL `/`, `/Home` ou `/Home/Index`.

> [!NOTE]
> Cet exemple met en évidence une différence importante en termes de programmation entre le routage par attributs et le routage conventionnel. Le routage par attributs nécessite des entrées en plus grand nombre pour spécifier une route ; la route conventionnelle par défaut gère les routes de façon plus succincte. Cependant, le routage par attributs permet (et nécessite) un contrôle précis des modèles de routes qui s’appliquent à chaque action.

Avec le routage par attributs, le nom du contrôleur et les noms des actions ne jouent **aucun** rôle dans la sélection de l’action. Cet exemple trouve une correspondance avec les mêmes URL que l’exemple précédent.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Les modèles de routes ci-dessus ne définissent pas de paramètres de route pour `action`, `area` et `controller`. En fait, ces paramètres de route ne sont pas autorisés dans les routes par attributs. Comme le modèle de route est déjà associé à une action, cela n’aurait pas de sens d’analyser le nom de l’action à partir de l’URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Routage par attributs avec des attributs Http[Verbe]

Le routage par attributs peut aussi utiliser les attributs `Http[Verb]`, comme `HttpPostAttribute`. Tous ces attributs peuvent accepter un modèle de route. Cet exemple montre deux actions qui correspondent au même modèle de route :

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Pour un chemin d’URL comme `/products`, l’action `ProductsApi.ListProducts` est exécutée quand le verbe HTTP est `GET`, et `ProductsApi.CreateProduct` est exécutée quand le verbe HTTP est `POST`. Le routage par attributs recherche d’abord une correspondance de l’URL par rapport à l’ensemble des modèles de routes défini par les attributs de la route. Une fois qu’un modèle de route correspond, les contraintes de `IActionConstraint` sont appliquées pour déterminer quelles actions peuvent être exécutées.

> [!TIP]
> Lors de la génération d’une API REST, il est rare que vous souhaitiez utiliser `[Route(...)]` sur une méthode d’action, car l’action acceptera toutes les méthodes http. Il est préférable d’utiliser les `Http*Verb*Attributes` plus spécifiques pour plus de précision quant à ce qui est pris en charge par votre API. Les clients des API REST doivent normalement connaître les chemins et les verbes HTTP qui correspondent à des opérations logiques spécifiques.

Dans la mesure où une route d’attribut s’applique à une action spécifique, il est facile de placer les paramètres nécessaires dans la définition du modèle de route. Dans cet exemple, `id` est obligatoire dans le chemin d’URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

L’action `ProductsApi.GetProduct(int)` est exécutée pour un chemin d’URL comme `/products/3`, mais pas pour un chemin d’URL comme `/products`. Consultez [Routage](xref:fundamentals/routing) pour obtenir une description complète des modèles de routes et des options associées.

## <a name="route-name"></a>Nom des routes

Le code suivant définit un *nom de route*`Products_List` :

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Les noms de routes peuvent être utilisés pour générer une URL basée sur une route spécifique. Les noms de routes n’ont pas d’impact sur le comportement de mise en correspondance des URL du routage et ils sont utilisés seulement pour la génération d’URL. Les noms de routes doivent être unique à l’échelle de l’application.

> [!NOTE]
> Comparez ceci avec la *route par défaut* conventionnelle, qui définit le paramètre `id` comme étant facultatif (`{id?}`). Cette possibilité de spécifier les API avec précision présente des avantages, par exemple de permettre de diriger `/products` et `/products/5` vers des actions différentes.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Combinaison de routes

Pour rendre le routage par attributs moins répétitif, les attributs de route sont combinés avec des attributs de route sur les actions individuelles. Les modèles de routes définis sur le contrôleur sont ajoutés à des modèles de routes sur les actions. Placer un attribut de route sur le contrôleur a pour effet que **toutes** les actions du contrôleur utilisent le routage par attributs.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

Dans cet exemple, le chemin d’URL `/products` peut être mis en correspondance avec `ProductsApi.ListProducts` et le chemin d’URL `/products/5` peut être mis en correspondance avec `ProductsApi.GetProduct(int)`. Ces deux actions correspondent uniquement à HTTP `GET` , car elles sont marquées avec le `HttpGetAttribute` .

Les modèles de routes appliqués à une action qui commencent par `/` ou `~/` ne sont pas combinés avec les modèles de routes appliqués au contrôleur. Cet exemple met en correspondance avec un ensemble de chemins d’URL similaires à la *route par défaut*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Ordonnancement des routes d’attributs

Contrairement aux itinéraires conventionnels, qui s’exécutent dans un ordre défini, le routage d’attributs génère une arborescence et met en correspondance tous les itinéraires simultanément. Il se comporte comme-si les entrées des routes étaient placées dans un ordre idéal ; les routes les plus spécifiques ont ainsi plus de probabilités de s’exécuter avant les routes plus générales.

Par exemple, une route comme `blog/search/{topic}` est plus spécifique qu’une route comme `blog/{*article}`. D’un point de vue logique, la route `blog/search/{topic}` « s’exécute » par défaut en premier, car il s’agit du seul ordre sensé. Avec le routage conventionnel, le développeur est responsable du placement des routes dans l’ordre souhaité.

Les routes d’attribut peuvent configurer un ordre en utilisant la propriété `Order` de tous les attributs de route fournis par le framework. Les routes sont traitées selon un ordre croissant de la propriété `Order`. L’ordre par défaut est `0`. La définition d’une route avec `Order = -1` fait que cette route « s’exécute » avant les routes qui ne définissent pas d’ordre. La définition d’une route avec `Order = 1` fait que cette route « s’exécute » après l’ordre des routes par défaut.

> [!TIP]
> Évitez de dépendre de `Order`. Si votre espace d’URL nécessite des valeurs d’ordre explicites pour router correctement, il est probable qu’il prête également à confusion pour les clients. D’une façon générale, le routage par attributs sélectionne la route correcte avec la mise en correspondance d’URL. Si l’ordre par défaut utilisé pour la génération d’URL ne fonctionne pas, l’utilisation à titre de remplacement d’un nom de route est généralement plus simple que d’appliquer la propriété `Order`.

Razor Le routage des pages et le routage du contrôleur MVC partagent une implémentation. Des informations sur l’ordre des itinéraires dans les Razor rubriques pages sont disponibles à la [ Razor page conventions de routage et d’application : ordre de routage](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Remplacement de jetons dans les modèles de routes ([contrôleur], [action], [zone])

Pour plus de commodité, les itinéraires d’attributs prennent en charge le *remplacement de jetons* en plaçant un jeton entre crochets ( `[` , `]` ). Les jetons `[action]`, `[area]` et `[controller]` sont remplacés par les valeurs du nom d’action, du nom de la zone et du nom du contrôleur de l’action où la route est définie. Dans l’exemple suivant, les actions correspondent aux chemins d’URL comme décrit dans les commentaires :

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Le remplacement des jetons se produit à la dernière étape de la création des routes d’attribut. L’exemple ci-dessus se comporte comme le code suivant :

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Les routes d’attribut peuvent aussi être combinées avec l’héritage. Combiné avec le remplacement de jetons, c’est particulièrement puissant.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Le remplacement des jetons s’applique aussi aux noms de routes définis par des routes d’attribut. `[Route("[controller]/[action]", Name="[controller]_[action]")]` génère un nom de route unique pour chaque action.

Pour faire correspondre le délimiteur littéral de remplacement de jetons `[` ou `]`, placez-le en échappement en répétant le caractère (`[[` ou `]]`).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons

Le remplacement des jetons peut être personnalisé à l’aide d’un transformateur de paramètre. Un transformateur de paramètre implémente `IOutboundParameterTransformer` et transforme la valeur des paramètres. Par exemple, un transformateur de paramètre `SlugifyParameterTransformer` personnalisé transforme la valeur de la route `SubscriptionManagement` en `subscription-management`.

`RouteTokenTransformerConvention` est une convention de modèle d’application qui :

* Applique un transformateur de paramètre à toutes les routes d’attribut dans une application.
* Personnalise les valeurs de jeton de route d’attribut quand elles sont remplacées.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` est inscrit en tant qu’option dans `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Routes multiples

Le routage par attributs prend en charge la définition de plusieurs routes pour atteindre la même action. L’utilisation la plus courante de ceci est d’imiter le comportement de la *route conventionnelle par défaut* , comme le montre l’exemple suivant :

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Le fait de placer plusieurs attributs de route sur le contrôleur signifie que chacun d’eux se combine avec chacun des attributs de route sur les méthodes d’action.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Quand plusieurs attributs de route (qui implémentent `IActionConstraint`) sont placés sur une action, chaque contrainte d’action se combine avec le modèle de route de l’attribut qui l’a défini.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Si utiliser plusieurs routes sur des actions peut paître puissant, il est préférable de conserver l’espace d’URL de votre application simple et bien définie. Utilisez plusieurs routes sur les actions seulement là où c’est nécessaire, par exemple pour prendre en charge des clients existants.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Spécification facultative de paramètres, de valeurs par défaut et de contraintes pour les routes d’attribut

Les routes d’attribut prennent en charge la même syntaxe inline que les routes conventionnelles pour spécifier des paramètres, des valeurs par défaut et des contraintes facultatifs.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Pour une description détaillée de la syntaxe du modèle de route, consultez [Informations de référence sur le modèle de route](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Attributs de route personnalisés avec `IRouteTemplateProvider`

Tous les attributs de route fournis dans le framework (`[Route(...)]`, `[HttpGet(...)]`, etc.) implémentent l’interface `IRouteTemplateProvider`. MVC recherche les attributs sur les classes de contrôleur et les méthodes d’action quand l’application démarre et utilise ceux qui implémentent `IRouteTemplateProvider` pour créer l’ensemble initial des routes.

Vous pouvez implémenter `IRouteTemplateProvider` pour définir vos propres attributs de route. Chaque `IRouteTemplateProvider` vous permet de définir une route avec un modèle, un nom et un ordre de route personnalisés :

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

L’attribut de l’exemple ci-dessus définit automatiquement `Template` sur `"api/[controller]"` quand `[MyApiController]` est appliqué.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Utilisation d’un modèle d’application pour personnaliser les routes d’attribut

Le *modèle d’application* est un modèle d’objet créé au démarrage avec toutes les métadonnées utilisée par MVC pour router et exécuter vos actions. Le *modèle d’application* inclut toutes les données collectées à partir des attributs de route (via `IRouteTemplateProvider`). Vous pouvez écrire des *conventions* pour modifier le modèle d’application au moment du démarrage pour personnaliser le comporte du routage. Cette section montre un exemple simple de personnalisation du routage avec le modèle d’application.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Routage mixte : routage conventionnel et routage par attributs

Les applications MVC peuvent combiner l’utilisation du routage conventionnel et du routage par attributs. Il est courant d’utiliser des routes conventionnelles pour les contrôleurs délivrant des pages HTML pour les navigateurs, et le routage par attributs pour les contrôleurs délivrant des API REST.

Les actions sont routées de façon conventionnelle ou routées par attribut. Le fait de placer une route sur le contrôleur ou sur l’action les rend « routés par attribut ». Les actions qui définissent des routes d’attribut ne sont pas accessibles via les routes conventionnelles et vice versa. **Tout** attribut de route sur le contrôleur a pour effet que toutes les actions du contrôleur sont routées par attributs.

> [!NOTE]
> Ce qui distingue les deux types de systèmes de routage est le processus appliqué une fois qu’une URL correspond à un modèle de route. Dans le routage conventionnel, les valeurs de route de la correspondance sont utilisées pour choisir l’action et le contrôleur dans une table de recherche comprenant toutes les actions routées de façon conventionnelle. Dans le routage par attributs, chaque modèle est déjà associé à une action, et aucune recherche supplémentaire n’est nécessaire.

## <a name="complex-segments"></a>Segments complexes

Les segments complexes (par exemple, `[Route("/dog{token}cat")]`), sont traités par la mise en correspondance des littéraux de droite à gauche de manière non gourmande. Consultez [le code source](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) pour obtenir une description. Pour plus d’informations, consultez [ce problème](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Génération des URL

Les applications MVC peuvent utiliser les fonctionnalités de génération d’URL de routage pour générer des liens URL vers des actions. La génération d’URL élimine le codage en dur des URL, ce qui rend votre code plus robuste et plus facile à maintenir. Cette section se concentre sur les fonctionnalités de génération d’URL fournies par MVC et couvre seulement les principes de base du fonctionnement de la génération d’URL. Pour une description détaillée de la génération d’URL, consultez [Routage](xref:fundamentals/routing).

L’interface `IUrlHelper` est l’élément d’infrastructure sous-jacent entre MVC et le routage pour la génération d’URL. Vous pouvez trouver une instance de `IUrlHelper` disponible via la propriété `Url` dans les composants controllers, views et view.

Dans cet exemple, l’interface `IUrlHelper` est utilisée via la propriété `Controller.Url` pour générer une URL vers une autre action.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Si l’application utilise la route conventionnelle par défaut, la valeur de la variable `url` est la chaîne de chemin d’URL `/UrlGeneration/Destination`. Ce chemin d’URL est créé par le routage en combinant les valeurs de route de la requête en cours (valeurs ambiantes) avec les valeurs passées à `Url.Action`, et en remplaçant les valeurs dans le modèle de route :

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

La valeur de chaque paramètre de route du modèle de route est remplacée en établissant une correspondance avec les valeurs et les valeurs ambiantes. Un paramètre de route qui n’a pas de valeur peut utiliser une valeur par défaut s’il en a une, ou il peut être ignoré s’il est facultatif (comme dans le cas de `id` dans cet exemple). La génération d’URL échoue si un paramètre de route obligatoire n’a pas de valeur correspondante. Si la génération d’URL échoue pour une route, la route suivante est essayée, ceci jusqu’à ce que toutes les routes aient été essayées ou qu’une correspondance soit trouvée.

L’exemple de `Url.Action` ci-dessus suppose un routage conventionnel, mais la génération d’URL fonctionne de façon similaire avec le routage par attributs, même si les concepts sont différents. Avec le routage conventionnel, les valeurs de route sont utilisées pour développer un modèle, et les valeurs de route pour `controller` et `action` apparaissent généralement dans ce modèle : ceci fonctionne car les URL mises en correspondance par le routage adhèrent à une *convention*. Dans le routage par attributs, les valeurs de route pour `controller` et `action` ne sont pas autorisées à apparaître dans le modèle : au lieu de cela, elles sont utilisées pour rechercher le modèle à utiliser.

Cet exemple utilise le routage par attributs :

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC génère une table de recherche de toutes les actions routées par attributs et recherche une correspondance avec les valeurs de `controller` et de `action` pour sélectionner le modèle de route à utiliser pour la génération d’URL. Dans l’exemple ci-dessus, `custom/url/to/destination` est généré.

### <a name="generating-urls-by-action-name"></a>Génération des URL par nom d’action

`Url.Action` (`IUrlHelper` . `Action`) et toutes les surcharges associées sont tous basés sur cette idée que vous voulez spécifier ce à quoi vous liez en spécifiant un nom de contrôleur et un nom d’action.

> [!NOTE]
> Quand vous utilisez `Url.Action`, les valeurs de route actuelles pour `controller` et `action` sont spécifiées pour vous : la valeur de `controller` et de `action` font partie des *valeurs ambiantes* **et des** *valeurs*. La méthode `Url.Action` utilise toujours les valeurs actuelles de `action` et de `controller`, et génère un chemin d’URL qui route vers l’action actuelle.

Le routage essaye d’utiliser les valeurs dans les valeurs ambiantes pour renseigner les informations que vous n’avez pas fournies lors de la génération d’une URL. En utilisant une route comme `{a}/{b}/{c}/{d}` et les valeurs ambiantes `{ a = Alice, b = Bob, c = Carol, d = David }`, le routage a suffisamment d’informations pour générer une URL sans aucune autre valeur supplémentaire, car tous les paramètres de route ont une valeur. Si vous avez ajouté la valeur `{ d = Donovan }`, la valeur `{ d = David }` est ignorée, et le chemin d’URL généré est `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Les chemins d’URL sont hiérarchiques. Dans l’exemple ci-dessus, si vous avez ajouté la valeur `{ c = Cheryl }`, les deux valeurs `{ c = Carol, d = David }` sont ignorées. Dans ce cas nous n’avons plus de valeur pour `d` et la génération d’URL échoue. Vous devez spécifier la valeur souhaitée de `c` et de `d`.  Vous vous attendez peut-être à rencontrer ce problème avec la route par défaut (`{controller}/{action}/{id?}`), mais vous rencontrerez rarement ce comportement en pratique, car `Url.Action` spécifie toujours explicitement une valeur pour `controller` et pour `action`.

Les surcharges plus longues de `Url.Action` prennent également un objet supplémentaire de *valeurs de route* pour fournir des valeurs pour les paramètres de route autres que `controller` et `action`. Vous verrez ceci plus couramment utilisé avec un `id` comme `Url.Action("Buy", "Products", new { id = 17 })`. Par convention, l’objet de *valeurs de route* objet est généralement un objet de type anonyme, mais il peut également être un `IDictionary<>` ou un *objet .NET traditionnel*. Toutes les valeurs de route supplémentaires qui ne correspondent pas aux paramètres de route sont placées dans la chaîne de requête.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Pour créer une URL absolue, utilisez une surcharge qui accepte un `protocol` :`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Génération des URL par route

Le code ci-dessus a montré la génération d’une URL en passant le nom du contrôleur et le nom de l’action. `IUrlHelper` fournit également la famille de méthodes `Url.RouteUrl`. Ces méthodes sont similaires à `Url.Action`, mais elle ne copient pas les valeurs actuelles de `action` et de `controller` vers les valeurs de route. L’utilisation la plus courante est de spécifier un nom de route pour utiliser une route spécifique pour générer l’URL, généralement *sans* spécifier un nom de contrôleur ou d’action.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Génération des URL en HTML

`IHtmlHelper` fournit les méthodes `HtmlHelper``Html.BeginForm` et `Html.ActionLink` pour générer respectivement les éléments `<form>` et `<a>`. Ces méthodes utilisent la méthode `Url.Action` pour générer une URL et ils acceptent les arguments similaires. Les pendants de `Url.RouteUrl` pour `HtmlHelper` sont `Html.BeginRouteForm` et `Html.RouteLink`, qui ont des fonctionnalités similaires.

Les TagHelpers génèrent des URL via le TagHelper `form` et le TagHelper `<a>`. Ils utilisent tous les deux `IUrlHelper` pour leur implémentation. Pour plus d’informations, consultez [Utilisation des formulaires](../views/working-with-forms.md).

Dans les vues, `IUrlHelper` est disponible via la propriété `Url` pour toute génération d’URL ad hoc non couverte par ce qui figure ci-dessus.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Génération des URL dans les résultats d’action

Les exemples précédents ont montré l’utilisation de `IUrlHelper` dans un contrôleur, alors que l’utilisation la plus courante dans un contrôleur est de générer une URL dans le cadre d’un résultat d’action.

Les classes de base `ControllerBase` et `Controller` fournissent des méthodes pratiques pour les résultats d’action qui référencent une autre action. Une utilisation typique est de rediriger après acceptation de l’entrée utilisateur.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Les méthodes de fabrique de résultats d’action suivent un modèle similaire aux méthodes sur `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Cas spécial pour les routes conventionnelles dédiées

Le routage conventionnel peut utiliser un type spécial de définition de route appelé *route conventionnelle dédiée*. Dans l’exemple ci-dessous, la route nommée `blog` est une route conventionnelle dédiée.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

En utilisant ces définitions de route, `Url.Action("Index", "Home")` génère le chemin d’URL `/` avec la route `default`, mais pourquoi ? Vous pouvez deviner que les valeurs de route `{ controller = Home, action = Index }` seraient suffisantes pour générer une URL avec `blog`, et que le résultat serait `/blog?action=Index&controller=Home`.

Les routes conventionnelles dédiées s’appuient sur un comportement spécial des valeurs par défaut qui n’ont pas de paramètre de route correspondant qui empêche la route d’être « trop globale » avec la génération d’URL. Dans ce cas, les valeurs par défaut sont `{ controller = Blog, action = Article }`, et ni `controller` ni `action` n’apparaissent comme paramètre de route. Quand le routage effectue une génération d’URL, les valeurs fournies doivent correspondre aux valeurs par défaut. La génération d’URL avec `blog` échoue, car les valeurs `{ controller = Home, action = Index }` ne correspondent pas à `{ controller = Blog, action = Article }`. Le routage essaye alors d’utiliser `default`, ce qui réussit.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Zones (Areas)

Les [zones](areas.md) sont une fonctionnalité de MVC utilisée pour organiser des fonctionnalités connexes dans un groupe sous la forme d’un espace de noms de routage distinct (pour les actions de contrôleur) et d’une structure de dossiers (pour les vues). L’utilisation de zones permet à une application d’avoir plusieurs contrôleurs portant le même nom, pour autant qu’ils soient dans des *zones* différentes. L’utilisation de zones crée une hiérarchie qui permet le routage par ajout d’un autre paramètre de route, `area`, à `controller` et à `action`. Cette section explique comment le routage interagit avec les zones. Pour plus d’informations sur l’utilisation des zones avec des vues, consultez [Zones](areas.md).

L’exemple suivant configure MVC pour utiliser la route conventionnelle par défaut et une *route de zone* pour une zone nommée `Blog` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Lors de la mise en correspondance d’un chemin d’URL comme `/Manage/Users/AddUser`, la première route produit les valeurs de route `{ area = Blog, controller = Users, action = AddUser }`. La valeur de route `area` est produite par une valeur par défaut pour `area` ; en fait, la route créée par `MapAreaRoute` est équivalente à la suivante :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` crée une route avec à la fois une valeur par défaut et une contrainte pour `area` en utilisant le nom de la zone fournie, dans ce cas `Blog`. La valeur par défaut garantit que la route produit toujours `{ area = Blog, ... }`, et la contrainte nécessite la valeur `{ area = Blog, ... }` pour la génération d’URL.

> [!TIP]
> Le routage conventionnel est dépendant de l’ordre. En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.

Avec l’exemple ci-dessus, les valeurs de route seraient mises en correspondance avec l’action suivante :

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` est ce qui indique qu’un contrôleur fait partie d’une zone : nous disons que ce contrôleur est dans la zone `Blog`. Les contrôleurs sans attribut `[Area]` ne sont membres d’aucune zone et ne sont **pas** trouvés en correspondance quand la valeur de route `area` est fournie par le routage. Dans l’exemple suivant, seul le premier contrôleur répertorié peut correspondre aux valeurs de route `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> L’espace de noms de chaque contrôleur est montré ici par souci d’exhaustivité, sans quoi les contrôleurs auraient un conflit de noms et généreraient une erreur de compilateur. Les espaces de noms de classe n’ont pas d’effet sur le routage de MVC.

Les deux premiers contrôleurs sont membres de zones, et ils sont trouvés en correspondance seulement quand le nom de leur zone respective est fourni par la valeur de route `area`. Le troisième contrôleur n’est membre d’aucune zone et peut être trouvé en correspondance seulement quand aucune valeur pour `area` n’est fournie par le routage.

> [!NOTE]
> En termes de mise en correspondance avec *aucune valeur* , l’absence de la valeur `area` est identique à une valeur null ou de chaîne vide pour `area`.

Lors de l’exécution d’une action à l’intérieur d’une zone, la valeur de route pour `area` est disponible en tant que *valeur ambiante* , que le routage peut utiliser pour la génération d’URL. Cela signifie que par défaut, les zones agissent *par attraction* pour la génération d’URL, comme le montre l’exemple suivant.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Présentation d’IActionConstraint

> [!NOTE]
> Cette section est une présentation détaillée des mécanismes internes du framework et de la façon dont MVC choisit une action à exécuter. Une application classique n’a pas besoin d’une `IActionConstraint` personnalisée.

Vous avez probablement déjà utilisé `IActionConstraint` même si vous n’êtes pas familiarisé avec l’interface. L’attribut `[HttpGet]` et les attributs `[Http-VERB]` similaires implémentent `IActionConstraint` de façon à limiter l’exécution d’une méthode d’action.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Dans l’hypothèse d’une route conventionnelle par défaut, le chemin d’URL `/Products/Edit` produirait les valeurs `{ controller = Products, action = Edit }`, qui seraient en correspondance avec **les deux** actions montrées ici. Dans la terminologie `IActionConstraint`, nous pouvons dire que ces deux actions sont considérées comme candidates, car elles correspondent toutes deux aux données de la route.

Quand `HttpGetAttribute` s’exécute, il indique que *Edit()* est une correspondance pour *GET* et qu’il n’est une correspondance pour aucun des autres verbes HTTP. L’action `Edit(...)` n’a aucune contrainte définie et elle ne correspond donc à aucun verbe HTTP. Par conséquent, dans l’hypothèse d’un `POST`, seul `Edit(...)` est en correspondance. Cependant, pour un `GET`, les deux actions peuvent néanmoins être en correspondance, mais une action avec `IActionConstraint` est toujours considérée comme étant *meilleure* qu’une action sans. Par conséquent, comme `Edit()` a `[HttpGet]`, elle est considérée comme étant plus spécifique et est sélectionnée si les deux actions peuvent correspondre.

D’un point de vue conceptuel, `IActionConstraint` est une forme de *surcharge* , mais au lieu de surcharger des méthodes portant le même nom, elle surcharge entre des actions qui correspondent à la même URL. Le routage par attributs utilise également `IActionConstraint` et peut aboutir à des actions de différents contrôleurs, toutes considérées comme candidates.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implémentation d’IActionConstraint

La façon la plus simple d’implémenter une `IActionConstraint` est de créer une classe dérivée de `System.Attribute` et de la placer sur vos actions et vos contrôleurs. MVC découvre automatiquement les `IActionConstraint` qui sont appliquées en tant qu’attributs. Vous pouvez utiliser le modèle d’application pour appliquer des contraintes, et il s’agit probablement de l’approche la plus souple, car elle vous permet de programmer des méta-informations indiquant comment elles sont appliquées.

Dans l’exemple suivant, une contrainte choisit une action en fonction de l' *indicatif du pays* à partir des données de l’itinéraire. [Exemple complet sur GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Vous êtes responsable de l’implémentation de la méthode `Accept` et du choix d’un « ordre » pour l’exécution de la contrainte. Dans ce cas, la méthode `Accept` retourne `true` pour indiquer que l’action est une correspondance quand la valeur de la route `country` correspond à la valeur. Ceci diffère d’un `RouteValueAttribute`, car elle permet à défaut d’appliquer une action sans attributs. L’exemple montre que si vous définissez une action `en-US`, un code pays comme `fr-FR` passe à défaut à un contrôleur plus générique auquel `[CountrySpecific(...)]` n’est pas appliqué.

La propriété `Order` décide de *l’étape* dont la contrainte fait partie. Les contraintes d’action s’exécutent dans des groupes en fonction de `Order`. Par exemple, tous les attributs de méthode HTTP fournis par le framework utilisent la même valeur pour `Order`, de façon à ce qu’ils s’exécutent dans la même étape. Vous pouvez avoir autant d’étapes que nécessaire pour implémenter les stratégies souhaitées.

> [!TIP]
> Pour décider d’une valeur pour `Order`, déterminez si votre contrainte doit ou non être appliquée avant les méthodes HTTP. Les nombres les plus petits s’exécutent en premier.

::: moniker-end
