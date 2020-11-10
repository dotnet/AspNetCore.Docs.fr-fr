---
title: Routage dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core routage est responsable de la mise en correspondance des requêtes HTTP et de la distribution aux points de terminaison exécutables.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
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
uid: fundamentals/routing
ms.openlocfilehash: e134832ad00b10bb01239afa06acc74d86707af1
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422559"
---
# <a name="routing-in-aspnet-core"></a>Routage dans ASP.NET Core

Par [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)et [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Le routage est responsable de la mise en correspondance des demandes HTTP entrantes et de la distribution de ces requêtes aux points de terminaison exécutables de l’application. Les [points de terminaison](#endpoint) sont les unités de code de gestion des requêtes exécutables de l’application. Les points de terminaison sont définis dans l’application et configurés au démarrage de l’application. Le processus de correspondance de point de terminaison peut extraire des valeurs de l’URL de la demande et fournir ces valeurs pour le traitement des demandes. À l’aide des informations de point de terminaison de l’application, le routage est également en mesure de générer des URL qui mappent aux points de terminaison.

Les applications peuvent configurer le routage à l’aide de :

- Controllers
- Razor Pages
- SignalR
- Services gRPC
- [Intergiciel (middleware](xref:fundamentals/middleware/index) ) prenant en charge les points de terminaison, tels que les [contrôles d’intégrité](xref:host-and-deploy/health-checks).
- Délégués et expressions lambda inscrits avec le routage.

Ce document traite des détails de bas niveau du routage de ASP.NET Core. Pour plus d’informations sur la configuration du routage :

* Pour les contrôleurs, consultez <xref:mvc/controllers/routing> .
* Pour les Razor conventions de pages, consultez <xref:razor-pages/razor-pages-conventions> .

Le système de routage des points de terminaison décrit dans ce document s’applique à ASP.NET Core 3,0 et versions ultérieures. Pour plus d’informations sur le système de routage précédent basé sur <xref:Microsoft.AspNetCore.Routing.IRouter> , sélectionnez la version ASP.NET Core 2,1 à l’aide de l’une des approches suivantes :

* Sélecteur de version d’une version précédente.
* Sélectionnez [ASP.net Core routage 2,1](?view=aspnetcore-2.1).

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Les exemples de téléchargement de ce document sont activés par une `Startup` classe spécifique. Pour exécuter un exemple spécifique, modifiez *Program.cs* pour appeler la `Startup` classe souhaitée.

## <a name="routing-basics"></a>Concepts de base du routage

Tous les modèles de ASP.NET Core incluent le routage dans le code généré. Le routage est inscrit dans le pipeline de l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) dans `Startup.Configure` .

Le code suivant illustre un exemple de routage de base :

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Le routage utilise une paire d’intergiciels (middleware), inscrite par <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> :

* `UseRouting` Ajoute la correspondance d’itinéraire au pipeline de l’intergiciel (middleware). Cet intergiciel (middleware) examine l’ensemble des points de terminaison définis dans l’application et sélectionne la [meilleure correspondance](#urlm) en fonction de la demande.
* `UseEndpoints` Ajoute l’exécution du point de terminaison au pipeline d’intergiciel (middleware). Il exécute le délégué associé au point de terminaison sélectionné.

L’exemple précédent comprend un seul *itinéraire vers le point de terminaison de code* à l’aide de la méthode [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) :

* Quand une `GET` requête HTTP est envoyée à l’URL racine `/` :
  * Le délégué de requête indiqué s’exécute.
  * `Hello World!` est écrit dans la réponse HTTP. Par défaut, l’URL racine `/` est `https://localhost:5001/` .
* Si la méthode de demande n’est pas `GET` ou si l’URL racine n’est pas `/` , aucun itinéraire ne correspond et un http 404 est retourné.

### <a name="endpoint"></a>Point de terminaison

<a name="endpoint"></a>

La `MapGet` méthode est utilisée pour définir un **point de terminaison**. Un point de terminaison est un point qui peut être :

* Sélectionné en faisant correspondre l’URL et la méthode HTTP.
* Exécuté, en exécutant le délégué.

Les points de terminaison qui peuvent être mis en correspondance et exécutés par l’application sont configurés dans `UseEndpoints` . Par exemple, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*> , <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> et les [méthodes similaires](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) connectent les délégués de demande au système de routage.
Des méthodes supplémentaires peuvent être utilisées pour connecter les fonctionnalités de ASP.NET Core Framework au système de routage :
- [Mapper Razor des pages pour les Razor pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [MapControllers pour les contrôleurs](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub \<THub> pour SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService \<TService> pour gRPC](xref:grpc/aspnetcore)

L’exemple suivant illustre le routage avec un modèle d’itinéraire plus sophistiqué :

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

La chaîne `/hello/{name:alpha}` est un **modèle de routage**. Il permet de configurer la mise en correspondance du point de terminaison. Dans ce cas, le modèle correspond à :

* Une URL telle que `/hello/Ryan`
* Tout chemin d’accès d’URL qui commence par `/hello/` , suivi d’une séquence de caractères alphabétiques.  `:alpha` applique une contrainte d’itinéraire qui correspond uniquement aux caractères alphabétiques. Les [contraintes de routage](#route-constraint-reference) sont expliquées plus loin dans ce document.

Le deuxième segment du chemin d’URL `{name:alpha}` :

* Est lié au `name` paramètre.
* Est capturé et stocké dans [HttpRequest. RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).

Le système de routage des points de terminaison décrit dans ce document est nouveau à partir de ASP.NET Core 3,0. Toutefois, toutes les versions de ASP.NET Core prennent en charge le même jeu de fonctionnalités de modèle de routage et de contraintes de routage.

L’exemple suivant illustre le routage avec des [contrôles d’intégrité](xref:host-and-deploy/health-checks) et l’autorisation :

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

L’exemple précédent montre comment :

* L’intergiciel d’autorisation peut être utilisé avec le routage.
* Les points de terminaison peuvent être utilisés pour configurer le comportement d’autorisation.

L' <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> appel ajoute un point de terminaison de contrôle d’intégrité. Le chaînage <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> sur cet appel joint une stratégie d’autorisation au point de terminaison.

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>L’appel de et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> ajoute l’intergiciel (middleware) d’authentification et d’autorisation. Ces intergiciels sont placés entre <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> et `UseEndpoints` afin de pouvoir :

* Consultez le point de terminaison sélectionné par `UseRouting` .
* Appliquez une stratégie d’autorisation avant <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> les distributions au point de terminaison.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Métadonnées de point de terminaison

Dans l’exemple précédent, il existe deux points de terminaison, mais seul le point de terminaison de contrôle d’intégrité a une stratégie d’autorisation attachée. Si la demande correspond au point de terminaison de contrôle d’intégrité, `/healthz` une vérification d’autorisation est effectuée. Cela démontre que des données supplémentaires peuvent être attachées aux points de terminaison. Ces données supplémentaires sont appelées **métadonnées** de point de terminaison :

* Les métadonnées peuvent être traitées par un intergiciel (middleware) qui prend en charge le routage.
* Les métadonnées peuvent être de n’importe quel type .NET.

## <a name="routing-concepts"></a>Concepts de routage

Le système de routage s’appuie sur le pipeline de l’intergiciel (middleware) en ajoutant le puissant concept de **point de terminaison** . Les points de terminaison représentent les unités de la fonctionnalité de l’application qui sont distinctes les unes des autres en termes de routage, d’autorisation et de tout nombre de systèmes de ASP.NET Core.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>Définition de point de terminaison ASP.NET Core

Un point de terminaison de ASP.NET Core est :

* Exécutable : a <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> .
* Extensible : a une collection de [métadonnées](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) .
* Sélectionnable : le cas échéant, contient des [informations de routage](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Énumérable : la collection de points de terminaison peut être listée en extrayant le <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> de [di](xref:fundamentals/dependency-injection).

Le code suivant montre comment récupérer et inspecter le point de terminaison correspondant à la requête actuelle :

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Le point de terminaison, s’il est sélectionné, peut être récupéré à partir du `HttpContext` . Ses propriétés peuvent être inspectées. Les objets de point de terminaison sont immuables et ne peuvent pas être modifiés après la création. Le type de point de terminaison le plus courant est <xref:Microsoft.AspNetCore.Routing.RouteEndpoint> . `RouteEndpoint` contient des informations qui peuvent être sélectionnées par le système de routage.

Dans le code précédent, [application. Utiliser](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configure un [intergiciel (middleware](xref:fundamentals/middleware/index)) en ligne.

<a name="mt"></a>

Le code suivant montre que, en fonction de l’endroit où `app.Use` est appelé dans le pipeline, il se peut qu’il n’y ait pas de point de terminaison :

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

Cet exemple précédent ajoute des `Console.WriteLine` instructions qui indiquent si un point de terminaison a été sélectionné ou non. Par souci de clarté, l’exemple assigne un nom complet au `/` point de terminaison fourni.

L’exécution de ce code avec une URL de `/` affiche :

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

L’exécution de ce code avec d’autres URL affiche :

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Cette sortie illustre ce qui suit :

* Le point de terminaison est toujours null avant que `UseRouting` ne soit appelé.
* Si une correspondance est trouvée, le point de terminaison n’a pas la valeur null entre `UseRouting` et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .
* L' `UseEndpoints` intergiciel est **Terminal** quand une correspondance est trouvée. L' [intergiciel (middleware) Terminal](#tm) est défini plus loin dans ce document.
* L’intergiciel (middleware) après l' `UseEndpoints` exécution uniquement quand aucune correspondance n’est trouvée.

L' `UseRouting` intergiciel utilise la méthode [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) pour attacher le point de terminaison au contexte actuel. Il est possible de remplacer l' `UseRouting` intergiciel par une logique personnalisée tout en bénéficiant des avantages de l’utilisation des points de terminaison. Les points de terminaison sont une primitive de bas niveau, comme l’intergiciel (middleware), et ne sont pas couplés à l’implémentation du routage. La plupart des applications n’ont pas besoin d’être remplacées `UseRouting` par une logique personnalisée.

L' `UseEndpoints` intergiciel (middleware) est conçu pour être utilisé en tandem avec l' `UseRouting` intergiciel (middleware). La logique de base pour exécuter un point de terminaison n’est pas compliquée. Utilisez <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> pour récupérer le point de terminaison, puis appelez sa <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> propriété.

Le code suivant montre comment l’intergiciel (middleware) peut influencer ou réagir au routage :

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

L’exemple précédent illustre deux concepts importants :

* L’intergiciel peut s’exécuter avant `UseRouting` de modifier les données sur lesquelles le routage fonctionne.
    * En général, l’intergiciel s’affiche avant que le routage ne modifie une propriété de la requête, telle que <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> , <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> ou <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> .
* L’intergiciel peut s’exécuter entre `UseRouting` et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> pour traiter les résultats du routage avant l’exécution du point de terminaison.
    * Intergiciel qui s’exécute entre `UseRouting` et `UseEndpoints` :
      * Inspecte généralement les métadonnées pour comprendre les points de terminaison.
      * Prend souvent des décisions de sécurité, comme c’est le cas par `UseAuthorization` et `UseCors` .
    * La combinaison d’intergiciels (middleware) et de métadonnées permet de configurer des stratégies par point de terminaison.

Le code précédent montre un exemple d’un intergiciel (middleware) personnalisé qui prend en charge les stratégies par point de terminaison. L’intergiciel écrit un *Journal d’audit* de l’accès aux données sensibles sur la console. L’intergiciel peut être configuré pour *auditer* un point de terminaison avec les `AuditPolicyAttribute` métadonnées. Cet exemple illustre un modèle d' *abonnement* dans lequel seuls les points de terminaison marqués comme sensibles sont audités. Il est possible de définir cette logique en sens inverse, en auditant tout ce qui n’est pas marqué comme sécurisé, par exemple. Le système de métadonnées du point de terminaison est flexible. Cette logique peut être conçue de la manière qui convient le mieux au cas d’usage.

L’exemple de code précédent est destiné à illustrer les concepts de base des points de terminaison. **L’exemple n’est pas destiné à une utilisation en production**. Une version plus complète d’un intergiciel (middleware) de *journaux d’audit* :

* Connectez-vous à un fichier ou à une base de données.
* Incluez des détails tels que l’utilisateur, l’adresse IP, le nom du point de terminaison sensible, et bien plus encore.

Les métadonnées de la stratégie d’audit `AuditPolicyAttribute` sont définies en tant que `Attribute` pour une utilisation plus facile avec les infrastructures basées sur des classes telles que les contrôleurs et SignalR . Lors de l’utilisation *de l’itinéraire vers le code* :

* Les métadonnées sont jointes à une API de générateur.
* Les infrastructures basées sur des classes incluent tous les attributs de la méthode et de la classe correspondantes lors de la création de points de terminaison.

Les meilleures pratiques pour les types de métadonnées sont de les définir comme des interfaces ou des attributs. Les interfaces et les attributs permettent de réutiliser le code. Le système de métadonnées est flexible et n’impose aucune restriction.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Comparaison entre un intergiciel et un routage de terminaux

L’exemple de code suivant compare l’utilisation de l’intergiciel avec le routage :

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Le style d’intergiciel (middleware) présenté avec est l’intergiciel (middleware) de `Approach 1:` **Terminal**. Il s’agit de l’intergiciel (middleware) Terminal Server, car il effectue une opération de correspondance :

* L’opération de correspondance dans l’exemple précédent concerne `Path == "/"` l’intergiciel et le `Path == "/Movie"` routage.
* Lorsqu’une correspondance est réussie, elle exécute certaines fonctionnalités et retourne, plutôt que d’appeler l' `next` intergiciel (middleware).

Il s’agit de l’intergiciel (middleware) Terminal Server, car il met fin à la recherche, exécute certaines fonctionnalités, puis retourne.

Comparaison entre un intergiciel et un routage de terminaux :
* Les deux approches permettent d’arrêter le pipeline de traitement :
    * L’intergiciel termine le pipeline en retournant plutôt qu’en appelant `next` .
    * Les points de terminaison sont toujours des terminaux.
* L’intergiciel (middleware) Terminal permet de positionner l’intergiciel (middleware) à un emplacement arbitraire dans le pipeline :
    * Les points de terminaison s’exécutent à la position de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .
* L’intergiciel (middleware) Terminal permet au code arbitraire de déterminer quand l’intergiciel correspond à :
    * Le code de correspondance d’itinéraire personnalisé peut être détaillé et difficile à écrire correctement.
    * Le routage fournit des solutions simples pour les applications classiques. La plupart des applications n’ont pas besoin de code de correspondance d’itinéraire personnalisé.
* Interface des points de terminaison avec un intergiciel (middleware) tel que `UseAuthorization` et `UseCors` .
    * L’utilisation d’un intergiciel (middleware) terminal avec `UseAuthorization` ou `UseCors` nécessite une interface manuelle avec le système d’autorisation.

Un [point de terminaison](#endpoint) définit les deux :

* Délégué pour traiter les demandes.
* Collection de métadonnées arbitraires. Les métadonnées sont utilisées pour implémenter des problèmes transversaux en fonction des stratégies et de la configuration attachées à chaque point de terminaison.

L’intergiciel (middleware) terminal peut être un outil efficace, mais peut nécessiter les éléments suivants :

* Une quantité significative de codage et de test.
* Intégration manuelle avec d’autres systèmes pour atteindre le niveau de flexibilité souhaité.

Envisagez l’intégration avec le routage avant d’écrire un intergiciel (middleware) Terminal.

Un intergiciel (middleware) terminal existant qui s’intègre à [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) ou <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> peut généralement être converti en point de terminaison prenant en charge le routage. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) illustre le modèle de routeur-Ware :
* Écrire une méthode d’extension sur <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> .
* Créez un pipeline d’intergiciel (middleware) imbriqué à l’aide de <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*> .
* Attachez l’intergiciel au nouveau pipeline. Dans ce cas, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> pipeline d’intergiciel (middleware) dans un <xref:Microsoft.AspNetCore.Http.RequestDelegate> .
* Appelez `Map` et fournissez le nouveau pipeline d’intergiciel (middleware).
* Retourne l’objet générateur fourni par `Map` à partir de la méthode d’extension.

Le code suivant illustre l’utilisation de [MapHealthChecks](xref:host-and-deploy/health-checks):

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

L’exemple précédent montre pourquoi le retour de l’objet générateur est important. Le retour de l’objet générateur permet au développeur de l’application de configurer des stratégies telles que l’autorisation pour le point de terminaison. Dans cet exemple, l’intergiciel (middleware) des contrôles d’intégrité n’a pas d’intégration directe avec le système d’autorisation.

Le système de métadonnées a été créé en réponse aux problèmes rencontrés par les auteurs d’extensibilité à l’aide de l’intergiciel (middleware) Terminal. Il est difficile pour chaque intergiciel d’implémenter sa propre intégration au système d’autorisation.

<a name="urlm"></a>

### <a name="url-matching"></a>Correspondance d’URL

* Est le processus par lequel le routage correspond à une requête entrante à un [point de terminaison](#endpoint).
* Est basé sur les données du chemin d’URL et des en-têtes.
* Peut être étendu pour prendre en compte toutes les données de la requête.

Lors de l’exécution d’un intergiciel (middleware) de routage, il définit un `Endpoint` et des valeurs de route à une [fonctionnalité de demande](xref:fundamentals/request-features) sur le <xref:Microsoft.AspNetCore.Http.HttpContext> à partir de la requête actuelle :

* L’appel de [HttpContext. GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) obtient le point de terminaison.
* `HttpRequest.RouteValues` récupère la collection de valeurs d’itinéraire.

L' [intergiciel](xref:fundamentals/middleware/index) s’exécutant après l’intergiciel (middleware) de routage peut inspecter le point de terminaison et agir. Par exemple, un intergiciel (middleware) d’autorisation peut interroger la collection de métadonnées du point de terminaison pour une stratégie d’autorisation. Une fois que tous les intergiciels dans le pipeline de traitement de requêtes sont exécutés, le délégué du point de terminaison sélectionné est appelé.

Le système de routage dans le routage de point de terminaison est responsable de toutes les décisions de distribution. Étant donné que l’intergiciel applique des stratégies basées sur le point de terminaison sélectionné, il est important que :

* Toute décision qui peut affecter la distribution ou l’application de stratégies de sécurité est effectuée à l’intérieur du système de routage.

> [!WARNING]
> Pour la compatibilité descendante, lors de l’exécution d’un délégué de point de terminaison de contrôleur ou Razor de pages, les propriétés de [RouteContext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) sont définies sur les valeurs appropriées en fonction du traitement de la demande effectué jusqu’à présent.
>
> Le `RouteContext` type sera marqué comme obsolète dans une version ultérieure :
>
> * Migrez `RouteData.Values` vers `HttpRequest.RouteValues` .
> * Migrez `RouteData.DataTokens` pour récupérer [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) à partir des métadonnées de point de terminaison.

La correspondance d’URL fonctionne dans un ensemble configurable de phases. Dans chaque phase, la sortie est un ensemble de correspondances. L’ensemble des correspondances peut être réduit davantage à la phase suivante. L’implémentation du routage ne garantit pas un ordre de traitement pour les points de terminaison correspondants. **Toutes les** correspondances possibles sont traitées en même temps. Les phases de correspondance d’URL se produisent dans l’ordre suivant. ASP.NET Core :

1. Traite le chemin d’accès de l’URL par rapport à l’ensemble des points de terminaison et de leurs modèles d’itinéraire, en collectant **toutes** les correspondances.
1. Prend la liste précédente et supprime les correspondances qui échouent avec les contraintes d’itinéraire appliquées.
1. Prend la liste précédente et supprime les correspondances qui échouent à l’ensemble des instances [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) .
1. Utilise [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) pour prendre une décision finale de la liste précédente.

La liste des points de terminaison est hiérarchisée en fonction des éléments suivants :

* [RouteEndpoint. Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [Priorité du modèle de routage](#rtp)

Tous les points de terminaison correspondants sont traités dans chaque phase jusqu’à ce que le <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> soit atteint. `EndpointSelector`Est la phase finale. Il choisit le point de terminaison dont la priorité est la plus élevée parmi les correspondances comme meilleure correspondance. S’il existe d’autres correspondances avec la même priorité que la meilleure correspondance, une exception de correspondance ambiguë est levée.

La priorité d’itinéraire est calculée sur la base d’un modèle d’itinéraire **plus spécifique** qui reçoit une priorité plus élevée. Par exemple, considérez les modèles `/hello` et `/{message}` :

* Les deux correspondent au chemin de l’URL `/hello` .
* `/hello`  est plus spécifique et par conséquent une priorité plus élevée.

En général, la priorité des itinéraires est un bon choix pour choisir la meilleure correspondance pour les types de schémas d’URL utilisés dans la pratique. <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order>À utiliser uniquement lorsque cela est nécessaire pour éviter une ambiguïté.

En raison des types d’extensibilité fournis par le routage, il n’est pas possible pour le système de routage de calculer à l’avance les itinéraires ambigus. Prenons l’exemple des modèles de routage `/{message:alpha}` et `/{message:int}` :

* La `alpha` contrainte correspond uniquement aux caractères alphabétiques.
* La `int` contrainte correspond uniquement aux nombres.
* Ces modèles ont la même priorité d’itinéraire, mais il n’y a pas d’URL unique correspondantes.
* Si le système de routage a signalé une erreur d’ambiguïté au démarrage, il bloquerait ce cas d’utilisation valide.

> [!WARNING]
>
> L’ordre des opérations à l’intérieur <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> n’influe pas sur le comportement du routage, à une exception près. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> et <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> attribuent automatiquement une valeur de commande à leurs points de terminaison en fonction de l’ordre dans lequel ils sont appelés. Cela simule le comportement à long terme des contrôleurs sans le système de routage fournissant les mêmes garanties que les implémentations de routage plus anciennes.
>
> Dans l’implémentation héritée du routage, il est possible d’implémenter l’extensibilité du routage qui dépend de l’ordre dans lequel les itinéraires sont traités. Routage des points de terminaison dans ASP.NET Core 3,0 et versions ultérieures :
> 
> * N’a pas de concept d’itinéraires.
> * Ne fournit pas de garanties de classement. Tous les points de terminaison sont traités à la fois.

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Priorité des modèles de routage et ordre de sélection des points de terminaison

La [priorité des modèles de routage](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) est un système qui affecte à chaque modèle de routage une valeur en fonction de sa spécificité. Priorité du modèle de routage :

* Évite d’avoir à ajuster l’ordre des points de terminaison dans les cas courants.
* Tente de faire correspondre les attentes de sens commun du comportement de routage.

Par exemple, considérez les modèles `/Products/List` et `/Products/{id}` . Il serait raisonnable de supposer que `/Products/List` est une meilleure correspondance que `/Products/{id}` pour le chemin d’accès de l’URL `/Products/List` . Cela fonctionne parce que le segment littéral `/List` est considéré comme ayant une meilleure priorité que le segment de paramètres `/{id}` .

Les détails du fonctionnement de la précédence sont associés à la façon dont les modèles de routage sont définis :

* Les modèles avec plus de segments sont considérés comme plus spécifiques.
* Un segment avec du texte littéral est considéré comme plus spécifique qu’un segment de paramètres.
* Un segment de paramètre avec une contrainte est considéré comme plus spécifique qu’un segment sans.
* Un segment complexe est considéré comme un segment de paramètre spécifique avec une contrainte.
* Les paramètres Catch-All sont les moins spécifiques. Pour obtenir des informations importantes sur les itinéraires d’interception, consultez la section « **catch-all »** dans la [référence de modèle de routage](#rtr) .

Consultez le [code source sur GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) pour obtenir une référence des valeurs exactes.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Concepts de génération d’URL

Génération d’URL :

* Est le processus par lequel le routage peut créer un chemin d’URL basé sur un ensemble de valeurs d’itinéraire.
* Permet une séparation logique entre les points de terminaison et les URL qui y accèdent.

Le routage des points de terminaison comprend l' <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API. `LinkGenerator` est un service Singleton disponible à partir de [di](xref:fundamentals/dependency-injection). L' `LinkGenerator` API peut être utilisée en dehors du contexte d’une demande en cours d’exécution. [Mvc. IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) et les scénarios qui reposent sur <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> , tels que les [balises d’assistance](xref:mvc/views/tag-helpers/intro), les applications auxiliaires html et les [résultats d’action](xref:mvc/controllers/actions), utilisent l' `LinkGenerator` API en interne pour fournir des fonctionnalités de génération de liens.

Le générateur de liens est basé sur le concept d’une **adresse** et de **schémas d’adresse**. Un schéma d’adresse est un moyen de déterminer les points de terminaison à prendre en compte pour la génération de liens. Par exemple, les scénarios nom de l’itinéraire et valeurs de routage de nombreux utilisateurs sont familiarisés avec les contrôleurs et les Razor pages sont implémentées en tant que schéma d’adresse.

Le générateur de liens peut établir une liaison avec les contrôleurs et les Razor pages à l’aide des méthodes d’extension suivantes :

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Les surcharges de ces méthodes acceptent les arguments qui incluent le `HttpContext` . Ces méthodes sont fonctionnellement équivalentes à [URL. action](xref:System.Web.Mvc.UrlHelper.Action*) et [URL. page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), mais offrent une flexibilité et des options supplémentaires.

Les `GetPath*` méthodes sont très similaires à `Url.Action` et `Url.Page` , car elles génèrent un URI contenant un chemin d’accès absolu. Les méthodes `GetUri*` génèrent toujours un URI absolu contenant un schéma et un hôte. Les méthodes qui acceptent un `HttpContext` génèrent un URI dans le contexte de la requête en cours d’exécution. Les valeurs de route [ambiante](#ambient) , le chemin d’accès de base de l’URL, le schéma et l’hôte de la demande en cours d’exécution sont utilisés, sauf s’ils sont substitués.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> est appelé avec une adresse. La génération d’un URI se fait en deux étapes :

1. Une adresse est liée à une liste de points de terminaison qui correspondent à l’adresse.
1. Le <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> de chaque point de terminaison est évalué jusqu’à ce qu’un modèle de route correspondant aux valeurs fournies soit trouvé. Le résultat obtenu est combiné avec d’autres parties de l’URI fournies par le générateur de liens, puis il est retourné.

Les méthodes fournies par <xref:Microsoft.AspNetCore.Routing.LinkGenerator> prennent en charge des fonctionnalités de génération de liens standard pour n’importe quel type d’adresse. La méthode la plus pratique pour utiliser le générateur de liens est d’utiliser des méthodes d’extension qui effectuent des opérations pour un type d’adresse spécifique :

| Méthode d’extension | Description |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Génère un URI avec un chemin absolu basé sur les valeurs fournies. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Génère un URI absolu basé sur les valeurs fournies.             |

> [!WARNING]
> Faites attention aux implications suivantes de l’appel de méthodes <xref:Microsoft.AspNetCore.Routing.LinkGenerator> :
>
> * Utilisez les méthodes d’extension `GetUri*` avec précaution dans une configuration d’application qui ne valide pas l’en-tête `Host` des requêtes entrantes. Si l' `Host` en-tête des demandes entrantes n’est pas validé, une entrée de demande non approuvée peut être renvoyée au client dans les URI d’une vue ou d’une page. Nous recommandons que toutes les applications de production configurent leur serveur pour qu’il valide l’en-tête `Host` par rapport à des valeurs valides connues.
>
> * Utilisez <xref:Microsoft.AspNetCore.Routing.LinkGenerator> avec précaution dans le middleware en combinaison avec `Map` ou `MapWhen`. `Map*` modifie le chemin de base de la requête en cours d’exécution, ce qui affecte la sortie de la génération de liens. Toutes les API <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permettent la spécification d’un chemin de base. Spécifiez un chemin d’accès de base vide pour annuler l' `Map*` impact sur la génération de liens.

### <a name="middleware-example"></a>Exemple de middleware

Dans l’exemple suivant, un intergiciel (middleware) utilise l' <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API pour créer un lien vers une méthode d’action qui répertorie les produits du magasin. En utilisant le générateur de liens en l’injectant dans une classe et en appelant `GenerateLink` est disponible pour n’importe quelle classe dans une application :

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Informations de référence sur les modèles de routage

Les jetons dans `{}` définissent les paramètres de routage qui sont liés si l’itinéraire est mis en correspondance. Plusieurs paramètres de routage peuvent être définis dans un segment de routage, mais les paramètres de routage doivent être séparés par une valeur littérale. Par exemple `{controller=Home}{action=Index}` n’est pas une route valide, car il n’y a aucune valeur littérale entre `{controller}` et `{action}`.  Les paramètres d’itinéraire doivent avoir un nom et des attributs supplémentaires peuvent être spécifiés.

Un texte littéral autre que les paramètres de routage (par exemple, `{id}`) et le séparateur de chemin `/` doit correspondre au texte présent dans l’URL. La correspondance de texte ne respecte pas la casse et est basée sur la représentation décodée du chemin d’accès de l’URL. Pour correspondre à un délimiteur de paramètre d’itinéraire littéral `{` ou `}` , échappez le délimiteur en répétant le caractère. Par exemple `{{` `}}` , ou.

Astérisque `*` ou double astérisque `**` :

* Peut être utilisé comme préfixe d’un paramètre d’itinéraire pour effectuer une liaison au reste de l’URI.
* Sont appelés paramètres **catch-all** . Par exemple, `blog/{**slug}`:
  * Correspond à n’importe quel URI commençant par `/blog` et dont la valeur est après.
  * La valeur suivante `/blog` est assignée à la valeur de route [Slug](https://developer.mozilla.org/docs/Glossary/Slug) .

[!INCLUDE[](~/includes/catchall.md)]

Les paramètres fourre-tout peuvent également établir une correspondance avec la chaîne vide.

Le paramètre Catch-All échappe les caractères appropriés lorsque l’itinéraire est utilisé pour générer une URL, y compris les caractères de séparation de chemin `/` . Par exemple, la route `foo/{*path}` avec les valeurs de route `{ path = "my/path" }` génère `foo/my%2Fpath`. Notez la barre oblique d’échappement. Pour les séparateurs de chemin aller-retour, utilisez le préfixe de paramètre de routage `**`. La route `foo/{**path}` avec `{ path = "my/path" }` génère `foo/my/path`.

Les modèles d’URL qui tentent de capturer un nom de fichier avec une extension de fichier facultative doivent faire l’objet de considérations supplémentaires. Prenez par exemple le modèle `files/{filename}.{ext?}`. Quand des valeurs existent à la fois pour `filename` et pour `ext`, les deux valeurs sont renseignées. Si seule une valeur `filename` existe dans l’URL, l’itinéraire correspond, car la fin `.` est facultative. Les URL suivantes correspondent à cette route :

* `/files/myFile.txt`
* `/files/myFile`

Les paramètres de route peuvent avoir des **valeurs par défaut** , désignées en spécifiant la valeur par défaut après le nom du paramètre, séparée par un signe égal (`=`). Par exemple, `{controller=Home}` définit `Home` comme valeur par défaut de `controller`. La valeur par défaut est utilisée si aucune valeur n’est présente dans l’URL pour le paramètre. Les paramètres de routage sont rendus facultatifs en ajoutant un point d’interrogation ( `?` ) à la fin du nom du paramètre. Par exemple, `id?`. La différence entre les valeurs facultatives et les paramètres d’itinéraire par défaut est la suivante :

* Un paramètre d’itinéraire avec une valeur par défaut produit toujours une valeur.
* Un paramètre facultatif a une valeur uniquement lorsqu’une valeur est fournie par l’URL de la requête.

Les paramètres de route peuvent avoir des contraintes, qui doivent correspondre à la valeur de route liée à partir de l’URL. Ajout `:` d’un nom de contrainte après le nom du paramètre d’itinéraire spécifie une contrainte Inline sur un paramètre d’itinéraire. Si la contrainte exige des arguments, ils sont fournis entre parenthèses `(...)` après le nom de la contrainte. Vous pouvez spécifier plusieurs *contraintes Inline* en ajoutant un autre `:` nom de contrainte et.

Le nom de la contrainte et les arguments sont passés au service <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> pour créer une instance de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> à utiliser dans le traitement des URL. Par exemple, le modèle de routage `blog/{article:minlength(10)}` spécifie une contrainte `minlength` avec l’argument `10`. Pour plus d’informations sur les contraintes de route et pour obtenir la liste des contraintes fournies par le framework, consultez la section [Informations de référence sur les contraintes de route](#route-constraint-reference).

Les paramètres de routage peuvent également avoir des transformateurs de paramètre. Les transformateurs de paramètres transforment la valeur d’un paramètre lors de la génération de liens et d’actions et de pages correspondantes en URL. Comme pour les contraintes, les transformateurs de paramètres peuvent être ajoutés Inline à un paramètre d’itinéraire en ajoutant un `:` nom de transformateur et après le nom du paramètre d’itinéraire. Par exemple, le modèle de routage `blog/{article:slugify}` spécifie un transformateur `slugify`. Pour plus d’informations sur les transformateurs de paramètre, consultez la section [Informations de référence sur les transformateurs de paramètre](#parameter-transformer-reference).

Le tableau suivant montre des exemples de modèles de routage et leur comportement :

| Modèle de routage                           | Exemple d’URI en correspondance    | URI de la requête&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Correspond seulement au chemin unique `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Correspond à `Page` et le définit sur `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Correspond à `Page` et le définit sur `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mappe au contrôleur `Products` et à l’action `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Correspond au `Products` contrôleur et à l'  `Details` action avec la `id` valeur 123. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Correspond au `Home` contrôleur et à la `Index` méthode. `id` est ignoré.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Correspond au `Products` contrôleur et à la `Index` méthode. `id` est ignoré.        |

L’utilisation d’un modèle est généralement l’approche la plus simple pour le routage. Il est également possible de spécifier des contraintes et des valeurs par défaut hors du modèle de routage.

### <a name="complex-segments"></a>Segments complexes

Les segments complexes sont traités en faisant correspondre les délimiteurs littéraux de droite à gauche d’une façon [non gourmande](#greedy) . Par exemple, `[Route("/a{b}c{d}")]` est un segment complexe.
Les segments complexes fonctionnent d’une manière particulière qui doit être comprise pour pouvoir les utiliser avec succès. L’exemple de cette section montre pourquoi les segments complexes ne fonctionnent vraiment que si le texte du délimiteur ne s’affiche pas dans les valeurs de paramètre. L’utilisation d’une [expression régulière](/dotnet/standard/base-types/regular-expressions) , puis l’extraction manuelle des valeurs est nécessaire pour les cas plus complexes.

[!INCLUDE[](~/includes/regex.md)]

Il s’agit d’un résumé des étapes que le routage effectue avec le modèle `/a{b}c{d}` et le chemin d’URL `/abcd` . `|`Est utilisé pour aider à visualiser le fonctionnement de l’algorithme :

* Le premier littéral, de droite à gauche, est `c` . Par conséquent, la `/abcd` recherche s’effectue à partir de Right et trouve `/ab|c|d` .
* Tout ce qui se trouve à droite ( `d` ) est maintenant mis en correspondance avec le paramètre d’itinéraire `{d}` .
* Le littéral suivant, de droite à gauche, est `a` . Dans ce cas `/ab|c|d` , la recherche commence là où nous l’avons laissée, puis `a` est trouvé `/|a|b|c|d` .
* La valeur à droite ( `b` ) est désormais mise en correspondance avec le paramètre d’itinéraire `{b}` .
* Il n’y a pas de texte restant et aucun modèle de route restant, donc il s’agit d’une correspondance.

Voici un exemple de cas négatif utilisant le même modèle `/a{b}c{d}` et le chemin d’URL `/aabcd` . Le `|` est utilisé pour aider à visualiser le fonctionnement de l’algorithme. Ce cas n’est pas une correspondance, qui est expliquée par le même algorithme :
* Le premier littéral, de droite à gauche, est `c` . Par conséquent, la `/aabcd` recherche s’effectue à partir de Right et trouve `/aab|c|d` .
* Tout ce qui se trouve à droite ( `d` ) est maintenant mis en correspondance avec le paramètre d’itinéraire `{d}` .
* Le littéral suivant, de droite à gauche, est `a` . Dans ce cas `/aab|c|d` , la recherche commence là où nous l’avons laissée, puis `a` est trouvé `/a|a|b|c|d` .
* La valeur à droite ( `b` ) est désormais mise en correspondance avec le paramètre d’itinéraire `{b}` .
* À ce stade, il reste du texte `a` , mais l’algorithme n’a plus de modèle de routage à analyser, donc ce n’est pas une correspondance.

Étant donné que l’algorithme de correspondance n’est [pas gourmand](#greedy):

* Il correspond à la plus petite quantité de texte possible dans chaque étape.
* Tout cas où la valeur de délimiteur apparaît à l’intérieur des valeurs de paramètre ne correspond pas.

Les expressions régulières permettent de mieux contrôler le comportement de leur correspondance.

<a name="greedy"></a>

La correspondance gourmande, également connue comme [correspondance tardive](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), correspond à la plus grande chaîne possible. Non gourmand correspond à la plus petite chaîne possible.

## <a name="route-constraint-reference"></a>Informations de référence sur les contraintes de routage

Les contraintes de route s’exécutent quand une correspondance s’est produite pour l’URL entrante, et le chemin de l’URL est tokenisé en valeurs de route. En général, les contraintes de routage inspectent la valeur de routage associée via le modèle de routage et font une décision true ou false indiquant si la valeur est acceptable ou non. Certaines contraintes de routage utilisent des données hors de la valeur de route pour déterminer si la requête peut être routée. Par exemple, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> peut accepter ou rejeter une requête en fonction de son verbe HTTP. Les contraintes sont utilisées dans le routage des requêtes et la génération des liens.

> [!WARNING]
> N’utilisez pas de contraintes pour la validation des entrées. Si des contraintes sont utilisées pour la validation d’entrée, une entrée non valide entraîne une `404` réponse introuvable. Une entrée non valide doit générer une `400` Demande incorrecte avec un message d’erreur approprié. Les contraintes de route sont utilisées pour lever l’ambiguïté entre des routes similaires, et non pas pour valider les entrées d’une route particulière.

Le tableau suivant montre des exemples de contraintes de routage et leur comportement attendu :

| contrainte | Exemple | Exemples de correspondances | Notes |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Correspond à n’importe quel entier |
| `bool` | `{active:bool}` | `true`, `FALSE` | Correspond à `true` ou `false` . Non-respect de la casse |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Correspond à une `DateTime` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Correspond à une `decimal` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Correspond à une `double` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Correspond à une `float` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Correspond à une valeur `Guid` valide |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Correspond à une valeur `long` valide |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La chaîne doit comporter au moins 4 caractères |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | La chaîne ne doit pas comporter plus de 8 caractères |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La chaîne doit comporter exactement 12 caractères |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La chaîne doit comporter au moins 8 caractères et pas plus de 16 caractères |
| `min(value)` | `{age:min(18)}` | `19` | La valeur entière doit être au moins égale à 18 |
| `max(value)` | `{age:max(120)}` | `91` | La valeur entière ne doit pas être supérieure à 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | La valeur entière doit être au moins égale à 18 mais ne doit pas être supérieure à 120 |
| `alpha` | `{name:alpha}` | `Rick` | La chaîne doit comporter un ou plusieurs caractères alphabétiques et ne respecte pas la `a` - `z` casse. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La chaîne doit correspondre à l’expression régulière. Consultez les conseils relatifs à la définition d’une expression régulière. |
| `required` | `{name:required}` | `Rick` | Utilisé pour garantir qu’une valeur autre qu’un paramètre est présente pendant la génération de l’URL |

[!INCLUDE[](~/includes/regex.md)]

Plusieurs contraintes séparées par des points-virgules peuvent être appliquées à un seul paramètre. Par exemple, la contrainte suivante limite un paramètre à une valeur entière supérieure ou égale à 1 :

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Les contraintes de routage qui vérifient l’URL et sont converties en un type CLR utilisent toujours la culture dite indifférente. Par exemple, la conversion vers le type CLR `int` ou `DateTime` . Ces contraintes supposent que l’URL n’est pas localisable. Les contraintes de routage fournies par le framework ne modifient pas les valeurs stockées dans les valeurs de route. Toutes les valeurs de route analysées à partir de l’URL sont stockées sous forme de chaînes. Par exemple, la contrainte `float` tente de convertir la valeur de route en valeur float, mais la valeur convertie est utilisée uniquement pour vérifier qu’elle peut être convertie en valeur float.

### <a name="regular-expressions-in-constraints"></a>Expressions régulières dans les contraintes

[!INCLUDE[](~/includes/regex.md)]

Les expressions régulières peuvent être spécifiées en tant que contraintes Inline à l’aide de la `regex(...)` contrainte d’itinéraire. Les méthodes de la <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> famille acceptent également un littéral d’objet de contraintes. Si ce formulaire est utilisé, les valeurs de chaîne sont interprétées comme des expressions régulières.

Le code suivant utilise une contrainte Regex inline :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Le code suivant utilise un littéral d’objet pour spécifier une contrainte Regex :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

Le framework ASP.NET Core ajoute `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` au constructeur d’expression régulière. Pour obtenir une description de ces membres, consultez <xref:System.Text.RegularExpressions.RegexOptions>.

Les expressions régulières utilisent des délimiteurs et des jetons similaires à ceux utilisés par le routage et le langage C#. Les jetons d’expression régulière doivent être placés dans une séquence d’échappement. Pour utiliser l’expression régulière `^\d{3}-\d{2}-\d{4}$` dans une contrainte incluse, utilisez l’une des méthodes suivantes :

* Remplacez les `\` caractères fournis dans la chaîne en tant que `\\` caractères dans le fichier source C# pour échapper le caractère d’échappement de la `\` chaîne.
* [Littéraux de chaîne Verbatim](/dotnet/csharp/language-reference/keywords/string).

Pour échapper les caractères de délimiteur de paramètre de routage `{` ,,,, `}` `[` `]` doublez les caractères dans l’expression, par exemple,,, `{{` `}}` `[[` , `]]` . Le tableau suivant montre une expression régulière et sa version échappée :

| Expression régulière    | Expression régulière placée dans une séquence d’échappement     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Les expressions régulières utilisées dans le routage commencent souvent par le `^` caractère et correspondent à la position de départ de la chaîne. Les expressions se terminent souvent par le `$` caractère et correspondent à la fin de la chaîne. Les `^` `$` caractères et garantissent que l’expression régulière correspond à l’intégralité de la valeur du paramètre d’itinéraire. Sans les `^` `$` caractères et, l’expression régulière correspond à toute sous-chaîne de la chaîne, ce qui est souvent indésirable. Le tableau suivant fournit des exemples et explique pourquoi ils correspondent ou ne parviennent pas à faire correspondre :

| Expression   | String    | Correspond | Commentaire               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | 123abc456 | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | mz        | Oui   | Correspondance avec l’expression    |
| `[a-z]{2}`   | MZ        | Oui   | Non-respect de la casse    |
| `^[a-z]{2}$` | hello     | Non    | Voir `^` et `$` ci-dessus |
| `^[a-z]{2}$` | 123abc456 | Non    | Voir `^` et `$` ci-dessus |

Pour plus d’informations sur la syntaxe des expressions régulières, consultez [Expressions régulières du .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Pour contraindre un paramètre à un ensemble connu de valeurs possibles, utilisez une expression régulière. Par exemple, `{action:regex(^(list|get|create)$)}` établit une correspondance avec la valeur de route `action` uniquement pour `list`, `get` ou `create`. Si elle est passée dans le dictionnaire de contraintes, la chaîne `^(list|get|create)$` est équivalente. Les contraintes passées dans le dictionnaire de contraintes qui ne correspondent pas à l’une des contraintes connues sont également traitées comme des expressions régulières. Les contraintes passées dans un modèle qui ne correspondent pas à l’une des contraintes connues ne sont pas traitées comme des expressions régulières.

### <a name="custom-route-constraints"></a>Contraintes d’itinéraire personnalisées

Vous pouvez créer des contraintes de routage personnalisées en implémentant l' <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface. L' `IRouteConstraint` interface contient <xref:System.Web.Routing.IRouteConstraint.Match*> , qui retourne `true` si la contrainte est satisfaite et dans le `false` cas contraire.

Les contraintes de routage personnalisées sont rarement nécessaires. Avant d’implémenter une contrainte d’itinéraire personnalisée, envisagez d’autres méthodes, telles que la liaison de modèle.

Le dossier ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) fournit de bons exemples de création de contraintes. Par exemple, [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).

Pour utiliser un personnalisé `IRouteConstraint` , le type de contrainte d’itinéraire doit être enregistré avec l’application <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> dans le conteneur de service. Un `ConstraintMap` est un dictionnaire qui mappe les clés de contrainte d’itinéraire aux implémentations `IRouteConstraint` qui valident ces contraintes. Le `ConstraintMap` d’une application peut être mis à jour dans `Startup.ConfigureServices` dans le cadre d’un appel [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou en configurant <xref:Microsoft.AspNetCore.Routing.RouteOptions> directement avec `services.Configure<RouteOptions>`. Par exemple :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

La contrainte précédente est appliquée dans le code suivant :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

L’implémentation de `MyCustomConstraint` empêche d' `0` être appliquée à un paramètre d’itinéraire :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Le code précédent :

* Empêche `0` dans le `{id}` segment de l’itinéraire.
* Est présenté pour fournir un exemple de base de l’implémentation d’une contrainte personnalisée. Il ne doit pas être utilisé dans une application de production.

Le code suivant est une meilleure approche pour empêcher un `id` contenant un qui est `0` traité :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Le code précédent présente les avantages suivants par rapport à l' `MyCustomConstraint` approche :

* Elle ne nécessite pas de contrainte personnalisée.
* Elle retourne une erreur plus descriptive lorsque le paramètre d’itinéraire contient `0` .

## <a name="parameter-transformer-reference"></a>Informations de référence sur le transformateur de paramètre

Transformateurs de paramètre :

* Exécuter lors de la génération d’un lien à l’aide de <xref:Microsoft.AspNetCore.Routing.LinkGenerator> .
* Implémentez <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.
* Sont configurés à l’aide de <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Prennent la valeur de routage du paramètre et la convertissent en une nouvelle valeur de chaîne.
* Aboutissent à l’utilisation de la valeur transformée dans le lien généré.

Par exemple, un transformateur de paramètre `slugify` personnalisé dans le modèle d’itinéraire `blog\{article:slugify}` avec `Url.Action(new { article = "MyTestArticle" })` génère `blog\my-test-article`.

Tenez compte de l' `IOutboundParameterTransformer` implémentation suivante :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Pour utiliser un transformateur de paramètre dans un modèle d’itinéraire, configurez-le à l’aide <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de dans `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

L’infrastructure de ASP.NET Core utilise des transformateurs de paramètre pour transformer l’URI dans lequel un point de terminaison est résolu. Par exemple, les transformateurs de paramètres transforment les valeurs d’itinéraire utilisées pour faire correspondre un `area` ,, `controller` `action` et `page` .

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Avec le modèle de route précédent, l’action `SubscriptionManagementController.GetAll` est mise en correspondance avec l’URI `/subscription-management/get-all` . Un transformateur de paramètre ne modifie pas les valeurs de routage utilisées pour générer un lien. Par exemple, `Url.Action("GetAll", "SubscriptionManagement")` produit `/subscription-management/get-all`.

ASP.NET Core fournit des conventions d’API pour l’utilisation de transformateurs de paramètres avec des itinéraires générés :

* La <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> convention MVC applique un transformateur de paramètre spécifié à tous les itinéraires d’attribut dans l’application. Le transformateur de paramètre transforme les jetons de routage d’attribut quand ils sont remplacés. Pour plus d’informations, consultez [Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages utilise la <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> Convention d’API. Cette Convention applique un transformateur de paramètre spécifié à toutes les Razor pages découvertes automatiquement. Le transformateur de paramètres transforme les segments de nom de fichier et de dossier des Razor itinéraires de pages. Pour plus d’informations, consultez [Utiliser un transformateur de paramètre pour personnaliser les routages de pages](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Informations de référence sur la génération d’URL

Cette section contient une référence pour l’algorithme implémenté par la génération d’URL. Dans la pratique, la plupart des exemples complexes de génération d’URL utilisent des contrôleurs ou des Razor pages. Pour plus d’informations, consultez  [routage dans les contrôleurs](xref:mvc/controllers/routing) .

Le processus de génération d’URL commence par un appel à [LinkGenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) ou à une méthode similaire. La méthode est fournie avec une adresse, un ensemble de valeurs d’itinéraire et éventuellement des informations sur la requête actuelle à partir de `HttpContext` .

La première étape consiste à utiliser l’adresse pour résoudre un jeu de points de terminaison candidats à l’aide d’un [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) qui correspond au type de l’adresse.

Une fois l’ensemble de candidats trouvé par le schéma d’adresse, les points de terminaison sont triés et traités de manière itérative jusqu’à ce qu’une opération de génération d’URL aboutisse. La génération d’URL ne vérifie **pas** les ambiguïtés, le premier résultat retourné est le résultat final.

### <a name="troubleshooting-url-generation-with-logging"></a>Résolution des problèmes de génération d’URL avec journalisation

La première étape de la résolution des problèmes liés à la génération d’URL consiste à définir le niveau de journalisation `Microsoft.AspNetCore.Routing` sur `TRACE` . `LinkGenerator` journalise de nombreux détails sur son traitement, ce qui peut être utile pour résoudre les problèmes.

Pour plus d’informations sur la génération d’URL, consultez [référence de génération d’URL](#ugr) .

### <a name="addresses"></a>Adresses

Les adresses sont le concept dans la génération d’URL utilisé pour lier un appel dans le générateur de liens à un jeu de points de terminaison candidats.

Les adresses sont un concept extensible qui est fourni avec deux implémentations par défaut :

* Utilisation du *nom de point de terminaison* ( `string` ) comme adresse :
    * Fournit des fonctionnalités similaires au nom d’itinéraire de MVC.
    * Utilise le <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> type de métadonnées.
    * Résout la chaîne fournie par rapport aux métadonnées de tous les points de terminaison inscrits.
    * Lève une exception au démarrage si plusieurs points de terminaison utilisent le même nom.
    * Recommandé pour une utilisation à usage général en dehors des contrôleurs et des Razor pages.
* Utilisation des *valeurs de route* ( <xref:Microsoft.AspNetCore.Routing.RouteValuesAddress> ) comme adresse :
    * Fournit des fonctionnalités similaires aux contrôleurs et aux Razor pages génération d’URL héritées.
    * Très complexe à étendre et à déboguer.
    * Fournit l’implémentation utilisée par `IUrlHelper` , les tag helpers, les applications auxiliaires html, les résultats d’action, etc.

Le rôle du schéma d’adresse consiste à effectuer l’association entre l’adresse et les points de terminaison correspondants à l’aide de critères arbitraires :

* Le modèle de nom de point de terminaison effectue une recherche de dictionnaire de base.
* Le schéma de valeurs d’itinéraire a un meilleur sous-ensemble complexe de l’algorithme Set.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Valeurs ambiantes et valeurs explicites

À partir de la requête actuelle, le routage accède aux valeurs d’itinéraire de la requête actuelle `HttpContext.Request.RouteValues` . Les valeurs associées à la requête actuelle sont appelées **valeurs ambiantes**. Pour des raisons de clarté, la documentation fait référence aux valeurs de route transmises aux méthodes en tant que **valeurs explicites**.

L’exemple suivant montre des valeurs ambiantes et des valeurs explicites. Il fournit des valeurs ambiantes de la demande actuelle et des valeurs explicites : `{ id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Le code précédent :

* Retourne `/Widget/Index/17`.
* Obtient <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [di](xref:fundamentals/dependency-injection).

Le code suivant ne fournit aucune valeur ambiante ni valeur explicite : `{ controller = "Home", action = "Subscribe", id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

La méthode précédente retourne `/Home/Subscribe/17`

Le code suivant dans le `WidgetController` retourne `/Widget/Subscribe/17` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Le code suivant fournit le contrôleur à partir des valeurs ambiantes dans la requête actuelle et les valeurs explicites : `{ action = "Edit", id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

Dans le code précédent :

* `/Gadget/Edit/17` est retourné.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> Obtient <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> .
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
génère une URL avec un chemin d’accès absolu pour une méthode d’action. L’URL contient le `action` nom et les `route` valeurs spécifiés.

Le code suivant fournit des valeurs ambiantes de la demande actuelle et des valeurs explicites : `{ page = "./Edit, id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Le code précédent affecte la `url` valeur  `/Edit/17` lorsque la Razor page Modifier contient la directive de page suivante :

 `@page "{id:int}"`

Si la page de modification ne contient pas le `"{id:int}"` modèle de routage, `url` est `/Edit?id=17` .

Le comportement de MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> ajoute une couche de complexité en plus des règles décrites ici :

* `IUrlHelper` fournit toujours les valeurs d’itinéraire de la requête actuelle comme valeurs ambiantes.
* [IUrlHelper. action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) copie toujours les `action` valeurs actuelles et de `controller` routage en tant que valeurs explicites, sauf si elles sont remplacées par le développeur.
* [IUrlHelper. page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) copie toujours la `page` valeur de route actuelle en tant que valeur explicite, sauf si elle est substituée. <!--by the user-->
* `IUrlHelper.Page` remplace toujours la `handler` valeur de route actuelle par `null` comme valeur explicite, sauf si elle est substituée.

Les utilisateurs sont souvent surpris par les détails de comportement des valeurs ambiantes, car MVC ne semble pas suivre ses propres règles. Pour des raisons d’historique et de compatibilité, certaines valeurs de route, telles que `action` ,, `controller` `page` et `handler` ont leur propre comportement spécial.

Les fonctionnalités équivalentes fournies par `LinkGenerator.GetPathByAction` et `LinkGenerator.GetPathByPage` dupliquent ces anomalies de `IUrlHelper` pour la compatibilité.

### <a name="url-generation-process"></a>Processus de génération d’URL

Une fois que le jeu de points de terminaison candidats est trouvé, l’algorithme de génération d’URL :

* Traite les points de terminaison de manière itérative.
* Retourne le premier résultat réussi.

La première étape de ce processus est appelée **invalidation** de la valeur de l’itinéraire.  L’invalidation de la valeur de routage est le processus par lequel le routage décide des valeurs d’itinéraire des valeurs ambiantes qui doivent être utilisées et qui doivent être ignorées. Chaque valeur ambiante est prise en compte et soit combinée avec les valeurs explicites, soit ignorée.

La meilleure façon de réfléchir au rôle des valeurs ambiantes est qu’elles essaient d’enregistrer les développeurs d’applications qui tapent, dans certains cas courants. Traditionnellement, les scénarios où les valeurs ambiantes sont utiles sont liés à MVC :

* Lors de la liaison à une autre action dans le même contrôleur, le nom du contrôleur n’a pas besoin d’être spécifié.
* Lors de la liaison à un autre contrôleur dans la même zone, il n’est pas nécessaire de spécifier le nom de la zone.
* Lors de la liaison à la même méthode d’action, il n’est pas nécessaire de spécifier des valeurs de route.
* Lors de la liaison à une autre partie de l’application, vous ne souhaitez pas transférer les valeurs de route qui n’ont aucune signification dans cette partie de l’application.

Les appels à `LinkGenerator` ou à `IUrlHelper` ce retour `null` sont généralement provoqués par la non-compréhension de l’invalidation de la valeur de l’itinéraire. Résolvez les problèmes d’invalidation de valeur de routage en spécifiant explicitement plus de valeurs d’itinéraire pour voir si cela résout le problème.

L’invalidation de la valeur de routage fonctionne en partant du principe que le schéma d’URL de l’application est hiérarchique, avec une hiérarchie formée de gauche à droite. Examinez le modèle de routage de contrôleur de base `{controller}/{action}/{id?}` pour avoir une idée intuitive de son fonctionnement dans la pratique. Une **modification apportée** à une valeur **invalide** toutes les valeurs d’itinéraire qui s’affichent à droite. Cela reflète l’hypothèse sur la hiérarchie. Si l’application a une valeur ambiante pour `id` , et que l’opération spécifie une autre valeur pour le `controller` :

* `id` ne sera pas réutilisé car `{controller}` est à gauche de `{id?}` .

Voici quelques exemples qui illustrent ce principe :

* Si les valeurs explicites contiennent une valeur pour `id` , la valeur ambiante de `id` est ignorée. Les valeurs ambiantes pour `controller` et `action` peuvent être utilisées.
* Si les valeurs explicites contiennent une valeur pour `action` , toute valeur ambiante pour `action` est ignorée. Les valeurs ambiantes pour `controller` peuvent être utilisées. Si la valeur explicite de `action` est différente de la valeur ambiante pour `action` , la `id` valeur n’est pas utilisée.  Si la valeur explicite de `action` est identique à la valeur ambiante pour `action` , la `id` valeur peut être utilisée.
* Si les valeurs explicites contiennent une valeur pour `controller` , toute valeur ambiante pour `controller` est ignorée. Si la valeur explicite de `controller` est différente de la valeur ambiante pour `controller` , les `action` `id` valeurs et ne sont pas utilisées. Si la valeur explicite de `controller` est identique à la valeur ambiante pour `controller` , les `action` valeurs et `id` peuvent être utilisées.

Ce processus est encore plus compliqué par l’existence d’itinéraires d’attribut et d’itinéraires conventionnels dédiés. Les itinéraires conventionnels du contrôleur, comme `{controller}/{action}/{id?}` spécifier une hiérarchie à l’aide de paramètres de routage. Pour les [itinéraires conventionnels dédiés](xref:mvc/controllers/routing#dcr) et les [itinéraires d’attributs](xref:mvc/controllers/routing#ar) vers les contrôleurs et les Razor pages :

* Il existe une hiérarchie de valeurs de route.
* Ils n’apparaissent pas dans le modèle.

Dans ce cas, la génération d’URL définit le concept de **valeurs requis** . Les points de terminaison créés par les contrôleurs et les Razor pages ont des valeurs requises spécifiées qui autorisent le fonctionnement de l’invalidation de la valeur de routage.

L’algorithme d’invalidation de la valeur de routage est en détail :

* Les noms de valeur requis sont combinés avec les paramètres de route, puis traités de gauche à droite.
* Pour chaque paramètre, la valeur ambiante et la valeur explicite sont comparées :
    * Si la valeur ambiante et la valeur explicite sont identiques, le processus se poursuit.
    * Si la valeur ambiante est présente et que la valeur explicite n’est pas, la valeur ambiante est utilisée lors de la génération de l’URL.
    * Si la valeur ambiante n’est pas présente et si la valeur explicite est, rejetez la valeur ambiante et toutes les valeurs ambiantes suivantes.
    * Si la valeur ambiante et la valeur explicite sont présentes, et si les deux valeurs sont différentes, rejetez la valeur ambiante et toutes les valeurs ambiantes suivantes.

À ce stade, l’opération de génération d’URL est prête à évaluer les contraintes de routage. L’ensemble de valeurs acceptées est associé aux valeurs par défaut du paramètre, qui sont fournies aux contraintes. Si les contraintes réussissent, l’opération continue.

Ensuite, les **valeurs acceptées** peuvent être utilisées pour développer le modèle de routage. Le modèle de routage est traité :

* De gauche à droite.
* La valeur acceptée est remplacée pour chaque paramètre.
* Dans les cas spéciaux suivants :
  * Si une valeur est manquante pour les valeurs acceptées et que le paramètre a une valeur par défaut, la valeur par défaut est utilisée.
  * Si une valeur est manquante pour les valeurs acceptées et que le paramètre est facultatif, le traitement continue.
  * Si un paramètre d’itinéraire à droite d’un paramètre facultatif manquant a une valeur, l’opération échoue.
  * <!-- review default-valued parameters optional parameters --> Les paramètres par défaut et les paramètres facultatifs contigus sont réduits dans la mesure du possible.

Les valeurs fournies explicitement qui ne correspondent pas à un segment de l’itinéraire sont ajoutées à la chaîne de requête. Le tableau suivant présente le résultat en cas d’utilisation du modèle de routage `{controller}/{action}/{id?}`.

| Valeurs ambiantes                     | Valeurs explicites                        | Résultats                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Problèmes liés à l’invalidation de la valeur de routage

À partir de ASP.NET Core 3,0, certains modèles de génération d’URL utilisés dans les versions ASP.NET Core antérieures ne fonctionnent pas correctement avec la génération d’URL. L’équipe ASP.NET Core prévoit d’ajouter des fonctionnalités pour répondre à ces besoins dans une version ultérieure. Pour le moment, la meilleure solution consiste à utiliser le routage hérité.

Le code suivant montre un exemple de schéma de génération d’URL qui n’est pas pris en charge par le routage.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

Dans le code précédent, le `culture` paramètre route est utilisé pour la localisation. Le souhait est de faire en sorte que le `culture` paramètre soit toujours accepté comme valeur ambiante. Toutefois, le `culture` paramètre n’est pas accepté comme valeur ambiante en raison de la façon dont les valeurs requises fonctionnent :

* Dans le `"default"` modèle de routage, le `culture` paramètre route est à gauche de `controller` , donc les modifications apportées à `controller` ne sont pas invalidées `culture` .
* Dans le `"blog"` modèle de routage, le `culture` paramètre d’itinéraire est considéré comme à droite de `controller` , qui apparaît dans les valeurs requises.

## <a name="configuring-endpoint-metadata"></a>Configuration des métadonnées de point de terminaison

Les liens suivants fournissent des informations sur la configuration des métadonnées de point de terminaison :

* [Activer cors avec routage du point de terminaison](xref:security/cors#enable-cors-with-endpoint-routing)
* [Exemple IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider) utilisant un `[MinimumAgeAuthorize]` attribut personnalisé
* [Tester l’authentification avec l’attribut [Authorize]](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Sélection du schéma avec l’attribut [Authorize]](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Appliquer des stratégies à l’aide de l’attribut [Authorize]](xref:security/authorization/policies#apply-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Correspondance d’hôte dans les itinéraires avec RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> applique une contrainte à l’itinéraire qui requiert l’hôte spécifié. Le `RequireHost` paramètre ou [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) peut être :

* Host : `www.domain.com` , correspond `www.domain.com` à n’importe quel port.
* Hôte avec caractère générique : `*.domain.com` , correspond à, `www.domain.com` `subdomain.domain.com` ou `www.subdomain.domain.com` sur n’importe quel port.
* Port : `*:5000` , correspond au port 5000 avec n’importe quel hôte.
* Hôte et port : `www.domain.com:5000` ou `*.domain.com:5000` , correspond à l’hôte et au port.

Plusieurs paramètres peuvent être spécifiés à l’aide `RequireHost` de ou de `[Host]` . La contrainte correspond aux hôtes valides pour l’un des paramètres. Par exemple, `[Host("domain.com", "*.domain.com")]` correspond à `domain.com` , `www.domain.com` et `subdomain.domain.com` .

Le code suivant utilise `RequireHost` pour exiger l’hôte spécifié sur l’itinéraire :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Le code suivant utilise l' `[Host]` attribut sur le contrôleur pour exiger l’un des ordinateurs hôtes spécifiés :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Lorsque l' `[Host]` attribut est appliqué à la fois au contrôleur et à la méthode d’action :

* L’attribut de l’action est utilisé.
* L’attribut Controller est ignoré.

## <a name="performance-guidance-for-routing"></a>Aide sur les performances pour le routage

La plupart des routages ont été mis à jour dans ASP.NET Core 3,0 pour améliorer les performances.

Quand une application présente des problèmes de performances, le routage est souvent soupçonné de le résoudre. La raison pour laquelle le routage est suspecté est que les infrastructures comme les contrôleurs et Razor les pages indiquent le temps passé dans l’infrastructure dans leurs messages de journalisation. Lorsqu’il y a une différence importante entre l’heure indiquée par les contrôleurs et la durée totale de la demande :

* Les développeurs éliminent leur code d’application comme source du problème.
* Il est courant de supposer que le routage est la cause.

Le routage est testé sur les performances à l’aide de milliers de points de terminaison. Il est peu probable qu’une application typique rencontre un problème de performances tout simplement trop important. La cause la plus courante de la lenteur des performances de routage est généralement un intergiciel (middleware) personnalisé.

L’exemple de code suivant illustre une technique de base pour réduire la source de délai :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Pour le routage temporel :

* Entrelacer chaque intergiciel à l’aide d’une copie de l’intergiciel (middleware) de minuterie indiquée dans le code précédent.
* Ajoutez un identificateur unique pour corréler les données de temporisation avec le code.

Il s’agit d’une méthode de base pour réduire le délai lorsqu’il est significatif, par exemple, plus que `10ms` .  La soustraction `Time 2` de `Time 1` signale le temps passé dans l' `UseRouting` intergiciel (middleware).

Le code suivant utilise une approche plus compacte pour le code de minutage précédent :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Fonctionnalités de routage potentiellement onéreuses

La liste suivante fournit des informations sur les fonctionnalités de routage relativement coûteuses par rapport aux modèles de routage de base :

* Expressions régulières : il est possible d’écrire des expressions régulières complexes ou ayant une durée d’exécution longue avec une petite quantité d’entrée.

* Segments complexes ( `{x}-{y}-{z}` ) : 
  * Sont beaucoup plus coûteuses que l’analyse d’un segment de chemin d’accès d’URL normal.
  * Entraîne l’allocation de beaucoup plus de sous-chaînes.
  * La logique de segment complexe n’a pas été mise à jour dans ASP.NET Core mise à jour des performances de routage 3,0.

* Accès synchrone aux données : de nombreuses applications complexes ont accès aux bases de données dans le cadre de leur routage. ASP.NET Core 2,2 et le routage antérieur peuvent ne pas fournir les points d’extensibilité appropriés pour prendre en charge le routage de l’accès aux bases de données. Par exemple, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> , et <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> sont synchrones. Les points d’extensibilité tels que <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> et <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> sont asynchrones.

## <a name="guidance-for-library-authors"></a>Conseils pour les auteurs de bibliothèque

Cette section contient des conseils pour les auteurs de bibliothèque qui créent sur le routage. Ces détails visent à garantir que les développeurs d’applications ont une bonne expérience en utilisant des bibliothèques et des infrastructures qui étendent le routage.

### <a name="define-endpoints"></a>Définir des points de terminaison

Pour créer une infrastructure qui utilise le routage pour la correspondance d’URL, commencez par définir une expérience utilisateur qui s’appuie sur <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .

**Effectuez** une génération par-dessus <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> . Cela permet aux utilisateurs de composer votre infrastructure avec d’autres fonctionnalités de ASP.NET Core sans confusion. Chaque modèle de ASP.NET Core comprend le routage. Supposons que le routage est présent et familier pour les utilisateurs.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**Retournez un** type concret sealed à partir d’un appel à `MapMyFramework(...)` qui implémente <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> . La plupart des méthodes d’infrastructure `Map...` suivent ce modèle. L' `IEndpointConventionBuilder` interface :

* Permet la composition des métadonnées.
* Est ciblé par une variété de méthodes d’extension.

La déclaration de votre propre type vous permet d’ajouter vos propres fonctionnalités propres au Framework au générateur. Il est OK d’inclure dans un wrapper un générateur déclaré par l’infrastructure et de transférer des appels vers celui-ci.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**Envisagez** d’écrire votre propre <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> . `EndpointDataSource` est la primitive de bas niveau pour déclarer et mettre à jour une collection de points de terminaison. `EndpointDataSource` est une API puissante utilisée par les contrôleurs et les Razor pages.

Les tests de routage ont un [exemple de base](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) d’une source de données qui ne met pas à jour.

**N’essayez pas** d’inscrire un `EndpointDataSource` par défaut. Obligez les utilisateurs à inscrire votre infrastructure dans <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> . La philosophie du routage est que rien n’est inclus par défaut et qu’il `UseEndpoints` s’agit de l’emplacement où inscrire des points de terminaison.

### <a name="creating-routing-integrated-middleware"></a>Création d’un intergiciel (middleware) intégré au routage

**Envisagez** de définir des types de métadonnées en tant qu’interface.

**Vous pouvez** utiliser les types de métadonnées en tant qu’attribut sur les classes et les méthodes.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Les infrastructures comme les contrôleurs et les Razor pages prennent en charge l’application des attributs de métadonnées aux types et aux méthodes. Si vous déclarez des types de métadonnées :

* Rendez-les accessibles en tant qu' [attributs](/dotnet/csharp/programming-guide/concepts/attributes/).
* La plupart des utilisateurs sont habitués à appliquer des attributs.

La déclaration d’un type de métadonnées en tant qu’interface ajoute une autre couche de flexibilité :

* Les interfaces sont composables.
* Les développeurs peuvent déclarer leurs propres types qui combinent plusieurs stratégies.

**Vous pouvez** remplacer les métadonnées, comme illustré dans l’exemple suivant :

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

La meilleure façon de suivre ces instructions consiste à éviter de définir des **métadonnées de marqueur** :

* Ne cherchez pas simplement la présence d’un type de métadonnées.
* Définissez une propriété sur les métadonnées et vérifiez la propriété.

La collection de métadonnées est triée et prend en charge le remplacement par priorité. Dans le cas de contrôleurs, les métadonnées de la méthode d’action sont plus spécifiques.

Faites en sorte que les intergiciels (middleware) **soient** utiles avec et sans routage.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization();
});
```

À titre d’exemple, considérez l' `UseAuthorization` intergiciel (middleware). L’intergiciel d’autorisation vous permet de passer une stratégie de secours. <!-- shown where?  (shown here) --> La stratégie de secours, si elle est spécifiée, s’applique aux deux :

* Points de terminaison sans stratégie spécifiée.
* Les demandes qui ne correspondent pas à un point de terminaison.

Cela rend l’intergiciel (middleware) d’autorisation utile en dehors du contexte du routage. L’intergiciel d’autorisation peut être utilisé pour la programmation d’intergiciel (middleware) classique.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Le routage est chargé de mapper les URI de requête aux points de terminaison et de distribuer les demandes entrantes à ces points de terminaison. Les routes sont définies dans l’application et configurées au démarrage de l’application. Une route peut éventuellement extraire des valeurs de l’URL contenue dans la requête, et ces valeurs peuvent ensuite être utilisées pour le traitement de la requête. À l’aide des informations de routage de l’application, le routage est également en mesure de générer des URL qui mappent aux points de terminaison.

Pour utiliser les scénarios de routage les plus récents dans ASP.NET Core 2.2, spécifiez la [version de compatibilité](xref:mvc/compatibility-version) à l’inscription des services MVC dans `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

L’option <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> détermine si le routage doit utiliser en interne une logique basée sur les points de terminaison ou la logique basée sur <xref:Microsoft.AspNetCore.Routing.IRouter> d’ASP.NET Core 2.1 ou antérieur. Quand la version de compatibilité est définie sur 2.2 ou ultérieur, la valeur par défaut est `true`. Définissez la valeur sur `false` pour utiliser la logique de routage antérieure :

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Pour plus d’informations sur le routage basé sur <xref:Microsoft.AspNetCore.Routing.IRouter>, consultez la [version ASP.NET Core 2.1 de cette rubrique](?view=aspnetcore-2.1).

> [!IMPORTANT]
> Ce document traite du routage ASP.NET Core de bas niveau. Pour plus d’informations sur le routage ASP.NET Core MVC, consultez <xref:mvc/controllers/routing>. Pour plus d’informations sur les conventions de routage dans les Razor pages, consultez <xref:razor-pages/razor-pages-conventions> .

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Concepts de base du routage

La plupart des applications doivent choisir un schéma de routage de base et descriptif pour que les URL soient lisibles et explicites. La route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` :

* Prend en charge un schéma de routage de base et descriptif.
* Est un point de départ pratique pour les applications basées sur une interface utilisateur.

Les développeurs ajoutent généralement des itinéraires succincts supplémentaires aux zones à trafic élevé d’une application dans des situations particulières en utilisant le [routage d’attributs](xref:mvc/controllers/routing#attribute-routing) ou des itinéraires conventionnels dédiés. Les exemples de situations spécialisées incluent les points de terminaison de blog et de commerce électronique.

Les API web doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources dans lequel les opérations sont représentées par des verbes HTTP. Cela signifie que de nombreuses opérations, par exemple, obtenir et poster, sur la même ressource logique utilisent la même URL. Le routage d’attributs fournit le niveau de contrôle nécessaire pour concevoir avec soin la disposition des points de terminaison publics d’une API.

Razor Pages les applications utilisent le routage conventionnel par défaut pour servir des ressources nommées dans le dossier *pages* d’une application. Des conventions supplémentaires vous permettent de personnaliser le Razor comportement de routage des pages. Pour plus d’informations, consultez <xref:razor-pages/index> et <xref:razor-pages/razor-pages-conventions>.

La prise en charge de la génération d’URL permet de développer l’application sans coder en dur les URL pour lier l’application. Cette prise en charge permet de commencer avec une configuration de routage de base, puis de modifier les routes une fois que la disposition des ressources de l’application est déterminée.

Le routage utilise des *points de terminaison* ( `Endpoint` ) pour représenter des points de terminaison logiques dans une application.

Un point de terminaison définit un délégué pour traiter les requêtes et une collection de métadonnées arbitraires. Les métadonnées sont utilisées pour implémenter des problèmes transversaux basés sur des stratégies et une configuration attachées à chaque point de terminaison.

Le système de routage a les caractéristiques suivantes :

* Une syntaxe des modèles de route est utilisée pour définir des routes avec des paramètres de route tokenisés.
* La configuration de points de terminaison de style conventionnel et de style « attribut » est autorisée.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> est utilisé pour déterminer si un paramètre d’URL contient une valeur valide pour une contrainte de point de terminaison donné.
* Les modèles d’application, tels que MVC/ Razor pages, inscrivent tous leurs points de terminaison, qui ont une implémentation prévisible des scénarios de routage.
* L’implémentation du routage prend des décisions de routage partout où vous le souhaitez dans le pipeline de middleware (intergiciel).
* Le middleware qui apparaît après un middleware de routage peut inspecter le résultat de la décision du middleware de routage quant au point de terminaison d’un URI de requête donné.
* Il est possible d’énumérer tous les points de terminaison dans l’application n’importe où dans le pipeline de middleware.
* Une application peut utiliser le routage pour générer des URL (par exemple pour la redirection ou pour des liens) en fonction des informations des points de terminaison et éviter ainsi les URL codées en dur, ce qui facilite la maintenance.
* La génération d’URL est basée sur des adresses, qui prennent en charge l’extensibilité arbitraire :

  * L’API du générateur de liens (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) peut être résolue partout avec [l’injection de dépendances](xref:fundamentals/dependency-injection) pour générer des URL.
  * Quand l’API du générateur de liens n’est pas disponible via l’injection de dépendances, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offre des méthodes pour générer des URL.

> [!NOTE]
> Avec la publication du routage de point de terminaison dans ASP.NET Core 2,2, la liaison de point de terminaison est limitée aux Razor actions et pages MVC/pages. Les expansions des fonctionnalités de liaison de point de terminaison sont prévues pour les versions futures.

Le routage est connecté au pipeline de [l’intergiciel (middleware)](xref:fundamentals/middleware/index) par la classe <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>. [ASP.net Core MVC](xref:mvc/overview) ajoute le routage au pipeline de l’intergiciel (middleware) dans le cadre de sa configuration et gère le routage dans les applications MVC et Razor pages. Pour découvrir comment utiliser le routage en tant que composant autonome, consultez la section [Utiliser le middleware de routage](#use-routing-middleware).

### <a name="url-matching"></a>Correspondance d’URL

La correspondance d’URL est le processus par lequel le routage distribue une requête entrante à un *point de terminaison*. Ce processus est basé sur des données présentes dans le chemin de l’URL, mais il peut être étendu pour prendre en compte toutes les données de la requête. La possibilité de distribuer des requêtes à des gestionnaires distincts est essentielle pour adapter la taille et la complexité d’une application.

Le système de routage dans le routage de point de terminaison est responsable de toutes les décisions de distribution. Comme le middleware applique des stratégies basées sur le point de terminaison sélectionné, il est important que les décisions susceptibles d’affecter la distribution ou l’application des stratégies de sécurité soient prises au sein du système de routage.

Quand le délégué du point de terminaison est exécuté, les propriétés de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) sont définies sur des valeurs appropriées en fonction du traitement des requêtes effectué jusqu’à présent.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) est un dictionnaire de *valeurs de route* produites à partir de la route. Ces valeurs sont généralement déterminées en décomposant l’URL en jetons. Elles peuvent être utilisées pour accepter l’entrée d’utilisateur ou pour prendre d’autres décisions relatives à la distribution à l’intérieur de l’application.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) est un conteneur de propriétés des données supplémentaires associées à la route mise en correspondance. Les <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> sont fournis pour prendre en charge l’association de données d’état à chaque route, de façon que l’application puisse prendre des décisions en fonction de la route avec laquelle la correspondance a été établie. Ces valeurs sont définies par le développeur et n’affectent **pas** le comportement du routage de quelque manière que ce soit. De plus, les valeurs dissimulées dans [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) peuvent être de n’importe quel type, contrairement aux valeurs [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), qui doivent être convertibles en chaînes et à partir de chaînes.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) est une liste des routes qui ont participé à la mise en correspondance correcte de la requête. Les routes peuvent être imbriquées les unes dans les autres. La propriété <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> reflète le chemin à travers l’arborescence logique des routes qui ont généré une correspondance. En général le premier élément de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> est la collection de routes et il doit être utilisé pour la génération d’URL. Le dernier élément de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> est le gestionnaire de routage avec lequel la correspondance a été établie.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Génération d’URL avec LinkGenerator

La génération d’URL est le processus par lequel le routage peut créer un chemin d’URL basé sur un ensemble de valeurs de route. Ceci permet une séparation logique entre vos points de terminaison et les URL qui y accèdent.

Le routage des points de terminaison inclut l’API de générateur de liens (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator> est un service singleton qui peut être récupéré à partir de [di](xref:fundamentals/dependency-injection). L’API peut être utilisée en dehors du contexte d’une requête en cours d’exécution. Le <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> de MVC et les scénarios qui s’appuient sur <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, comme les [Tag Helpers](xref:mvc/views/tag-helpers/intro), les helpers HTML et les [résultats d’action](xref:mvc/controllers/actions), utilisent le générateur de liens pour fournir les fonctionnalités de création de liens.

Le générateur de liens est basé sur le concept d’une *adresse* et de *schémas d’adresse*. Un schéma d’adresse est un moyen de déterminer les points de terminaison à prendre en compte pour la génération de liens. Par exemple, les scénarios nom de l’itinéraire et valeurs de routage de nombreux utilisateurs sont familiarisés avec MVC/les Razor pages sont implémentés en tant que schéma d’adresse.

Le générateur de liens peut lier des Razor actions et des pages MVC/pages à l’aide des méthodes d’extension suivantes :

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Une surcharge de ces méthodes accepte des arguments qui incluent le `HttpContext`. Ces méthodes sont fonctionnellement équivalentes à `Url.Action` et à `Url.Page`, mais elles offrent davantage de flexibilité et d’options.

Les méthodes `GetPath*` sont les plus proches de `Url.Action` et de `Url.Page` en ce qu’elles génèrent un URI contenant un chemin absolu. Les méthodes `GetUri*` génèrent toujours un URI absolu contenant un schéma et un hôte. Les méthodes qui acceptent un `HttpContext` génèrent un URI dans le contexte de la requête en cours d’exécution. Les valeurs de route ambiante, le chemin de base d’URL, le schéma et l’hôte de la requête en cours d’exécution sont utilisés, sauf s’ils sont remplacés.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> est appelé avec une adresse. La génération d’un URI se fait en deux étapes :

1. Une adresse est liée à une liste de points de terminaison qui correspondent à l’adresse.
1. Le `RoutePattern` de chaque point de terminaison est évalué jusqu’à ce qu’un modèle de route correspondant aux valeurs fournies soit trouvé. Le résultat obtenu est combiné avec d’autres parties de l’URI fournies par le générateur de liens, puis il est retourné.

Les méthodes fournies par <xref:Microsoft.AspNetCore.Routing.LinkGenerator> prennent en charge des fonctionnalités de génération de liens standard pour n’importe quel type d’adresse. La façon la plus pratique d’utiliser le générateur de liens est de le faire via des méthodes d’extension qui effectuent des opérations pour un type d’adresse spécifique.

| Méthode d’extension   | Description                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Génère un URI avec un chemin absolu basé sur les valeurs fournies. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Génère un URI absolu basé sur les valeurs fournies.             |

> [!WARNING]
> Faites attention aux implications suivantes de l’appel de méthodes <xref:Microsoft.AspNetCore.Routing.LinkGenerator> :
>
> * Utilisez les méthodes d’extension `GetUri*` avec précaution dans une configuration d’application qui ne valide pas l’en-tête `Host` des requêtes entrantes. Si l’en-tête `Host` des requêtes entrantes n’est pas validé, l’entrée de requête non approuvée peut être renvoyée au client dans les URI d’une page/vue. Nous recommandons que toutes les applications de production configurent leur serveur pour qu’il valide l’en-tête `Host` par rapport à des valeurs valides connues.
>
> * Utilisez <xref:Microsoft.AspNetCore.Routing.LinkGenerator> avec précaution dans le middleware en combinaison avec `Map` ou `MapWhen`. `Map*` modifie le chemin de base de la requête en cours d’exécution, ce qui affecte la sortie de la génération de liens. Toutes les API <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permettent la spécification d’un chemin de base. Spécifiez toujours un chemin de base vide pour annuler l’effet de `Map*` sur la génération de liens.

## <a name="differences-from-earlier-versions-of-routing"></a>Différences par rapport aux versions précédentes du routage

Il existe quelques différences entre le routage de points de terminaison d’ASP.NET Core 2.2 ou ultérieur et celui des versions antérieures d’ASP.NET Core :

* Le système de routage de points de terminaison ne prend pas en charge l’extensibilité basée sur <xref:Microsoft.AspNetCore.Routing.IRouter>, notamment l’héritage de <xref:Microsoft.AspNetCore.Routing.Route>.

* Le routage de points de terminaison ne prend pas en charge [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Utilisez la [version de compatibilité](xref:mvc/compatibility-version) 2,1 ( `.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)` ) pour continuer à utiliser le shim de compatibilité.

* Le routage de points de terminaison a un comportement différent pour la casse des URI générés lors de l’utilisation de routes conventionnelles.

  Considérez le modèle de route par défaut suivant :

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Supposez que vous générez un lien vers une action avec la route suivante :

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Avec le routage basé sur <xref:Microsoft.AspNetCore.Routing.IRouter>, ce code génère un URI `/blog/ReadPost/17`, qui respecte la casse de la valeur de la route fournie. Le routage de points de terminaison dans ASP.NET Core 2.2 ou ultérieur produit `/Blog/ReadPost/17` (« Blog » commence par une majuscule). Le routage de points de terminaison fournit l’interface `IOutboundParameterTransformer`, qui peut être utilisée pour personnaliser ce comportement de façon globale ou pour appliquer des conventions différentes pour le mappage d’URL.

  Pour plus d’informations, consultez la section [Informations de référence sur les transformateurs de paramètre](#parameter-transformer-reference).

* La génération de liens utilisée par MVC/ Razor pages avec des itinéraires conventionnels se comporte différemment lors de la tentative de liaison à un contrôleur, une action ou une page qui n’existe pas.

  Considérez le modèle de route par défaut suivant :

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Supposez que vous générez un lien vers une action en utilisant le modèle par défaut avec ce qui suit :

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Avec le routage basé sur `IRouter`, le résultat est toujours `/Blog/ReadPost/17`, même si le `BlogController` n’existe pas ou n’a pas de méthode d’action `ReadPost`. Comme prévu, le routage de points de terminaison dans ASP.NET Core 2.2 ou ultérieur produit `/Blog/ReadPost/17` si la méthode d’action existe. *Cependant, le routage de points de terminaison produit une chaîne vide si l’action n’existe pas.* Sur le plan conceptuel, le routage de points de terminaison ne fait pas l’hypothèse que le point de terminaison existe si l’action n’existe pas.

* L’ *algorithme d’invalidation de valeur ambiante* de la génération de liens se comporte différemment quand il est utilisé avec le routage de points de terminaison.

  L’ *invalidation de valeur ambiante* est l’algorithme qui décide quelles valeurs de route provenant de la requête en cours d’exécution (les valeurs ambiantes) peuvent être utilisées dans les opérations de génération de liens. Le routage conventionnel a toujours invalidé les valeurs de route supplémentaires lors de la liaison à une action différente. Le routage d’attributs n’a pas ce comportement avant la publication d’ASP.NET Core 2.2. Dans les versions antérieures d’ASP.NET Core, les liens vers une autre action utilisant les mêmes noms de paramètre de route provoquaient des erreurs de génération de liens. Dans ASP.NET Core 2.2 ou ultérieur, les deux formes de routage invalident les valeurs lors de la liaison vers une autre action.

  Considérez l’exemple suivant dans ASP.NET Core 2.1 ou antérieur. Lors de la liaison à une autre action (ou une autre page), les valeurs de route peuvent être réutilisées de façon non souhaitée.

  Dans */Pages/Store/Product.cshtml*  :

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  Dans */Pages/Login.cshtml*  :

  ```cshtml
  @page "{id?}"
  ```

  Si l’URI est `/Store/Product/18` dans ASP.NET Core 2.1 ou antérieur, le lien généré sur la page Store/Info par `@Url.Page("/Login")` est `/Login/18`. La valeur 18 pour `id` est réutilisée, même si la destination du lien est une partie entièrement différente de l’application. La valeur de route pour `id` dans le contexte de la page `/Login` est probablement une valeur d’ID utilisateur, et non pas une valeur d’ID de produit de magasin.

  Dans le routage de points de terminaison avec ASP.NET Core 2.2 ou ultérieur, le résultat est `/Login`. Les valeurs ambiantes ne sont pas réutilisées quand la destination liée est une action ou une page différente.

* Syntaxe des paramètres de route avec aller-retour : les barres obliques ne sont pas encodées lors de l’utilisation d’une syntaxe de paramètre passe-partout avec double astérisque (`**`).

  Pendant la génération de liens, le système de routage encode la valeur capturée dans un paramètre passe-partout avec double astérisque (`**`) (par exemple `{**myparametername}`) sans les barres obliques. Le passe-partout avec double astérisque est pris en charge avec le routage basé sur `IRouter` dans ASP.NET Core 2.2 ou ultérieur.

  La syntaxe de paramètre passe-partout avec un seul astérisque dans les versions antérieures d’ASP.NET Core (`{*myparametername}`) reste prise en charge, et les barres obliques sont encodées.

  | Route              | Lien généré avec<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (la barre oblique est encodée)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Exemple de middleware

Dans l’exemple suivant, un middleware utilise l’API <xref:Microsoft.AspNetCore.Routing.LinkGenerator> pour créer un lien vers une méthode d’action qui liste les produits d’un magasin. L’utilisation du générateur de liens en l’injectant dans une classe et en appelant `GenerateLink` est disponible pour n’importe quelle classe dans une application.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>Créer des itinéraires

La plupart des applications créent des routes en appelant <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ou l’une des méthodes d’extension similaires définies sur <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Toutes les méthodes d’extension de <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> créent une instance de <xref:Microsoft.AspNetCore.Routing.Route> et l’ajoutent à la collection de routes.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> n’accepte pas de paramètre de gestionnaire de routage. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ajoute uniquement des routes gérées par <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Pour plus d’informations sur le routage dans MVC, consultez <xref:mvc/controllers/routing>.

L’exemple de code suivant est un exemple d’appel à <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> utilisé par une définition de route ASP.NET Core MVC classique :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ce modèle établit une correspondance avec un chemin d’URL et extrait les valeurs de route . Par exemple, le chemin `/Products/Details/17` génère les valeurs de route suivantes : `{ controller = Products, action = Details, id = 17 }`.

Les valeurs de route sont déterminées en divisant le chemin d’URL en segments et en mettant en correspondance chaque segment avec le nom des *paramètres de routage* dans le modèle de routage. Les paramètres de routage sont nommés. Vous définissez des paramètres en plaçant leur nom entre des accolades `{ ... }`.

Le modèle précédent peut également mettre en correspondance le chemin d’URL `/` et produire les valeurs `{ controller = Home, action = Index }`. Cela s’explique par le fait que les paramètres de routage `{controller}` et `{action}` ont des valeurs par défaut et que le paramètre de routage `id` est facultatif. Un signe égal (`=`) suivi d’une valeur après le nom du paramètre de routage définit une valeur par défaut pour le paramètre. Un point d’interrogation (`?`) après le nom du paramètre de routage définit un paramètre facultatif.

Les paramètres de routage ayant une valeur par défaut produisent *toujours* une valeur de routage quand la route correspond. Les paramètres facultatifs ne produisent pas de valeur de route s’il n’y a pas de segment de chemin d’URL correspondant. Pour obtenir une description complète des scénarios et de la syntaxe des modèles de routage, consultez la section [Informations de référence sur les modèles de routage](#route-template-reference).

Dans l’exemple suivant, la définition du paramètre de route `{id:int}` définit une [contrainte de route](#route-constraint-reference) pour le paramètre de route `id` :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ce modèle établit une correspondance avec un chemin d’URL comme `/Products/Details/17`, mais pas `/Products/Details/Apples`. Les contraintes de routage implémentent <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> et inspectent les valeurs de route pour les vérifier. Dans cet exemple, la valeur de route `id` doit être convertible en entier. Pour obtenir une explication des contraintes de route fournies par le framework, consultez [Informations de référence sur les contraintes de route](#route-constraint-reference).

Des surcharges supplémentaires de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> acceptent des values pour `constraints`, `dataTokens` et `defaults`. L’utilisation classique de ces paramètres consiste à passer un objet typé anonymement, où les noms des propriétés du type anonyme correspondent aux noms de paramètre de routage.

Les exemples de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> suivants créent des routes équivalentes :

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> La syntaxe inline pour la définition des contraintes et des valeurs par défaut peut être pratique pour les routes simples. Cependant, certains scénarios, comme les jetons de données, ne sont pas pris en charge par la syntaxe inline.

L’exemple suivant montre quelques autres scénarios :

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Le modèle précédent établit une correspondance avec un chemin d’URL comme `/Blog/All-About-Routing/Introduction` et extrait les valeurs `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Les valeurs de route par défaut pour `controller` et `action` sont produites par la route, même s’il n’existe aucun paramètre de routage correspondant dans le modèle. Il est possible de spécifier des valeurs par défaut dans le modèle de route. Le paramètre de route `article` est défini comme *passe-partout* par la présence d’un double astérisque (`**`) avant le nom du paramètre de route. Les paramètres de routage fourre-tout capturent le reste du chemin d’URL et peuvent également établir une correspondance avec la chaîne vide.

L’exemple suivant ajoute des contraintes de route et des jetons de données :

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Le modèle précédent établit une correspondance avec un chemin d’URL comme `/en-US/Products/5`, et extrait les valeurs `{ controller = Products, action = Details, id = 5 }` et les jetons de données `{ locale = en-US }`.

![Jetons Windows de variables locales](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Génération d’URL de classe de route

La classe <xref:Microsoft.AspNetCore.Routing.Route> peut également effectuer une génération d’URL en combinant un ensemble de valeurs de route et son modèle de routage. Il s’agit logiquement du processus inverse de la mise en correspondance du chemin d’URL.

> [!TIP]
> Pour mieux comprendre la génération d’URL, imaginez l’URL que vous voulez générer, puis pensez à la façon dont un modèle de routage établirait une correspondance avec cette URL. Quelles valeurs seraient produites ? Cela équivaut approximativement à la façon dont la génération d’URL fonctionne dans la classe <xref:Microsoft.AspNetCore.Routing.Route>.

L’exemple suivant utilise une route par défaut ASP.NET Core MVC générale :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Avec les valeurs de route `{ controller = Products, action = List }`, l’URL `/Products/List` est générée. Les valeurs de route remplacent les paramètres de routage correspondant pour former le chemin d’URL. Dans la mesure où `id` est un paramètre de route facultatif, l’URL est générée correctement sans valeur pour `id`.

Avec les valeurs de route `{ controller = Home, action = Index }`, l’URL `/` est générée. Les valeurs de route fournies correspondent aux valeurs par défaut, et les segments correspondant aux valeurs par défaut peuvent être omis sans risque.

Les deux URL générées effectuent un aller-retour avec la définition de route suivante (`/Home/Index` et `/`), et produisent les mêmes valeurs de route que celles utilisées pour générer l’URL.

> [!NOTE]
> Pour générer des URL, une application utilisant ASP.NET Core MVC doit utiliser <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> au lieu d’effectuer un appel directement dans le routage.

Pour plus d’informations sur la génération d’URL, consultez la section [Informations de référence sur la génération d’URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Utilisation du middleware de routage

Référencez le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) dans le fichier projet de l’application.

Ajoutez le routage au conteneur de service dans `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Les routes doivent être configurées dans la méthode `Startup.Configure`. L’exemple d’application utilise les API suivantes :

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Correspond uniquement aux requêtes HTTP.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

Le tableau suivant montre les réponses avec les URI donnés.

| URI                    | response                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Valeurs de route : [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Valeurs de route : [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Valeurs de route : [operation, track], [id, -3] |
| `/package/track/`      | La requête passe à travers ceci, aucune correspondance.              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | La requête passe à travers ceci, correspondance seulement avec HTTP GET. |
| `GET /hello/Joe/Smith` | La requête passe à travers ceci, aucune correspondance.              |

Le framework fournit un ensemble de méthodes d’extension pour la création de routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) :

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Les méthodes `Map[Verb]` utilisent des contraintes pour limiter la route au verbe HTTP dans le nom de la méthode. Par exemple, consultez <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> et <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Informations de référence sur les modèles de routage

Les jetons placés entre accolades (`{ ... }`) définissent des *paramètres de routage* qui sont liés si une correspondance est trouvée pour la route. Vous pouvez définir plusieurs paramètres de routage dans un segment de route, mais ils doivent être séparés par une valeur littérale. Par exemple `{controller=Home}{action=Index}` n’est pas une route valide, car il n’y a aucune valeur littérale entre `{controller}` et `{action}`. Ces paramètres de routage doivent avoir un nom, et ils autorisent la spécification d’attributs supplémentaires.

Un texte littéral autre que les paramètres de routage (par exemple, `{id}`) et le séparateur de chemin `/` doit correspondre au texte présent dans l’URL. La correspondance de texte ne respecte pas la casse et est basée sur la représentation décodée du chemin des URL. Pour mettre en correspondance un délimiteur de paramètre de route littéral (`{` ou `}`), placez-le dans une séquence d’échappement en répétant le caractère (`{{` ou `}}`).

Les modèles d’URL qui tentent de capturer un nom de fichier avec une extension de fichier facultative doivent faire l’objet de considérations supplémentaires. Prenez par exemple le modèle `files/{filename}.{ext?}`. Quand des valeurs existent à la fois pour `filename` et pour `ext`, les deux valeurs sont renseignées. Si seule une valeur existe pour `filename` dans l’URL, une correspondance est trouvée pour la route, car le point final (`.`) est facultatif. Les URL suivantes correspondent à cette route :

* `/files/myFile.txt`
* `/files/myFile`

Vous pouvez utiliser un astérisque (`*`) ou un double astérisque (`**`) comme préfixe d’un paramètre de route à lier au reste de l’URI. Ils sont appelés des paramètres *passe-partout*. Par exemple, `blog/{**slug}` établit une correspondance avec n’importe quel URI commençant par `/blog` et suivi de n’importe quelle valeur, qui est affectée à la valeur de route `slug`. Les paramètres fourre-tout peuvent également établir une correspondance avec la chaîne vide.

Le paramètre fourre-tout place les caractères appropriés dans une séquence d’échappement lorsque la route est utilisée pour générer une URL, y compris les caractères de séparation de chemin (`/`). Par exemple, la route `foo/{*path}` avec les valeurs de route `{ path = "my/path" }` génère `foo/my%2Fpath`. Notez la barre oblique d’échappement. Pour les séparateurs de chemin aller-retour, utilisez le préfixe de paramètre de routage `**`. La route `foo/{**path}` avec `{ path = "my/path" }` génère `foo/my/path`.

Les paramètres de route peuvent avoir des *valeurs par défaut* , désignées en spécifiant la valeur par défaut après le nom du paramètre, séparée par un signe égal (`=`). Par exemple, `{controller=Home}` définit `Home` comme valeur par défaut de `controller`. La valeur par défaut est utilisée si aucune valeur n’est présente dans l’URL pour le paramètre. Vous pouvez rendre facultatifs les paramètres de route en ajoutant un point d’interrogation (`?`) à la fin du nom du paramètre, comme dans `id?`. La différence entre les valeurs facultatives et les paramètres de route par défaut est qu’un paramètre de route ayant une valeur par défaut produit toujours une valeur, tandis qu’un paramètre facultatif a une valeur seulement quand celle-ci est fournie par l’URL de requête.

Les paramètres de route peuvent avoir des contraintes, qui doivent correspondre à la valeur de route liée à partir de l’URL. L’ajout d’un signe deux-points (`:`) et d’un nom de contrainte après le nom du paramètre de routage spécifie une *contrainte inline* sur un paramètre de routage. Si la contrainte nécessite des arguments, ils sont fournis entre parenthèses (`(...)`) après le nom de la contrainte. Il est possible de spécifier plusieurs contraintes inline en ajoutant un autre signe deux-points (`:`) et le nom d’une autre contrainte.

Le nom de la contrainte et les arguments sont passés au service <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> pour créer une instance de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> à utiliser dans le traitement des URL. Par exemple, le modèle de routage `blog/{article:minlength(10)}` spécifie une contrainte `minlength` avec l’argument `10`. Pour plus d’informations sur les contraintes de route et pour obtenir la liste des contraintes fournies par le framework, consultez la section [Informations de référence sur les contraintes de route](#route-constraint-reference).

Les paramètres de route peuvent également contenir des transformateurs de paramètre, qui transforment une valeur de paramètre lors de la génération des liens et de la mise en correspondance des actions et des pages avec les URL. À l’instar des contraintes, les transformateurs de paramètre peuvent être ajoutés inline à un paramètre de routage en ajoutant un signe deux-points (`:`) et le nom du transformateur après le nom du paramètre de routage. Par exemple, le modèle de routage `blog/{article:slugify}` spécifie un transformateur `slugify`. Pour plus d’informations sur les transformateurs de paramètre, consultez la section [Informations de référence sur les transformateurs de paramètre](#parameter-transformer-reference).

Le tableau suivant montre des exemples de modèles de route et leur comportement.

| Modèle de routage                           | Exemple d’URI en correspondance    | URI de la requête&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Correspond seulement au chemin unique `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Correspond à `Page` et le définit sur `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Correspond à `Page` et le définit sur `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mappe au contrôleur `Products` et à l’action `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mappe au contrôleur `Products` et à l’action `Details` (`id` défini sur 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mappe au contrôleur `Home` et à la méthode `Index` (`id` est ignoré).        |

L’utilisation d’un modèle est généralement l’approche la plus simple pour le routage. Il est également possible de spécifier des contraintes et des valeurs par défaut hors du modèle de routage.

> [!TIP]
> Activez la [journalisation](xref:fundamentals/logging/index) pour voir comment les implémentations de routage intégrées, comme <xref:Microsoft.AspNetCore.Routing.Route>, établissent des correspondances avec les requêtes.

## <a name="reserved-routing-names"></a>Noms de routage réservés

Les mots clés suivants sont des noms réservés qui ne peuvent pas être utilisés comme paramètres ou noms de routage :

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Informations de référence sur les contraintes de routage

Les contraintes de route s’exécutent quand une correspondance s’est produite pour l’URL entrante, et le chemin de l’URL est tokenisé en valeurs de route. En général, les contraintes de routage inspectent la valeur de route associée par le biais du modèle de routage, et créent une décision oui/non indiquant si la valeur est, ou non, acceptable. Certaines contraintes de routage utilisent des données hors de la valeur de route pour déterminer si la requête peut être routée. Par exemple, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> peut accepter ou rejeter une requête en fonction de son verbe HTTP. Les contraintes sont utilisées dans le routage des requêtes et la génération des liens.

> [!WARNING]
> N’utilisez pas de contraintes pour la **validation des entrées**. Si des contraintes sont utilisées pour la **validation des entrées** , une entrée non valide génère une réponse *404 - Introuvable* au lieu d’une réponse *400 - Requête incorrecte* avec un message d’erreur approprié. Les contraintes de route sont utilisées pour **lever l’ambiguïté** entre des routes similaires, et non pas pour valider les entrées d’une route particulière.

Le tableau suivant montre des exemples de contrainte de route et leur comportement attendu.

| Contrainte | Exemple | Exemples de correspondances | Notes |
|------------|---------|-----------------|-------|
| `int` | `{id:int}` | `123456789`, `-123456789` | Correspond à n’importe quel entier.|
| `bool` | `{active:bool}` | `true`, `FALSE` | Correspond à `true` ou `false` . Non-respect de la casse.|
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Correspond à une `DateTime` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Correspond à une `decimal` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Correspond à une `double` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Correspond à une `float` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Correspond à une `Guid` valeur valide.|
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Correspond à une `long` valeur valide.|
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La chaîne doit comporter au moins 4 caractères.|
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | La chaîne ne doit pas comporter plus de 8 caractères.|
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La chaîne doit contenir exactement 12 caractères.|
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La chaîne doit être au moins égale à 8 et comporter jusqu’à 16 caractères.|
| `min(value)` | `{age:min(18)}` | `19` | La valeur entière doit être au moins égale à 18.|
| `max(value)` | `{age:max(120)}` | `91` | Valeur entière maximale de 120.|
| `range(min,max)` | `{age:range(18,120)}` | `91` | La valeur entière doit être au moins égale à 18 et 120.|
| `alpha` | `{name:alpha}` | `Rick` | La chaîne doit comporter un ou plusieurs caractères alphabétiques `a` - `z` . Non-respect de la casse.|
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La chaîne doit correspondre à l’expression régulière. Consultez les conseils relatifs à la définition d’une expression régulière.|
| `required` | `{name:required}` | `Rick` | Permet d’appliquer qu’une valeur sans paramètre est présente pendant la génération de l’URL.|

Il est possible d’appliquer plusieurs contraintes séparées par un point-virgule à un même paramètre. Par exemple, la contrainte suivante limite un paramètre à une valeur entière supérieure ou égale à 1 :

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant. ces contraintes partent du principe que l’URL n’est pas localisable. Les contraintes de routage fournies par le framework ne modifient pas les valeurs stockées dans les valeurs de route. Toutes les valeurs de route analysées à partir de l’URL sont stockées sous forme de chaînes. Par exemple, la contrainte `float` tente de convertir la valeur de route en valeur float, mais la valeur convertie est utilisée uniquement pour vérifier qu’elle peut être convertie en valeur float.

## <a name="regular-expressions"></a>Expressions régulières

Le framework ASP.NET Core ajoute `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` au constructeur d’expression régulière. Pour obtenir une description de ces membres, consultez <xref:System.Text.RegularExpressions.RegexOptions>.

Les expressions régulières utilisent des délimiteurs et des jetons similaires à ceux utilisés par le routage et le langage C#. Les jetons d’expression régulière doivent être placés dans une séquence d’échappement. Pour utiliser l’expression régulière `^\d{3}-\d{2}-\d{4}$` dans le routage :

* L’expression doit avoir les caractères de barre oblique inverse uniques `\` fournis dans la chaîne sous la forme de caractères barre oblique inverse doubles `\\` dans le code source.
* L’expression régulière doit nous permettre d' `\\` échapper le caractère d’échappement de la `\` chaîne.
* L’expression régulière n’est pas nécessaire `\\` lors de l’utilisation de [littéraux de chaîne Verbatim](/dotnet/csharp/language-reference/keywords/string).

Pour échapper les caractères de délimiteur de paramètre de routage `{` , `}` ,, `[` `]` , doublez les caractères dans l’expression `{{` , `}` `[[` `]]` ,,. Le tableau suivant montre une expression régulière et la version échappée :

| Expression régulière    | Expression régulière en échappement     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Les expressions régulières utilisées dans le routage commencent souvent par le `^` caractère de signe insertion et correspondent à la position de départ de la chaîne. Les expressions se terminent souvent par le `$` caractère de signe dollar et la fin de la chaîne. Les caractères `^` et `$` garantissent que l’expression régulière établit une correspondance avec la totalité de la valeur du paramètre de route. Sans les caractères `^` et `$`, l’expression régulière peut correspondre à n’importe quelle sous-chaîne dans la chaîne, ce qui est souvent indésirable. Le tableau suivant contient des exemples et explique pourquoi ils établissent ou non une correspondance.

| Expression   | String    | Correspond | Commentaire               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | 123abc456 | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | mz        | Oui   | Correspondance avec l’expression    |
| `[a-z]{2}`   | MZ        | Oui   | Non-respect de la casse    |
| `^[a-z]{2}$` | hello     | Non    | Voir `^` et `$` ci-dessus |
| `^[a-z]{2}$` | 123abc456 | Non    | Voir `^` et `$` ci-dessus |

Pour plus d’informations sur la syntaxe des expressions régulières, consultez [Expressions régulières du .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Pour contraindre un paramètre à un ensemble connu de valeurs possibles, utilisez une expression régulière. Par exemple, `{action:regex(^(list|get|create)$)}` établit une correspondance avec la valeur de route `action` uniquement pour `list`, `get` ou `create`. Si elle est passée dans le dictionnaire de contraintes, la chaîne `^(list|get|create)$` est équivalente. Les contraintes passées dans le dictionnaire de contraintes (c’est-à-dire qui ne sont pas inline dans un modèle) qui ne correspondent pas à l’une des contraintes connues sont également traitées comme des expressions régulières.

## <a name="custom-route-constraints"></a>Contraintes d’itinéraire personnalisées

Outre les contraintes d’itinéraire intégré, les contraintes d’itinéraire personnalisé peuvent être créées en implémentant l’interface <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. L’interface <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contient une méthode unique, `Match`, qui retourne `true` si la contrainte est satisfaite et `false` dans le cas contraire.

Pour utiliser un <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personnalisé, le type de contrainte d’itinéraire doit être inscrit avec le <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de l’application dans le conteneur de service de l’application. Un <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> est un dictionnaire qui mappe les clés de contrainte d’itinéraire aux implémentations <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> qui valident ces contraintes. Le <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> d’une application peut être mis à jour dans `Startup.ConfigureServices` dans le cadre d’un appel [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou en configurant <xref:Microsoft.AspNetCore.Routing.RouteOptions> directement avec `services.Configure<RouteOptions>`. Par exemple :

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

La contrainte peut ensuite être appliquée aux itinéraires de la manière habituelle, en utilisant le nom spécifié lors de l’inscription du type de contrainte. Par exemple :

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Informations de référence sur le transformateur de paramètre

Transformateurs de paramètre :

* Sont exécutés lors de la génération d’un lien pour un <xref:Microsoft.AspNetCore.Routing.Route>.
* Implémentez `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* Sont configurés à l’aide de <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Prennent la valeur de routage du paramètre et la convertissent en une nouvelle valeur de chaîne.
* Aboutissent à l’utilisation de la valeur transformée dans le lien généré.

Par exemple, un transformateur de paramètre `slugify` personnalisé dans le modèle d’itinéraire `blog\{article:slugify}` avec `Url.Action(new { article = "MyTestArticle" })` génère `blog\my-test-article`.

Pour utiliser un transformateur de paramètre dans un modèle d’itinéraire, configurez-le d’abord en utilisant <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> dans `Startup.ConfigureServices` :

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Les transformateurs de paramètre sont utilisés par le framework pour transformer l’URI où un point de terminaison est résolu. Par exemple, ASP.NET Core MVC utilise des transformateurs de paramètre pour convertir la valeur de routage utilisée et la faire correspondre à un `area`, `controller`, `action` et `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Avec la route précédente, l’action `SubscriptionManagementController.GetAll` est mise en correspondance avec l’URI `/subscription-management/get-all`. Un transformateur de paramètre ne modifie pas les valeurs de routage utilisées pour générer un lien. Par exemple, `Url.Action("GetAll", "SubscriptionManagement")` produit `/subscription-management/get-all`.

ASP.NET Core fournit des conventions d’API pour l’utilisation des transformateurs de paramètre avec des routages générés :

* La convention de l’API `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` est fournie avec ASP.NET Core MVC. Cette convention applique un transformateur de paramètre spécifié à tous les routages d’attributs dans l’application. Le transformateur de paramètre transforme les jetons de routage d’attribut quand ils sont remplacés. Pour plus d’informations, consultez [Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Les pages possèdent la `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` Convention d’API. Cette Convention applique un transformateur de paramètre spécifié à toutes les Razor pages découvertes automatiquement. Le transformateur de paramètres transforme les segments de nom de fichier et de dossier des Razor itinéraires de pages. Pour plus d’informations, consultez [Utiliser un transformateur de paramètre pour personnaliser les routages de pages](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Informations de référence sur la génération d’URL

L’exemple suivant montre comment générer un lien vers une route selon un dictionnaire de valeurs de route et un <xref:Microsoft.AspNetCore.Routing.RouteCollection> données.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Le <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> généré à la fin de l’exemple précédent est `/package/create/123`. Le dictionnaire fournit les valeurs de route `operation` et `id` du modèle « Suivi de package de route », `package/{operation}/{id}`. Pour plus d’informations, consultez l’exemple de code dans la section [Utilisation du middleware de routage](#use-routing-middleware) ou l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Le deuxième paramètre pour le constructeur <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> est une collection de *valeurs ambiantes*. Les valeurs ambiantes sont pratiques à utiliser, car elles limitent le nombre de valeurs qu’un développeur doit spécifier dans un contexte de requête. Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens. Dans l’action `About` de `HomeController` d’une application ASP.NET Core MVC, vous n’avez pas besoin de spécifier la valeur de route du contrôleur pour créer un lien vers l’action `Index` : la valeur ambiante de `Home` est utilisée.

Les valeurs ambiantes qui ne correspondent pas à un paramètre sont ignorées. Les valeurs ambiantes sont également ignorées quand une valeur explicitement fournie remplace la valeur ambiante. La mise en correspondance se produit de gauche à droite dans l’URL.

Les valeurs fournies explicitement mais qui n’ont pas de correspondance avec un segment de la route sont ajoutées à la chaîne de requête. Le tableau suivant présente le résultat en cas d’utilisation du modèle de routage `{controller}/{action}/{id?}`.

| Valeurs ambiantes                     | Valeurs explicites                        | Résultats                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Si une route a une valeur par défaut qui ne correspond pas à un paramètre et que cette valeur est explicitement fournie, elle doit correspondre à la valeur par défaut :

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

La génération de liens génère un lien pour cette route seulement quand les valeurs correspondantes pour `controller` et pour `action` sont fournies.

## <a name="complex-segments"></a>Segments complexes

Les segments complexes (par exemple, `[Route("/x{token}y")]`) sont traités par la mise en correspondance des littéraux de droite à gauche de manière non gourmande. Consultez [ce code](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) pour obtenir une explication détaillée de la façon dont les segments complexes sont mis en correspondance. [L’exemple de code](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) n’est pas utilisé par ASP.NET Core, mais il fournit une bonne explication des segments complexes.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Le routage est responsable du mappage des URI des requêtes aux gestionnaires de routage et de la distribution des requêtes entrantes. Les routes sont définies dans l’application et configurées au démarrage de l’application. Une route peut éventuellement extraire des valeurs de l’URL contenue dans la requête, et ces valeurs peuvent ensuite être utilisées pour le traitement de la requête. En utilisant des routes configurées à partir de l’application, le routage est en mesure de générer des URL qui mappent à des gestionnaires de routage.

Pour utiliser les scénarios de routage les plus récents dans ASP.NET Core 2.1, spécifiez la [version de compatibilité](xref:mvc/compatibility-version) à l’inscription des services MVC dans `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Ce document traite du routage ASP.NET Core de bas niveau. Pour plus d’informations sur le routage ASP.NET Core MVC, consultez <xref:mvc/controllers/routing>. Pour plus d’informations sur les conventions de routage dans les Razor pages, consultez <xref:razor-pages/razor-pages-conventions> .

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Concepts de base du routage

La plupart des applications doivent choisir un schéma de routage de base et descriptif pour que les URL soient lisibles et explicites. La route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` :

* Prend en charge un schéma de routage de base et descriptif.
* Est un point de départ pratique pour les applications basées sur une interface utilisateur.

Les développeurs ajoutent fréquemment des routes laconiques supplémentaires aux zones à trafic élevé de l’application dans des situations particulières (par exemple des points de terminaison de blog ou d’e-commerce) en utilisant le [routage d’attributs](xref:mvc/controllers/routing#attribute-routing) ou des routes conventionnelles dédiées.

Les API web doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources dans lequel les opérations sont représentées par des verbes HTTP. Cela signifie que plusieurs opérations (comme GET et POST) sur la même ressource logique utilisent la même URL. Le routage d’attributs fournit le niveau de contrôle nécessaire pour concevoir avec soin la disposition des points de terminaison publics d’une API.

Razor Pages les applications utilisent le routage conventionnel par défaut pour servir des ressources nommées dans le dossier *pages* d’une application. Des conventions supplémentaires vous permettent de personnaliser le Razor comportement de routage des pages. Pour plus d’informations, consultez <xref:razor-pages/index> et <xref:razor-pages/razor-pages-conventions>.

La prise en charge de la génération d’URL permet de développer l’application sans coder en dur les URL pour lier l’application. Cette prise en charge permet de commencer avec une configuration de routage de base, puis de modifier les routes une fois que la disposition des ressources de l’application est déterminée.

Le routage utilise des implémentations d’itinéraires de <xref:Microsoft.AspNetCore.Routing.IRouter> à :

* Mapper les requêtes entrantes à des *gestionnaires de routage*.
* Générer les URL utilisées dans les réponses.

Par défaut, une application a une seule collection de routes. Quand une requête arrive, les routes de la collection sont traitées dans l’ordre où elles se trouvent dans la collection. Le framework tente de mettre en correspondance l’URL d’une requête entrante avec une route de la collection en appelant la méthode <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> sur chaque route de la collection. Une réponse peut utiliser le routage pour générer des URL (par exemple pour la redirection ou pour des liens) en fonction des informations des routes et éviter ainsi les URL codées en dur, ce qui facilite la maintenance.

Le système de routage a les caractéristiques suivantes :

* Une syntaxe des modèles de route est utilisée pour définir des routes avec des paramètres de route tokenisés.
* La configuration de points de terminaison de style conventionnel et de style « attribut » est autorisée.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> est utilisé pour déterminer si un paramètre d’URL contient une valeur valide pour une contrainte de point de terminaison donné.
* Les modèles d’application, tels que MVC/ Razor pages, inscrivent tous leurs itinéraires, qui ont une implémentation prévisible des scénarios de routage.
* Une réponse peut utiliser le routage pour générer des URL (par exemple pour la redirection ou pour des liens) en fonction des informations des routes et éviter ainsi les URL codées en dur, ce qui facilite la maintenance.
* La génération d’URL est basée sur des routes, qui prennent en charge l’extensibilité arbitraire. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offre des méthodes pour générer des URL.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Le routage est connecté au pipeline de [l’intergiciel (middleware)](xref:fundamentals/middleware/index) par la classe <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>. [ASP.net Core MVC](xref:mvc/overview) ajoute le routage au pipeline de l’intergiciel (middleware) dans le cadre de sa configuration et gère le routage dans les applications MVC et Razor pages. Pour découvrir comment utiliser le routage en tant que composant autonome, consultez la section [Utiliser le middleware de routage](#use-routing-middleware).

### <a name="url-matching"></a>Correspondance d’URL

La correspondance d’URL est le processus par lequel le routage distribue une requête entrante à un *gestionnaire*. Ce processus est basé sur des données présentes dans le chemin de l’URL, mais il peut être étendu pour prendre en compte toutes les données de la requête. La possibilité de distribuer des requêtes à des gestionnaires distincts est essentielle pour adapter la taille et la complexité d’une application.

Les requêtes entrantes entrent dans <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, qui appelle la méthode <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> sur chaque route dans l’ordre. L’instance <xref:Microsoft.AspNetCore.Routing.IRouter> détermine s’il faut *gérer* la requête en affectant à [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) un <xref:Microsoft.AspNetCore.Http.RequestDelegate> non null. Si une route définit un gestionnaire pour la requête, le traitement de la route s’arrête et le gestionnaire est appelé pour traiter la requête. Si aucun gestionnaire de routage n’est trouvé pour traiter la requête, le middleware passe la requête au middleware suivant dans le pipeline de requête.

L’entrée principale de <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> est le [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associé à la requête actuelle. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) et [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) sont des sorties définies après la mise en correspondance d’une route.

Une correspondance qui appelle <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> définit également les propriétés de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) sur des valeurs appropriées en fonction du traitement des requêtes effectué jusqu’à présent.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) est un dictionnaire de *valeurs de route* produites à partir de la route. Ces valeurs sont généralement déterminées en décomposant l’URL en jetons. Elles peuvent être utilisées pour accepter l’entrée d’utilisateur ou pour prendre d’autres décisions relatives à la distribution à l’intérieur de l’application.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) est un conteneur de propriétés des données supplémentaires associées à la route mise en correspondance. Les <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> sont fournis pour prendre en charge l’association de données d’état à chaque route, de façon que l’application puisse prendre des décisions en fonction de la route avec laquelle la correspondance a été établie. Ces valeurs sont définies par le développeur et n’affectent **pas** le comportement du routage de quelque manière que ce soit. De plus, les valeurs dissimulées dans [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) peuvent être de n’importe quel type, contrairement aux valeurs [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), qui doivent être convertibles en chaînes et à partir de chaînes.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) est une liste des routes qui ont participé à la mise en correspondance correcte de la requête. Les routes peuvent être imbriquées les unes dans les autres. La propriété <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> reflète le chemin à travers l’arborescence logique des routes qui ont généré une correspondance. En général le premier élément de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> est la collection de routes et il doit être utilisé pour la génération d’URL. Le dernier élément de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> est le gestionnaire de routage avec lequel la correspondance a été établie.

<a name="lg"></a>

### <a name="url-generation"></a>Génération d’URL

La génération d’URL est le processus par lequel le routage peut créer un chemin d’URL basé sur un ensemble de valeurs de route. Ceci permet une séparation logique entre les gestionnaires de routes et les URL qui y accèdent.

La génération d’URL suit un processus itératif similaire, mais elle commence par un appel du code utilisateur ou de framework à la méthode <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> de la collection de routes. La méthode <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> de chaque *route* est appelée en séquence jusqu’à ce qu’un <xref:Microsoft.AspNetCore.Routing.VirtualPathData> non null soit retourné.

Les entrées principales dans <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> sont les suivantes :

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Les routes utilisent principalement les valeurs de routage fournies par <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> et par <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> pour décider où il est possible de générer une URL et quelles sont les valeurs à inclure. Les <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> sont l’ensemble des valeurs de route produites à partir de la mise en correspondance de la requête actuelle. En revanche, les <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> sont les valeurs de route qui spécifient la façon de générer l’URL souhaitée pour l’opération actuelle. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> est fourni au cas où une route aurait besoin d’obtenir des services ou des données supplémentaires associés au contexte actuel.

> [!TIP]
> Considérez [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) comme un ensemble de remplacements pour [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). La génération d’URL tente de réutiliser des valeurs de route de la requête actuelle afin de générer les URL pour des liens utilisant la même route ou les mêmes valeurs de route.

La sortie de <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> est un <xref:Microsoft.AspNetCore.Routing.VirtualPathData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> est l’équivalent de <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> contient le <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> de l’URL de sortie et des propriétés supplémentaires qui doivent être définies par la route.

La propriété [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) contient le *chemin virtuel* produit par la route. Selon vos besoins, vous devrez peut-être traiter plus avant le chemin. Si vous souhaitez afficher l’URL générée au format HTML, ajoutez un préfixe au chemin de base de l’application.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) est une référence à la route qui a généré avec succès l’URL.

La propriété [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) est un dictionnaire de données supplémentaires relatives à la route qui a généré l’URL. Il s’agit de l’équivalent de [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Créer des itinéraires

Le routage fournit la classe <xref:Microsoft.AspNetCore.Routing.Route> comme implémentation standard d’<xref:Microsoft.AspNetCore.Routing.IRouter>. <xref:Microsoft.AspNetCore.Routing.Route> utilise la syntaxe des *modèles de routage* pour définir des modèles à mettre en correspondance avec le chemin d’URL quand <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> est appelé. <xref:Microsoft.AspNetCore.Routing.Route> utilise le même modèle de routage pour générer une URL quand <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> est appelé.

La plupart des applications créent des routes en appelant <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ou l’une des méthodes d’extension similaires définies sur <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Toutes les méthodes d’extension de <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> créent une instance de <xref:Microsoft.AspNetCore.Routing.Route> et l’ajoutent à la collection de routes.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> n’accepte pas de paramètre de gestionnaire de routage. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ajoute uniquement des routes gérées par <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Le gestionnaire par défaut est un `IRouter`, et le gestionnaire est susceptible de ne pas traiter la requête. Par exemple, ASP.NET Core MVC est généralement configuré comme gestionnaire par défaut qui gère uniquement les requêtes correspondant à un contrôleur et une action disponibles. Pour plus d’informations sur le routage dans MVC, consultez <xref:mvc/controllers/routing>.

L’exemple de code suivant est un exemple d’appel à <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> utilisé par une définition de route ASP.NET Core MVC classique :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ce modèle établit une correspondance avec un chemin d’URL et extrait les valeurs de route . Par exemple, le chemin `/Products/Details/17` génère les valeurs de route suivantes : `{ controller = Products, action = Details, id = 17 }`.

Les valeurs de route sont déterminées en divisant le chemin d’URL en segments et en mettant en correspondance chaque segment avec le nom des *paramètres de routage* dans le modèle de routage. Les paramètres de routage sont nommés. Vous définissez des paramètres en plaçant leur nom entre des accolades `{ ... }`.

Le modèle précédent peut également mettre en correspondance le chemin d’URL `/` et produire les valeurs `{ controller = Home, action = Index }`. Cela s’explique par le fait que les paramètres de routage `{controller}` et `{action}` ont des valeurs par défaut et que le paramètre de routage `id` est facultatif. Un signe égal (`=`) suivi d’une valeur après le nom du paramètre de routage définit une valeur par défaut pour le paramètre. Un point d’interrogation (`?`) après le nom du paramètre de routage définit un paramètre facultatif.

Les paramètres de routage ayant une valeur par défaut produisent *toujours* une valeur de routage quand la route correspond. Les paramètres facultatifs ne produisent pas de valeur de route s’il n’y a pas de segment de chemin d’URL correspondant. Pour obtenir une description complète des scénarios et de la syntaxe des modèles de routage, consultez la section [Informations de référence sur les modèles de routage](#route-template-reference).

Dans l’exemple suivant, la définition du paramètre de route `{id:int}` définit une [contrainte de route](#route-constraint-reference) pour le paramètre de route `id` :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ce modèle établit une correspondance avec un chemin d’URL comme `/Products/Details/17`, mais pas `/Products/Details/Apples`. Les contraintes de routage implémentent <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> et inspectent les valeurs de route pour les vérifier. Dans cet exemple, la valeur de route `id` doit être convertible en entier. Pour obtenir une explication des contraintes de route fournies par le framework, consultez [Informations de référence sur les contraintes de route](#route-constraint-reference).

Des surcharges supplémentaires de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> acceptent des values pour `constraints`, `dataTokens` et `defaults`. L’utilisation classique de ces paramètres consiste à passer un objet typé anonymement, où les noms des propriétés du type anonyme correspondent aux noms de paramètre de routage.

Les exemples de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> suivants créent des routes équivalentes :

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> La syntaxe inline pour la définition des contraintes et des valeurs par défaut peut être pratique pour les routes simples. Cependant, certains scénarios, comme les jetons de données, ne sont pas pris en charge par la syntaxe inline.

L’exemple suivant montre quelques autres scénarios :

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Le modèle précédent établit une correspondance avec un chemin d’URL comme `/Blog/All-About-Routing/Introduction` et extrait les valeurs `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Les valeurs de route par défaut pour `controller` et `action` sont produites par la route, même s’il n’existe aucun paramètre de routage correspondant dans le modèle. Il est possible de spécifier des valeurs par défaut dans le modèle de route. Le paramètre de route `article` est défini comme *passe-partout* par la présence d’un astérisque (`*`) avant le nom du paramètre de route. Les paramètres de routage fourre-tout capturent le reste du chemin d’URL et peuvent également établir une correspondance avec la chaîne vide.

L’exemple suivant ajoute des contraintes de route et des jetons de données :

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Le modèle précédent établit une correspondance avec un chemin d’URL comme `/en-US/Products/5`, et extrait les valeurs `{ controller = Products, action = Details, id = 5 }` et les jetons de données `{ locale = en-US }`.

![Jetons Windows de variables locales](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Génération d’URL de classe de route

La classe <xref:Microsoft.AspNetCore.Routing.Route> peut également effectuer une génération d’URL en combinant un ensemble de valeurs de route et son modèle de routage. Il s’agit logiquement du processus inverse de la mise en correspondance du chemin d’URL.

> [!TIP]
> Pour mieux comprendre la génération d’URL, imaginez l’URL que vous voulez générer, puis pensez à la façon dont un modèle de routage établirait une correspondance avec cette URL. Quelles valeurs seraient produites ? Cela équivaut approximativement à la façon dont la génération d’URL fonctionne dans la classe <xref:Microsoft.AspNetCore.Routing.Route>.

L’exemple suivant utilise une route par défaut ASP.NET Core MVC générale :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Avec les valeurs de route `{ controller = Products, action = List }`, l’URL `/Products/List` est générée. Les valeurs de route remplacent les paramètres de routage correspondant pour former le chemin d’URL. Dans la mesure où `id` est un paramètre de route facultatif, l’URL est générée correctement sans valeur pour `id`.

Avec les valeurs de route `{ controller = Home, action = Index }`, l’URL `/` est générée. Les valeurs de route fournies correspondent aux valeurs par défaut, et les segments correspondant aux valeurs par défaut peuvent être omis sans risque.

Les deux URL générées effectuent un aller-retour avec la définition de route suivante (`/Home/Index` et `/`), et produisent les mêmes valeurs de route que celles utilisées pour générer l’URL.

> [!NOTE]
> Pour générer des URL, une application utilisant ASP.NET Core MVC doit utiliser <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> au lieu d’effectuer un appel directement dans le routage.

Pour plus d’informations sur la génération d’URL, consultez la section [Informations de référence sur la génération d’URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Utiliser l’intergiciel (middleware) de routage

Référencez le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) dans le fichier projet de l’application.

Ajoutez le routage au conteneur de service dans `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Les routes doivent être configurées dans la méthode `Startup.Configure`. L’exemple d’application utilise les API suivantes :

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Correspond uniquement aux requêtes HTTP.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

Le tableau suivant montre les réponses avec les URI donnés.

| URI                    | response                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Valeurs de route : [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Valeurs de route : [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Valeurs de route : [operation, track], [id, -3] |
| `/package/track/`      | La requête passe à travers ceci, aucune correspondance.              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | La requête passe à travers ceci, correspondance seulement avec HTTP GET. |
| `GET /hello/Joe/Smith` | La requête passe à travers ceci, aucune correspondance.              |

Si vous configurez une seule route, appelez <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> en passant une instance `IRouter`. Vous n’avez pas besoin d’utiliser <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

Le framework fournit un ensemble de méthodes d’extension pour la création de routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) :

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Certaines des méthodes listées, comme <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, nécessitent un <xref:Microsoft.AspNetCore.Http.RequestDelegate>. <xref:Microsoft.AspNetCore.Http.RequestDelegate> est utilisé comme *gestionnaire de routage* quand une correspondance est trouvée pour la route. D’autres méthodes de cette famille permettent de configurer un pipeline de middleware utilisé comme gestionnaire de routage. Si la méthode `Map*` n’accepte pas de gestionnaire, comme <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, elle utilise le <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.

Les méthodes `Map[Verb]` utilisent des contraintes pour limiter la route au verbe HTTP dans le nom de la méthode. Par exemple, consultez <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> et <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Informations de référence sur les modèles de routage

Les jetons placés entre accolades (`{ ... }`) définissent des *paramètres de routage* qui sont liés si une correspondance est trouvée pour la route. Vous pouvez définir plusieurs paramètres de routage dans un segment de route, mais ils doivent être séparés par une valeur littérale. Par exemple `{controller=Home}{action=Index}` n’est pas une route valide, car il n’y a aucune valeur littérale entre `{controller}` et `{action}`. Ces paramètres de routage doivent avoir un nom, et ils autorisent la spécification d’attributs supplémentaires.

Un texte littéral autre que les paramètres de routage (par exemple, `{id}`) et le séparateur de chemin `/` doit correspondre au texte présent dans l’URL. La correspondance de texte ne respecte pas la casse et est basée sur la représentation décodée du chemin des URL. Pour mettre en correspondance un délimiteur de paramètre de route littéral (`{` ou `}`), placez-le dans une séquence d’échappement en répétant le caractère (`{{` ou `}}`).

Les modèles d’URL qui tentent de capturer un nom de fichier avec une extension de fichier facultative doivent faire l’objet de considérations supplémentaires. Prenez par exemple le modèle `files/{filename}.{ext?}`. Quand des valeurs existent à la fois pour `filename` et pour `ext`, les deux valeurs sont renseignées. Si seule une valeur existe pour `filename` dans l’URL, une correspondance est trouvée pour la route, car le point final (`.`) est facultatif. Les URL suivantes correspondent à cette route :

* `/files/myFile.txt`
* `/files/myFile`

Vous pouvez utiliser l’astérisque (`*`) comme préfixe d’un paramètre de route à lier au reste de l’URI. Cela s’appelle un paramètre *fourre-tout*. Par exemple, `blog/{*slug}` établit une correspondance avec n’importe quel URI commençant par `/blog` et suivi de n’importe quelle valeur, qui est affectée à la valeur de route `slug`. Les paramètres fourre-tout peuvent également établir une correspondance avec la chaîne vide.

Le paramètre fourre-tout place les caractères appropriés dans une séquence d’échappement lorsque la route est utilisée pour générer une URL, y compris les caractères de séparation de chemin (`/`). Par exemple, la route `foo/{*path}` avec les valeurs de route `{ path = "my/path" }` génère `foo/my%2Fpath`. Notez la barre oblique d’échappement.

Les paramètres de route peuvent avoir des *valeurs par défaut* , désignées en spécifiant la valeur par défaut après le nom du paramètre, séparée par un signe égal (`=`). Par exemple, `{controller=Home}` définit `Home` comme valeur par défaut de `controller`. La valeur par défaut est utilisée si aucune valeur n’est présente dans l’URL pour le paramètre. Vous pouvez rendre facultatifs les paramètres de route en ajoutant un point d’interrogation (`?`) à la fin du nom du paramètre, comme dans `id?`. La différence entre les valeurs facultatives et les paramètres de route par défaut est qu’un paramètre de route ayant une valeur par défaut produit toujours une valeur, tandis qu’un paramètre facultatif a une valeur seulement quand celle-ci est fournie par l’URL de requête.

Les paramètres de route peuvent avoir des contraintes, qui doivent correspondre à la valeur de route liée à partir de l’URL. L’ajout d’un signe deux-points (`:`) et d’un nom de contrainte après le nom du paramètre de routage spécifie une *contrainte inline* sur un paramètre de routage. Si la contrainte nécessite des arguments, ils sont fournis entre parenthèses (`(...)`) après le nom de la contrainte. Il est possible de spécifier plusieurs contraintes inline en ajoutant un autre signe deux-points (`:`) et le nom d’une autre contrainte.

Le nom de la contrainte et les arguments sont passés au service <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> pour créer une instance de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> à utiliser dans le traitement des URL. Par exemple, le modèle de routage `blog/{article:minlength(10)}` spécifie une contrainte `minlength` avec l’argument `10`. Pour plus d’informations sur les contraintes de route et pour obtenir la liste des contraintes fournies par le framework, consultez la section [Informations de référence sur les contraintes de route](#route-constraint-reference).

Le tableau suivant montre des exemples de modèles de route et leur comportement.

| Modèle de routage                           | Exemple d’URI en correspondance    | URI de la requête&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Correspond seulement au chemin unique `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Correspond à `Page` et le définit sur `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Correspond à `Page` et le définit sur `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mappe au contrôleur `Products` et à l’action `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mappe au contrôleur `Products` et à l’action `Details` (`id` défini sur 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mappe au contrôleur `Home` et à la méthode `Index` (`id` est ignoré).        |

L’utilisation d’un modèle est généralement l’approche la plus simple pour le routage. Il est également possible de spécifier des contraintes et des valeurs par défaut hors du modèle de routage.

> [!TIP]
> Activez la [journalisation](xref:fundamentals/logging/index) pour voir comment les implémentations de routage intégrées, comme <xref:Microsoft.AspNetCore.Routing.Route>, établissent des correspondances avec les requêtes.

## <a name="route-constraint-reference"></a>Informations de référence sur les contraintes de routage

Les contraintes de route s’exécutent quand une correspondance s’est produite pour l’URL entrante, et le chemin de l’URL est tokenisé en valeurs de route. En général, les contraintes de routage inspectent la valeur de route associée par le biais du modèle de routage, et créent une décision oui/non indiquant si la valeur est, ou non, acceptable. Certaines contraintes de routage utilisent des données hors de la valeur de route pour déterminer si la requête peut être routée. Par exemple, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> peut accepter ou rejeter une requête en fonction de son verbe HTTP. Les contraintes sont utilisées dans le routage des requêtes et la génération des liens.

> [!WARNING]
> N’utilisez pas de contraintes pour la **validation des entrées**. Si des contraintes sont utilisées pour la **validation des entrées** , une entrée non valide génère une réponse *404 - Introuvable* au lieu d’une réponse *400 - Requête incorrecte* avec un message d’erreur approprié. Les contraintes de route sont utilisées pour **lever l’ambiguïté** entre des routes similaires, et non pas pour valider les entrées d’une route particulière.

Le tableau suivant montre des exemples de contrainte de route et leur comportement attendu.

| contrainte | Exemple | Exemples de correspondances | Notes |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Correspond à n’importe quel entier |
| `bool` | `{active:bool}` | `true`, `FALSE` | Correspond à `true` ou à `false` (non-respect de la casse) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Correspond à une `DateTime` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Correspond à une `decimal` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Correspond à une `double` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Correspond à une `float` valeur valide dans la culture dite indifférente. Consultez l’avertissement précédent.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Correspond à une valeur `Guid` valide |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Correspond à une valeur `long` valide |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La chaîne doit comporter au moins 4 caractères |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | La chaîne ne doit pas comporter plus de 8 caractères |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La chaîne doit comporter exactement 12 caractères |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La chaîne doit comporter au moins 8 caractères et pas plus de 16 caractères |
| `min(value)` | `{age:min(18)}` | `19` | La valeur entière doit être au moins égale à 18 |
| `max(value)` | `{age:max(120)}` | `91` | La valeur entière ne doit pas être supérieure à 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | La valeur entière doit être au moins égale à 18 mais ne doit pas être supérieure à 120 |
| `alpha` | `{name:alpha}` | `Rick` | La chaîne doit se composer d’un ou de plusieurs caractères alphabétiques (`a`-`z`, non-respect de la casse) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La chaîne doit correspondre à l’expression régulière (voir les conseils relatifs à la définition d’une expression régulière) |
| `required` | `{name:required}` | `Rick` | Utilisé pour garantir qu’une valeur autre qu’un paramètre est présente pendant la génération de l’URL |

Il est possible d’appliquer plusieurs contraintes séparées par un point-virgule à un même paramètre. Par exemple, la contrainte suivante limite un paramètre à une valeur entière supérieure ou égale à 1 :

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant. ces contraintes partent du principe que l’URL n’est pas localisable. Les contraintes de routage fournies par le framework ne modifient pas les valeurs stockées dans les valeurs de route. Toutes les valeurs de route analysées à partir de l’URL sont stockées sous forme de chaînes. Par exemple, la contrainte `float` tente de convertir la valeur de route en valeur float, mais la valeur convertie est utilisée uniquement pour vérifier qu’elle peut être convertie en valeur float.

## <a name="regular-expressions"></a>Expressions régulières

Le framework ASP.NET Core ajoute `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` au constructeur d’expression régulière. Pour obtenir une description de ces membres, consultez <xref:System.Text.RegularExpressions.RegexOptions>.

Les expressions régulières utilisent les délimiteurs et des jetons semblables à ceux utilisés par le service de routage et le langage C#. Les jetons d’expression régulière doivent être placés dans une séquence d’échappement. Pour que l’expression régulière `^\d{3}-\d{2}-\d{4}$` puisse être utilisée dans le routage, il faut que les caractères `\` (une seule barre oblique inverse) soit fournis dans la chaîne sous la forme `\\` (double barre oblique inverse) dans le fichier source C#, pour placer en échappement le caractère d’échappement de chaîne `\` (sauf en cas d’utilisation de [littéraux de chaîne textuelle](/dotnet/csharp/language-reference/keywords/string)). Pour placer en échappement les caractères de délimiteur de paramètre de route (`{`, `}`, `[`, `]`), doublez les caractères dans l’expression (`{{`, `}`, `[[`, `]]`). Le tableau suivant montre une expression régulière et la version placée en échappement.

| Expression régulière    | Expression régulière en échappement     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Les expressions régulières utilisées dans le routage commencent souvent par un caret (`^`) et correspondent à la position de début de la chaîne. Les expressions se terminent souvent par le signe dollar (`$`) de caractère et correspondent à la fin de la chaîne. Les caractères `^` et `$` garantissent que l’expression régulière établit une correspondance avec la totalité de la valeur du paramètre de route. Sans les caractères `^` et `$`, l’expression régulière peut correspondre à n’importe quelle sous-chaîne dans la chaîne, ce qui est souvent indésirable. Le tableau suivant contient des exemples et explique pourquoi ils établissent ou non une correspondance.

| Expression   | String    | Correspond | Commentaire               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | 123abc456 | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | mz        | Oui   | Correspondance avec l’expression    |
| `[a-z]{2}`   | MZ        | Oui   | Non-respect de la casse    |
| `^[a-z]{2}$` | hello     | Non    | Voir `^` et `$` ci-dessus |
| `^[a-z]{2}$` | 123abc456 | Non    | Voir `^` et `$` ci-dessus |

Pour plus d’informations sur la syntaxe des expressions régulières, consultez [Expressions régulières du .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Pour contraindre un paramètre à un ensemble connu de valeurs possibles, utilisez une expression régulière. Par exemple, `{action:regex(^(list|get|create)$)}` établit une correspondance avec la valeur de route `action` uniquement pour `list`, `get` ou `create`. Si elle est passée dans le dictionnaire de contraintes, la chaîne `^(list|get|create)$` est équivalente. Les contraintes passées dans le dictionnaire de contraintes (c’est-à-dire qui ne sont pas inline dans un modèle) qui ne correspondent pas à l’une des contraintes connues sont également traitées comme des expressions régulières.

## <a name="custom-route-constraints"></a>Contraintes d’itinéraire personnalisé

Outre les contraintes d’itinéraire intégré, les contraintes d’itinéraire personnalisé peuvent être créées en implémentant l’interface <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. L’interface <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contient une méthode unique, `Match`, qui retourne `true` si la contrainte est satisfaite et `false` dans le cas contraire.

Pour utiliser un <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personnalisé, le type de contrainte d’itinéraire doit être inscrit avec le <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de l’application dans le conteneur de service de l’application. Un <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> est un dictionnaire qui mappe les clés de contrainte d’itinéraire aux implémentations <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> qui valident ces contraintes. Le <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> d’une application peut être mis à jour dans `Startup.ConfigureServices` dans le cadre d’un appel [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou en configurant <xref:Microsoft.AspNetCore.Routing.RouteOptions> directement avec `services.Configure<RouteOptions>`. Par exemple :

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

La contrainte peut ensuite être appliquée aux itinéraires de la manière habituelle, en utilisant le nom spécifié lors de l’inscription du type de contrainte. Par exemple :

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>Informations de référence sur la génération d’URL

L’exemple suivant montre comment générer un lien vers une route selon un dictionnaire de valeurs de route et un <xref:Microsoft.AspNetCore.Routing.RouteCollection> données.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Le <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> généré à la fin de l’exemple précédent est `/package/create/123`. Le dictionnaire fournit les valeurs de route `operation` et `id` du modèle « Suivi de package de route », `package/{operation}/{id}`. Pour plus d’informations, consultez l’exemple de code dans la section [Utilisation du middleware de routage](#use-routing-middleware) ou l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Le deuxième paramètre pour le constructeur <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> est une collection de *valeurs ambiantes*. Les valeurs ambiantes sont pratiques à utiliser, car elles limitent le nombre de valeurs qu’un développeur doit spécifier dans un contexte de requête. Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens. Dans l’action `About` de `HomeController` d’une application ASP.NET Core MVC, vous n’avez pas besoin de spécifier la valeur de route du contrôleur pour créer un lien vers l’action `Index` : la valeur ambiante de `Home` est utilisée.

Les valeurs ambiantes qui ne correspondent pas à un paramètre sont ignorées. Les valeurs ambiantes sont également ignorées quand une valeur explicitement fournie remplace la valeur ambiante. La mise en correspondance se produit de gauche à droite dans l’URL.

Les valeurs fournies explicitement mais qui n’ont pas de correspondance avec un segment de la route sont ajoutées à la chaîne de requête. Le tableau suivant présente le résultat en cas d’utilisation du modèle de routage `{controller}/{action}/{id?}`.

| Valeurs ambiantes                     | Valeurs explicites                        | Résultats                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Si une route a une valeur par défaut qui ne correspond pas à un paramètre et que cette valeur est explicitement fournie, elle doit correspondre à la valeur par défaut :

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

La génération de liens génère un lien pour cette route seulement quand les valeurs correspondantes pour `controller` et pour `action` sont fournies.

## <a name="complex-segments"></a>Segments complexes

Les segments complexes (par exemple, `[Route("/x{token}y")]`) sont traités par la mise en correspondance des littéraux de droite à gauche de manière non gourmande. Consultez [ce code](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) pour obtenir une explication détaillée de la façon dont les segments complexes sont mis en correspondance. [L’exemple de code](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) n’est pas utilisé par ASP.NET Core, mais il fournit une bonne explication des segments complexes.

::: moniker-end
