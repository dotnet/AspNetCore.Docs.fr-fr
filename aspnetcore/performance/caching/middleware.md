---
title: Intergiciel de mise en cache des réponses dans ASP.NET Core
author: rick-anderson
description: Découvrez comment configurer et utiliser le middleware de mise en cache des réponses dans ASP.NET Core.
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
uid: performance/caching/middleware
ms.openlocfilehash: 0f1f5dfcb9595270a9659a02141f7d1eba5c44ef
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587695"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Intergiciel de mise en cache des réponses dans ASP.NET Core

Par [John Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

Cet article explique comment configurer l’intergiciel (middleware) de mise en cache des réponses dans une application ASP.NET Core. L’intergiciel détermine quand les réponses sont pouvant être mises en cache, stocke les réponses et sert les réponses du cache. Pour obtenir une présentation de la mise en cache HTTP et de l' [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribut, consultez [mise en cache des réponses](xref:performance/caching/response).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Configuration

L’intergiciel (middleware) de mise en cache des réponses est implicitement disponible pour les applications ASP.NET Core via le Framework partagé.

Dans `Startup.ConfigureServices` , ajoutez l’intergiciel de mise en cache des réponses à la collection de services :

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Configurez l’application pour utiliser l’intergiciel avec la <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> méthode d’extension, qui ajoute l’intergiciel au pipeline de traitement des demandes dans `Startup.Configure` :

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A> doit être appelé avant <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> lors de l’utilisation de l' [intergiciel (middleware) cors](xref:security/cors).

L’exemple d’application ajoute des en-têtes pour contrôler la mise en cache lors des requêtes suivantes :

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): met en cache les réponses pouvant être mises en cache pendant 10 secondes.
* [Variation](https://tools.ietf.org/html/rfc7231#section-7.1.4): configure l’intergiciel (middleware) pour traiter une réponse mise en cache uniquement si l’en-tête [accepter-encodage](https://tools.ietf.org/html/rfc7231#section-5.3.4) des demandes suivantes correspond à celui de la demande d’origine.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Les en-têtes précédents ne sont pas écrits dans la réponse et sont remplacés lorsqu’un contrôleur, une action ou une Razor page :

* A un attribut [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . Cela s’applique même si une propriété n’est pas définie. Par exemple, si vous omettez la propriété [VaryByHeader](./response.md#vary) , l’en-tête correspondant sera supprimé de la réponse.

L’intergiciel (middleware) de mise en cache des réponses met uniquement en cache les réponses du serveur qui génèrent un code d’état 200 (OK). Toutes les autres réponses, y compris les [pages d’erreur](xref:fundamentals/error-handling), sont ignorées par l’intergiciel (middleware).

> [!WARNING]
> Les réponses contenant du contenu pour les clients authentifiés doivent être marquées comme ne pouvant pas être mises en cache pour empêcher l’intergiciel de stocker et desservir ces réponses. Pour plus d’informations sur la façon dont l’intergiciel détermine si une réponse peut être mise en cache, consultez [conditions de mise en cache](#conditions-for-caching) .

## <a name="options"></a>Options

Les options de mise en cache des réponses sont indiquées dans le tableau suivant.

| Option | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Plus grande taille pouvant être mise en cache pour le corps de la réponse en octets. La valeur par défaut est `64 * 1024 * 1024` (64 Mo). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Taille limite de l’intergiciel (middleware) du cache de réponse en octets. La valeur par défaut est `100 * 1024 * 1024` (100 Mo). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Détermine si les réponses sont mises en cache sur les chemins d’accès sensibles à la casse. La valeur par défaut est `false`. |

L’exemple suivant configure l’intergiciel (middleware) sur :

* Met en cache les réponses dont la taille du corps est inférieure ou égale à 1 024 octets.
* Stockez les réponses en respectant les chemins sensibles à la casse. Par exemple, `/page1` et `/Page1` sont stockés séparément.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Lors de l’utilisation de contrôleurs d’API Web ou de modèles d’API MVC/Web Razor , l' [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribut spécifie les paramètres nécessaires à la définition des en-têtes appropriés pour la mise en cache des réponses. Le seul paramètre de l' `[ResponseCache]` attribut qui requiert strictement l’intergiciel est <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , qui ne correspond pas à un en-tête HTTP réel. Pour plus d’informations, consultez <xref:performance/caching/response#responsecache-attribute>.

Quand vous n’utilisez pas l' `[ResponseCache]` attribut, la mise en cache des réponses peut être modifiée avec `VaryByQueryKeys` . Utilisez le <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directement à partir du [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

L’utilisation d’une valeur unique égale à `*` dans fait `VaryByQueryKeys` varier le cache par tous les paramètres de requête de demande.

## <a name="http-headers-used-by-response-caching-middleware"></a>En-têtes HTTP utilisés par l’intergiciel (middleware) de mise en cache des réponses

Le tableau suivant fournit des informations sur les en-têtes HTTP qui affectent la mise en cache des réponses.

| En-tête | Détails |
| ------ | ------- |
| `Authorization` | La réponse n’est pas mise en cache si l’en-tête existe. |
| `Cache-Control` | L’intergiciel (middleware) prend uniquement en compte les réponses de mise en cache marquées avec la `public` directive de cache. Contrôlez la mise en cache avec les paramètres suivants :<ul><li>âge maximal</li><li>Max-&#8224; obsolète</li><li>min-frais</li><li>must-revalidate</li><li>no-cache</li><li>non-Store</li><li>uniquement-en cas de mise en cache</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-revalider&#8225;</li></ul>&#8224;si aucune limite n’est spécifiée à `max-stale` , l’intergiciel n’entreprend aucune action.<br>&#8225;`proxy-revalidate` a le même effet que `must-revalidate` .<br><br>Pour plus d’informations, consultez [RFC 7231 : Request Cache-Control directives](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | Un `Pragma: no-cache` en-tête dans la demande produit le même effet que `Cache-Control: no-cache` . Cet en-tête est substitué par les directives pertinentes dans l' `Cache-Control` en-tête, le cas échéant. Pris en compte pour la compatibilité descendante avec HTTP/1.0. |
| `Set-Cookie` | La réponse n’est pas mise en cache si l’en-tête existe. Tout intergiciel dans le pipeline de traitement des demandes qui définit un ou plusieurs cookie s empêche l’intergiciel de mise en cache de la réponse (par exemple, le [ cookie fournisseur TempData basé sur](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | L' `Vary` en-tête est utilisé pour faire varier la réponse mise en cache par un autre en-tête. Par exemple, mettez en cache les réponses par encodage en incluant l' `Vary: Accept-Encoding` en-tête, qui met en cache les réponses pour les demandes avec en-têtes `Accept-Encoding: gzip` et `Accept-Encoding: text/plain` séparément. Une réponse avec une valeur d’en-tête de `*` n’est jamais stockée. |
| `Expires` | Une réponse considérée comme périmée par cet en-tête n’est pas stockée ou récupérée, sauf si elle est remplacée par d’autres `Cache-Control` en-têtes. |
| `If-None-Match` | La réponse complète est traitée à partir du cache si la valeur n’est pas `*` et que la `ETag` de la réponse ne correspond à aucune des valeurs fournies. Dans le cas contraire, une réponse 304 (non modifiée) est traitée. |
| `If-Modified-Since` | Si l' `If-None-Match` en-tête n’est pas présent, une réponse complète est traitée à partir du cache si la date de réponse mise en cache est plus récente que la valeur fournie. Dans le cas contraire, une réponse *304-non modifiée* est traitée. |
| `Date` | En cas de service à partir du cache, l' `Date` en-tête est défini par l’intergiciel (middleware) s’il n’a pas été fourni dans la réponse d’origine. |
| `Content-Length` | En cas de service à partir du cache, l' `Content-Length` en-tête est défini par l’intergiciel (middleware) s’il n’a pas été fourni dans la réponse d’origine. |
| `Age` | L' `Age` en-tête envoyé dans la réponse d’origine est ignoré. L’intergiciel (middleware) calcule une nouvelle valeur lors de la fourniture d’une réponse mise en cache. |

## <a name="caching-respects-request-cache-control-directives"></a>Mise en cache respecte les directives de Cache-Control de demande

L’intergiciel respecte les règles de la [spécification de mise en cache HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Les règles requièrent qu’un cache honore un `Cache-Control` en-tête valide envoyé par le client. Dans le cadre de la spécification, un client peut faire des demandes avec une `no-cache` valeur d’en-tête et forcer le serveur à générer une nouvelle réponse pour chaque demande. Actuellement, il n’y a pas de contrôle du développeur sur ce comportement de mise en cache lors de l’utilisation de l’intergiciel (middleware), car l’intergiciel respecte la spécification officielle de mise en cache.

Pour plus de contrôle sur le comportement de mise en cache, explorez les autres fonctionnalités de mise en cache de ASP.NET Core. Consultez les rubriques suivantes :

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Dépannage

Si le comportement de mise en cache n’est pas le même que prévu, vérifiez que les réponses sont mises en cache et peuvent être servies à partir du cache. Examinez les en-têtes entrants de la demande et les en-têtes sortants de la réponse. Activez la [journalisation](xref:fundamentals/logging/index) pour faciliter le débogage.

Lors du test et du dépannage du comportement de mise en cache, un navigateur peut définir des en-têtes de demande qui affectent la mise en cache de façon indésirable. Par exemple, un navigateur peut définir l' `Cache-Control` en-tête sur `no-cache` ou lors de l' `max-age=0` actualisation d’une page. Les outils suivants peuvent définir explicitement des en-têtes de demande et sont préférés pour le test de mise en cache :

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Conditions de mise en cache

* La demande doit aboutir à une réponse du serveur avec un code d’état 200 (OK).
* La méthode de demande doit être obtenir ou HEAD.
* Dans `Startup.Configure` , l’intergiciel de mise en cache des réponses doit être placé avant l’intergiciel (middleware) qui requiert la mise en cache. Pour plus d’informations, consultez <xref:fundamentals/middleware/index>.
* L' `Authorization` en-tête ne doit pas être présent.
* `Cache-Control` les paramètres d’en-tête doivent être valides et la réponse doit être marquée `public` et non marquée `private` .
* L’en- `Pragma: no-cache` tête ne doit pas être présent si l' `Cache-Control` en-tête n’est pas présent, car l’en `Cache-Control` -tête remplace l' `Pragma` en-tête lorsqu’il est présent.
* L' `Set-Cookie` en-tête ne doit pas être présent.
* `Vary` les paramètres d’en-tête doivent être valides et ne doivent pas être égaux à `*` .
* La `Content-Length` valeur d’en-tête (si définie) doit correspondre à la taille du corps de la réponse.
* Le <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> n’est pas utilisé.
* La réponse ne doit pas être périmée comme spécifié par l' `Expires` en-tête et les `max-age` `s-maxage` directives de cache et.
* La mise en mémoire tampon des réponses doit réussir. La taille de la réponse doit être inférieure à la valeur configurée ou par défaut <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . La taille du corps de la réponse doit être inférieure à la valeur configurée ou par défaut <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* La réponse doit être mise en cache conformément aux spécifications de la [norme RFC 7234](https://tools.ietf.org/html/rfc7234) . Par exemple, la `no-store` directive ne doit pas exister dans les champs d’en-tête de demande ou de réponse. Pour plus d’informations, consultez la *section 3 : stockage des réponses dans les caches* de [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Le système anti-contrefaçon pour générer des jetons sécurisés afin d’empêcher les attaques par falsification de requête intersites (CSRF) définit les `Cache-Control` `Pragma` en-têtes et afin `no-cache` que les réponses ne soient pas mises en cache. Pour plus d’informations sur la façon de désactiver des jetons anti-contrefaçon pour les éléments de formulaire HTML, consultez <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cet article explique comment configurer l’intergiciel (middleware) de mise en cache des réponses dans une application ASP.NET Core. L’intergiciel détermine quand les réponses sont pouvant être mises en cache, stocke les réponses et sert les réponses du cache. Pour obtenir une présentation de la mise en cache HTTP et de l' [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribut, consultez [mise en cache des réponses](xref:performance/caching/response).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Configuration

Utilisez le logiciel [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou ajoutez une référence de package au package [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

Dans `Startup.ConfigureServices` , ajoutez l’intergiciel de mise en cache des réponses à la collection de services :

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Configurez l’application pour utiliser l’intergiciel avec la <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> méthode d’extension, qui ajoute l’intergiciel au pipeline de traitement des demandes dans `Startup.Configure` :

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

L’exemple d’application ajoute des en-têtes pour contrôler la mise en cache lors des requêtes suivantes :

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): met en cache les réponses pouvant être mises en cache pendant 10 secondes.
* [Variation](https://tools.ietf.org/html/rfc7231#section-7.1.4): configure l’intergiciel (middleware) pour traiter une réponse mise en cache uniquement si l’en-tête [accepter-encodage](https://tools.ietf.org/html/rfc7231#section-5.3.4) des demandes suivantes correspond à celui de la demande d’origine.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Les en-têtes précédents ne sont pas écrits dans la réponse et sont remplacés lorsqu’un contrôleur, une action ou une Razor page :

* A un attribut [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . Cela s’applique même si une propriété n’est pas définie. Par exemple, si vous omettez la propriété [VaryByHeader](./response.md#vary) , l’en-tête correspondant sera supprimé de la réponse.

L’intergiciel (middleware) de mise en cache des réponses met uniquement en cache les réponses du serveur qui génèrent un code d’état 200 (OK). Toutes les autres réponses, y compris les [pages d’erreur](xref:fundamentals/error-handling), sont ignorées par l’intergiciel (middleware).

> [!WARNING]
> Les réponses contenant du contenu pour les clients authentifiés doivent être marquées comme ne pouvant pas être mises en cache pour empêcher l’intergiciel de stocker et desservir ces réponses. Pour plus d’informations sur la façon dont l’intergiciel détermine si une réponse peut être mise en cache, consultez [conditions de mise en cache](#conditions-for-caching) .

## <a name="options"></a>Options

Les options de mise en cache des réponses sont indiquées dans le tableau suivant.

| Option | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Plus grande taille pouvant être mise en cache pour le corps de la réponse en octets. La valeur par défaut est `64 * 1024 * 1024` (64 Mo). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Taille limite de l’intergiciel (middleware) du cache de réponse en octets. La valeur par défaut est `100 * 1024 * 1024` (100 Mo). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Détermine si les réponses sont mises en cache sur les chemins d’accès sensibles à la casse. La valeur par défaut est `false`. |

L’exemple suivant configure l’intergiciel (middleware) sur :

* Met en cache les réponses dont la taille du corps est inférieure ou égale à 1 024 octets.
* Stockez les réponses en respectant les chemins sensibles à la casse. Par exemple, `/page1` et `/Page1` sont stockés séparément.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Lors de l’utilisation de contrôleurs d’API Web ou de modèles d’API MVC/Web Razor , l' [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribut spécifie les paramètres nécessaires à la définition des en-têtes appropriés pour la mise en cache des réponses. Le seul paramètre de l' `[ResponseCache]` attribut qui requiert strictement l’intergiciel est <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , qui ne correspond pas à un en-tête HTTP réel. Pour plus d’informations, consultez <xref:performance/caching/response#responsecache-attribute>.

Quand vous n’utilisez pas l' `[ResponseCache]` attribut, la mise en cache des réponses peut être modifiée avec `VaryByQueryKeys` . Utilisez le <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directement à partir du [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

L’utilisation d’une valeur unique égale à `*` dans fait `VaryByQueryKeys` varier le cache par tous les paramètres de requête de demande.

## <a name="http-headers-used-by-response-caching-middleware"></a>En-têtes HTTP utilisés par l’intergiciel (middleware) de mise en cache des réponses

Le tableau suivant fournit des informations sur les en-têtes HTTP qui affectent la mise en cache des réponses.

| En-tête | Détails |
| ------ | ------- |
| `Authorization` | La réponse n’est pas mise en cache si l’en-tête existe. |
| `Cache-Control` | L’intergiciel (middleware) prend uniquement en compte les réponses de mise en cache marquées avec la `public` directive de cache. Contrôlez la mise en cache avec les paramètres suivants :<ul><li>âge maximal</li><li>Max-&#8224; obsolète</li><li>min-frais</li><li>must-revalidate</li><li>no-cache</li><li>non-Store</li><li>uniquement-en cas de mise en cache</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-revalider&#8225;</li></ul>&#8224;si aucune limite n’est spécifiée à `max-stale` , l’intergiciel n’entreprend aucune action.<br>&#8225;`proxy-revalidate` a le même effet que `must-revalidate` .<br><br>Pour plus d’informations, consultez [RFC 7231 : Request Cache-Control directives](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | Un `Pragma: no-cache` en-tête dans la demande produit le même effet que `Cache-Control: no-cache` . Cet en-tête est substitué par les directives pertinentes dans l' `Cache-Control` en-tête, le cas échéant. Pris en compte pour la compatibilité descendante avec HTTP/1.0. |
| `Set-Cookie` | La réponse n’est pas mise en cache si l’en-tête existe. Tout intergiciel dans le pipeline de traitement des demandes qui définit un ou plusieurs cookie s empêche l’intergiciel de mise en cache de la réponse (par exemple, le [ cookie fournisseur TempData basé sur](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | L' `Vary` en-tête est utilisé pour faire varier la réponse mise en cache par un autre en-tête. Par exemple, mettez en cache les réponses par encodage en incluant l' `Vary: Accept-Encoding` en-tête, qui met en cache les réponses pour les demandes avec en-têtes `Accept-Encoding: gzip` et `Accept-Encoding: text/plain` séparément. Une réponse avec une valeur d’en-tête de `*` n’est jamais stockée. |
| `Expires` | Une réponse considérée comme périmée par cet en-tête n’est pas stockée ou récupérée, sauf si elle est remplacée par d’autres `Cache-Control` en-têtes. |
| `If-None-Match` | La réponse complète est traitée à partir du cache si la valeur n’est pas `*` et que la `ETag` de la réponse ne correspond à aucune des valeurs fournies. Dans le cas contraire, une réponse 304 (non modifiée) est traitée. |
| `If-Modified-Since` | Si l' `If-None-Match` en-tête n’est pas présent, une réponse complète est traitée à partir du cache si la date de réponse mise en cache est plus récente que la valeur fournie. Dans le cas contraire, une réponse *304-non modifiée* est traitée. |
| `Date` | En cas de service à partir du cache, l' `Date` en-tête est défini par l’intergiciel (middleware) s’il n’a pas été fourni dans la réponse d’origine. |
| `Content-Length` | En cas de service à partir du cache, l' `Content-Length` en-tête est défini par l’intergiciel (middleware) s’il n’a pas été fourni dans la réponse d’origine. |
| `Age` | L' `Age` en-tête envoyé dans la réponse d’origine est ignoré. L’intergiciel (middleware) calcule une nouvelle valeur lors de la fourniture d’une réponse mise en cache. |

## <a name="caching-respects-request-cache-control-directives"></a>Mise en cache respecte les directives de Cache-Control de demande

L’intergiciel respecte les règles de la [spécification de mise en cache HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Les règles requièrent qu’un cache honore un `Cache-Control` en-tête valide envoyé par le client. Dans le cadre de la spécification, un client peut faire des demandes avec une `no-cache` valeur d’en-tête et forcer le serveur à générer une nouvelle réponse pour chaque demande. Actuellement, il n’y a pas de contrôle du développeur sur ce comportement de mise en cache lors de l’utilisation de l’intergiciel (middleware), car l’intergiciel respecte la spécification officielle de mise en cache.

Pour plus de contrôle sur le comportement de mise en cache, explorez les autres fonctionnalités de mise en cache de ASP.NET Core. Consultez les rubriques suivantes :

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Dépannage

Si le comportement de mise en cache n’est pas le même que prévu, vérifiez que les réponses sont mises en cache et peuvent être servies à partir du cache. Examinez les en-têtes entrants de la demande et les en-têtes sortants de la réponse. Activez la [journalisation](xref:fundamentals/logging/index) pour faciliter le débogage.

Lors du test et du dépannage du comportement de mise en cache, un navigateur peut définir des en-têtes de demande qui affectent la mise en cache de façon indésirable. Par exemple, un navigateur peut définir l' `Cache-Control` en-tête sur `no-cache` ou lors de l' `max-age=0` actualisation d’une page. Les outils suivants peuvent définir explicitement des en-têtes de demande et sont préférés pour le test de mise en cache :

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Conditions de mise en cache

* La demande doit aboutir à une réponse du serveur avec un code d’état 200 (OK).
* La méthode de demande doit être obtenir ou HEAD.
* Dans `Startup.Configure` , l’intergiciel de mise en cache des réponses doit être placé avant l’intergiciel (middleware) qui requiert la mise en cache. Pour plus d’informations, consultez <xref:fundamentals/middleware/index>.
* L' `Authorization` en-tête ne doit pas être présent.
* `Cache-Control` les paramètres d’en-tête doivent être valides et la réponse doit être marquée `public` et non marquée `private` .
* L’en- `Pragma: no-cache` tête ne doit pas être présent si l' `Cache-Control` en-tête n’est pas présent, car l’en `Cache-Control` -tête remplace l' `Pragma` en-tête lorsqu’il est présent.
* L' `Set-Cookie` en-tête ne doit pas être présent.
* `Vary` les paramètres d’en-tête doivent être valides et ne doivent pas être égaux à `*` .
* La `Content-Length` valeur d’en-tête (si définie) doit correspondre à la taille du corps de la réponse.
* Le <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> n’est pas utilisé.
* La réponse ne doit pas être périmée comme spécifié par l' `Expires` en-tête et les `max-age` `s-maxage` directives de cache et.
* La mise en mémoire tampon des réponses doit réussir. La taille de la réponse doit être inférieure à la valeur configurée ou par défaut <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . La taille du corps de la réponse doit être inférieure à la valeur configurée ou par défaut <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* La réponse doit être mise en cache conformément aux spécifications de la [norme RFC 7234](https://tools.ietf.org/html/rfc7234) . Par exemple, la `no-store` directive ne doit pas exister dans les champs d’en-tête de demande ou de réponse. Pour plus d’informations, consultez la *section 3 : stockage des réponses dans les caches* de [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Le système anti-contrefaçon pour générer des jetons sécurisés afin d’empêcher les attaques par falsification de requête intersites (CSRF) définit les `Cache-Control` `Pragma` en-têtes et afin `no-cache` que les réponses ne soient pas mises en cache. Pour plus d’informations sur la façon de désactiver des jetons anti-contrefaçon pour les éléments de formulaire HTML, consultez <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end