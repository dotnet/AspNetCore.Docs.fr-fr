---
title: Utiliser le modèle d’application dans ASP.NET Core
author: ardalis
description: Découvrez comment lire et manipuler le modèle d’application pour modifier le comportement des éléments MVC dans ASP.NET Core.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/controllers/application-model
ms.openlocfilehash: a7a654eb43c0dbf375af911d8d5353ac4c04a825
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060935"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="ebc32-103">Utiliser le modèle d’application dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebc32-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="ebc32-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ebc32-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ebc32-105">ASP.NET Core MVC définit un *modèle d’application* représentant les composants d’une application MVC.</span><span class="sxs-lookup"><span data-stu-id="ebc32-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="ebc32-106">Vous pouvez lire et manipuler ce modèle pour modifier le comportement des éléments MVC.</span><span class="sxs-lookup"><span data-stu-id="ebc32-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="ebc32-107">Par défaut, MVC suit certaines conventions pour déterminer quelles classes sont considérées comme des contrôleurs, quelles méthodes sur ces classes sont des actions, et comment les paramètres et le routage se comportent.</span><span class="sxs-lookup"><span data-stu-id="ebc32-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="ebc32-108">Vous pouvez personnaliser ce comportement en fonction des besoins de votre application en créant vos propres conventions, et en les appliquant globalement ou en tant qu’attributs.</span><span class="sxs-lookup"><span data-stu-id="ebc32-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="ebc32-109">Modèles et fournisseurs</span><span class="sxs-lookup"><span data-stu-id="ebc32-109">Models and Providers</span></span>

