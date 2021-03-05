---
title: Mettre en forme les données des réponses dans l’API web ASP.NET Core
author: rick-anderson
description: Découvrez comment mettre en forme les données des réponses dans l’API web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 5fa7bc15bac2609c89fea54f8a788aaf9f5ad055
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119030"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Mettre en forme les données des réponses dans l’API web ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC prend en charge la mise en forme des données de réponse. Les données de réponse peuvent être formatées à l’aide de formats spécifiques ou en réponse au format demandé par le client.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Résultats des actions spécifiques au format

Certains types de résultats d’action sont spécifiques à un format particulier, comme <xref:Microsoft.AspNetCore.Mvc.JsonResult> et <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Les actions peuvent retourner des résultats mis en forme dans un format particulier, indépendamment des préférences du client. Par exemple, le retour de `JsonResult` retourne des données au format JSON. Le retour de `ContentResult` ou une chaîne retourne des données de chaîne au format texte brut.

Une action n’est pas requise pour retourner un type spécifique. ASP.NET Core prend en charge toute valeur de retour d’objet.  Les résultats des actions qui retournent des objets qui ne sont pas des <xref:Microsoft.AspNetCore.Mvc.IActionResult> types sont sérialisés à l’aide de l' <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implémentation appropriée. Pour plus d’informations, consultez <xref:web-api/action-return-types>.

