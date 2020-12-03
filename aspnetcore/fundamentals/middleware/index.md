---
title: Intergiciel (middleware) ASP.NET Core
author: rick-anderson
description: Découvrez l’intergiciel (middleware) ASP.NET Core et le pipeline de requête.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: bdeccf81a3bb620c2e1fe15a798d5a83375842c8
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556539"
---
# <a name="aspnet-core-middleware"></a>Intergiciel (middleware) ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)

Un middleware est un logiciel qui est assemblé dans un pipeline d’application pour gérer les requêtes et les réponses. Chaque composant :

* Choisit de passer la requête au composant suivant dans le pipeline.
* Peut travailler avant et après le composant suivant dans le pipeline.

Les délégués de requête sont utilisés pour créer le pipeline de requête. Les délégués de requête gèrent chaque requête HTTP.

Les délégués de requête sont configurés à l’aide des méthodes d’extension <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> et <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>. Chaque délégué de requête peut être spécifié inline comme méthode anonyme (appelée intergiciel inline) ou peut être défini dans une classe réutilisable. Ces classes réutilisables et les méthodes anonymes inline sont des *middlewares*, également appelés *composants de middleware*. Chaque composant de middleware du pipeline de requête est chargé d’appeler le composant suivant du pipeline ou de court-circuiter le pipeline. Lorsqu’un middleware effectue un court-circuit, on parle de *middleware terminal*, car il empêche tout autre middleware de traiter la requête.

<xref:migration/http-modules> explique la différence entre les pipelines de requêtes dans ASP.NET Core et ASP.NET 4.x, puis fournit d’autres exemples de middlewares.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Créer un pipeline de middleware avec IApplicationBuilder

Le pipeline de requête ASP.NET Core est composé d’une séquence de délégués de requête, appelés l’un après l’autre. Le diagramme suivant illustre le concept. Le thread d’exécution suit les flèches noires.

![Modèle de traitement des requêtes montrant une requête qui arrive et qui est traitée par trois middlewares, puis la réponse qui quitte l’application. Chaque intergiciel exécute sa logique et transmet la requête à l’intergiciel suivant à l’instruction next(). Une fois que le troisième middleware a traité la requête, celle-ci repasse par les deux middlewares précédents dans l’ordre inverse pour être à nouveau traitée après ses instructions next(), avant de quitter l’application sous forme de réponse au client.](index/_static/request-delegate-pipeline.png)

Chaque délégué peut effectuer des opérations avant et après le délégué suivant. Les délégués de gestion des exceptions doivent être appelés tôt dans le pipeline pour qu’ils puissent intercepter les exceptions qui se produisent dans les phases ultérieures du pipeline.

L’application ASP.NET Core la plus simple possible définit un seul délégué de requête qui gère toutes les requêtes. Ce cas ne fait pas appel à un pipeline de requête réel. À la place, une seule fonction anonyme est appelée en réponse à chaque requête HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Chaînez plusieurs délégués de requête avec <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>. Le paramètre `next` représente le délégué suivant dans le pipeline. Vous pouvez court-circuiter le pipeline en n’appelant *pas* le paramètre *next*. Vous pouvez généralement effectuer des actions à la fois avant et après le délégué suivant, comme illustré dans cet exemple :

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Quand un délégué ne passe pas une requête au délégué suivant, on parle alors de *court-circuit du pipeline de requête*. Un court-circuit est souvent souhaitable car il évite le travail inutile. Par exemple, le [middleware Fichier statique](xref:fundamentals/static-files) peut agir en tant que *middleware terminal* en traitant une requête pour un fichier statique et en court-circuitant le reste du pipeline. Le middleware ajouté au pipeline avant de le middleware qui met fin à la poursuite du traitement traite tout de même le code après les instructions `next.Invoke`. Toutefois, consultez l’avertissement suivant à propos de la tentative d’écriture sur une réponse qui a déjà été envoyée.

