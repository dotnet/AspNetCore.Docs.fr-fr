---
title: API JSON de base avec Route-to-code dans ASP.net Core
author: jamesnk
description: Découvrez comment utiliser Route-to-code les méthodes d’extension et JSON pour créer des API Web JSON légères.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556604"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>API JSON de base avec Route-to-code dans ASP.net Core

Par [James Newton-King](https://github.com/jamesnk)

ASP.NET Core prend en charge plusieurs façons de créer des API Web JSON :

* [ASP.net Core API Web](xref:web-api/index) fournit une infrastructure complète pour la création d’API. Un service est créé en héritant de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Certaines fonctionnalités fournies par l’infrastructure incluent la liaison de modèle, la validation, la négociation de contenu, la mise en forme d’entrée et de sortie, et OpenAPI.
* Route-to-code n’est pas une alternative au Framework pour ASP.NET Core API Web. Route-to-code connecte [ASP.net Core routage](xref:fundamentals/routing) directement à votre code. Votre code lit la demande et écrit la réponse. Route-to-code ne dispose pas de fonctionnalités avancées de l’API Web, mais il n’y a pas non plus de configuration requise pour l’utiliser.

Route-to-code est une bonne approche lors de la création d’API Web JSON de petite et de base.

## <a name="create-json-web-apis"></a>Créer des API Web JSON

ASP.NET Core fournit des méthodes d’assistance qui facilitent la création d’API Web JSON :

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> vérifie l' `Content-Type` en-tête pour un type de contenu JSON.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> lit JSON à partir de la demande et le désérialise vers le type spécifié.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> écrit la valeur spécifiée au format JSON dans le corps de la réponse et définit le type de contenu de la réponse sur `application/json` .

Les API JSON basées sur un itinéraire sont spécifiées dans *Startup.cs*. L’itinéraire et la logique de l’API sont configurés dans dans le cadre <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> du pipeline de requête d’une application.

### <a name="write-json-response"></a>Écrire une réponse JSON

Prenons le code suivant qui configure une API JSON pour une application :

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

Le code précédent :

* Ajoute un point de terminaison d’API HTTP d’extraction avec `/hello/{name:alpha}` comme modèle de routage.
* Lorsque l’itinéraire est mis en correspondance, l’API lit la `name` valeur de route à partir de la demande.
* Écrit un type anonyme en tant que réponse JSON avec `WriteAsJsonAsync` .

### <a name="read-json-request"></a>Lire la requête JSON

`HasJsonContentType` et `ReadFromJsonAsync` peuvent être utilisés pour désérialiser une réponse JSON dans une API JSON basée sur l’itinéraire :

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

Le code précédent :

* Ajoute un point de terminaison d’API HTTP après `/weather` comme modèle de routage.
* Lorsque l’itinéraire est mis en correspondance, `HasJsonContentType` valide le type de contenu de la demande. Un type de contenu non JSON retourne un code d’État 415.
* Si le type de contenu est JSON, le contenu de la demande est désérialisé par `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>Configurer la sérialisation JSON

Il existe deux façons de personnaliser la sérialisation JSON :

* Les options de sérialisation par défaut peuvent être configurées avec <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> dans la `Startup.ConfigureServices` méthode.
* `WriteAsJsonAsync` et `ReadFromJsonAsync` ont des surcharges qui acceptent un <xref:System.Text.Json.JsonSerializerOptions> objet. Cet objet d’options remplace les options par défaut.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Route-to-code prend en charge l’authentification et l’autorisation. Les attributs, tels que `[Authorize]` et `[AllowAnonymous]` , ne peuvent pas être placés sur des points de terminaison qui mappent à un délégué de requête. Au lieu de cela, les métadonnées d’autorisation sont ajoutées à l’aide des <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> méthodes d’extension et.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>Injection de dépendances

L' [injection de dépendance (di)](xref:fundamentals/dependency-injection) à l’aide d’un constructeur n’est pas possible avec Route-to-code . L’API Web crée un contrôleur pour vous avec les services injectés dans le constructeur. Un type n’est pas créé lors de l’exécution d’un point de terminaison, les services doivent donc être résolus manuellement.

Les API basées sur l’itinéraire peuvent utiliser <xref:System.IServiceProvider> pour résoudre les services :

* Les services de durée de vie temporaires et délimitées, tels que `DbContext` , doivent être résolus à partir de [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) dans le délégué de demande d’un point de terminaison.
* Les services de durée de vie Singleton, tels que `ILogger` , peuvent être résolus à partir de [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider). Les services peuvent être résolus en dehors des délégués de demande et partagés entre les points de terminaison.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

Les API qui utilisent l’injection de compte doivent largement envisager d’utiliser un type d’application ASP.NET Core qui prend en charge DI. Par exemple, ASP.NET Core API Web. L’injection de service à l’aide du constructeur d’un contrôleur est plus facile que de résoudre manuellement les services.

## <a name="api-project-structure"></a>Structure de projet d’API

Les API basées sur l’itinéraire n’ont pas besoin d’être situées dans *Startup.cs*. Les API peuvent être placées dans d’autres fichiers et mappées au démarrage avec `UseEndpoints` . Cette approche réduit la taille du fichier de configuration de démarrage.

Considérons la classe statique suivante `UserApi` qui définit une `Map` méthode. La méthode mappe les API basées sur l’itinéraire.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

Dans la `Startup.Configure` méthode, la `Map` méthode et les méthodes statiques de la classe sont appelées dans `UseEndpoints` :

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Fonctionnalités notables manquantes par rapport à l’API Web

Route-to-code est conçu pour les API JSON de base. Il ne prend pas en charge la plupart des fonctionnalités avancées fournies par ASP.NET Core API Web.

Les fonctionnalités non fournies par Route-to-code sont les suivantes :

* Liaison de données
* Validation du modèle
* OpenAPI/Swagger
* Négociation de contenu
* Injection de dépendances de constructeur
* `ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))

Envisagez d’utiliser [ASP.net Core API Web](xref:web-api/index) pour créer une API si elle requiert certaines des fonctionnalités de la liste précédente.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
