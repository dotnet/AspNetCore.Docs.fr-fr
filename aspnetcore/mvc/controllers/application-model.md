---
title: Utiliser le modèle d’application dans ASP.NET Core
author: ardalis
description: Découvrez comment lire et manipuler le modèle d’application pour modifier le comportement des éléments MVC dans ASP.NET Core.
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
uid: mvc/controllers/application-model
ms.openlocfilehash: a7a654eb43c0dbf375af911d8d5353ac4c04a825
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060935"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>Utiliser le modèle d’application dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC définit un *modèle d’application* représentant les composants d’une application MVC. Vous pouvez lire et manipuler ce modèle pour modifier le comportement des éléments MVC. Par défaut, MVC suit certaines conventions pour déterminer quelles classes sont considérées comme des contrôleurs, quelles méthodes sur ces classes sont des actions, et comment les paramètres et le routage se comportent. Vous pouvez personnaliser ce comportement en fonction des besoins de votre application en créant vos propres conventions, et en les appliquant globalement ou en tant qu’attributs.

## <a name="models-and-providers"></a>Modèles et fournisseurs

Le modèle d’application ASP.NET Core MVC inclut des les interfaces abstraites et des classes d’implémentation concrètes qui décrivent une application MVC. Ce modèle est le résultat de la découverte par MVC des contrôleurs, des actions, des paramètres d’action, des routes et des filtres de l’application en fonction de conventions par défaut. Grâce au modèle d’application, vous pouvez modifier votre application pour suivre des conventions différentes du comportement de MVC par défaut. Les paramètres, les noms, les routes et les filtres sont utilisés comme données de configuration pour les actions et les contrôleurs.

Le modèle d’application ASP.NET Core MVC a la structure suivante :

* ApplicationModel
  * Contrôleurs (ControllerModel)
    * Actions (ActionModel)
      * Paramètres (ParameterModel)

Chaque niveau du modèle a accès à une collection `Properties` commune, et les niveaux inférieurs peuvent accéder à et remplacer les valeurs de propriétés définies par les niveaux supérieurs de la hiérarchie. Les propriétés sont rendues persistantes dans `ActionDescriptor.Properties` quand les actions sont créées. Ensuite, quand une requête est traitée, toutes les propriétés ajoutées ou modifiées par une convention sont accessibles via `ActionContext.ActionDescriptor.Properties`. L’utilisation de propriétés est un bon moyen de configurer vos filtres, vos classeurs de modèles, etc. à l’échelle d’une action.

> [!NOTE]
> La collection `ActionDescriptor.Properties` n’est pas thread-safe (pour les écritures) une fois que le démarrage de l’application est terminé. Les conventions sont la meilleure façon d’ajouter des données de façon sécurisée à cette collection.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET Core MVC charge le modèle d’application en utilisant un modèle de fournisseur, défini par l’interface [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider). Cette section aborde certains aspects de l’implémentation interne concernant la façon dont fonctionne de ce fournisseur. Il s’agit d’une rubrique avancée : la plupart des applications qui tirent parti du modèle d’application doivent le faire en utilisant les conventions.

Les implémentations de l’interface `IApplicationModelProvider` sont « encapsulées » les unes dans les autres, avec chaque implémentation appelant `OnProvidersExecuting` dans un ordre croissant basé sur sa propriété `Order`. La méthode `OnProvidersExecuted` est ensuite appelée dans l’ordre inverse. Le framework définit plusieurs fournisseurs :

D’abord (`Order=-1000`) :

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Ensuite (`Order=-990`) :

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> L’ordre dans deux fournisseurs avec la même valeur pour `Order` sont appelés n’est pas défini et vous ne pouvez donc pas vous baser sur celui-ci.

> [!NOTE]
> `IApplicationModelProvider` est un concept avancé que les auteurs de framework peuvent étendre. D’une façon générale, les applications doivent utiliser des conventions et les frameworks doivent utiliser des fournisseurs. La principale différence est que les fournisseurs s’exécutent toujours avant les conventions.

`DefaultApplicationModelProvider` établit la plupart des comportements par défaut utilisés par ASP.NET Core MVC. Ses responsabilités incluent :

* L’ajout de filtres globaux au contexte
* L’ajout de contrôleurs au contexte
* L’ajout de méthodes publiques de contrôleur en tant qu’actions
* L’ajout de paramètres de méthode d’action au contexte
* L’application de routes et d’autres attributs