> [!WARNING]
> N’appelez pas `next.Invoke` une fois que la réponse a été envoyée au client. Les changements apportés à <xref:Microsoft.AspNetCore.Http.HttpResponse> après le démarrage de la réponse lèvent une exception. Par exemple, [la définition d’en-têtes et d’un code d’État lève une exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started). Écrire dans le corps de la réponse après avoir appelé `next` :
>
> * Peut entraîner une violation de protocole. Par exemple, écrire plus que le `Content-Length` indiqué.
> * Peut altérer le format du corps. Par exemple, l’écriture d’un pied de page HTML dans un fichier CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> est un indice utile pour indiquer si les en-têtes ont été envoyés ou si le corps a fait l’objet d’écritures.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> les délégués ne reçoivent pas de `next` paramètre. Le premier `Run` délégué est toujours terminal et met fin au pipeline. `Run` est une convention. Certains composants de l’intergiciel (middleware) peuvent exposer des `Run[Middleware]` méthodes qui s’exécutent à la fin du pipeline :

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Dans l’exemple précédent, le `Run` délégué écrit `"Hello from 2nd delegate."` dans la réponse, puis termine le pipeline. Si un `Use` autre `Run` délégué ou est ajouté après le `Run` délégué, il n’est pas appelé.

<a name="order"></a>

## <a name="middleware-order"></a>Ordre des intergiciels (middleware)

Le diagramme suivant illustre le pipeline de traitement des demandes complet pour ASP.NET Core les applications MVC et Razor pages. Vous pouvez voir comment, dans une application classique, les intergiciels existants sont triés et où les intergiciels personnalisés sont ajoutés. Vous avez le contrôle total sur la manière de réorganiser les intergiciels existants ou d’injecter de nouvelles intergiciels personnalisées si nécessaire pour vos scénarios.

![Pipeline de l’intergiciel (middleware) ASP.NET Core](index/_static/middleware-pipeline.svg)

L’intergiciel (middleware) du **point de terminaison** dans le diagramme précédent exécute le pipeline de filtre pour le type d’application correspondant &mdash; MVC ou les Razor pages.

![Pipeline de filtre ASP.NET Core](index/_static/mvc-endpoint.svg)

L’ordre dans lequel les composants de middleware sont ajoutés dans la méthode `Startup.Configure` définit l’ordre dans lequel ils sont appelés sur les requêtes et l’ordre inverse pour la réponse. L’ordre est **essentiel** pour la sécurité, les performances et les fonctionnalités.

La `Startup.Configure` méthode suivante ajoute des composants d’intergiciel (middleware) liés à la sécurité dans l’ordre recommandé par défaut :

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

Dans le code précédent :

