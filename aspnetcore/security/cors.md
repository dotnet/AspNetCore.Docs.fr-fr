---
title: Activer les requêtes Cross-Origin (CORS) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment CORS comme norme pour autoriser ou rejeter des demandes Cross-Origin dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: b057e5e08b8a4d0f9bcd68f92102cad309655acc
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413507"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Activer les requêtes Cross-Origin (CORS) dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)

Cet article explique comment activer CORS dans une application ASP.NET Core.

La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web. Cette restriction est appelée *stratégie de même origine*. La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site. Parfois, vous souhaiterez peut-être autoriser d’autres sites à effectuer des demandes Cross-Origin à votre application. Pour plus d’informations, consultez l' [article Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Partage des ressources Cross-Origin](https://www.w3.org/TR/cors/) (cors) :

* Est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.
* N’est **pas** une fonctionnalité de sécurité, cors assouplit la sécurité. Une API n’est pas plus sûre en autorisant CORS. Pour plus d’informations, consultez fonctionnement de [cors](#how-cors).
* Permet à un serveur d’autoriser explicitement certaines demandes Cross-Origin tout en rejetant d’autres.
* Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Même origine

Deux URL ont la même origine si elles ont des schémas, des hôtes et des ports identiques ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Ces deux URL ont le même origine :

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Ces URL ont des origines différentes des deux précédentes URL :

* `https://example.net`: Domaine différent
* `https://www.example.com/foo.html`: Autre sous-domaine
* `http://example.com/foo.html`: Schéma différent
* `https://example.com:9000/foo.html`: Autre port

## <a name="enable-cors"></a>Activez CORS

Il existe trois façons d’activer CORS :

* Dans l’intergiciel (middleware) à l’aide d’une [stratégie nommée](#np) ou d’une [stratégie par défaut](#dp).
* Utilisation du [routage de point de terminaison](#ecors).
* Avec l’attribut [[EnableCors]](#attr) .

L’utilisation de l’attribut [[EnableCors]](#attr) avec une stratégie nommée offre un contrôle plus fin pour limiter les points de terminaison qui prennent en charge cors.

> [!WARNING]
> <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> doit être appelé dans le bon ordre. Pour plus d’informations, consultez [ordre des intergiciels (middleware](xref:fundamentals/middleware/index#middleware-order)). Par exemple, `UseCors` doit être appelé avant <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> lors de l’utilisation de `UseResponseCaching` .

Chaque approche est décrite en détail dans les sections suivantes.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS avec la stratégie et l’intergiciel (middleware) nommés

L’intergiciel (middleware) CORS gère les demandes Cross-Origin. Le code suivant applique une stratégie CORS à tous les points de terminaison de l’application avec les origines spécifiées :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

Le code précédent :

* Définit le nom de la stratégie sur `_myAllowSpecificOrigins` . Le nom de la stratégie est arbitraire.
* Appelle la <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> méthode d’extension et spécifie la  `_myAllowSpecificOrigins` stratégie cors. `UseCors` Ajoute l’intergiciel (middleware) CORS. L’appel à `UseCors` doit être placé après `UseRouting` , mais avant `UseAuthorization` . Pour plus d’informations, consultez [ordre des intergiciels (middleware](xref:fundamentals/middleware/index#middleware-order)).
* Appelle <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). L’expression lambda prend un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objet. Les [options de configuration](#cors-policy-options), telles que `WithOrigins` , sont décrites plus loin dans cet article.
* Active la `_myAllowSpecificOrigins` stratégie cors pour tous les points de terminaison de contrôleur. Consultez [routage des points de terminaison](#ecors) pour appliquer une stratégie cors à des points de terminaison spécifiques.
* Lors de l’utilisation de l’intergiciel (middleware) de [mise en cache des réponses](xref:performance/caching/middleware), appelez <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> avant <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .

Avec le routage de point de terminaison, l’intergiciel (middleware) CORS **doit** être configuré pour s’exécuter entre les appels à `UseRouting` et `UseEndpoints` .

Consultez la page [test cors](#testc) pour obtenir des instructions sur le test de code similaire au code précédent.

L' <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> appel de méthode ajoute des services cors au conteneur de services de l’application :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Pour plus d’informations, consultez les [options de stratégie cors](#cpo) dans ce document.

Les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthodes peuvent être chaînées, comme illustré dans le code suivant :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Remarque : l’URL spécifiée **ne doit pas** contenir de barre oblique finale ( `/` ). Si l’URL se termine par `/` , la comparaison retourne `false` et aucun en-tête n’est retourné.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS avec la stratégie et l’intergiciel (middleware) par défaut

Le code en surbrillance suivant active la stratégie CORS par défaut :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Le code précédent applique la stratégie CORS par défaut à tous les points de terminaison de contrôleur.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Activer cors avec routage du point de terminaison

L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les [demandes de prévols automatiques](#apf). Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/20709) et [test cors avec routage des points de terminaison et [HttpOptions]](#tcer).

Avec le routage de point de terminaison, CORS peut être activé sur une base par point de terminaison à l’aide de l' <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ensemble de méthodes d’extension :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

Dans le code précédent :

* `app.UseCors` active l’intergiciel (middleware) CORS. Étant donné qu’une stratégie par défaut n’a pas été configurée, `app.UseCors()` seuls n’activent cors.
* Les `/echo` points de terminaison et des contrôleurs autorisent les demandes Cross-Origin à l’aide de la stratégie spécifiée.
* Les `/echo2` Razor points de terminaison et pages n’autorisent **pas** les demandes Cross-Origin, car aucune stratégie par défaut n’a été spécifiée.

L’attribut [[DisableCors]](#dc) ne désactive **pas**  cors qui a été activé par le routage de point de terminaison avec `RequireCors` .

Consultez [test cors avec routage des points de terminaison et [HttpOptions]](#tcer) pour obtenir des instructions sur le test de code similaire au précédent.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Activer CORS avec des attributs

L’activation de CORS avec l’attribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) et l’application d’une stratégie nommée uniquement aux points de terminaison qui requièrent cors fournissent le contrôle le plus fin.

L’attribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de cors globalement. L' `[EnableCors]` attribut Active cors pour les points de terminaison sélectionnés, plutôt que tous les points de terminaison :

* `[EnableCors]` spécifie la stratégie par défaut.
* `[EnableCors("{Policy String}")]` spécifie une stratégie nommée.

L' `[EnableCors]` attribut peut être appliqué aux éléments suivants :

* Razor Pagination `PageModel`
* Contrôleur
* Méthode d’action du contrôleur

Différentes stratégies peuvent être appliquées à des contrôleurs, des modèles de page ou des méthodes d’action avec l' `[EnableCors]` attribut. Lorsque l' `[EnableCors]` attribut est appliqué à un contrôleur, à un modèle de page ou à une méthode d’action, et que cors est activé dans l’intergiciel (middleware), **les deux** stratégies sont appliquées. **Nous vous recommandons de combiner les stratégies. Utilisez l'** `[EnableCors]` **attribut ou l’intergiciel (middleware), pas les deux dans la même application.**

Le code suivant applique une stratégie différente à chaque méthode :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Le code suivant crée deux stratégies CORS :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Pour un meilleur contrôle de la limitation des demandes CORS :

* Utilisez `[EnableCors("MyPolicy")]` avec une stratégie nommée.
* Ne définissez pas de stratégie par défaut.
* N’utilisez pas le [routage de point de terminaison](#ecors).

Le code de la section suivante répond à la liste précédente.

Consultez la page [test cors](#testc) pour obtenir des instructions sur le test de code similaire au code précédent.

<a name="dc"></a>

### <a name="disable-cors"></a>Désactiver CORS

L’attribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ne désactive **pas**  cors qui a été activé par le routage des [points de terminaison](#ecors).

Le code suivant définit la stratégie CORS `"MyPolicy"` :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Le code suivant désactive CORS pour l' `GetValues2` action :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Le code précédent :

* N’active pas CORS avec [routage du point de terminaison](#ecors).
* Ne définit pas une [stratégie cors par défaut](#dp).
* Utilise [[EnableCors ("MyPolicy")]](#attr) pour activer la `"MyPolicy"` stratégie cors pour le contrôleur.
* Désactive CORS pour la `GetValues2` méthode.

Consultez la page [test cors](#testc) pour obtenir des instructions sur le test du code précédent.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Options de stratégie CORS

Cette section décrit les différentes options qui peuvent être définies dans une stratégie CORS :

* [Définir les origines autorisées](#set-the-allowed-origins)
* [Définir les méthodes HTTP autorisées](#set-the-allowed-http-methods)
* [Définir les en-têtes de demande autorisés](#set-the-allowed-request-headers)
* [Définir les en-têtes de réponse exposés](#set-the-exposed-response-headers)
* [Informations d’identification dans les demandes Cross-Origin](#credentials-in-cross-origin-requests)
* [Définir le délai d’expiration du prévols](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> est appelé dans `Startup.ConfigureServices` . Pour certaines options, il peut être utile de lire d’abord la section [How cors Works](#how-cors) .

## <a name="set-the-allowed-origins"></a>Définir les origines autorisées

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Autorise les requêtes CORS de toutes les origines avec n’importe quel schéma ( `http` ou `https` ). `AllowAnyOrigin` n’est pas sécurisé, car *un site Web* peut effectuer des demandes Cross-Origin à l’application.

> [!NOTE]
> La spécification de `AllowAnyOrigin` et `AllowCredentials` de est une configuration non sécurisée et peut entraîner une falsification de requête intersites. Le service CORS retourne une réponse CORS non valide lorsqu’une application est configurée avec les deux méthodes.

`AllowAnyOrigin` affecte les demandes préliminaires et l' `Access-Control-Allow-Origin` en-tête. Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Définit la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriété de la stratégie pour qu’elle soit une fonction qui permet aux origines de correspondre à un domaine générique configuré lors de l’évaluation si l’origine est autorisée.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Définir les méthodes HTTP autorisées

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Autorise toute méthode HTTP :
* Affecte les demandes préliminaires et l' `Access-Control-Allow-Methods` en-tête. Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Définir les en-têtes de demande autorisés

Pour autoriser l’envoi d’en-têtes spécifiques dans une demande CORS, appelée [créer des en-têtes de demande](https://xhr.spec.whatwg.org/#request), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> et spécifiez les en-têtes autorisés :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Pour autoriser tous les [en-têtes de demande d’auteur](https://www.w3.org/TR/cors/#author-request-headers), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader` affecte les demandes préliminaires et l’en-tête [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) . Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .

Une stratégie d’intergiciel (middleware) CORS correspondant à des en-têtes spécifiques spécifiés par `WithHeaders` n’est possible que lorsque les en-têtes envoyés dans `Access-Control-Request-Headers` correspondent exactement aux en-têtes indiqués dans `WithHeaders` .

Par exemple, considérez une application configurée comme suit :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

L’intergiciel (middleware) CORS refuse une demande préliminaire avec l’en-tête de demande suivant, car `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) n’est pas listé dans `WithHeaders` :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

L’application renvoie une réponse *200 OK* , mais n’envoie pas les en-têtes cors. Par conséquent, le navigateur n’essaie pas la demande Cross-Origin.

### <a name="set-the-exposed-response-headers"></a>Définir les en-têtes de réponse exposés

Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application. Pour plus d’informations, consultez la page [relative au partage des ressources Cross-Origin W3C (terminologie) : en-tête de réponse simple](https://www.w3.org/TR/cors/#simple-response-header).

Les en-têtes de réponse qui sont disponibles par défaut sont :

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La spécification CORS appelle ces en *-têtes de réponse simples* en-têtes. Pour mettre d’autres en-têtes à la disposition de l’application, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Informations d’identification dans les demandes Cross-Origin

Les informations d’identification nécessitent un traitement spécial dans une demande CORS. Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande Cross-Origin. Les informations d’identification incluent cookie les schémas d’authentification s et http. Pour envoyer des informations d’identification avec une demande Cross-Origin, le client doit affecter à la valeur `XMLHttpRequest.withCredentials` `true` .

Utilisation `XMLHttpRequest` directe :

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Utilisation de jQuery :

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Utilisation de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Le serveur doit autoriser les informations d’identification. Pour autoriser les informations d’identification Cross-Origin, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

La réponse HTTP comprend un `Access-Control-Allow-Credentials` en-tête qui indique au navigateur que le serveur autorise les informations d’identification pour une demande Cross-Origin.

Si le navigateur envoie des informations d’identification mais que la réponse n’inclut pas d' `Access-Control-Allow-Credentials` en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande Cross-Origin échoue.

L’autorisation des informations d’identification Cross-Origin est un risque pour la sécurité. Un site Web dans un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application pour le compte de l’utilisateur sans la connaissance de l’utilisateur. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La spécification CORS indique également que le paramètre Origins to `"*"` (All Origins) n’est pas valide si l' `Access-Control-Allow-Credentials` en-tête est présent.

<a name="pref"></a>

## <a name="preflight-requests"></a>Demandes préliminaires

Pour certaines demandes CORS, le navigateur envoie une demande d' [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) supplémentaire avant d’effectuer la demande réelle. Cette demande porte le nom de [demande préliminaire](https://developer.mozilla.org/docs/Glossary/Preflight_request). Le navigateur peut ignorer la demande préliminaire si **toutes** les conditions suivantes sont remplies :

* La méthode de demande est : obtenir, début ou publication.
* L’application ne définit pas les en-têtes de requête autres que `Accept` , `Accept-Language` ,, `Content-Language` `Content-Type` ou `Last-Event-ID` .
* L' `Content-Type` en-tête, s’il est défini, a l’une des valeurs suivantes :
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La règle sur les en-têtes de demande définie pour la demande du client s’applique aux en-têtes définis par l’application en appelant `setRequestHeader` sur l' `XMLHttpRequest` objet. La spécification CORS appelle ces en-têtes [créer des en-](https://www.w3.org/TR/cors/#author-request-headers)têtes de demande. La règle ne s’applique pas aux en-têtes que le navigateur peut définir, tels que `User-Agent` , `Host` ou `Content-Length` .

Voici un exemple de réponse similaire à la demande préliminaire effectuée à partir du bouton **[placer un test]** dans la section [cors de test](#testc) de ce document.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

La demande préliminaire utilise la méthode [http options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) . Il peut inclure les en-têtes suivants :

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): méthode http qui sera utilisée pour la demande réelle.
* [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): liste des en-têtes de requête que l’application définit sur la demande réelle. Comme indiqué précédemment, cela n’inclut pas les en-têtes définis par le navigateur, tels que `User-Agent` .
* [Access-Control-allow-Methods](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Si la demande préliminaire est refusée, l’application retourne une `200 OK` réponse, mais ne définit pas les en-têtes cors. Par conséquent, le navigateur n’essaie pas la demande Cross-Origin. Pour obtenir un exemple de demande préliminaire refusée, consultez la section « [test cors](#testc) » de ce document.

À l’aide des outils F12, l’application console affiche une erreur semblable à l’une des options suivantes, selon le navigateur :

* Firefox : requête Cross-Origin bloquée : la même stratégie d’origine interdit la lecture de la ressource distante à l’adresse `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` . (Raison : la demande CORS n’a pas pu être effectuée). [En savoir plus](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Basé sur le chrome : l’accès à la récupération sur « https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 » à partir de l’origine « https://cors3.azurewebsites.net » a été bloqué par la stratégie cors : la réponse à la demande préliminaire ne réussit pas la vérification du contrôle d’accès : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée. Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.

Pour autoriser des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Pour autoriser tous les [en-têtes de demande d’auteur](https://www.w3.org/TR/cors/#author-request-headers), appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Les navigateurs ne sont pas cohérents dans la façon dont ils sont définis `Access-Control-Request-Headers` . Si l’une des deux :

* Les en-têtes sont définis sur une valeur autre que `"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> est appelé : inclure au moins `Accept` , `Content-Type` , et `Origin` , ainsi que tous les en-têtes personnalisés que vous souhaitez prendre en charge.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Code de demande préliminaire automatique

Lorsque la stratégie CORS est appliquée :

* Globalement en appelant `app.UseCors` `Startup.Configure` .
* À l’aide de l' `[EnableCors]` attribut.

ASP.NET Core répond à la demande d’OPTIONS préliminaires.

L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les demandes de prévols automatiques.

La section « [cors de test](#testc) » de ce document illustre ce comportement.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>Attribut [HttpOptions] pour les demandes préliminaires

Lorsque CORS est activé avec la stratégie appropriée, ASP.NET Core répond généralement aux demandes préliminaires CORS automatiquement. Dans certains scénarios, cela peut ne pas être le cas. Par exemple, à l’aide de [cors avec routage du point de terminaison](#ecors).

Le code suivant utilise l’attribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) pour créer des points de terminaison pour les demandes d’options :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Consultez [test cors avec routage des points de terminaison et [HttpOptions]](#tcer) pour obtenir des instructions sur le test du code précédent.

### <a name="set-the-preflight-expiration-time"></a>Définir le délai d’expiration du prévols

L' `Access-Control-Max-Age` en-tête spécifie la durée pendant laquelle la réponse à la demande préliminaire peut être mise en cache. Pour définir cet en-tête, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Fonctionnement de CORS

Cette section décrit ce qui se produit dans une demande [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages http.

* CORS n’est **pas** une fonctionnalité de sécurité. CORS est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.
  * Par exemple, un acteur malveillant peut utiliser des [scripts inter-sites (XSS)](xref:security/cross-site-scripting) sur votre site et exécuter une requête intersite vers son site Active cors pour voler des informations.
* Une API n’est pas plus sûre en autorisant CORS.
  * C’est au client (navigateur) d’appliquer CORS. Le serveur exécute la requête et retourne la réponse, c’est le client qui retourne une erreur et bloque la réponse. Par exemple, l’un des outils suivants affiche la réponse du serveur :
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Un navigateur Web en entrant l’URL dans la barre d’adresses.
* C’est un moyen pour un serveur d’autoriser les navigateurs à exécuter une requête d’API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) Cross-Origin qui serait sinon interdite.
  * Les navigateurs sans CORS ne peuvent pas effectuer de requêtes Cross-Origin. Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) était utilisé pour contourner cette restriction. JSONP n’utilise pas XHR, elle utilise la `<script>` balise pour recevoir la réponse. Les scripts sont autorisés à être chargés sur plusieurs origines.

La [spécification cors](https://www.w3.org/TR/cors/) a introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes Cross-Origin. Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes Cross-Origin. Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.

[Bouton put test](https://cors3.azurewebsites.net/test) sur l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) déployé

Voici un exemple de demande Cross-Origin à partir du bouton de test des [valeurs](https://cors3.azurewebsites.net/) vers `https://cors1.azurewebsites.net/api/values` . L' `Origin` en-tête :

* Fournit le domaine du site qui effectue la demande.
* Est obligatoire et doit être différent de l’hôte.

**En-têtes généraux**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**En-têtes de réponse**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**En-têtes de demande**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

Dans `OPTIONS` les demandes, le serveur définit l’en-tête des **en-têtes de réponse** `Access-Control-Allow-Origin: {allowed origin}` dans la réponse. Par exemple, l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)de requête de bouton [delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) déployé `OPTIONS` contient les en-têtes suivants :

**En-têtes généraux**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**En-têtes de réponse**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**En-têtes de demande**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Dans les **en-têtes de réponse** précédents, le serveur définit l’en-tête [Access-Control-allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) dans la réponse. La `https://cors1.azurewebsites.net` valeur de cet en-tête correspond à l' `Origin` en-tête de la demande.

Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> est appelé, la `Access-Control-Allow-Origin: *` valeur de caractère générique est retournée. `AllowAnyOrigin` autorise toute origine.

Si la réponse n’inclut pas l' `Access-Control-Allow-Origin` en-tête, la demande Cross-Origin échoue. Plus précisément, le navigateur n’autorise pas la demande. Même si le serveur retourne une réponse correcte, le navigateur ne rend pas la réponse disponible pour l’application cliente.

<a name="options"></a>

### <a name="display-options-requests"></a>Afficher les demandes OPTIONS

Par défaut, les navigateurs Chrome et Edge n’affichent pas les demandes OPTIONS sous l’onglet Réseau des outils F12. Pour afficher les demandes OPTIONS dans ces navigateurs :

* `chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`
* Désactivez l’indicateur.
* redémarrer.

Firefox affiche les demandes d’OPTIONS par défaut.

## <a name="cors-in-iis"></a>CORS dans IIS

Lors du déploiement sur IIS, CORS doit s’exécuter avant l’authentification Windows si le serveur n’est pas configuré pour autoriser l’accès anonyme. Pour prendre en charge ce scénario, le [module cors IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.

<a name="testc"></a>

## <a name="test-cors"></a>Tester CORS

L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) contient du code pour tester cors. Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample). L’exemple est un projet d’API avec des Razor pages ajoutées :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` doit uniquement être utilisé pour tester un exemple d’application semblable à l' [exemple de code de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

Les éléments suivants `ValuesController` fournissent les points de terminaison pour le test :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) est fourni par le package NuGet [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) et affiche des informations sur l’itinéraire.

Testez l’exemple de code précédent à l’aide de l’une des approches suivantes :

* Utilisez l’exemple d’application déployée à l’adresse [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) . Il n’est pas nécessaire de télécharger l’exemple.
* Exécutez l’exemple avec l' `dotnet run` URL par défaut de `https://localhost:5001` .
* Exécutez l’exemple à partir de Visual Studio avec le port défini sur 44398 pour une URL de `https://localhost:44398` .

À l’aide d’un navigateur avec les outils F12 :

* Sélectionnez le bouton **valeurs** et passez en revue les en-têtes sous l’onglet **réseau** .
* Sélectionnez le bouton **put test** . Consultez [afficher les demandes d’options](#options) pour obtenir des instructions sur l’affichage de la demande d’options. Le **test put** crée deux demandes, une demande préliminaire options et la demande put.
* Sélectionnez le **`GetValues2 [DisableCors]`** bouton pour déclencher une demande cors ayant échoué. Comme mentionné dans le document, la réponse retourne 200 réussite, mais la demande CORS n’est pas effectuée. Sélectionnez l’onglet **console** pour afficher l’erreur cors. En fonction du navigateur, une erreur semblable à la suivante s’affiche :

     L’accès à l’extraction à `'https://cors1.azurewebsites.net/api/values/GetValues2'` partir de l’origine `'https://cors3.azurewebsites.net'` a été bloqué par la stratégie cors : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée. Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.
     
Les points de terminaison compatibles CORS peuvent être testés à l’aide d’un outil, tel que le terme « [bouclé](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)» ou « [postal](https://www.getpostman.com/)». Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l' `Origin` en-tête doit différer de l’hôte recevant la demande. Si la requête n’est pas *une origine croisée* en fonction de la valeur de l' `Origin` en-tête :

* Il n’est pas nécessaire d’utiliser un intergiciel (middleware) CORS pour traiter la requête.
* Les en-têtes CORS ne sont pas retournés dans la réponse.

La commande suivante utilise `curl` pour émettre une demande d’options avec des informations :

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>De test CORS avec routage du point de terminaison et [HttpOptions]

L’activation de CORS sur une base par point de terminaison à l’aide de `RequireCors` ne prend actuellement **pas** en charge les [demandes de prévols automatiques](#apf). Prenons le code suivant qui utilise le [routage de point de terminaison pour activer cors](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Les éléments suivants `TodoItems1Controller` fournissent des points de terminaison pour le test :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Testez le code précédent à partir de la [page de test](https://cors1.azurewebsites.net/test?number=1) de l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé.

Les boutons **supprimer [EnableCors]** et **obtenir [EnableCors]** fonctionnent correctement, car les points de terminaison ont `[EnableCors]` et répondent aux demandes préliminaires. Les autres points de terminaison échouent. Le bouton d' **extraction** échoue, car le [code JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envoie :

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Les éléments suivants `TodoItems2Controller` fournissent des points de terminaison similaires, mais incluent du code explicite pour répondre aux demandes d’options :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Testez le code précédent à partir de la [page de test](https://cors1.azurewebsites.net/test?number=2) de l’exemple déployé. Dans la liste déroulante **contrôleur** , sélectionnez **prévols** , puis **définir le contrôleur**. Tous les appels CORS aux points de terminaison ont été `TodoItems2Controller` correctement effectués.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Partage des ressources Cross-Origin (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Prise en main du module IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet article explique comment activer CORS dans une application ASP.NET Core.

La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web. Cette restriction est appelée *stratégie de même origine*. La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site. Parfois, vous souhaiterez peut-être autoriser d’autres sites à effectuer des demandes Cross-Origin à votre application. Pour plus d’informations, consultez l' [article Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Partage des ressources Cross-Origin](https://www.w3.org/TR/cors/) (cors) :

* Est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.
* N’est **pas** une fonctionnalité de sécurité, cors assouplit la sécurité. Une API n’est pas plus sûre en autorisant CORS. Pour plus d’informations, consultez fonctionnement de [cors](#how-cors).
* Permet à un serveur d’autoriser explicitement certaines demandes Cross-Origin tout en rejetant d’autres.
* Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Même origine

Deux URL ont la même origine si elles ont des schémas, des hôtes et des ports identiques ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Ces deux URL ont le même origine :

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Ces URL ont des origines différentes des deux précédentes URL :

* `https://example.net`: Domaine différent
* `https://www.example.com/foo.html`: Autre sous-domaine
* `http://example.com/foo.html`: Schéma différent
* `https://example.com:9000/foo.html`: Autre port

Internet Explorer ne prend pas en compte le port lors de la comparaison des origines.

## <a name="cors-with-named-policy-and-middleware"></a>CORS avec la stratégie et l’intergiciel (middleware) nommés

L’intergiciel (middleware) CORS gère les demandes Cross-Origin. Le code suivant active CORS pour l’application entière avec l’origine spécifiée :

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Le code précédent :

* Définit le nom de la stratégie sur « \_ myAllowSpecificOrigins ». Le nom de la stratégie est arbitraire.
* Appelle la <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> méthode d’extension, qui active cors.
* Appelle <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). L’expression lambda prend un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objet. Les [options de configuration](#cors-policy-options), telles que `WithOrigins` , sont décrites plus loin dans cet article.

L' <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> appel de méthode ajoute des services cors au conteneur de services de l’application :

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Pour plus d’informations, consultez les [options de stratégie cors](#cpo) dans ce document.

La <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthode peut chaîner des méthodes, comme indiqué dans le code suivant :

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Remarque : l’URL **ne doit pas** contenir de barre oblique finale ( `/` ). Si l’URL se termine par `/` , la comparaison retourne `false` et aucun en-tête n’est retourné.

Le code suivant applique des stratégies CORS à tous les points de terminaison des applications par le biais de l’intergiciel (middleware) CORS :
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Remarque : `UseCors` doit être appelé avant `UseMvc` .

Consultez [activer cors dans les Razor pages, les contrôleurs et les méthodes d’action](#ecors) pour appliquer la stratégie cors au niveau de la page/du contrôleur/action.

Consultez la page [test cors](#test) pour obtenir des instructions sur le test de code similaire au code précédent.

## <a name="enable-cors-with-attributes"></a>Activer CORS avec des attributs

L’attribut [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fournit une alternative à l’application de cors globalement. L' `[EnableCors]` attribut Active cors pour les points de terminaison sélectionnés, plutôt que tous les points de terminaison.

Utilisez `[EnableCors]` pour spécifier la stratégie par défaut et `[EnableCors("{Policy String}")]` spécifier une stratégie.

L' `[EnableCors]` attribut peut être appliqué aux éléments suivants :

* Razor Pagination `PageModel`
* Contrôleur
* Méthode d’action du contrôleur

Vous pouvez appliquer différentes stratégies à Controller/page-Model/action avec l'  `[EnableCors]` attribut. Lorsque l' `[EnableCors]` attribut est appliqué à un modèle de contrôleurs/page/méthode d’action et que cors est activé dans l’intergiciel (middleware), **les deux** stratégies sont appliquées. Nous vous recommandons de **ne pas** combiner les stratégies. Utilisez l' `[EnableCors]` attribut ou l’intergiciel (middleware), **pas les deux**. Lorsque vous utilisez `[EnableCors]` , ne définissez **pas** de stratégie par défaut.

Le code suivant applique une stratégie différente à chaque méthode :

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Le code suivant crée une stratégie CORS par défaut et une stratégie nommée `"AnotherPolicy"` :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Désactiver CORS

L’attribut [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) désactive cors pour le contrôleur/page-Model/action.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Options de stratégie CORS

Cette section décrit les différentes options qui peuvent être définies dans une stratégie CORS :

* [Définir les origines autorisées](#set-the-allowed-origins)
* [Définir les méthodes HTTP autorisées](#set-the-allowed-http-methods)
* [Définir les en-têtes de demande autorisés](#set-the-allowed-request-headers)
* [Définir les en-têtes de réponse exposés](#set-the-exposed-response-headers)
* [Informations d’identification dans les demandes Cross-Origin](#credentials-in-cross-origin-requests)
* [Définir le délai d’expiration du prévols](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> est appelé dans `Startup.ConfigureServices` . Pour certaines options, il peut être utile de lire d’abord la section [How cors Works](#how-cors) .

## <a name="set-the-allowed-origins"></a>Définir les origines autorisées

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Autorise les requêtes CORS de toutes les origines avec n’importe quel schéma ( `http` ou `https` ). `AllowAnyOrigin` n’est pas sécurisé, car *un site Web* peut effectuer des demandes Cross-Origin à l’application.

> [!NOTE]
> La spécification de `AllowAnyOrigin` et `AllowCredentials` de est une configuration non sécurisée et peut entraîner une falsification de requête intersites. Pour une application sécurisée, spécifiez une liste exacte d’origines si le client doit s’autoriser à accéder aux ressources du serveur.

`AllowAnyOrigin` affecte les demandes préliminaires et l' `Access-Control-Allow-Origin` en-tête. Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Définit la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriété de la stratégie pour qu’elle soit une fonction qui permet aux origines de correspondre à un domaine générique configuré lors de l’évaluation si l’origine est autorisée.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Définir les méthodes HTTP autorisées

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Autorise toute méthode HTTP :
* Affecte les demandes préliminaires et l' `Access-Control-Allow-Methods` en-tête. Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Définir les en-têtes de demande autorisés

Pour autoriser l’envoi d’en-têtes spécifiques dans une demande CORS, appelée *créer des en-têtes de demande*, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> et spécifiez les en-têtes autorisés :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Pour autoriser tous les en-têtes de demande d’auteur, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Ce paramètre affecte les demandes préliminaires et l' `Access-Control-Request-Headers` en-tête. Pour plus d’informations, consultez la section [demandes préliminaires](#preflight-requests) .

L’intergiciel (middleware) CORS autorise toujours l’envoi de quatre en-têtes dans le `Access-Control-Request-Headers` , quelles que soient les valeurs configurées dans les en-têtes CorsPolicy. Cette liste d’en-têtes comprend les éléments suivants :

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Par exemple, considérez une application configurée comme suit :

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

L’intergiciel (middleware) CORS répond correctement à une demande préliminaire avec l’en-tête de demande suivant, car `Content-Language` est toujours autorisé :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Définir les en-têtes de réponse exposés

Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application. Pour plus d’informations, consultez la page [relative au partage des ressources Cross-Origin W3C (terminologie) : en-tête de réponse simple](https://www.w3.org/TR/cors/#simple-response-header).

Les en-têtes de réponse qui sont disponibles par défaut sont :

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La spécification CORS appelle ces en *-têtes de réponse simples* en-têtes. Pour mettre d’autres en-têtes à la disposition de l’application, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Informations d’identification dans les demandes Cross-Origin

Les informations d’identification nécessitent un traitement spécial dans une demande CORS. Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande Cross-Origin. Les informations d’identification incluent cookie les schémas d’authentification s et http. Pour envoyer des informations d’identification avec une demande Cross-Origin, le client doit affecter à la valeur `XMLHttpRequest.withCredentials` `true` .

Utilisation `XMLHttpRequest` directe :

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Utilisation de jQuery :

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Utilisation de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Le serveur doit autoriser les informations d’identification. Pour autoriser les informations d’identification Cross-Origin, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

La réponse HTTP comprend un `Access-Control-Allow-Credentials` en-tête qui indique au navigateur que le serveur autorise les informations d’identification pour une demande Cross-Origin.

Si le navigateur envoie des informations d’identification mais que la réponse n’inclut pas d' `Access-Control-Allow-Credentials` en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande Cross-Origin échoue.

L’autorisation des informations d’identification Cross-Origin est un risque pour la sécurité. Un site Web dans un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application pour le compte de l’utilisateur sans la connaissance de l’utilisateur. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La spécification CORS indique également que le paramètre Origins to `"*"` (All Origins) n’est pas valide si l' `Access-Control-Allow-Credentials` en-tête est présent.

### <a name="preflight-requests"></a>Demandes préliminaires

Pour certaines demandes CORS, le navigateur envoie une demande supplémentaire avant d’effectuer la demande réelle. Cette demande porte le nom de *demande préliminaire*. Le navigateur peut ignorer la demande préliminaire si les conditions suivantes sont remplies :

* La méthode de demande est : obtenir, début ou publication.
* L’application ne définit pas les en-têtes de requête autres que `Accept` , `Accept-Language` ,, `Content-Language` `Content-Type` ou `Last-Event-ID` .
* L' `Content-Type` en-tête, s’il est défini, a l’une des valeurs suivantes :
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La règle sur les en-têtes de demande définie pour la demande du client s’applique aux en-têtes définis par l’application en appelant `setRequestHeader` sur l' `XMLHttpRequest` objet. La spécification CORS appelle ces en-têtes *créer des en-* têtes de demande. La règle ne s’applique pas aux en-têtes que le navigateur peut définir, tels que `User-Agent` , `Host` ou `Content-Length` .

Voici un exemple de demande préliminaire :

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

La requête de pré-vol utilise la méthode HTTP OPTIONS. Il comprend deux en-têtes spéciaux :

* `Access-Control-Request-Method`: Méthode HTTP qui sera utilisée pour la demande réelle.
* `Access-Control-Request-Headers`: Liste des en-têtes de requête que l’application définit sur la demande réelle. Comme indiqué précédemment, cela n’inclut pas les en-têtes définis par le navigateur, tels que `User-Agent` .

<!-- I think this needs to be removed, was put here accidently -->

Lorsque CORS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes préliminaires CORS. Consultez l' [attribut [HttpOptions] pour les demandes préliminaires](#pro).

Une demande préliminaire CORS peut inclure un `Access-Control-Request-Headers` en-tête, qui indique au serveur les en-têtes envoyés avec la demande réelle.

Pour autoriser des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Pour autoriser tous les en-têtes de demande d’auteur, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Les navigateurs ne sont pas entièrement cohérents dans la façon dont ils sont définis `Access-Control-Request-Headers` . Si vous définissez des en-têtes autres que `"*"` (ou utilisez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), vous devez inclure au moins `Accept` , `Content-Type` , et `Origin` , ainsi que tous les en-têtes personnalisés que vous souhaitez prendre en charge.

Voici un exemple de réponse à la demande préliminaire (en supposant que le serveur autorise la demande) :

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

La réponse comprend un `Access-Control-Allow-Methods` en-tête qui répertorie les méthodes autorisées et éventuellement un `Access-Control-Allow-Headers` en-tête, qui répertorie les en-têtes autorisés. Si la demande préliminaire est réussie, le navigateur envoie la demande réelle.

Si la demande préliminaire est refusée, l’application renvoie une réponse *200 OK* , mais n’envoie pas les en-têtes cors. Par conséquent, le navigateur n’essaie pas la demande Cross-Origin.

### <a name="set-the-preflight-expiration-time"></a>Définir le délai d’expiration du prévols

L' `Access-Control-Max-Age` en-tête spécifie la durée pendant laquelle la réponse à la demande préliminaire peut être mise en cache. Pour définir cet en-tête, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Fonctionnement de CORS

Cette section décrit ce qui se produit dans une demande [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages http.

* CORS n’est **pas** une fonctionnalité de sécurité. CORS est une norme W3C qui permet à un serveur d’assouplir la stratégie de même origine.
  * Par exemple, un acteur malveillant peut utiliser l’option [empêcher les scripts inter-sites (XSS)](xref:security/cross-site-scripting) sur votre site et exécuter une requête intersite vers son site Active cors pour voler des informations.
* Votre API n’est pas plus sûre en autorisant CORS.
  * C’est au client (navigateur) d’appliquer CORS. Le serveur exécute la requête et retourne la réponse, c’est le client qui retourne une erreur et bloque la réponse. Par exemple, l’un des outils suivants affiche la réponse du serveur :
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Un navigateur Web en entrant l’URL dans la barre d’adresses.
* C’est un moyen pour un serveur d’autoriser les navigateurs à exécuter une requête d’API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) ou [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) Cross-Origin qui serait sinon interdite.
  * Les navigateurs (sans CORS) ne peuvent pas effectuer de demandes Cross-Origin. Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) était utilisé pour contourner cette restriction. JSONP n’utilise pas XHR, elle utilise la `<script>` balise pour recevoir la réponse. Les scripts sont autorisés à être chargés sur plusieurs origines.

La [spécification cors](https://www.w3.org/TR/cors/) a introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes Cross-Origin. Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes Cross-Origin. Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.

Voici un exemple de demande Cross-Origin. L' `Origin` en-tête fournit le domaine du site qui effectue la demande. L' `Origin` en-tête est obligatoire et doit être différent de l’hôte.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Si le serveur autorise la demande, il définit l' `Access-Control-Allow-Origin` en-tête dans la réponse. La valeur de cet en-tête correspond à l' `Origin` en-tête de la demande ou à la valeur du caractère générique `"*"` , ce qui signifie que toute origine est autorisée :

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Si la réponse n’inclut pas l' `Access-Control-Allow-Origin` en-tête, la demande Cross-Origin échoue. Plus précisément, le navigateur n’autorise pas la demande. Même si le serveur retourne une réponse correcte, le navigateur ne rend pas la réponse disponible pour l’application cliente.

<a name="test"></a>

## <a name="test-cors"></a>Tester CORS

Pour tester CORS :

1. [Créez un projet d’API](xref:tutorials/first-web-api). Vous pouvez également [Télécharger l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors).
1. Activez CORS à l’aide de l’une des approches décrites dans ce document. Par exemple :

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` doit uniquement être utilisé pour tester un exemple d’application semblable à l' [exemple de code de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Créez un projet d’application Web ( Razor pages ou Mvc). L’exemple utilise des Razor pages. Vous pouvez créer l’application Web dans la même solution que le projet d’API.
1. Ajoutez le code en surbrillance suivant au fichier *index. cshtml* :

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Dans le code précédent, remplacez `url: 'https://<web app>.azurewebsites.net/api/values/1',` par l’URL de l’application déployée.
1. Déployez le projet API. Par exemple, [déployez sur Azure](xref:host-and-deploy/azure-apps/index).
1. Exécutez les Razor pages ou l’application MVC à partir du bureau, puis cliquez sur le bouton **test** . Utilisez les outils F12 pour passer en revue les messages d’erreur.
1. Supprimez l’origine localhost de `WithOrigins` et déployez l’application. Vous pouvez également exécuter l’application cliente avec un autre port. Par exemple, exécutez à partir de Visual Studio.
1. Testez avec l’application cliente. Les échecs CORS retournent une erreur, mais le message d’erreur n’est pas disponible pour JavaScript. Utilisez l’onglet Console des outils F12 pour afficher l’erreur. En fonction du navigateur, vous recevez une erreur (dans la console outils F12) semblable à ce qui suit :

   * Utilisation de Microsoft Edge :

     **SEC7120 : [CORS] l’origine `https://localhost:44375` n’a pas été trouvée `https://localhost:44375` dans l’en-tête de réponse Access-Control-allow-Origin pour la ressource Cross-Origin à l’adresse `https://webapi.azurewebsites.net/api/values/1`**

   * Utilisation de chrome :

     **L’accès à XMLHttpRequest à `https://webapi.azurewebsites.net/api/values/1` partir de l’origine `https://localhost:44375` a été bloqué par la stratégie cors : aucun en-tête « Access-Control-allow-Origin » n’est présent sur la ressource demandée.**
     
Les points de terminaison compatibles CORS peuvent être testés à l’aide d’un outil tel que [Fiddler](https://www.telerik.com/fiddler) ou [postal](https://www.getpostman.com/). Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l' `Origin` en-tête doit différer de l’hôte recevant la demande. Si la requête n’est pas *une origine croisée* en fonction de la valeur de l' `Origin` en-tête :

* Il n’est pas nécessaire d’utiliser un intergiciel (middleware) CORS pour traiter la requête.
* Les en-têtes CORS ne sont pas retournés dans la réponse.

## <a name="cors-in-iis"></a>CORS dans IIS

Lors du déploiement sur IIS, CORS doit s’exécuter avant l’authentification Windows si le serveur n’est pas configuré pour autoriser l’accès anonyme. Pour prendre en charge ce scénario, le [module cors IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Partage des ressources Cross-Origin (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Prise en main du module IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