Certains comportements intégrés sont implémentées par le `DefaultApplicationModelProvider`. Ce fournisseur est chargé de construire le [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , qui à son tour référence [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) les [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) instances, et [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) . La classe `DefaultApplicationModelProvider` est un détail d’implémentation interne du framework qui changera à coup sûr dans le futur. 

Le `AuthorizationApplicationModelProvider` est responsable de l’application du comportement associé aux attributs `AuthorizeFilter` et `AllowAnonymousFilter`. [Découvrez plus d’informations sur ces fonctionnalités](xref:security/authorization/simple).

Le `CorsApplicationModelProvider` implémente le comportement associé au `IEnableCorsAttribute` et au `IDisableCorsAttribute`, et le `DisableCorsAuthorizationFilter`. [Découvrez plus d’informations sur CORS](xref:security/cors).

## <a name="conventions"></a>Conventions

Le modèle d’application définit des abstractions de convention qui fournissent un moyen de personnaliser le comportement des modèles plus simple que de remplacer tout le modèle ou tout le fournisseur. Ces abstractions constituent le moyen recommandé pour modifier le comportement de votre application. Les conventions vous permettent d’écrire du code qui applique dynamiquement des personnalisations. Bien que les [filtres](xref:mvc/controllers/filters) offrent un moyen de modifier le comportement de l’infrastructure, les personnalisations vous permettent de contrôler le fonctionnement de l’ensemble de l’application.

Les conventions suivantes sont disponibles :

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

Les conventions sont appliquées en les ajoutant aux options MVC ou en implémentant `Attribute` des et en les appliquant à des contrôleurs, des actions ou des paramètres d’action (similaires à [`Filters`](xref:mvc/controllers/filters) ). Contrairement aux filtres, les conventions sont exécutées seulement lors du démarrage de l’application, et non pas dans le cadre de chaque requête.

### <a name="sample-modifying-the-applicationmodel"></a>Exemple : Modification du ApplicationModel

La convention suivante est utilisée pour ajouter une propriété au modèle d’application. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

Les conventions de modèle d’application sont appliquées en tant qu’options quand MVC est ajouté à `ConfigureServices` dans `Startup`.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

Les propriétés sont accessibles à partir de la collection de propriétés `ActionDescriptor` dans les actions de contrôleur :

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Exemple : Modification de la description de ControllerModel

Comme dans l’exemple précédent, le modèle de contrôleur peut également être modifié pour y inclure des propriétés personnalisées. Celles-ci remplacent les propriétés existantes portant le même nom que celui spécifié dans le modèle d’application. L’attribut de convention suivant ajoute une description au niveau du contrôleur :

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Cette convention est appliquée en tant qu’attribut sur un contrôleur.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

Vous accédez à la propriété « description » de la même façon que dans les exemples précédents.

### <a name="sample-modifying-the-actionmodel-description"></a>Exemple : Modification de la description de ActionModel

Une convention d’attribut distincte peut être appliquée à des actions individuelles, en remplaçant le comportement déjà appliqué au niveau de l’application ou du contrôleur.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

L’application de ceci à une action dans l’exemple de contrôleur précédent montre comment elle remplace la convention au niveau du contrôleur :

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Exemple : Modification du ParameterModel

La convention suivante peut être appliquée à des paramètres d’action pour modifier leur `BindingInfo`. La convention suivante nécessite que le paramètre soit un paramètre de route ; les autres sources potentielles de liaison (comme les valeurs de la chaîne de requête) sont ignorées.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

L’attribut peut être appliqué à n’importe quel paramètre d’action :

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Exemple : Modification du nom de ActionModel

La convention suivante modifie le `ActionModel` pour mettre à jour le *nom* de l’action à laquelle il est appliqué. Le nouveau nom est fourni à l’attribut en tant que paramètre. Ce nouveau nom est utilisé par le routage : il affecte donc la route utilisée pour atteindre cette méthode d’action.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Cet attribut est appliqué à une méthode d’action dans le `HomeController` :

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

Même si le nom de la méthode est `SomeName`, l’attribut remplace la convention MVC d’utiliser le nom de méthode et remplace le nom de l’action par `MyCoolAction`. Ainsi, la route utilisée pour atteindre cette action est `/Home/MyCoolAction`.

> [!NOTE]
> Cet exemple est essentiellement identique à l’utilisation de l’attribut intégré [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute).

### <a name="sample-custom-routing-convention"></a>Exemple : Convention de routage personnalisée

Vous pouvez utiliser une `IApplicationModelConvention` pour personnaliser le fonctionnement du routage. Par exemple, la convention suivante intègre des espaces de noms de contrôleurs dans leurs routes, en remplaçant `.` dans l’espace de noms par `/` dans la route :

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

La convention est ajoutée en tant qu’option dans Startup.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> Vous pouvez ajouter des conventions à votre [intergiciel](xref:fundamentals/middleware/index) en accédant à `MvcOptions` avec `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

Cet exemple applique cette convention aux routes qui n’utilisent pas le routage par attributs où le contrôleur a « Namespace » dans son nom. Le contrôleur suivant illustre cette convention :

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Utilisation du modèle d’application dans WebApiCompatShim

ASP.NET Core MVC utilise un autre ensemble de conventions d’ASP.NET Web API 2. Avec des conventions personnalisées, vous pouvez modifier le comportement d’une application ASP.NET Core MVC pour le mettre en cohérence avec celui d’une application d’API web. Microsoft livre le [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) spécialement à cette fin.

> [!NOTE]
> Découvrez plus d’informations sur la [migration depuis l’API web ASP.NET](xref:migration/webapi).

Pour utiliser le shim de compatibilité d’API web, vous devez ajouter le package à votre projet, puis ajouter les conventions à MVC en appelant `AddWebApiConventions` dans `Startup` :

```csharp
services.AddMvc().AddWebApiConventions();
```

Les conventions fournies par le shim sont appliquées seulement aux parties de l’application auxquelles certains attributs sont appliqués. Les quatre attributs suivants sont utilisés pour spécifier quels contrôleurs doivent avoir leurs conventions modifiées par les conventions du shim :

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Conventions d’actions

Le `UseWebApiActionConventionsAttribute` est utilisé pour mapper la méthode HTTP à des actions en fonction de leur nom (par exemple `Get` est mappé à `HttpGet`). Il s’applique seulement aux actions qui n’utilisent pas le routage par attributs.

### <a name="overloading"></a>Surcharge

Le `UseWebApiOverloadingAttribute` est utilisé pour appliquer la convention `WebApiOverloadingApplicationModelConvention`. Cette convention ajoute une `OverloadActionConstraint` au processus de sélection d’actions, qui limite les actions candidates à celles pour lesquelles la requête satisfait à tous les paramètres non facultatifs.

### <a name="parameter-conventions"></a>Conventions de paramètres

Le `UseWebApiParameterConventionsAttribute` est utilisé pour appliquer la convention d’actions `WebApiParameterConventionsApplicationModelConvention`. Cette convention spécifie que des types simples utilisés comme paramètres d’actions sont liés à partir de l’URI par défaut, alors que les types complexes sont liés à partir du corps de la requête.

### <a name="routes"></a>Itinéraires

Le `UseWebApiRoutesAttribute` spécifie si la convention de contrôleur `WebApiApplicationModelConvention` est appliquée. Quand elle est activée, cette convention est utilisée pour ajouter la prise en charge de [zones](xref:mvc/controllers/areas) pour la route.

En plus d’un ensemble de conventions, le package de compatibilité inclut une classe de base `System.Web.Http.ApiController` qui remplace celle fournie par l’API web. Ceci permet à vos contrôleurs écrits pour API web et héritant de son `ApiController` de fonctionner comme prévu quand ils s’exécutent sur ASP.NET Core MVC. Tous les `UseWebApi*` attributs répertoriés plus haut sont appliqués à la classe de contrôleur de base. Le `ApiController` expose les propriétés, les méthodes et les types de résultats qui sont compatibles avec ceux trouvés dans l’API web.

## <a name="using-apiexplorer-to-document-your-app"></a>Utilisation d’ApiExplorer pour documenter votre application

Le modèle d’application expose une [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) propriété à chaque niveau qui peut être utilisé pour parcourir la structure de l’application. Ceci peut être utilisé pour [générer des pages d’aide pour votre API web avec des outils comme Swagger](xref:tutorials/web-api-help-pages-using-swagger). La propriété `ApiExplorer` expose une propriété `IsVisible` qui peut être définie pour spécifier les parties du modèle de votre application qui doivent être exposées. Vous pouvez configurer ce paramètre en utilisant une convention :

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

Avec cette approche (et avec des conventions supplémentaires si nécessaire), vous pouvez activer ou désactiver la visibilité de l’API à n’importe quel niveau au sein de votre application. 
