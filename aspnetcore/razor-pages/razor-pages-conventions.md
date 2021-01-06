---
title: Razor Conventions de routage et d’application des pages dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les conventions du fournisseur de modèles de routes et d’applications vous permettent de gérer le routage, la découverte et le traitement des pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 2947bf0b697ca01f17d260b9f31aa3cc79d457b6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059869"
---
# <a name="no-locrazor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor Conventions de routage et d’application des pages dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Découvrez comment utiliser [les conventions du fournisseur de modèles d’application et](xref:mvc/controllers/application-model#conventions) de routage de page pour contrôler le routage, la découverte et le traitement des pages dans les Razor applications de pages.

Quand vous devez configurer des routages de pages personnalisés pour des pages individuelles, configurez le routage vers les pages avec la convention [AddPageRoute](#configure-a-page-route) décrite plus loin dans cette rubrique.

Pour spécifier un itinéraire de page, ajouter des segments de routage ou ajouter des paramètres à un itinéraire, utilisez la directive de la page `@page` . Pour plus d’informations, consultez [itinéraires personnalisés](xref:razor-pages/index#custom-routes).

Il existe des mots réservés qui ne peuvent pas être utilisés en tant que segments de routage ou noms de paramètres. Pour plus d’informations, consultez [routage : noms de routage réservés](xref:mvc/controllers/routing#reserved-routing-names).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

| Scénario | L’exemple montre... |
| -------- | --------------------------- |
| [Conventions de modèle](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Ajouter un modèle de route et un en-tête aux pages d’une application. |
| [Conventions d’actions de routage de pages](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Ajouter un modèle de route aux pages d’un dossier et à une page unique. |
| [Conventions d’actions de modèle de page](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtre, expression lambda ou fabrique de filtres)</li></ul> | Ajouter un en-tête dans les pages d’un dossier, ajouter un en-tête dans une page unique et configurer une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) pour ajouter un en-tête dans les pages d’une application. |

Razor Les conventions de pages sont configurées à l’aide d’une <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> surcharge configure <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> dans `Startup.ConfigureServices` . Les exemples de convention suivants sont expliqués plus loin dans cette rubrique :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a>Ordre de routage

Les itinéraires spécifient un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pour le traitement (correspondance d’itinéraires).

| JSON            | Comportement |
| :--------------: | -------- |
| -1               | L’itinéraire est traité avant le traitement des autres itinéraires. |
| 0                | L’ordre n’est pas spécifié (valeur par défaut). Si vous n’affectez pas `Order` ( `Order = null` ), l’itinéraire est défini par défaut `Order` sur 0 (zéro) pour le traitement. |
| 1, 2, &hellip; n | Spécifie l’ordre de traitement des itinéraires. |

Le traitement des itinéraires est établi par Convention :

* Les itinéraires sont traités dans l’ordre séquentiel (-1, 0, 1, 2, &hellip; n).
* Lorsque les itinéraires ont le même `Order` , l’itinéraire le plus spécifique est mis en correspondance en premier, suivi d’itinéraires moins spécifiques.
* Lorsque des itinéraires avec le même `Order` nombre de paramètres correspondent à une URL de demande, les itinéraires sont traités dans l’ordre dans lequel ils sont ajoutés au <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Si possible, évitez de dépendre d’un ordre de traitement des itinéraires établi. En règle générale, le routage sélectionne l’itinéraire correct avec la correspondance d’URL. Si vous devez définir des `Order` Propriétés de routage pour acheminer les demandes correctement, le schéma de routage de l’application est sans doute confus pour les clients et fragiles à gérer. Cherchez à simplifier le schéma de routage de l’application. L’exemple d’application requiert un ordre de traitement des itinéraires explicites pour illustrer plusieurs scénarios de routage à l’aide d’une seule application. Toutefois, vous devez tenter d’éviter d’avoir à définir l’itinéraire `Order` dans les applications de production.

Razor Le routage des pages et le routage du contrôleur MVC partagent une implémentation. Des informations sur l’ordre des itinéraires dans les rubriques MVC sont disponibles dans [routage vers actions du contrôleur : classement des itinéraires des attributs](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Conventions de modèle

Ajoutez un délégué pour <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pour ajouter des [conventions de modèle](xref:mvc/controllers/application-model#conventions) qui s’appliquent aux Razor pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Ajouter une convention de modèle de routage à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle de routage de page.

L’exemple d’application ajoute un modèle de routage `{globalTemplate?}` à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `1`. Cela garantit le comportement de correspondance d’itinéraire suivant dans l’exemple d’application :

* Un modèle de routage pour `TheContactPage/{text?}` est ajouté plus loin dans la rubrique. L’itinéraire de la page de contact a un ordre par défaut `null` ( `Order = 0` ), de sorte qu’il corresponde avant le `{globalTemplate?}` modèle de routage.
* Un `{aboutTemplate?}` modèle de routage est ajouté plus loin dans la rubrique. Le modèle `{aboutTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand la page About est demandée sur `/About/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 1`) et non `RouteData.Values["aboutTemplate"]` (`Order = 2`) en raison de la définition de la propriété `Order`.
* Un `{otherPagesTemplate?}` modèle de routage est ajouté plus loin dans la rubrique. Le modèle `{otherPagesTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand une page du dossier *pages/OtherPages* est demandée avec un paramètre d’itinéraire (par exemple, `/OtherPages/Page1/RouteDataValue` ), « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Razor Les options de pages, telles que l’ajout <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , sont ajoutées lorsque des Razor pages sont ajoutées à la collection de services dans `Startup.ConfigureServices` . Pour obtenir un exemple, consultez [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue` et examinez le résultat :

![La page About est demandée avec un segment de routage pour GlobalRouteValue. La page affichée montre que la valeur des données de routage est capturée dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Ajouter une convention de modèle d’application à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle d’application de la page.

Pour illustrer cette convention et bien d’autres, plus loin dans cette rubrique, l’exemple d’application inclut une classe `AddHeaderAttribute`. Le constructeur de classe accepte une chaîne `name` et un tableau de chaînes `values`. Ces valeurs sont utilisées dans sa méthode `OnResultExecuting` pour définir un en-tête de réponse. La classe complète est affichée dans la section [Conventions d’actions de modèle de page](#page-model-action-conventions), plus loin dans cette rubrique.

L’exemple d’application utilise la classe `AddHeaderAttribute` pour ajouter un en-tête, `GlobalHeader`, à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que GlobalHeader a été ajouté.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Ajouter une convention de modèle de gestionnaire à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle de gestionnaire de page.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Conventions d’actions de routage de pages

Le fournisseur de modèles de routage par défaut qui dérive de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> appelle des conventions qui sont conçues pour fournir des points d’extensibilité pour la configuration des itinéraires de page.

### <a name="folder-route-model-convention"></a>Convention de modèle de routage de dossier

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> objet qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pour toutes les pages du dossier spécifié.

L’exemple d’application utilise <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour ajouter un modèle de routage `{otherPagesTemplate?}` aux pages du dossier *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela permet de s’assurer que le modèle pour `{globalTemplate?}` (défini plus tôt dans la rubrique `1` ) est prioritaire pour la première position de valeur de données de routage lorsqu’une valeur de routage unique est fournie. Si une page du dossier *pages/OtherPages* est demandée avec une valeur de paramètre d’itinéraire (par exemple, `/OtherPages/Page1/RouteDataValue` ), « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` et examinez le résultat :

![La page Page1 du dossier OtherPages est demandée avec un segment de routage pour GlobalRouteValue et OtherPagesRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convention de modèle de routage de page

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> pour créer et ajouter un objet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pour la page avec le nom spécifié.

L’exemple d’application utilise `AddPageRouteModelConvention` pour ajouter un modèle de routage `{aboutTemplate?}` à la page About :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela permet de s’assurer que le modèle pour `{globalTemplate?}` (défini plus tôt dans la rubrique `1` ) est prioritaire pour la première position de valeur de données de routage lorsqu’une valeur de routage unique est fournie. Si la page about est demandée avec une valeur de paramètre d’itinéraire à `/About/RouteDataValue` , « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue/AboutRouteValue` et examinez le résultat :

![La page About est demandée avec des segments de routage pour GlobalRouteValue et AboutRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Utiliser un transformateur de paramètre pour personnaliser les itinéraires de page

Les itinéraires de page générés par ASP.NET Core peuvent être personnalisés à l’aide d’un transformateur de paramètres. Un transformateur de paramètre implémente `IOutboundParameterTransformer` et transforme la valeur des paramètres. Par exemple, un transformateur de paramètre `SlugifyParameterTransformer` personnalisé transforme la valeur de la route `SubscriptionManagement` en `subscription-management`.

La `PageRouteTransformerConvention` Convention de modèle de routage de page applique un transformateur de paramètre aux segments de nom de dossier et de fichier des itinéraires de page générés automatiquement dans une application. Par exemple, le Razor fichier de pages sur */pages/SubscriptionManagement/viewall.cshtml* aurait son itinéraire réécrit de `/SubscriptionManagement/ViewAll` à `/subscription-management/view-all` .

`PageRouteTransformerConvention` convertit uniquement les segments générés automatiquement d’un itinéraire de page provenant du Razor dossier pages et du nom de fichier. Elle ne transforme pas les segments de routage ajoutés avec la `@page` directive. La Convention ne transforme pas non plus les itinéraires ajoutés par <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .

Le `PageRouteTransformerConvention` est inscrit en tant qu’option dans `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Configurer un routage de page

Utilisez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> pour configurer un itinéraire vers une page au niveau du chemin de page spécifié. Les liens générés qui pointent vers la page utilisent le routage que vous avez spécifié. `AddPageRoute` utilise `AddPageRouteModelConvention` pour établir le routage.

L’exemple d’application crée un routage vers `/TheContactPage` pour *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

La page Contact est également accessible sur `/Contact` via son routage par défaut.

Le routage personnalisé de l’exemple d’application vers la page Contact permet un segment de routage `text` facultatif (`{text?}`). Cette page inclut également ce segment facultatif dans sa directive `@page`, au cas où le visiteur accèderait à la page via le routage `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Notez que l’URL générée pour le lien **Contact** dans la page affichée reflète le routage mis à jour :

![Lien Contact de l’exemple d’application dans la barre de navigation](razor-pages-conventions/_static/contact-link.png)

![Le lien Contact dans la page HTML affichée indique que l’attribut href a la valeur ’/TheContactPage’](razor-pages-conventions/_static/contact-link-source.png)

Visitez la page Contact via son routage ordinaire, `/Contact`, ou via le routage personnalisé, `/TheContactPage`. Si vous indiquez un segment de routage `text` supplémentaire, la page affiche le segment HTML que vous fournissez :

![Exemple dans le navigateur Edge d’un segment de routage ‘text’ facultatif fourni pour ‘TextValue’ dans l’URL. La page affichée montre la valeur du segment ‘text’.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Conventions d’actions de modèle de page

Le fournisseur de modèles de page par défaut qui implémente <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> appelle des conventions conçues pour fournir des points d’extensibilité pour la configuration des modèles de page. Ces conventions sont utiles durant la génération et la modification de scénarios de découverte et de traitement de pages.

Pour les exemples de cette section, l’exemple d’application utilise une `AddHeaderAttribute` classe, qui est un <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , qui applique un en-tête de réponse :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

À l’aide de conventions, l’exemple montre comment appliquer l’attribut à toutes les pages d’un dossier et à une seule page.

**Convention de modèle d’application de dossier**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> objet qui appelle une action sur <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> les instances de toutes les pages situées dans le dossier spécifié.

L’exemple illustre l’utilisation de `AddFolderApplicationModelConvention` en ajoutant un en-tête, `OtherPagesHeader`, aux pages situées dans le dossier *OtherPages* de l’application :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page OtherPages/Page1 montrent que OtherPagesHeader a été ajouté.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convention de modèle d’application de page**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> pour créer et ajouter un objet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pour la page avec le nom spécifié.

L’exemple illustre l’utilisation de `AddPageApplicationModelConvention` en ajoutant un en-tête, `AboutHeader`, à la page About :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que AboutHeader a été ajouté.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurer un filtre**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configure le filtre spécifié à appliquer. Vous pouvez implémenter une classe de filtre. Toutefois, l’exemple d’application montre comment implémenter un filtre dans une expression lambda, laquelle est implémentée en arrière-plan en tant que fabrique qui retourne un filtre :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Le modèle d’application de page est utilisé pour vérifier le chemin relatif des segments qui mènent à la page Page2 dans le dossier *OtherPages*. Si la condition est satisfaite, un en-tête est ajouté. Sinon, `EmptyFilter` est appliqué.

`EmptyFilter` est un [filtre d’action](xref:mvc/controllers/filters#action-filters). Étant donné que les filtres d’action sont ignorés par Razor les pages, le n' `EmptyFilter` a aucun effet comme prévu si le chemin d’accès ne contient pas `OtherPages/Page2` .

Demandez la page Page2 de l’exemple sur `localhost:5000/OtherPages/Page2` et examinez les en-têtes pour voir le résultat :

![OtherPagesPage2Header est ajouté à la réponse pour Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurer une fabrique de filtres**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configure la fabrique spécifiée pour appliquer des [filtres](xref:mvc/controllers/filters) à toutes les Razor pages.

L’exemple d’application illustre l’utilisation d’une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) en ajoutant un en-tête, `FilterFactoryHeader`, et deux valeurs aux pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que deux en-têtes FilterFactoryHeader ont été ajoutés.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtres MVC et filtre de page (IPageFilter)

Les [filtres d’action](xref:mvc/controllers/filters#action-filters) MVC sont ignorés par les Razor pages, car les Razor pages utilisent des méthodes de gestionnaire. Vous pouvez utiliser d’autres types de filtre MVC : [Autorisation](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) et [Résultat](xref:mvc/controllers/filters#result-filters). Pour plus d’informations, consultez la rubrique [Filtres](xref:mvc/controllers/filters).

Le filtre de page ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) est un filtre qui s’applique aux Razor pages. Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Découvrez comment utiliser [les conventions du fournisseur de modèles d’application et](xref:mvc/controllers/application-model#conventions) de routage de page pour contrôler le routage, la découverte et le traitement des pages dans les Razor applications de pages.

Quand vous devez configurer des routages de pages personnalisés pour des pages individuelles, configurez le routage vers les pages avec la convention [AddPageRoute](#configure-a-page-route) décrite plus loin dans cette rubrique.

Pour spécifier un itinéraire de page, ajouter des segments de routage ou ajouter des paramètres à un itinéraire, utilisez la directive de la page `@page` . Pour plus d’informations, consultez [itinéraires personnalisés](xref:razor-pages/index#custom-routes).

Il existe des mots réservés qui ne peuvent pas être utilisés en tant que segments de routage ou noms de paramètres. Pour plus d’informations, consultez [routage : noms de routage réservés](xref:fundamentals/routing#reserved-routing-names).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

| Scénario | L’exemple montre... |
| -------- | --------------------------- |
| [Conventions de modèle](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Ajouter un modèle de route et un en-tête aux pages d’une application. |
| [Conventions d’actions de routage de pages](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Ajouter un modèle de route aux pages d’un dossier et à une page unique. |
| [Conventions d’actions de modèle de page](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtre, expression lambda ou fabrique de filtres)</li></ul> | Ajouter un en-tête dans les pages d’un dossier, ajouter un en-tête dans une page unique et configurer une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) pour ajouter un en-tête dans les pages d’une application. |

Razor Les conventions de pages sont ajoutées et configurées à l’aide de la <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> méthode d’extension to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> sur la collection de services dans la `Startup` classe. Les exemples de convention suivants sont expliqués plus loin dans cette rubrique :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordre de routage

Les itinéraires spécifient un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pour le traitement (correspondance d’itinéraires).

| JSON            | Comportement |
| :--------------: | -------- |
| -1               | L’itinéraire est traité avant le traitement des autres itinéraires. |
| 0                | L’ordre n’est pas spécifié (valeur par défaut). Si vous n’affectez pas `Order` ( `Order = null` ), l’itinéraire est défini par défaut `Order` sur 0 (zéro) pour le traitement. |
| 1, 2, &hellip; n | Spécifie l’ordre de traitement des itinéraires. |

Le traitement des itinéraires est établi par Convention :

* Les itinéraires sont traités dans l’ordre séquentiel (-1, 0, 1, 2, &hellip; n).
* Lorsque les itinéraires ont le même `Order` , l’itinéraire le plus spécifique est mis en correspondance en premier, suivi d’itinéraires moins spécifiques.
* Lorsque des itinéraires avec le même `Order` nombre de paramètres correspondent à une URL de demande, les itinéraires sont traités dans l’ordre dans lequel ils sont ajoutés au <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Si possible, évitez de dépendre d’un ordre de traitement des itinéraires établi. En règle générale, le routage sélectionne l’itinéraire correct avec la correspondance d’URL. Si vous devez définir des `Order` Propriétés de routage pour acheminer les demandes correctement, le schéma de routage de l’application est sans doute confus pour les clients et fragiles à gérer. Cherchez à simplifier le schéma de routage de l’application. L’exemple d’application requiert un ordre de traitement des itinéraires explicites pour illustrer plusieurs scénarios de routage à l’aide d’une seule application. Toutefois, vous devez tenter d’éviter d’avoir à définir l’itinéraire `Order` dans les applications de production.

Razor Le routage des pages et le routage du contrôleur MVC partagent une implémentation. Des informations sur l’ordre des itinéraires dans les rubriques MVC sont disponibles dans [routage vers actions du contrôleur : classement des itinéraires des attributs](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Conventions de modèle

Ajoutez un délégué pour <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pour ajouter des [conventions de modèle](xref:mvc/controllers/application-model#conventions) qui s’appliquent aux Razor pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Ajouter une convention de modèle de routage à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle de routage de page.

L’exemple d’application ajoute un modèle de routage `{globalTemplate?}` à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `1`. Cela garantit le comportement de correspondance d’itinéraire suivant dans l’exemple d’application :

* Un modèle de routage pour `TheContactPage/{text?}` est ajouté plus loin dans la rubrique. L’itinéraire de la page de contact a un ordre par défaut `null` ( `Order = 0` ), de sorte qu’il corresponde avant le `{globalTemplate?}` modèle de routage.
* Un `{aboutTemplate?}` modèle de routage est ajouté plus loin dans la rubrique. Le modèle `{aboutTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand la page About est demandée sur `/About/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 1`) et non `RouteData.Values["aboutTemplate"]` (`Order = 2`) en raison de la définition de la propriété `Order`.
* Un `{otherPagesTemplate?}` modèle de routage est ajouté plus loin dans la rubrique. Le modèle `{otherPagesTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand une page du dossier *pages/OtherPages* est demandée avec un paramètre d’itinéraire (par exemple, `/OtherPages/Page1/RouteDataValue` ), « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Razor Les options de pages, telles que l’ajout <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , sont ajoutées lorsque MVC est ajouté à la collection de services dans `Startup.ConfigureServices` . Pour obtenir un exemple, consultez [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue` et examinez le résultat :

![La page About est demandée avec un segment de routage pour GlobalRouteValue. La page affichée montre que la valeur des données de routage est capturée dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Ajouter une convention de modèle d’application à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle d’application de la page.

Pour illustrer cette convention et bien d’autres, plus loin dans cette rubrique, l’exemple d’application inclut une classe `AddHeaderAttribute`. Le constructeur de classe accepte une chaîne `name` et un tableau de chaînes `values`. Ces valeurs sont utilisées dans sa méthode `OnResultExecuting` pour définir un en-tête de réponse. La classe complète est affichée dans la section [Conventions d’actions de modèle de page](#page-model-action-conventions), plus loin dans cette rubrique.

L’exemple d’application utilise la classe `AddHeaderAttribute` pour ajouter un en-tête, `GlobalHeader`, à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que GlobalHeader a été ajouté.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Ajouter une convention de modèle de gestionnaire à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle de gestionnaire de page.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Conventions d’actions de routage de pages

Le fournisseur de modèles de routage par défaut qui dérive de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> appelle des conventions qui sont conçues pour fournir des points d’extensibilité pour la configuration des itinéraires de page.

### <a name="folder-route-model-convention"></a>Convention de modèle de routage de dossier

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> objet qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pour toutes les pages du dossier spécifié.

L’exemple d’application utilise <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour ajouter un modèle de routage `{otherPagesTemplate?}` aux pages du dossier *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela permet de s’assurer que le modèle pour `{globalTemplate?}` (défini plus tôt dans la rubrique `1` ) est prioritaire pour la première position de valeur de données de routage lorsqu’une valeur de routage unique est fournie. Si une page du dossier *pages/OtherPages* est demandée avec une valeur de paramètre d’itinéraire (par exemple, `/OtherPages/Page1/RouteDataValue` ), « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` et examinez le résultat :

![La page Page1 du dossier OtherPages est demandée avec un segment de routage pour GlobalRouteValue et OtherPagesRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convention de modèle de routage de page

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> pour créer et ajouter un objet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pour la page avec le nom spécifié.

L’exemple d’application utilise `AddPageRouteModelConvention` pour ajouter un modèle de routage `{aboutTemplate?}` à la page About :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela permet de s’assurer que le modèle pour `{globalTemplate?}` (défini plus tôt dans la rubrique `1` ) est prioritaire pour la première position de valeur de données de routage lorsqu’une valeur de routage unique est fournie. Si la page about est demandée avec une valeur de paramètre d’itinéraire à `/About/RouteDataValue` , « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue/AboutRouteValue` et examinez le résultat :

![La page About est demandée avec des segments de routage pour GlobalRouteValue et AboutRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Utiliser un transformateur de paramètre pour personnaliser les itinéraires de page

Les itinéraires de page générés par ASP.NET Core peuvent être personnalisés à l’aide d’un transformateur de paramètres. Un transformateur de paramètre implémente `IOutboundParameterTransformer` et transforme la valeur des paramètres. Par exemple, un transformateur de paramètre `SlugifyParameterTransformer` personnalisé transforme la valeur de la route `SubscriptionManagement` en `subscription-management`.

La `PageRouteTransformerConvention` Convention de modèle de routage de page applique un transformateur de paramètre aux segments de nom de dossier et de fichier des itinéraires de page générés automatiquement dans une application. Par exemple, le Razor fichier de pages sur */pages/SubscriptionManagement/viewall.cshtml* aurait son itinéraire réécrit de `/SubscriptionManagement/ViewAll` à `/subscription-management/view-all` .

`PageRouteTransformerConvention` convertit uniquement les segments générés automatiquement d’un itinéraire de page provenant du Razor dossier pages et du nom de fichier. Elle ne transforme pas les segments de routage ajoutés avec la `@page` directive. La Convention ne transforme pas non plus les itinéraires ajoutés par <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .

Le `PageRouteTransformerConvention` est inscrit en tant qu’option dans `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
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

## <a name="configure-a-page-route"></a>Configurer un routage de page

Utilisez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> pour configurer un itinéraire vers une page au niveau du chemin de page spécifié. Les liens générés qui pointent vers la page utilisent le routage que vous avez spécifié. `AddPageRoute` utilise `AddPageRouteModelConvention` pour établir le routage.

L’exemple d’application crée un routage vers `/TheContactPage` pour *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

La page Contact est également accessible sur `/Contact` via son routage par défaut.

Le routage personnalisé de l’exemple d’application vers la page Contact permet un segment de routage `text` facultatif (`{text?}`). Cette page inclut également ce segment facultatif dans sa directive `@page`, au cas où le visiteur accèderait à la page via le routage `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Notez que l’URL générée pour le lien **Contact** dans la page affichée reflète le routage mis à jour :

![Lien Contact de l’exemple d’application dans la barre de navigation](razor-pages-conventions/_static/contact-link.png)

![Le lien Contact dans la page HTML affichée indique que l’attribut href a la valeur ’/TheContactPage’](razor-pages-conventions/_static/contact-link-source.png)

Visitez la page Contact via son routage ordinaire, `/Contact`, ou via le routage personnalisé, `/TheContactPage`. Si vous indiquez un segment de routage `text` supplémentaire, la page affiche le segment HTML que vous fournissez :

![Exemple dans le navigateur Edge d’un segment de routage ‘text’ facultatif fourni pour ‘TextValue’ dans l’URL. La page affichée montre la valeur du segment ‘text’.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Conventions d’actions de modèle de page

Le fournisseur de modèles de page par défaut qui implémente <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> appelle des conventions conçues pour fournir des points d’extensibilité pour la configuration des modèles de page. Ces conventions sont utiles durant la génération et la modification de scénarios de découverte et de traitement de pages.

Pour les exemples de cette section, l’exemple d’application utilise une `AddHeaderAttribute` classe, qui est un <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , qui applique un en-tête de réponse :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

À l’aide de conventions, l’exemple montre comment appliquer l’attribut à toutes les pages d’un dossier et à une seule page.

**Convention de modèle d’application de dossier**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> objet qui appelle une action sur <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> les instances de toutes les pages situées dans le dossier spécifié.

L’exemple illustre l’utilisation de `AddFolderApplicationModelConvention` en ajoutant un en-tête, `OtherPagesHeader`, aux pages situées dans le dossier *OtherPages* de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page OtherPages/Page1 montrent que OtherPagesHeader a été ajouté.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convention de modèle d’application de page**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> pour créer et ajouter un objet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pour la page avec le nom spécifié.

L’exemple illustre l’utilisation de `AddPageApplicationModelConvention` en ajoutant un en-tête, `AboutHeader`, à la page About :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que AboutHeader a été ajouté.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurer un filtre**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configure le filtre spécifié à appliquer. Vous pouvez implémenter une classe de filtre. Toutefois, l’exemple d’application montre comment implémenter un filtre dans une expression lambda, laquelle est implémentée en arrière-plan en tant que fabrique qui retourne un filtre :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Le modèle d’application de page est utilisé pour vérifier le chemin relatif des segments qui mènent à la page Page2 dans le dossier *OtherPages*. Si la condition est satisfaite, un en-tête est ajouté. Sinon, `EmptyFilter` est appliqué.

`EmptyFilter` est un [filtre d’action](xref:mvc/controllers/filters#action-filters). Étant donné que les filtres d’action sont ignorés par Razor les pages, le n' `EmptyFilter` a aucun effet comme prévu si le chemin d’accès ne contient pas `OtherPages/Page2` .

Demandez la page Page2 de l’exemple sur `localhost:5000/OtherPages/Page2` et examinez les en-têtes pour voir le résultat :

![OtherPagesPage2Header est ajouté à la réponse pour Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurer une fabrique de filtres**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configure la fabrique spécifiée pour appliquer des [filtres](xref:mvc/controllers/filters) à toutes les Razor pages.

L’exemple d’application illustre l’utilisation d’une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) en ajoutant un en-tête, `FilterFactoryHeader`, et deux valeurs aux pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que deux en-têtes FilterFactoryHeader ont été ajoutés.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtres MVC et filtre de page (IPageFilter)

Les [filtres d’action](xref:mvc/controllers/filters#action-filters) MVC sont ignorés par les Razor pages, car les Razor pages utilisent des méthodes de gestionnaire. Vous pouvez utiliser d’autres types de filtre MVC : [Autorisation](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) et [Résultat](xref:mvc/controllers/filters#result-filters). Pour plus d’informations, consultez la rubrique [Filtres](xref:mvc/controllers/filters).

Le filtre de page ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) est un filtre qui s’applique aux Razor pages. Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Découvrez comment utiliser [les conventions du fournisseur de modèles d’application et](xref:mvc/controllers/application-model#conventions) de routage de page pour contrôler le routage, la découverte et le traitement des pages dans les Razor applications de pages.

Quand vous devez configurer des routages de pages personnalisés pour des pages individuelles, configurez le routage vers les pages avec la convention [AddPageRoute](#configure-a-page-route) décrite plus loin dans cette rubrique.

Pour spécifier un itinéraire de page, ajouter des segments de routage ou ajouter des paramètres à un itinéraire, utilisez la directive de la page `@page` . Pour plus d’informations, consultez [itinéraires personnalisés](xref:razor-pages/index#custom-routes).

Il existe des mots réservés qui ne peuvent pas être utilisés en tant que segments de routage ou noms de paramètres. Pour plus d’informations, consultez [routage : noms de routage réservés](xref:fundamentals/routing#reserved-routing-names).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

| Scénario | L’exemple montre... |
| -------- | --------------------------- |
| [Conventions de modèle](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Ajouter un modèle de route et un en-tête aux pages d’une application. |
| [Conventions d’actions de routage de pages](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Ajouter un modèle de route aux pages d’un dossier et à une page unique. |
| [Conventions d’actions de modèle de page](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtre, expression lambda ou fabrique de filtres)</li></ul> | Ajouter un en-tête dans les pages d’un dossier, ajouter un en-tête dans une page unique et configurer une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) pour ajouter un en-tête dans les pages d’une application. |

Razor Les conventions de pages sont ajoutées et configurées à l’aide de la <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> méthode d’extension to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> sur la collection de services dans la `Startup` classe. Les exemples de convention suivants sont expliqués plus loin dans cette rubrique :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordre de routage

Les itinéraires spécifient un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pour le traitement (correspondance d’itinéraires).

| JSON            | Comportement |
| :--------------: | -------- |
| -1               | L’itinéraire est traité avant le traitement des autres itinéraires. |
| 0                | L’ordre n’est pas spécifié (valeur par défaut). Si vous n’affectez pas `Order` ( `Order = null` ), l’itinéraire est défini par défaut `Order` sur 0 (zéro) pour le traitement. |
| 1, 2, &hellip; n | Spécifie l’ordre de traitement des itinéraires. |

Le traitement des itinéraires est établi par Convention :

* Les itinéraires sont traités dans l’ordre séquentiel (-1, 0, 1, 2, &hellip; n).
* Lorsque les itinéraires ont le même `Order` , l’itinéraire le plus spécifique est mis en correspondance en premier, suivi d’itinéraires moins spécifiques.
* Lorsque des itinéraires avec le même `Order` nombre de paramètres correspondent à une URL de demande, les itinéraires sont traités dans l’ordre dans lequel ils sont ajoutés au <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Si possible, évitez de dépendre d’un ordre de traitement des itinéraires établi. En règle générale, le routage sélectionne l’itinéraire correct avec la correspondance d’URL. Si vous devez définir des `Order` Propriétés de routage pour acheminer les demandes correctement, le schéma de routage de l’application est sans doute confus pour les clients et fragiles à gérer. Cherchez à simplifier le schéma de routage de l’application. L’exemple d’application requiert un ordre de traitement des itinéraires explicites pour illustrer plusieurs scénarios de routage à l’aide d’une seule application. Toutefois, vous devez tenter d’éviter d’avoir à définir l’itinéraire `Order` dans les applications de production.

Razor Le routage des pages et le routage du contrôleur MVC partagent une implémentation. Des informations sur l’ordre des itinéraires dans les rubriques MVC sont disponibles dans [routage vers actions du contrôleur : classement des itinéraires des attributs](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Conventions de modèle

Ajoutez un délégué pour <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pour ajouter des [conventions de modèle](xref:mvc/controllers/application-model#conventions) qui s’appliquent aux Razor pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Ajouter une convention de modèle de routage à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle de routage de page.

L’exemple d’application ajoute un modèle de routage `{globalTemplate?}` à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `1`. Cela garantit le comportement de correspondance d’itinéraire suivant dans l’exemple d’application :

* Un modèle de routage pour `TheContactPage/{text?}` est ajouté plus loin dans la rubrique. L’itinéraire de la page de contact a un ordre par défaut `null` ( `Order = 0` ), de sorte qu’il corresponde avant le `{globalTemplate?}` modèle de routage.
* Un `{aboutTemplate?}` modèle de routage est ajouté plus loin dans la rubrique. Le modèle `{aboutTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand la page About est demandée sur `/About/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 1`) et non `RouteData.Values["aboutTemplate"]` (`Order = 2`) en raison de la définition de la propriété `Order`.
* Un `{otherPagesTemplate?}` modèle de routage est ajouté plus loin dans la rubrique. Le modèle `{otherPagesTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand une page du dossier *pages/OtherPages* est demandée avec un paramètre d’itinéraire (par exemple, `/OtherPages/Page1/RouteDataValue` ), « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Razor Les options de pages, telles que l’ajout <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , sont ajoutées lorsque MVC est ajouté à la collection de services dans `Startup.ConfigureServices` . Pour obtenir un exemple, consultez [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue` et examinez le résultat :

![La page About est demandée avec un segment de routage pour GlobalRouteValue. La page affichée montre que la valeur des données de routage est capturée dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Ajouter une convention de modèle d’application à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle d’application de la page.

Pour illustrer cette convention et bien d’autres, plus loin dans cette rubrique, l’exemple d’application inclut une classe `AddHeaderAttribute`. Le constructeur de classe accepte une chaîne `name` et un tableau de chaînes `values`. Ces valeurs sont utilisées dans sa méthode `OnResultExecuting` pour définir un en-tête de réponse. La classe complète est affichée dans la section [Conventions d’actions de modèle de page](#page-model-action-conventions), plus loin dans cette rubrique.

L’exemple d’application utilise la classe `AddHeaderAttribute` pour ajouter un en-tête, `GlobalHeader`, à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que GlobalHeader a été ajouté.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Ajouter une convention de modèle de gestionnaire à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> à la collection d' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances appliquées pendant la construction du modèle de gestionnaire de page.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Conventions d’actions de routage de pages

Le fournisseur de modèles de routage par défaut qui dérive de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> appelle des conventions qui sont conçues pour fournir des points d’extensibilité pour la configuration des itinéraires de page.

### <a name="folder-route-model-convention"></a>Convention de modèle de routage de dossier

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> objet qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pour toutes les pages du dossier spécifié.

L’exemple d’application utilise <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour ajouter un modèle de routage `{otherPagesTemplate?}` aux pages du dossier *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela permet de s’assurer que le modèle pour `{globalTemplate?}` (défini plus tôt dans la rubrique `1` ) est prioritaire pour la première position de valeur de données de routage lorsqu’une valeur de routage unique est fournie. Si une page du dossier *pages/OtherPages* est demandée avec une valeur de paramètre d’itinéraire (par exemple, `/OtherPages/Page1/RouteDataValue` ), « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` et examinez le résultat :

![La page Page1 du dossier OtherPages est demandée avec un segment de routage pour GlobalRouteValue et OtherPagesRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convention de modèle de routage de page

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> pour créer et ajouter un objet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pour la page avec le nom spécifié.

L’exemple d’application utilise `AddPageRouteModelConvention` pour ajouter un modèle de routage `{aboutTemplate?}` à la page About :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela permet de s’assurer que le modèle pour `{globalTemplate?}` (défini plus tôt dans la rubrique `1` ) est prioritaire pour la première position de valeur de données de routage lorsqu’une valeur de routage unique est fournie. Si la page about est demandée avec une valeur de paramètre d’itinéraire à `/About/RouteDataValue` , « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` ( `Order = 1` ) et non `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) en raison de la définition de la `Order` propriété.

Dans la mesure du possible, ne définissez pas `Order` , ce qui donne `Order = 0` . Utilisez le routage pour sélectionner l’itinéraire correct.

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue/AboutRouteValue` et examinez le résultat :

![La page About est demandée avec des segments de routage pour GlobalRouteValue et AboutRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Configurer un routage de page

Utilisez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> pour configurer un itinéraire vers une page au niveau du chemin de page spécifié. Les liens générés qui pointent vers la page utilisent le routage que vous avez spécifié. `AddPageRoute` utilise `AddPageRouteModelConvention` pour établir le routage.

L’exemple d’application crée un routage vers `/TheContactPage` pour *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

La page Contact est également accessible sur `/Contact` via son routage par défaut.

Le routage personnalisé de l’exemple d’application vers la page Contact permet un segment de routage `text` facultatif (`{text?}`). Cette page inclut également ce segment facultatif dans sa directive `@page`, au cas où le visiteur accèderait à la page via le routage `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Notez que l’URL générée pour le lien **Contact** dans la page affichée reflète le routage mis à jour :

![Lien Contact de l’exemple d’application dans la barre de navigation](razor-pages-conventions/_static/contact-link.png)

![Le lien Contact dans la page HTML affichée indique que l’attribut href a la valeur ’/TheContactPage’](razor-pages-conventions/_static/contact-link-source.png)

Visitez la page Contact via son routage ordinaire, `/Contact`, ou via le routage personnalisé, `/TheContactPage`. Si vous indiquez un segment de routage `text` supplémentaire, la page affiche le segment HTML que vous fournissez :

![Exemple dans le navigateur Edge d’un segment de routage ‘text’ facultatif fourni pour ‘TextValue’ dans l’URL. La page affichée montre la valeur du segment ‘text’.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Conventions d’actions de modèle de page

Le fournisseur de modèles de page par défaut qui implémente <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> appelle des conventions conçues pour fournir des points d’extensibilité pour la configuration des modèles de page. Ces conventions sont utiles durant la génération et la modification de scénarios de découverte et de traitement de pages.

Pour les exemples de cette section, l’exemple d’application utilise une `AddHeaderAttribute` classe, qui est un <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , qui applique un en-tête de réponse :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

À l’aide de conventions, l’exemple montre comment appliquer l’attribut à toutes les pages d’un dossier et à une seule page.

**Convention de modèle d’application de dossier**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour créer et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> objet qui appelle une action sur <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> les instances de toutes les pages situées dans le dossier spécifié.

L’exemple illustre l’utilisation de `AddFolderApplicationModelConvention` en ajoutant un en-tête, `OtherPagesHeader`, aux pages situées dans le dossier *OtherPages* de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page OtherPages/Page1 montrent que OtherPagesHeader a été ajouté.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convention de modèle d’application de page**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> pour créer et ajouter un objet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> qui appelle une action sur le <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pour la page avec le nom spécifié.

L’exemple illustre l’utilisation de `AddPageApplicationModelConvention` en ajoutant un en-tête, `AboutHeader`, à la page About :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que AboutHeader a été ajouté.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurer un filtre**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configure le filtre spécifié à appliquer. Vous pouvez implémenter une classe de filtre. Toutefois, l’exemple d’application montre comment implémenter un filtre dans une expression lambda, laquelle est implémentée en arrière-plan en tant que fabrique qui retourne un filtre :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Le modèle d’application de page est utilisé pour vérifier le chemin relatif des segments qui mènent à la page Page2 dans le dossier *OtherPages*. Si la condition est satisfaite, un en-tête est ajouté. Sinon, `EmptyFilter` est appliqué.

`EmptyFilter` est un [filtre d’action](xref:mvc/controllers/filters#action-filters). Étant donné que les filtres d’action sont ignorés par Razor les pages, le n' `EmptyFilter` a aucun effet comme prévu si le chemin d’accès ne contient pas `OtherPages/Page2` .

Demandez la page Page2 de l’exemple sur `localhost:5000/OtherPages/Page2` et examinez les en-têtes pour voir le résultat :

![OtherPagesPage2Header est ajouté à la réponse pour Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurer une fabrique de filtres**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configure la fabrique spécifiée pour appliquer des [filtres](xref:mvc/controllers/filters) à toutes les Razor pages.

L’exemple d’application illustre l’utilisation d’une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) en ajoutant un en-tête, `FilterFactoryHeader`, et deux valeurs aux pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que deux en-têtes FilterFactoryHeader ont été ajoutés.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtres MVC et filtre de page (IPageFilter)

Les [filtres d’action](xref:mvc/controllers/filters#action-filters) MVC sont ignorés par les Razor pages, car les Razor pages utilisent des méthodes de gestionnaire. Vous pouvez utiliser d’autres types de filtre MVC : [Autorisation](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) et [Résultat](xref:mvc/controllers/filters#result-filters). Pour plus d’informations, consultez la rubrique [Filtres](xref:mvc/controllers/filters).

Le filtre de page ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) est un filtre qui s’applique aux Razor pages. Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