* Les intergiciels (middleware) qui ne sont pas ajoutés lors de la création d’une nouvelle application Web avec des [comptes d’utilisateurs individuels](xref:security/authentication/identity) sont commentés.
* Tous les intergiciels (middleware) ne doivent pas être placés dans cet ordre exact, mais beaucoup d’entre eux. Par exemple :
  * `UseCors`, `UseAuthentication` et `UseAuthorization` doivent être placés dans l’ordre indiqué.
  * `UseCors``UseResponseCaching`la valeur doit être antérieure à [la suite de ce bogue](https://github.com/dotnet/aspnetcore/issues/23218).

Dans certains scénarios, l’intergiciel (middleware) aura un ordre différent. Par exemple, la mise en cache et le classement de la compression sont spécifiques au scénario et il existe plusieurs ordonnances valides. Par exemple :

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

Avec le code précédent, l’UC peut être enregistrée en mettant en cache la réponse compressée, mais vous risquez de mettre en cache plusieurs représentations d’une ressource à l’aide de différents algorithmes de compression tels que gzip ou brotli.

L’ordre suivant combine des fichiers statiques pour autoriser la mise en cache des fichiers statiques compressés :

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
app.UseStaticFiles();
```

La méthode `Startup.Configure` suivante ajoute des composants middleware utiles pour les scénarios d’application courants :

1. Gestion des erreurs/exceptions
   * Quand l’application s’exécute dans l’environnement de développement :
     * Le middleware Page d’exceptions du développeur (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) signale des erreurs de runtime de l’application.
     * L’intergiciel (middleware) de page d’erreur de base de données signale des erreurs d’exécution.
   * Quand l’application s’exécute dans l’environnement de production :
     * Le middleware Gestionnaire d'exceptions (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) intercepte des exceptions levées dans les middlewares suivants.
     * Le middleware Protocole HSTS (HTTP Strict Transport Security) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) ajoute l’en-tête `Strict-Transport-Security`.
1. Le middleware Redirection HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirige les requêtes HTTP vers HTTPS.
1. Le middleware Fichier statique (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) retourne des fichiers statiques et court-circuite tout traitement supplémentaire de la requête.
1. Cookie L’intergiciel ( <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A> ) de stratégie convertit l’application en RGPD (union Règlement général sur la protection des données européenne).
1. Intergiciel (middleware <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ) de routage () pour acheminer les demandes.
1. Le middleware Authentification (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) tente d’authentifier l’utilisateur avant qu’il ne soit autorisé à accéder aux ressources sécurisées.
1. L’intergiciel () d’autorisation <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> autorise un utilisateur à accéder à des ressources sécurisées.
1. Le middleware Session (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) établit et maintient l’état de la session. Si l’application utilise l’état de session, appelez l’intergiciel (middleware) de session après l’intergiciel (middleware) Cookie de stratégie et avant l’intergiciel (middleware) Mvc.
1. Intergiciel (middleware) de routage des points de terminaison ( <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> avec <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A> ) pour ajouter des Razor points de terminaison de pages au pipeline de requête.

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

Dans l’exemple de code précédent, chaque méthode d’extension d’intergiciel (middleware) est exposée sur <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> à travers l’espace de noms <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> est le premier composant d’intergiciel ajouté au pipeline. Par conséquent, le middleware Gestion des exceptions intercepte toutes les exceptions qui se produisent dans les appels ultérieurs.

Le middleware Fichier statique est appelé tôt dans le pipeline pour qu’il puisse gérer les requêtes et procéder au court-circuit sans passer par les composants restants. Le middleware de fichiers statiques **ne fournit aucune** vérification d’autorisation. Tous les fichiers pris en charge par l’intergiciel (middleware) de fichiers statiques, y compris ceux qui se trouvent sous *wwwroot*, sont disponibles publiquement. Pour obtenir une approche permettant de sécuriser les fichiers statiques, consultez <xref:fundamentals/static-files>.

Si la requête n’est pas gérée par le middleware Fichier statique, elle est transmise au middleware Authentification (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), qui effectue l’authentification. Le middleware Authentification ne court-circuite pas les requêtes non authentifiées. Bien que l’intergiciel d’authentification authentifie les demandes, l’autorisation (et le rejet) se produit uniquement lorsque MVC sélectionne une Razor page ou un contrôleur MVC et une action spécifiques.

L’exemple suivant montre un ordre de middlewares où les requêtes pour les fichiers statiques sont gérées par le middleware Fichier statique avant le middleware Compression de la réponse. Les fichiers statiques ne sont pas compressés avec cet ordre de middlewares. Les Razor réponses de pages peuvent être compressées.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseRouting();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

Pour les applications à page unique (SPAs), l’intergiciel (middleware) SPA <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> vient généralement en dernier dans le pipeline de l’intergiciel (middleware). L’intergiciel (middleware) SPA est le dernier :

* Pour permettre à tous les autres intergiciels de répondre d’abord aux demandes correspondantes.
* Pour autoriser l’exécution de la fonction de routage de l’application serveur pour tous les itinéraires non reconnus par l’application serveur.

Pour plus d’informations sur les modèles de projet [rereact](xref:spa/react) et [angulaire](xref:spa/angular) , consultez les guides.

### <a name="forwarded-headers-middleware-order"></a>Ordre intergiciel des en-têtes transférés

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a>Créer une branche pour le pipeline de l’intergiciel

Les extensions <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> sont utilisées comme convention pour créer une branche dans le pipeline. `Map` crée une branche dans le pipeline de requête en fonction des correspondances du chemin de requête donné. Si le chemin de requête commence par le chemin donné, la branche est exécutée.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

Le tableau suivant présente les requêtes et les réponses de `http://localhost:1234` avec le code précédent.

| Requête             | response                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Quand `Map` est utilisé, les segments de chemin mis en correspondance sont supprimés de `HttpRequest.Path` et ajoutés à `HttpRequest.PathBase` pour chaque requête.

`Map` prend en charge l’imbrication, par exemple :

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` peut également faire correspondre plusieurs segments à la fois :

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> crée une branche dans le pipeline de requête en fonction du résultat du prédicat donné. Un prédicat de type `Func<HttpContext, bool>` peut être utilisé pour mapper les requêtes à une nouvelle branche du pipeline. Dans l’exemple suivant, un prédicat est utilisé pour détecter la présence d’une variable de chaîne de requête `branch` :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

Le tableau suivant présente les requêtes et les réponses de `http://localhost:1234` avec le code précédent :

| Requête                       | response                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> branche également le pipeline de requêtes en fonction du résultat du prédicat donné. Contrairement à `MapWhen` , cette branche est rejointe au pipeline principal s’il n’y a pas de court-circuit ou qu’il contient un intergiciel (middleware) de terminal :

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=18-19)]