<span data-ttu-id="ebc32-110">Le modèle d’application ASP.NET Core MVC inclut des les interfaces abstraites et des classes d’implémentation concrètes qui décrivent une application MVC.</span><span class="sxs-lookup"><span data-stu-id="ebc32-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="ebc32-111">Ce modèle est le résultat de la découverte par MVC des contrôleurs, des actions, des paramètres d’action, des routes et des filtres de l’application en fonction de conventions par défaut.</span><span class="sxs-lookup"><span data-stu-id="ebc32-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="ebc32-112">Grâce au modèle d’application, vous pouvez modifier votre application pour suivre des conventions différentes du comportement de MVC par défaut.</span><span class="sxs-lookup"><span data-stu-id="ebc32-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="ebc32-113">Les paramètres, les noms, les routes et les filtres sont utilisés comme données de configuration pour les actions et les contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="ebc32-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="ebc32-114">Le modèle d’application ASP.NET Core MVC a la structure suivante :</span><span class="sxs-lookup"><span data-stu-id="ebc32-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="ebc32-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="ebc32-115">ApplicationModel</span></span>
  * <span data-ttu-id="ebc32-116">Contrôleurs (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="ebc32-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="ebc32-117">Actions (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="ebc32-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="ebc32-118">Paramètres (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="ebc32-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="ebc32-119">Chaque niveau du modèle a accès à une collection `Properties` commune, et les niveaux inférieurs peuvent accéder à et remplacer les valeurs de propriétés définies par les niveaux supérieurs de la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="ebc32-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="ebc32-120">Les propriétés sont rendues persistantes dans `ActionDescriptor.Properties` quand les actions sont créées.</span><span class="sxs-lookup"><span data-stu-id="ebc32-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="ebc32-121">Ensuite, quand une requête est traitée, toutes les propriétés ajoutées ou modifiées par une convention sont accessibles via `ActionContext.ActionDescriptor.Properties`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="ebc32-122">L’utilisation de propriétés est un bon moyen de configurer vos filtres, vos classeurs de modèles, etc. à l’échelle d’une action.</span><span class="sxs-lookup"><span data-stu-id="ebc32-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="ebc32-123">La collection `ActionDescriptor.Properties` n’est pas thread-safe (pour les écritures) une fois que le démarrage de l’application est terminé.</span><span class="sxs-lookup"><span data-stu-id="ebc32-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="ebc32-124">Les conventions sont la meilleure façon d’ajouter des données de façon sécurisée à cette collection.</span><span class="sxs-lookup"><span data-stu-id="ebc32-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="ebc32-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="ebc32-125">IApplicationModelProvider</span></span>

<span data-ttu-id="ebc32-126">ASP.NET Core MVC charge le modèle d’application en utilisant un modèle de fournisseur, défini par l’interface [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider).</span><span class="sxs-lookup"><span data-stu-id="ebc32-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="ebc32-127">Cette section aborde certains aspects de l’implémentation interne concernant la façon dont fonctionne de ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ebc32-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="ebc32-128">Il s’agit d’une rubrique avancée : la plupart des applications qui tirent parti du modèle d’application doivent le faire en utilisant les conventions.</span><span class="sxs-lookup"><span data-stu-id="ebc32-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="ebc32-129">Les implémentations de l’interface `IApplicationModelProvider` sont « encapsulées » les unes dans les autres, avec chaque implémentation appelant `OnProvidersExecuting` dans un ordre croissant basé sur sa propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="ebc32-130">La méthode `OnProvidersExecuted` est ensuite appelée dans l’ordre inverse.</span><span class="sxs-lookup"><span data-stu-id="ebc32-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="ebc32-131">Le framework définit plusieurs fournisseurs :</span><span class="sxs-lookup"><span data-stu-id="ebc32-131">The framework defines several providers:</span></span>

<span data-ttu-id="ebc32-132">D’abord (`Order=-1000`) :</span><span class="sxs-lookup"><span data-stu-id="ebc32-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="ebc32-133">Ensuite (`Order=-990`) :</span><span class="sxs-lookup"><span data-stu-id="ebc32-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="ebc32-134">L’ordre dans deux fournisseurs avec la même valeur pour `Order` sont appelés n’est pas défini et vous ne pouvez donc pas vous baser sur celui-ci.</span><span class="sxs-lookup"><span data-stu-id="ebc32-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="ebc32-135">`IApplicationModelProvider` est un concept avancé que les auteurs de framework peuvent étendre.</span><span class="sxs-lookup"><span data-stu-id="ebc32-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="ebc32-136">D’une façon générale, les applications doivent utiliser des conventions et les frameworks doivent utiliser des fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="ebc32-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="ebc32-137">La principale différence est que les fournisseurs s’exécutent toujours avant les conventions.</span><span class="sxs-lookup"><span data-stu-id="ebc32-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="ebc32-138">`DefaultApplicationModelProvider` établit la plupart des comportements par défaut utilisés par ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ebc32-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="ebc32-139">Ses responsabilités incluent :</span><span class="sxs-lookup"><span data-stu-id="ebc32-139">Its responsibilities include:</span></span>

* <span data-ttu-id="ebc32-140">L’ajout de filtres globaux au contexte</span><span class="sxs-lookup"><span data-stu-id="ebc32-140">Adding global filters to the context</span></span>
* <span data-ttu-id="ebc32-141">L’ajout de contrôleurs au contexte</span><span class="sxs-lookup"><span data-stu-id="ebc32-141">Adding controllers to the context</span></span>
* <span data-ttu-id="ebc32-142">L’ajout de méthodes publiques de contrôleur en tant qu’actions</span><span class="sxs-lookup"><span data-stu-id="ebc32-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="ebc32-143">L’ajout de paramètres de méthode d’action au contexte</span><span class="sxs-lookup"><span data-stu-id="ebc32-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="ebc32-144">L’application de routes et d’autres attributs</span><span class="sxs-lookup"><span data-stu-id="ebc32-144">Applying route and other attributes</span></span>

<span data-ttu-id="ebc32-145">Certains comportements intégrés sont implémentées par le `DefaultApplicationModelProvider`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="ebc32-146">Ce fournisseur est chargé de construire le [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , qui à son tour référence [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) les [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) instances, et [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) .</span><span class="sxs-lookup"><span data-stu-id="ebc32-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) instances.</span></span> <span data-ttu-id="ebc32-147">La classe `DefaultApplicationModelProvider` est un détail d’implémentation interne du framework qui changera à coup sûr dans le futur.</span><span class="sxs-lookup"><span data-stu-id="ebc32-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="ebc32-148">Le `AuthorizationApplicationModelProvider` est responsable de l’application du comportement associé aux attributs `AuthorizeFilter` et `AllowAnonymousFilter`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="ebc32-149">[Découvrez plus d’informations sur ces fonctionnalités](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="ebc32-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="ebc32-150">Le `CorsApplicationModelProvider` implémente le comportement associé au `IEnableCorsAttribute` et au `IDisableCorsAttribute`, et le `DisableCorsAuthorizationFilter`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="ebc32-151">[Découvrez plus d’informations sur CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="ebc32-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="ebc32-152">Conventions</span><span class="sxs-lookup"><span data-stu-id="ebc32-152">Conventions</span></span>

<span data-ttu-id="ebc32-153">Le modèle d’application définit des abstractions de convention qui fournissent un moyen de personnaliser le comportement des modèles plus simple que de remplacer tout le modèle ou tout le fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ebc32-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="ebc32-154">Ces abstractions constituent le moyen recommandé pour modifier le comportement de votre application.</span><span class="sxs-lookup"><span data-stu-id="ebc32-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="ebc32-155">Les conventions vous permettent d’écrire du code qui applique dynamiquement des personnalisations.</span><span class="sxs-lookup"><span data-stu-id="ebc32-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="ebc32-156">Bien que les [filtres](xref:mvc/controllers/filters) offrent un moyen de modifier le comportement de l’infrastructure, les personnalisations vous permettent de contrôler le fonctionnement de l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="ebc32-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app works together.</span></span>

<span data-ttu-id="ebc32-157">Les conventions suivantes sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="ebc32-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="ebc32-158">Les conventions sont appliquées en les ajoutant aux options MVC ou en implémentant `Attribute` des et en les appliquant à des contrôleurs, des actions ou des paramètres d’action (similaires à [`Filters`](xref:mvc/controllers/filters) ).</span><span class="sxs-lookup"><span data-stu-id="ebc32-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="ebc32-159">Contrairement aux filtres, les conventions sont exécutées seulement lors du démarrage de l’application, et non pas dans le cadre de chaque requête.</span><span class="sxs-lookup"><span data-stu-id="ebc32-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="ebc32-160">Exemple : Modification du ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="ebc32-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="ebc32-161">La convention suivante est utilisée pour ajouter une propriété au modèle d’application.</span><span class="sxs-lookup"><span data-stu-id="ebc32-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="ebc32-162">Les conventions de modèle d’application sont appliquées en tant qu’options quand MVC est ajouté à `ConfigureServices` dans `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="ebc32-163">Les propriétés sont accessibles à partir de la collection de propriétés `ActionDescriptor` dans les actions de contrôleur :</span><span class="sxs-lookup"><span data-stu-id="ebc32-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="ebc32-164">Exemple : Modification de la description de ControllerModel</span><span class="sxs-lookup"><span data-stu-id="ebc32-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="ebc32-165">Comme dans l’exemple précédent, le modèle de contrôleur peut également être modifié pour y inclure des propriétés personnalisées.</span><span class="sxs-lookup"><span data-stu-id="ebc32-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="ebc32-166">Celles-ci remplacent les propriétés existantes portant le même nom que celui spécifié dans le modèle d’application.</span><span class="sxs-lookup"><span data-stu-id="ebc32-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="ebc32-167">L’attribut de convention suivant ajoute une description au niveau du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="ebc32-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="ebc32-168">Cette convention est appliquée en tant qu’attribut sur un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="ebc32-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="ebc32-169">Vous accédez à la propriété « description » de la même façon que dans les exemples précédents.</span><span class="sxs-lookup"><span data-stu-id="ebc32-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="ebc32-170">Exemple : Modification de la description de ActionModel</span><span class="sxs-lookup"><span data-stu-id="ebc32-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="ebc32-171">Une convention d’attribut distincte peut être appliquée à des actions individuelles, en remplaçant le comportement déjà appliqué au niveau de l’application ou du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="ebc32-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="ebc32-172">L’application de ceci à une action dans l’exemple de contrôleur précédent montre comment elle remplace la convention au niveau du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="ebc32-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="ebc32-173">Exemple : Modification du ParameterModel</span><span class="sxs-lookup"><span data-stu-id="ebc32-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="ebc32-174">La convention suivante peut être appliquée à des paramètres d’action pour modifier leur `BindingInfo`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="ebc32-175">La convention suivante nécessite que le paramètre soit un paramètre de route ; les autres sources potentielles de liaison (comme les valeurs de la chaîne de requête) sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="ebc32-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="ebc32-176">L’attribut peut être appliqué à n’importe quel paramètre d’action :</span><span class="sxs-lookup"><span data-stu-id="ebc32-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="ebc32-177">Exemple : Modification du nom de ActionModel</span><span class="sxs-lookup"><span data-stu-id="ebc32-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="ebc32-178">La convention suivante modifie le `ActionModel` pour mettre à jour le *nom* de l’action à laquelle il est appliqué.</span><span class="sxs-lookup"><span data-stu-id="ebc32-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="ebc32-179">Le nouveau nom est fourni à l’attribut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="ebc32-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="ebc32-180">Ce nouveau nom est utilisé par le routage : il affecte donc la route utilisée pour atteindre cette méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="ebc32-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="ebc32-181">Cet attribut est appliqué à une méthode d’action dans le `HomeController` :</span><span class="sxs-lookup"><span data-stu-id="ebc32-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="ebc32-182">Même si le nom de la méthode est `SomeName`, l’attribut remplace la convention MVC d’utiliser le nom de méthode et remplace le nom de l’action par `MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="ebc32-183">Ainsi, la route utilisée pour atteindre cette action est `/Home/MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="ebc32-184">Cet exemple est essentiellement identique à l’utilisation de l’attribut intégré [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute).</span><span class="sxs-lookup"><span data-stu-id="ebc32-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="ebc32-185">Exemple : Convention de routage personnalisée</span><span class="sxs-lookup"><span data-stu-id="ebc32-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="ebc32-186">Vous pouvez utiliser une `IApplicationModelConvention` pour personnaliser le fonctionnement du routage.</span><span class="sxs-lookup"><span data-stu-id="ebc32-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="ebc32-187">Par exemple, la convention suivante intègre des espaces de noms de contrôleurs dans leurs routes, en remplaçant `.` dans l’espace de noms par `/` dans la route :</span><span class="sxs-lookup"><span data-stu-id="ebc32-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="ebc32-188">La convention est ajoutée en tant qu’option dans Startup.</span><span class="sxs-lookup"><span data-stu-id="ebc32-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="ebc32-189">Vous pouvez ajouter des conventions à votre [intergiciel](xref:fundamentals/middleware/index) en accédant à `MvcOptions` avec `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span><span class="sxs-lookup"><span data-stu-id="ebc32-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="ebc32-190">Cet exemple applique cette convention aux routes qui n’utilisent pas le routage par attributs où le contrôleur a « Namespace » dans son nom.</span><span class="sxs-lookup"><span data-stu-id="ebc32-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="ebc32-191">Le contrôleur suivant illustre cette convention :</span><span class="sxs-lookup"><span data-stu-id="ebc32-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="ebc32-192">Utilisation du modèle d’application dans WebApiCompatShim</span><span class="sxs-lookup"><span data-stu-id="ebc32-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="ebc32-193">ASP.NET Core MVC utilise un autre ensemble de conventions d’ASP.NET Web API 2.</span><span class="sxs-lookup"><span data-stu-id="ebc32-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="ebc32-194">Avec des conventions personnalisées, vous pouvez modifier le comportement d’une application ASP.NET Core MVC pour le mettre en cohérence avec celui d’une application d’API web.</span><span class="sxs-lookup"><span data-stu-id="ebc32-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="ebc32-195">Microsoft livre le [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) spécialement à cette fin.</span><span class="sxs-lookup"><span data-stu-id="ebc32-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="ebc32-196">Découvrez plus d’informations sur la [migration depuis l’API web ASP.NET](xref:migration/webapi).</span><span class="sxs-lookup"><span data-stu-id="ebc32-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="ebc32-197">Pour utiliser le shim de compatibilité d’API web, vous devez ajouter le package à votre projet, puis ajouter les conventions à MVC en appelant `AddWebApiConventions` dans `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ebc32-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="ebc32-198">Les conventions fournies par le shim sont appliquées seulement aux parties de l’application auxquelles certains attributs sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="ebc32-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="ebc32-199">Les quatre attributs suivants sont utilisés pour spécifier quels contrôleurs doivent avoir leurs conventions modifiées par les conventions du shim :</span><span class="sxs-lookup"><span data-stu-id="ebc32-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="ebc32-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="ebc32-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="ebc32-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="ebc32-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="ebc32-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="ebc32-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="ebc32-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="ebc32-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="ebc32-204">Conventions d’actions</span><span class="sxs-lookup"><span data-stu-id="ebc32-204">Action Conventions</span></span>

<span data-ttu-id="ebc32-205">Le `UseWebApiActionConventionsAttribute` est utilisé pour mapper la méthode HTTP à des actions en fonction de leur nom (par exemple `Get` est mappé à `HttpGet`).</span><span class="sxs-lookup"><span data-stu-id="ebc32-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="ebc32-206">Il s’applique seulement aux actions qui n’utilisent pas le routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="ebc32-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="ebc32-207">Surcharge</span><span class="sxs-lookup"><span data-stu-id="ebc32-207">Overloading</span></span>

<span data-ttu-id="ebc32-208">Le `UseWebApiOverloadingAttribute` est utilisé pour appliquer la convention `WebApiOverloadingApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="ebc32-209">Cette convention ajoute une `OverloadActionConstraint` au processus de sélection d’actions, qui limite les actions candidates à celles pour lesquelles la requête satisfait à tous les paramètres non facultatifs.</span><span class="sxs-lookup"><span data-stu-id="ebc32-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="ebc32-210">Conventions de paramètres</span><span class="sxs-lookup"><span data-stu-id="ebc32-210">Parameter Conventions</span></span>

<span data-ttu-id="ebc32-211">Le `UseWebApiParameterConventionsAttribute` est utilisé pour appliquer la convention d’actions `WebApiParameterConventionsApplicationModelConvention`.</span><span class="sxs-lookup"><span data-stu-id="ebc32-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="ebc32-212">Cette convention spécifie que des types simples utilisés comme paramètres d’actions sont liés à partir de l’URI par défaut, alors que les types complexes sont liés à partir du corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="ebc32-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="ebc32-213">Itinéraires</span><span class="sxs-lookup"><span data-stu-id="ebc32-213">Routes</span></span>

<span data-ttu-id="ebc32-214">Le `UseWebApiRoutesAttribute` spécifie si la convention de contrôleur `WebApiApplicationModelConvention` est appliquée.</span><span class="sxs-lookup"><span data-stu-id="ebc32-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="ebc32-215">Quand elle est activée, cette convention est utilisée pour ajouter la prise en charge de [zones](xref:mvc/controllers/areas) pour la route.</span><span class="sxs-lookup"><span data-stu-id="ebc32-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="ebc32-216">En plus d’un ensemble de conventions, le package de compatibilité inclut une classe de base `System.Web.Http.ApiController` qui remplace celle fournie par l’API web.</span><span class="sxs-lookup"><span data-stu-id="ebc32-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="ebc32-217">Ceci permet à vos contrôleurs écrits pour API web et héritant de son `ApiController` de fonctionner comme prévu quand ils s’exécutent sur ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="ebc32-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="ebc32-218">Tous les `UseWebApi*` attributs répertoriés plus haut sont appliqués à la classe de contrôleur de base.</span><span class="sxs-lookup"><span data-stu-id="ebc32-218">All of the `UseWebApi*` attributes listed earlier are applied to the base controller class.</span></span> <span data-ttu-id="ebc32-219">Le `ApiController` expose les propriétés, les méthodes et les types de résultats qui sont compatibles avec ceux trouvés dans l’API web.</span><span class="sxs-lookup"><span data-stu-id="ebc32-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="ebc32-220">Utilisation d’ApiExplorer pour documenter votre application</span><span class="sxs-lookup"><span data-stu-id="ebc32-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="ebc32-221">Le modèle d’application expose une [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) propriété à chaque niveau qui peut être utilisé pour parcourir la structure de l’application.</span><span class="sxs-lookup"><span data-stu-id="ebc32-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="ebc32-222">Ceci peut être utilisé pour [générer des pages d’aide pour votre API web avec des outils comme Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="ebc32-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="ebc32-223">La propriété `ApiExplorer` expose une propriété `IsVisible` qui peut être définie pour spécifier les parties du modèle de votre application qui doivent être exposées.</span><span class="sxs-lookup"><span data-stu-id="ebc32-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="ebc32-224">Vous pouvez configurer ce paramètre en utilisant une convention :</span><span class="sxs-lookup"><span data-stu-id="ebc32-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="ebc32-225">Avec cette approche (et avec des conventions supplémentaires si nécessaire), vous pouvez activer ou désactiver la visibilité de l’API à n’importe quel niveau au sein de votre application.</span><span class="sxs-lookup"><span data-stu-id="ebc32-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