La méthode d’assistance intégrée retourne des <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> données au format JSON : [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

L’exemple de téléchargement retourne la liste des auteurs. À l’aide des outils de développement du navigateur F12 ou du [poster](https://www.getpostman.com/tools) avec le code précédent :

* L’en-tête de réponse contenant **Content-type :** `application/json; charset=utf-8` est affiché.
* Les en-têtes de demande s’affichent. Par exemple, l' `Accept` en-tête. L' `Accept` en-tête est ignoré par le code précédent.

Pour retourner des données mises en forme en texte brut, utilisez <xref:Microsoft.AspNetCore.Mvc.ContentResult> et le helper <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

Dans le code précédent, le `Content-Type` retourné est `text/plain` . Retour d’une chaîne `Content-Type` `text/plain` :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Pour les actions avec plusieurs types de retour, retournez `IActionResult` . Par exemple, le retour de codes d’état HTTP différents en fonction du résultat des opérations effectuées.

## <a name="content-negotiation"></a>Négociation de contenu

La négociation de contenu se produit lorsque le client spécifie un [en-tête Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Le format par défaut utilisé par ASP.NET Core est [JSON](https://json.org/). La négociation de contenu est :

* Implémenté par <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .
* Intégré aux résultats d’action spécifiques au code d’État retournés par les méthodes d’assistance. Les méthodes d’assistance des résultats d’action sont basées sur `ObjectResult` .

Quand un type de modèle est retourné, le type de retour est `ObjectResult` .

La méthode d’action suivante utilise les méthodes helper `Ok` et `NotFound` :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Par défaut, ASP.NET Core prend en charge `application/json` `text/json` les `text/plain` types de média, et. Les outils tels que [Fiddler](https://www.telerik.com/fiddler) ou [postal](https://www.getpostman.com/tools) peuvent définir l' `Accept` en-tête de demande pour spécifier le format de retour. Lorsque l' `Accept` en-tête contient un type pris en charge par le serveur, ce type est retourné. La section suivante montre comment ajouter des formateurs supplémentaires.

Les actions de contrôleur peuvent retourner des POCO (Plain Old CLR Objects). Lorsqu’un POCO est retourné, le runtime crée automatiquement un `ObjectResult` objet qui encapsule l’objet. Le client obtient l’objet sérialisé mis en forme. Si l’objet retourné est `null` , une `204 No Content` réponse est retournée.

Retour d’un type d’objet :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

Dans le code précédent, une demande d’alias d’auteur valide retourne une `200 OK` réponse avec les données de l’auteur. Une demande d’alias non valide retourne une `204 No Content` réponse.

### <a name="the-accept-header"></a>En-tête Accept

La *négociation* de contenu a lieu lorsqu’un `Accept` en-tête apparaît dans la demande. Quand une demande contient un en-tête Accept, ASP.NET Core :

* Énumère les types de médias dans l’en-tête Accept dans l’ordre de préférence.
* Tente de trouver un formateur capable de produire une réponse dans l’un des formats spécifiés.

Si aucun formateur trouvé pouvant satisfaire la demande du client, ASP.NET Core :

* Retourne `406 Not Acceptable` si <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> a la valeur `true` , ou-
* Tente de trouver le premier formateur qui peut produire une réponse.

Si aucun formateur n’est configuré pour le format demandé, le premier formateur qui peut mettre en forme l’objet est utilisé. Si aucun `Accept` en-tête n’apparaît dans la requête :

* Le premier formateur qui peut gérer l’objet est utilisé pour sérialiser la réponse.
* Aucune négociation n’a lieu. Le serveur détermine le format à retourner.

Si l’en-tête Accept contient `*/*` , l’en-tête est ignoré, sauf si a la valeur `RespectBrowserAcceptHeader` true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

### <a name="browsers-and-content-negotiation"></a>Navigateurs et négociation de contenu

Contrairement aux clients d’API typiques, les navigateurs Web fournissent des `Accept` en-têtes. Le navigateur Web spécifie de nombreux formats, y compris des caractères génériques. Par défaut, lorsque le Framework détecte que la demande provient d’un navigateur :

* L' `Accept` en-tête est ignoré.
* Le contenu est renvoyé au format JSON, sauf s’il a été configuré autrement.

Cela offre une expérience plus cohérente entre les navigateurs lors de l’utilisation des API.

Pour configurer une application pour honorer les en-têtes Accept du navigateur, définissez <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> sur `true` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Configurer les formateurs

Les applications qui doivent prendre en charge des formats supplémentaires peuvent ajouter les packages NuGet appropriés et configurer la prise en charge. Il existe des formateurs distincts pour les entrées et pour les sorties. Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding). Les formateurs de sortie sont utilisés pour mettre en forme les réponses. Pour plus d’informations sur la création d’un formateur personnalisé, consultez [formateurs personnalisés](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Ajouter la prise en charge du format XML

Les formateurs XML implémentés à l’aide <xref:System.Xml.Serialization.XmlSerializer> de sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Le code précédent sérialise les résultats à l’aide de `XmlSerializer` .

Lorsque vous utilisez le code précédent, les méthodes de contrôleur retournent le format approprié en fonction de l' `Accept` en-tête de la demande.

### <a name="configure-systemtextjson-based-formatters"></a>Configurer des `System.Text.Json` formateurs basés sur

Les fonctionnalités des `System.Text.Json` formateurs basés sur peuvent être configurées à l’aide de <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> . La mise en forme par défaut est la casse mixte. Le code en surbrillance suivant définit la mise en forme casse Pascal :

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

La méthode d’action suivante appelle [ControllerBase. Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) pour créer une <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> réponse :

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

Avec le code précédent :

  * `https://localhost:5001/WeatherForecast/temperature` retourne casse Pascal.
  * `https://localhost:5001/WeatherForecast/error` retourne la casse mixte. La réponse d’erreur est toujours la casse mixte, même lorsque l’application définit le format sur casse Pascal. `ProblemDetails` suit [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), qui spécifie en minuscules

Le code suivant définit casse Pascal et ajoute un convertisseur personnalisé :

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide de `JsonResult` . Par exemple :

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Ajouter la prise en charge du format JSON basé sur Newtonsoft.Json

Avant ASP.NET Core 3,0, les formateurs JSON utilisés par défaut ont été implémentés à l’aide du `Newtonsoft.Json` Package. Dans ASP.NET Core 3.0 ou version ultérieure, les formateurs JSON par défaut sont basés sur `System.Text.Json`. La prise en charge des `Newtonsoft.Json` formateurs et des fonctionnalités de base est disponible en installant le [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) package NuGet et en le configurant dans `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Dans le code précédent, l’appel à `AddNewtonsoftJson` configure les fonctionnalités suivantes de l’API Web, du MVC et des Razor pages à utiliser `Newtonsoft.Json` :

* Formateurs d’entrée et de sortie qui lisent et écrivent du code JSON
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [JSON Patch](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [TempData](xref:fundamentals/app-state#tempdata)

Certaines fonctionnalités peuvent ne pas fonctionner correctement avec `System.Text.Json` les formateurs basés sur et nécessitent une référence aux `Newtonsoft.Json` formateurs basés sur. Continuer à utiliser les `Newtonsoft.Json` formateurs basés sur, si l’application :

* Utilise des `Newtonsoft.Json` attributs. Par exemple, `[JsonProperty]` ou `[JsonIgnore]`.
* Personnalise les paramètres de sérialisation.
* S’appuie sur les fonctionnalités `Newtonsoft.Json` fournies par.
* Configure `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Avant ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepte une instance de `JsonSerializerSettings` spécifique à `Newtonsoft.Json`.
* Génère une documentation [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).

Les fonctionnalités des `Newtonsoft.Json` formateurs basés sur peuvent être configurées à l’aide de `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide de `JsonResult` . Par exemple :

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Ajouter la prise en charge du format XML

La mise en forme XML requiert le package NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .

Les formateurs XML implémentés à l’aide <xref:System.Xml.Serialization.XmlSerializer> de sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Le code précédent sérialise les résultats à l’aide de `XmlSerializer` .

Lorsque vous utilisez le code précédent, les méthodes de contrôleur doivent retourner le format approprié en fonction de l' `Accept` en-tête de la requête.

::: moniker-end

### <a name="specify-a-format"></a>Spécifier un format

Pour restreindre les formats de réponse, appliquez le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre. Comme la plupart des [filtres](xref:mvc/controllers/filters), `[Produces]` peut être appliqué au niveau de l’action, du contrôleur ou de l’étendue globale :

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre précédent :

* Force toutes les actions du contrôleur à retourner des réponses au format JSON.
* Si d’autres formateurs sont configurés et que le client spécifie un format différent, JSON est retourné.

Pour plus d’informations, consultez [filtres](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formateurs de cas spéciaux

Certains cas spéciaux sont implémentés avec des formateurs intégrés. Par défaut, `string` les types de retour sont mis en forme en tant que *texte/plain* (*texte/html* si demandé via l' `Accept` en-tête). Ce comportement peut être supprimé en supprimant le <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> . Les formateurs sont supprimés de la `ConfigureServices` méthode. Les actions qui ont un type de retour d’objet de modèle retournent `204 No Content` lors du retour de `null` . Ce comportement peut être supprimé en supprimant le <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> . Le code suivant supprime `StringOutputFormatter` et `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Sans le `StringOutputFormatter` , le formateur JSON intégré met en forme les `string` types de retour. Si le formateur JSON intégré est supprimé et qu’un formateur XML est disponible, le formateur XML met en forme les `string` types de retour. Sinon, les `string` types de retour retournent `406 Not Acceptable` .

Sans `HttpNoContentOutputFormatter`, les objets null sont mis en forme avec le formateur configuré. Par exemple :

* Le formateur JSON retourne une réponse avec un corps de `null` .
* Le formateur XML retourne un élément XML vide avec l’ensemble d’attributs `xsi:nil="true"` .

## <a name="response-format-url-mappings"></a>Mappages d’URL de format de réponse

Les clients peuvent demander un format particulier dans le cadre de l’URL, par exemple :

* Dans la chaîne de requête ou dans une partie du chemin d’accès.
* En utilisant une extension de fichier spécifique au format, par exemple. XML ou. JSON.

Le mappage du chemin de la requête doit être spécifié dans la route utilisée par l’API. Par exemple :

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

L’itinéraire précédent permet de spécifier le format demandé en tant qu’extension de fichier facultative. L' [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribut vérifie l’existence de la valeur de format dans le `RouteData` et mappe le format de réponse au formateur approprié lorsque la réponse est créée.

|           Routage        |             Formateur              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Le formateur de sortie par défaut    |
| `/api/products/5.json` | Le formateur JSON (s’il est configuré) |
| `/api/products/5.xml`  | Le formateur XML (s’il est configuré)  |