Dans l’exemple précédent, une réponse « hello from principal pipeline ». est écrit pour toutes les demandes. Si la requête comprend une variable de chaîne de requête `branch` , sa valeur est consignée avant que le pipeline principal soit rejoint.

## <a name="built-in-middleware"></a>Intergiciels (middleware) intégrés

ASP.NET Core est fourni avec les composants de middleware suivant. La colonne *Ordre* fournit des notes sur l’emplacement du middleware dans le pipeline de traitement de la requête et sur les conditions dans lesquelles le middleware peut mettre fin au traitement de la requête. Lorsqu’un middleware court-circuite le pipeline de traitement de la requête et empêche tout middleware en aval de traiter une requête, on parle de *middleware terminal*. Pour plus d’informations sur le court-circuit, consultez la section [Créer un pipeline de middlewares avec IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Intergiciel (middleware) | Description | Commande |
| ---------- | ----------- | ----- |
| [Authentification](xref:security/authentication/identity) | Prend en charge l’authentification. | Avant que `HttpContext.User` ne soit nécessaire. Terminal pour les rappels OAuth. |
| [Autorisation](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | Fournit la prise en charge des autorisations. | Immédiatement après l’intergiciel (middleware) d’authentification. |
| [Cookie Renvoi](xref:security/gdpr) | Effectue le suivi du consentement des utilisateurs pour le stockage des informations personnelles et applique les normes minimales pour les cookie champs, tels que `secure` et `SameSite` . | Avant l’intergiciel (middleware) qui émet cookie s. Exemples : authentification, session, MVC (TempData). |
| [CORS](xref:security/cors) | Configure le partage des ressources cross-origin (CORS). | Avant les composants qui utilisent CORS. `UseCors``UseResponseCaching`la valeur doit être antérieure à [la suite de ce bogue](https://github.com/dotnet/aspnetcore/issues/23218).|
| [Diagnostics](xref:fundamentals/error-handling) | Plusieurs intergiciels distincts qui fournissent une page d’exception de développeur, la gestion des exceptions, les pages de codes d’État et la page Web par défaut pour les nouvelles applications. | Avant les composants qui génèrent des erreurs. Terminal pour les exceptions ou service de la page Web par défaut pour les nouvelles applications. |
| [En-têtes transférés](xref:host-and-deploy/proxy-load-balancer) | Transfère les en-têtes en proxy vers la requête actuelle. | Avant les composants qui consomment les champs mis à jour. Exemples : schéma, hôte, IP du client, méthode. |
| [Contrôle d’intégrité](xref:host-and-deploy/health-checks) | Contrôle l’intégrité d’une application ASP.NET Core et de ses dépendances, notamment la disponibilité de la base de données. | Terminal si une requête correspond à un point de terminaison de contrôle d’intégrité. |
| [Propagation d’en-tête](xref:fundamentals/http-requests#header-propagation-middleware) | Propage les en-têtes HTTP de la demande entrante aux demandes du client HTTP sortantes. |
| [Remplacement de la méthode HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Autorise une requête POST entrante à remplacer la méthode. | Avant les composants qui consomment la méthode mise à jour. |
| [Redirection HTTPs](xref:security/enforcing-ssl#require-https) | Redirige toutes les requêtes HTTP vers HTTPS. | Avant les composants qui consomment l’URL. |
| [HSTS (HTTP Strict Transport Security)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware d’amélioration de la sécurité qui ajoute un en-tête de réponse spécial. | Avant l’envoi des réponses et après les composants qui modifient les requêtes. Exemples : en-têtes transférés, réécriture d’URL. |
| [MVC](xref:mvc/overview) | Traite les demandes avec MVC/ Razor pages. | Terminal si une requête correspond à un itinéraire. |
| [OWIN](xref:fundamentals/owin) | Interopérabilité avec le middleware, les serveurs et les applications OWIN. | Terminal si le middleware OWIN traite entièrement la requête. |
| [Mise en cache des réponses](xref:performance/caching/middleware) | Prend en charge la mise en cache des réponses. | Avant les composants qui nécessitent la mise en cache. `UseCORS` doit être antérieur `UseResponseCaching` .|
| [Compression de la réponse](xref:performance/response-compression) | Prend en charge la compression des réponses. | Avant les composants qui nécessitent la compression. |
| [Localisation des requêtes](xref:fundamentals/localization) | Prend en charge la localisation. | Avant la localisation des composants sensibles. |
| [Routage de point de terminaison](xref:fundamentals/routing) | Définit et contraint des routes de requête. | Terminal pour les routes correspondantes. |
| [SPA](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | Gère toutes les demandes à partir de ce point dans la chaîne de l’intergiciel (middleware) en retournant la page par défaut pour l’application à page unique (SPA) | À la fin de la chaîne, les autres middlewares pour traiter les fichiers statiques, les actions MVC, etc., sont prioritaires.|
| [Session](xref:fundamentals/app-state) | Prend en charge la gestion des sessions utilisateur. | Avant les composants qui nécessitent la session. | 
| [Fichiers statiques](xref:fundamentals/static-files) | Prend en charge le traitement des fichiers statiques et l’exploration des répertoires. | Terminal si une requête correspond à un fichier. |
| [URL Rewrite](xref:fundamentals/url-rewriting) | Prend en charge la réécriture d’URL et la redirection des requêtes. | Avant les composants qui consomment l’URL. |
| [WebSockets](xref:fundamentals/websockets) | Autorise le protocole WebSockets. | Avant les composants qui sont nécessaires pour accepter les requêtes WebSocket. |

## <a name="additional-resources"></a>Ressources supplémentaires

* Les [options de durée de vie et d’inscription](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contiennent un exemple complet d’intergiciel (middleware) avec des services de durée de vie *délimités*, *transitoires* et *singletons* .
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)

Un middleware est un logiciel qui est assemblé dans un pipeline d’application pour gérer les requêtes et les réponses. Chaque composant :

* Choisit de passer la requête au composant suivant dans le pipeline.
* Peut travailler avant et après le composant suivant dans le pipeline.

Les délégués de requête sont utilisés pour créer le pipeline de requête. Les délégués de requête gèrent chaque requête HTTP.

Les délégués de requête sont configurés à l’aide des méthodes d’extension <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> et <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>. Chaque délégué de requête peut être spécifié inline comme méthode anonyme (appelée intergiciel inline) ou peut être défini dans une classe réutilisable. Ces classes réutilisables et les méthodes anonymes inline sont des *middlewares*, également appelés *composants de middleware*. Chaque composant de middleware du pipeline de requête est chargé d’appeler le composant suivant du pipeline ou de court-circuiter le pipeline. Lorsqu’un middleware effectue un court-circuit, on parle de *middleware terminal*, car il empêche tout autre middleware de traiter la requête.

<xref:migration/http-modules> explique la différence entre les pipelines de requêtes dans ASP.NET Core et ASP.NET 4.x, puis fournit d’autres exemples de middlewares.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Créer un pipeline de middleware avec IApplicationBuilder

Le pipeline de requête ASP.NET Core est composé d’une séquence de délégués de requête, appelés l’un après l’autre. Le diagramme suivant illustre le concept. Le thread d’exécution suit les flèches noires.

![Modèle de traitement des requêtes montrant une requête qui arrive et qui est traitée par trois middlewares, puis la réponse qui quitte l’application. Chaque intergiciel exécute sa logique et transmet la requête à l’intergiciel suivant à l’instruction next(). Une fois que le troisième middleware a traité la requête, celle-ci repasse par les deux middlewares précédents dans l’ordre inverse pour être à nouveau traitée après ses instructions next(), avant de quitter l’application sous forme de réponse au client.](index/_static/request-delegate-pipeline.png)

Chaque délégué peut effectuer des opérations avant et après le délégué suivant. Les délégués de gestion des exceptions doivent être appelés tôt dans le pipeline pour qu’ils puissent intercepter les exceptions qui se produisent dans les phases ultérieures du pipeline.

L’application ASP.NET Core la plus simple possible définit un seul délégué de requête qui gère toutes les requêtes. Ce cas ne fait pas appel à un pipeline de requête réel. À la place, une seule fonction anonyme est appelée en réponse à chaque requête HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Le premier délégué <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> termine le pipeline.

Chaînez plusieurs délégués de requête avec <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>. Le paramètre `next` représente le délégué suivant dans le pipeline. Vous pouvez court-circuiter le pipeline en n’appelant *pas* le paramètre *next*. Vous pouvez généralement effectuer des actions à la fois avant et après le délégué suivant, comme illustré dans cet exemple :

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Quand un délégué ne passe pas une requête au délégué suivant, on parle alors de *court-circuit du pipeline de requête*. Un court-circuit est souvent souhaitable car il évite le travail inutile. Par exemple, le [middleware Fichier statique](xref:fundamentals/static-files) peut agir en tant que *middleware terminal* en traitant une requête pour un fichier statique et en court-circuitant le reste du pipeline. Le middleware ajouté au pipeline avant de le middleware qui met fin à la poursuite du traitement traite tout de même le code après les instructions `next.Invoke`. Toutefois, consultez l’avertissement suivant à propos de la tentative d’écriture sur une réponse qui a déjà été envoyée.

> [!WARNING]
> N’appelez pas `next.Invoke` une fois que la réponse a été envoyée au client. Les changements apportés à <xref:Microsoft.AspNetCore.Http.HttpResponse> après le démarrage de la réponse lèvent une exception. Par exemple, les changements comme la définition des en-têtes et du code d’état lèvent une exception. Écrire dans le corps de la réponse après avoir appelé `next` :
>
> * Peut entraîner une violation de protocole. Par exemple, écrire plus que le `Content-Length` indiqué.
> * Peut altérer le format du corps. Par exemple, l’écriture d’un pied de page HTML dans un fichier CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> est un indice utile pour indiquer si les en-têtes ont été envoyés ou si le corps a fait l’objet d’écritures.

<a name="order"></a>

## <a name="middleware-order"></a>Ordre des intergiciels (middleware)

L’ordre dans lequel les composants de middleware sont ajoutés dans la méthode `Startup.Configure` définit l’ordre dans lequel ils sont appelés sur les requêtes et l’ordre inverse pour la réponse. L’ordre est **essentiel** pour la sécurité, les performances et les fonctionnalités.

La `Startup.Configure` méthode suivante ajoute les composants de l’intergiciel (middleware) associés à la sécurité dans l’ordre recommandé :

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

Dans le code précédent :

* Les intergiciels (middleware) qui ne sont pas ajoutés lors de la création d’une nouvelle application Web avec des [comptes d’utilisateurs individuels](xref:security/authentication/identity) sont commentés.
* Tous les intergiciels (middleware) ne doivent pas être placés dans cet ordre exact, mais beaucoup d’entre eux. Par exemple, `UseCors` et `UseAuthentication` doivent être placés dans l’ordre indiqué.

La méthode `Startup.Configure` suivante ajoute des composants middleware utiles pour les scénarios d’application courants :

1. Gestion des erreurs/exceptions
   * Quand l’application s’exécute dans l’environnement de développement :
     * Le middleware Page d’exceptions du développeur (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) signale des erreurs de runtime de l’application.
     * Le middleware Page d’erreur de base de données (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) signale des erreurs de runtime de la base de données.
   * Quand l’application s’exécute dans l’environnement de production :
     * Le middleware Gestionnaire d'exceptions (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) intercepte des exceptions levées dans les middlewares suivants.
     * Le middleware Protocole HSTS (HTTP Strict Transport Security) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) ajoute l’en-tête `Strict-Transport-Security`.
1. Le middleware Redirection HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirige les requêtes HTTP vers HTTPS.
1. Le middleware Fichier statique (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) retourne des fichiers statiques et court-circuite tout traitement supplémentaire de la requête.
1. Cookie L’intergiciel ( <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A> ) de stratégie convertit l’application en RGPD (union Règlement général sur la protection des données européenne).
1. Le middleware Authentification (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) tente d’authentifier l’utilisateur avant qu’il ne soit autorisé à accéder aux ressources sécurisées.
1. Le middleware Session (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) établit et maintient l’état de la session. Si l’application utilise l’état de session, appelez l’intergiciel (middleware) de session après l’intergiciel (middleware) Cookie de stratégie et avant l’intergiciel (middleware) Mvc.
1. MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) pour ajouter MVC au pipeline de requête.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

Dans l’exemple de code précédent, chaque méthode d’extension d’intergiciel (middleware) est exposée sur <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> à travers l’espace de noms <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> est le premier composant d’intergiciel ajouté au pipeline. Par conséquent, le middleware Gestion des exceptions intercepte toutes les exceptions qui se produisent dans les appels ultérieurs.

Le middleware Fichier statique est appelé tôt dans le pipeline pour qu’il puisse gérer les requêtes et procéder au court-circuit sans passer par les composants restants. Le middleware de fichiers statiques **ne fournit aucune** vérification d’autorisation. Tous les fichiers pris en charge par l’intergiciel (middleware) de fichiers statiques, y compris ceux qui se trouvent sous *wwwroot*, sont disponibles publiquement. Pour obtenir une approche permettant de sécuriser les fichiers statiques, consultez <xref:fundamentals/static-files>.

Si la requête n’est pas gérée par le middleware Fichier statique, elle est transmise au middleware Authentification (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), qui effectue l’authentification. Le middleware Authentification ne court-circuite pas les requêtes non authentifiées. Bien que l’intergiciel d’authentification authentifie les demandes, l’autorisation (et le rejet) se produit uniquement lorsque MVC sélectionne une Razor page ou un contrôleur MVC et une action spécifiques.

L’exemple suivant montre un ordre de middlewares où les requêtes pour les fichiers statiques sont gérées par le middleware Fichier statique avant le middleware Compression de la réponse. Les fichiers statiques ne sont pas compressés avec cet ordre de middlewares. Les réponses MVC de <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> peuvent être compressées.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Use, Run et Map

Configurez le pipeline HTTP avec <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> et <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>. La méthode `Use` peut court-circuiter le pipeline (autrement dit, si elle n’appelle pas un délégué de requête `next`). `Run` est une convention, et certains composants d’intergiciel peuvent exposer des méthodes `Run[Middleware]` qui s’exécutent à la fin du pipeline.

Les extensions <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> sont utilisées comme convention pour créer une branche dans le pipeline. `Map` crée une branche dans le pipeline de requête en fonction des correspondances du chemin de requête donné. Si le chemin de requête commence par le chemin donné, la branche est exécutée.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

Le tableau suivant présente les requêtes et les réponses de `http://localhost:1234` avec le code précédent.

| Requête             | response                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Quand `Map` est utilisé, les segments de chemin mis en correspondance sont supprimés de `HttpRequest.Path` et ajoutés à `HttpRequest.PathBase` pour chaque requête.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> crée une branche dans le pipeline de requête en fonction du résultat du prédicat donné. Un prédicat de type `Func<HttpContext, bool>` peut être utilisé pour mapper les requêtes à une nouvelle branche du pipeline. Dans l’exemple suivant, un prédicat est utilisé pour détecter la présence d’une variable de chaîne de requête `branch` :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

Le tableau suivant présente les requêtes et les réponses de `http://localhost:1234` avec le code précédent.

| Requête                       | response                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

`Map` prend en charge l’imbrication, par exemple :

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` peut également faire correspondre plusieurs segments à la fois :

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Intergiciels (middleware) intégrés

ASP.NET Core est fourni avec les composants de middleware suivant. La colonne *Ordre* fournit des notes sur l’emplacement du middleware dans le pipeline de traitement de la requête et sur les conditions dans lesquelles le middleware peut mettre fin au traitement de la requête. Lorsqu’un middleware court-circuite le pipeline de traitement de la requête et empêche tout middleware en aval de traiter une requête, on parle de *middleware terminal*. Pour plus d’informations sur le court-circuit, consultez la section [Créer un pipeline de middlewares avec IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Intergiciel (middleware) | Description | Commande |
| ---------- | ----------- | ----- |
| [Authentification](xref:security/authentication/identity) | Prend en charge l’authentification. | Avant que `HttpContext.User` ne soit nécessaire. Terminal pour les rappels OAuth. |
| [Cookie Renvoi](xref:security/gdpr) | Effectue le suivi du consentement des utilisateurs pour le stockage des informations personnelles et applique les normes minimales pour les cookie champs, tels que `secure` et `SameSite` . | Avant l’intergiciel (middleware) qui émet cookie s. Exemples : authentification, session, MVC (TempData). |
| [CORS](xref:security/cors) | Configure le partage des ressources cross-origin (CORS). | Avant les composants qui utilisent CORS. |
| [Diagnostics](xref:fundamentals/error-handling) | Plusieurs intergiciels distincts qui fournissent une page d’exception de développeur, la gestion des exceptions, les pages de codes d’État et la page Web par défaut pour les nouvelles applications. | Avant les composants qui génèrent des erreurs. Terminal pour les exceptions ou service de la page Web par défaut pour les nouvelles applications. |
| [En-têtes transférés](xref:host-and-deploy/proxy-load-balancer) | Transfère les en-têtes en proxy vers la requête actuelle. | Avant les composants qui consomment les champs mis à jour. Exemples : schéma, hôte, IP du client, méthode. |
| [Contrôle d’intégrité](xref:host-and-deploy/health-checks) | Contrôle l’intégrité d’une application ASP.NET Core et de ses dépendances, notamment la disponibilité de la base de données. | Terminal si une requête correspond à un point de terminaison de contrôle d’intégrité. |
| [Remplacement de la méthode HTTP](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Autorise une requête POST entrante à remplacer la méthode. | Avant les composants qui consomment la méthode mise à jour. |
| [Redirection HTTPs](xref:security/enforcing-ssl#require-https) | Redirige toutes les requêtes HTTP vers HTTPS. | Avant les composants qui consomment l’URL. |
| [HSTS (HTTP Strict Transport Security)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware d’amélioration de la sécurité qui ajoute un en-tête de réponse spécial. | Avant l’envoi des réponses et après les composants qui modifient les requêtes. Exemples : en-têtes transférés, réécriture d’URL. |
| [MVC](xref:mvc/overview) | Traite les demandes avec MVC/ Razor pages. | Terminal si une requête correspond à un itinéraire. |
| [OWIN](xref:fundamentals/owin) | Interopérabilité avec le middleware, les serveurs et les applications OWIN. | Terminal si le middleware OWIN traite entièrement la requête. |
| [Mise en cache des réponses](xref:performance/caching/middleware) | Prend en charge la mise en cache des réponses. | Avant les composants qui nécessitent la mise en cache. |
| [Compression de la réponse](xref:performance/response-compression) | Prend en charge la compression des réponses. | Avant les composants qui nécessitent la compression. |
| [Localisation des requêtes](xref:fundamentals/localization) | Prend en charge la localisation. | Avant la localisation des composants sensibles. |
| [Routage de point de terminaison](xref:fundamentals/routing) | Définit et contraint des routes de requête. | Terminal pour les routes correspondantes. |
| [Session](xref:fundamentals/app-state) | Prend en charge la gestion des sessions utilisateur. | Avant les composants qui nécessitent la session. |
| [Fichiers statiques](xref:fundamentals/static-files) | Prend en charge le traitement des fichiers statiques et l’exploration des répertoires. | Terminal si une requête correspond à un fichier. |
| [URL Rewrite](xref:fundamentals/url-rewriting) | Prend en charge la réécriture d’URL et la redirection des requêtes. | Avant les composants qui consomment l’URL. |
| [WebSockets](xref:fundamentals/websockets) | Autorise le protocole WebSockets. | Avant les composants qui sont nécessaires pour accepter les requêtes WebSocket. |

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
