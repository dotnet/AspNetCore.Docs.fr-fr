---
title: Session dans ASP.NET Core
author: rick-anderson
description: Découvrez les approches permettant de préserver la session entre les demandes.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
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
uid: fundamentals/app-state
ms.openlocfilehash: c11b748f9d79235b14c9541019da6e1fb3428af6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93051406"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Gestion de session et d’état dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)et [Diana Larose](https://github.com/DianaLaRose)

HTTP est un protocole sans état. Par défaut, les requêtes HTTP sont des messages indépendants qui ne conservent pas les valeurs utilisateur. Cet article décrit plusieurs approches permettant de conserver les données utilisateur entre les demandes.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gestion de l'état

L’état peut être stocké à l’aide de plusieurs approches. Chacune d’elles est abordée plus loin dans cette rubrique.

| Approche du stockage | Mécanisme de stockage |
| ---------------- | ----------------- |
| [Cookiex](#cookies) | HTTP cookie s. Peut inclure des données stockées à l’aide du code d’application côté serveur. |
| [État de session](#session-state) | HTTP cookie s et code d’application côté serveur |
| [TempData](#tempdata) | HTTP cookie s ou état de session |
| [Chaînes de requête](#query-strings) | Chaînes de requête HTTP |
| [Champs masqués](#hidden-fields) | Champs de formulaires HTTP |
| [HttpContext.Items](#httpcontextitems) | Code d’application côté serveur |
| [Cache](#cache) | Code d’application côté serveur |

## <a name="no-loccookies"></a>Cookies

Cookiestocke les données entre les demandes. Étant donné que les cookie sont envoyés avec chaque demande, leur taille doit être réduite à un minimum. Dans l’idéal, seul un identificateur doit être stocké dans un cookie avec les données stockées par l’application. La plupart des navigateurs limitent la cookie taille à 4096 octets. Seul un nombre limité de cookie s est disponible pour chaque domaine.

Étant donné que cookie les s sont sujettes à falsification, elles doivent être validées par l’application. Cookiepeuvent être supprimés par les utilisateurs et expirent sur les clients. Toutefois, cookie les s sont généralement la forme la plus durable de la persistance des données sur le client.

Cookieles s sont souvent utilisées pour la personnalisation, où le contenu est personnalisé pour un utilisateur connu. L’utilisateur est uniquement identifié, et pas authentifié dans la plupart des cas. Le cookie peut stocker le nom de l’utilisateur, le nom du compte ou un ID d’utilisateur unique, tel qu’un GUID. Le cookie peut être utilisé pour accéder aux paramètres personnalisés de l’utilisateur, tels que la couleur d’arrière-plan de votre site Web préféré.

Consultez les [RGPD (General Data Protection Regulations) de l’Union européenne](https://ec.europa.eu/info/law/law-topic/data-protection) lors de l’émission cookie et du traitement des problèmes de confidentialité. Pour plus d’informations, consultez [Prise en charge du règlement général sur la protection des données (RGPD) de l’Union Européenne dans ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>État de la session

L’état de session est un scénario ASP.NET Core pour le stockage des données utilisateur pendant que l’utilisateur parcourt une application web. L’état de session utilise un magasin tenu à jour par l’application afin de conserver les données entre les requêtes d’un client. Les données de session sont soutenus par un cache et considérées comme des données éphémères. Le site doit continuer à fonctionner sans les données de session. Les données d’application critiques doivent être stockées dans la base de données utilisateur et mises en cache dans la session uniquement à des fins d’optimisation des performances.

La session n’est pas prise en charge dans les [SignalR](xref:signalr/index) applications, car un [ SignalR Hub](xref:signalr/hubs) peut s’exécuter indépendamment d’un contexte http. Par exemple, cela peut se produire quand une longue requête d’interrogation est ouverte par un hub au-delà de la durée de vie du contexte de la requête HTTP.

ASP.NET Core conserve l’état de session en fournissant un cookie au client qui contient un ID de session. cookieID de session :

* Est envoyé à l’application avec chaque demande.
* Est utilisé par l’application pour extraire les données de session.

L’état de session présente les comportements suivants :

* La session cookie est spécifique au navigateur. Les sessions ne sont pas partagées entre les navigateurs.
* cookieLes sessions sont supprimées à la fin de la session du navigateur.
* Si une cookie est reçue pour une session ayant expiré, une nouvelle session qui utilise la même session est créée cookie .
* Les sessions vides ne sont pas conservées. La session doit avoir au moins une valeur définie pour conserver la session entre les demandes. Quand une session n’est pas conservée, un nouvel ID de session est généré pour chaque nouvelle requête.
* L’application conserve une session pendant une période limitée après la dernière requête. L’application définit le délai d’expiration d’une session ou utilise la valeur par défaut de 20 minutes. L’état de session est idéal pour le stockage des données utilisateur :
  * Cela est spécifique à une session particulière.
  * Où les données ne nécessitent pas de stockage permanent entre les sessions.
* Les données de session sont supprimées lorsque l' <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> implémentation est appelée ou lorsque la session expire.
* Il n’existe pas de mécanisme par défaut pour informer le code de l’application qu’un navigateur client a été fermé ou lorsque la session cookie est supprimée ou a expiré sur le client.
* Les États cookie de session s ne sont pas marqués comme essentiels par défaut. L’état de session n’est pas fonctionnel, sauf si le suivi est autorisé par le visiteur du site. Pour plus d’informations, consultez <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Ne stockez pas de données sensibles dans l’état de session. Il se peut que l’utilisateur ne ferme pas le navigateur et n’efface pas la session cookie . Certains navigateurs maintiennent des sessions valides cookie entre les fenêtres de navigateur. Une session peut ne pas être restreinte à un seul utilisateur. L’utilisateur suivant peut continuer à parcourir l’application avec la même session cookie .

Le fournisseur de cache en mémoire stocke les données de session dans la mémoire du serveur où réside l’application. Dans un scénario de batterie de serveurs :

* Utilisez des *sessions persistantes* pour lier chaque session à une instance d’application spécifique sur un serveur donné. [Azure App Service](https://azure.microsoft.com/services/app-service/) utilise [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) pour appliquer les sessions persistantes par défaut. Toutefois, les sessions rémanentes peuvent impacter l’extensibilité et compliquer les mises à jour des applications web. Une meilleure approche consiste à utiliser le cache distribué Redis ou SQL Server, qui ne nécessite pas de sessions persistantes. Pour plus d’informations, consultez <xref:performance/caching/distributed>.
* La session cookie est chiffrée via <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . La protection des données doit être correctement configurée pour lire cookie les sessions sur chaque ordinateur. Pour plus d’informations, consultez <xref:security/data-protection/introduction> et [Fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurer l’état de session

Le package [Microsoft. AspNetCore. session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) :

* Est inclus implicitement par le Framework.
* Fournit un intergiciel (middleware) pour gérer l’état de session.

Pour activer le middleware Session, `Startup` doit contenir les éléments suivants :

* L’un des <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> caches de mémoire. L’implémentation `IDistributedCache` est utilisée comme magasin de stockage pour la session. Pour plus d’informations, consultez <xref:performance/caching/distributed>.
* Appel à <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> dans `ConfigureServices` .
* Appel à <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> dans `Configure` .

Le code suivant montre comment configurer le fournisseur de session en mémoire avec une implémentation en mémoire par défaut de `IDistributedCache` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

Le code précédent définit un délai d’expiration réduit pour simplifier les tests.

L’ordre des middlewares est important.  Appelez `UseSession` après `UseRouting` et avant `UseEndpoints` . Consultez [classement des intergiciels (middleware](xref:fundamentals/middleware/index#order)).

[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) est disponible une fois l’état de session configuré.

`HttpContext.Session` n’est pas accessible avant que `UseSession` ait été appelée.

Une nouvelle session avec une nouvelle session cookie ne peut pas être créée après que l’application a commencé l’écriture dans le flux de réponse. L’exception est enregistrée dans le journal de serveur web et n’est pas affichée pas dans le navigateur.

### <a name="load-session-state-asynchronously"></a>Charger l’état de session de façon asynchrone

Dans ASP.NET Core, le fournisseur de session par défaut charge les enregistrements de session à partir du magasin de stockage sous-jacent de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> façon asynchrone uniquement si la <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> méthode est appelée explicitement avant les <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> méthodes, ou <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> . Si `LoadAsync` n’est pas appelée en premier, l’enregistrement de session sous-jacent est chargé de façon synchrone, ce qui peut entraîner une baisse des performances à l’échelle.

Pour que les applications appliquent ce modèle, encapsulez les <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> <xref:Microsoft.AspNetCore.Session.DistributedSession> implémentations et avec des versions qui lèvent une exception si la `LoadAsync` méthode n’est pas appelée avant `TryGetValue` , `Set` ou `Remove` . Inscrivez ensuite les versions incluses dans le wrapper auprès du conteneur de services.

### <a name="session-options"></a>Options de session

Pour remplacer les valeurs par défaut de la session, utilisez <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Option | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Détermine les paramètres utilisés pour créer le cookie . <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> la valeur par défaut est <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> la valeur par défaut est <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> la valeur par défaut est <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> la valeur par défaut est `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> la valeur par défaut est `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` indique la durée pendant laquelle la session peut être inactive avant que son contenu soit abandonné. Chaque accès à la session réinitialise le délai d’expiration. Ce paramètre s’applique uniquement au contenu de la session, pas au cookie . La valeur par défaut est 20 minutes. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Durée maximale autorisée pour charger une session à partir du magasin ou pour la valider dans le magasin. Ce paramètre peut s’appliquer uniquement aux opérations asynchrones. Ce délai d’expiration peut être désactivé à l’aide de <xref:System.Threading.Timeout.InfiniteTimeSpan> . La valeur par défaut est de 1 minute. |

La session utilise un cookie pour suivre et identifier les demandes d’un navigateur unique. Par défaut, ce cookie nom est nommé `.AspNetCore.Session` et utilise le chemin d’accès `/` . Étant donné que la cookie valeur par défaut ne spécifie pas de domaine, elle n’est pas mise à la disposition du script côté client sur la page (car la <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> valeur par défaut est `true` ).

Pour remplacer les cookie valeurs par défaut de la session, utilisez <xref:Microsoft.AspNetCore.Builder.SessionOptions> :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

L’application utilise la <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> propriété pour déterminer la durée pendant laquelle une session peut être inactive avant que son contenu dans le cache du serveur soit abandonné. Cette propriété est indépendante de l' cookie expiration. Chaque requête qui passe par le [middleware Session](xref:Microsoft.AspNetCore.Session.SessionMiddleware) réinitialise le délai d’expiration.

L’état de session est *sans verrouillage*. Si deux requêtes tentent simultanément de modifier le contenu d’une session, la dernière requête remplace la première. `Session` est implémentée comme une *session cohérente*, ce qui signifie que tout le contenu est stocké au même emplacement. Quand deux requêtes tentent de modifier différentes valeurs de session, la dernière requête peut remplacer les modifications de session effectuées par la première.

### <a name="set-and-get-session-values"></a>Définir et obtenir des valeurs Session

L’état de session est accessible à partir d’une Razor classe pages ou d’une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> classe MVC avec <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Cette propriété est une <xref:Microsoft.AspNetCore.Http.ISession> implémentation.

L’implémentation de `ISession` fournit plusieurs méthodes d’extension pour définir et récupérer des valeurs de chaîne et d’entier. Les méthodes d’extension se trouvent dans l' <xref:Microsoft.AspNetCore.Http> espace de noms.

Méthodes d’extension `ISession` :

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

L’exemple suivant récupère la valeur de session de la `IndexModel.SessionKeyName` clé ( `_Name` dans l’exemple d’application) dans une Razor page pages :

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

L’exemple suivant montre comment définir et obtenir un entier et une chaîne :

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Toutes les données de session doivent être sérialisées pour activer un scénario de cache distribué, même si vous utilisez le cache en mémoire. Les sérialiseurs de chaînes et d’entiers sont fournis par les méthodes d’extension de <xref:Microsoft.AspNetCore.Http.ISession> . Les types complexes doivent être sérialisés par l’utilisateur à l’aide d’un autre mécanisme, tel que JSON.

Utilisez l’exemple de code suivant pour sérialiser des objets :

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

L’exemple suivant montre comment définir et obtenir un objet sérialisable avec la `SessionExtensions` classe :

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core expose les Razor pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) ou Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Cette propriété stocke les données jusqu’à ce qu’elles soient lues dans une autre requête. Les méthodes [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) et [Peek (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) peuvent être utilisées pour examiner les données sans suppression à la fin de la requête. [Keep](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) marque tous les éléments dans le dictionnaire pour la rétention. `TempData` est :

* Utile pour la redirection quand des données sont requises pour plus d’une requête unique.
* Implémenté par les `TempData` fournisseurs à l’aide cookie de ou de l’état de session.

## <a name="tempdata-samples"></a>Exemples TempData

Examinez la page suivante qui crée un client :

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

La page suivante affiche `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Dans le balisage précédent, à la fin de la demande, `TempData["Message"]` n’est **pas** supprimé, car `Peek` est utilisé. L’actualisation de la page affiche le contenu de `TempData["Message"]` .

Le balisage suivant est semblable au code précédent, mais utilise `Keep` pour conserver les données à la fin de la requête :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

La navigation entre les pages *IndexPeek* et *IndexKeep* ne sera pas supprimée `TempData["Message"]` .

Le code suivant affiche `TempData["Message"]` , mais à la fin de la demande, `TempData["Message"]` est supprimé :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Fournisseurs TempData

Le cookie fournisseur TempData basé sur utilise par défaut pour stocker TempData dans cookie s.

Les cookie données sont chiffrées à l’aide de <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> , encodées avec <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> , puis segmentées. La cookie taille maximale est inférieure à [4096 octets](http://www.faqs.org/rfcs/rfc2965.html) en raison du chiffrement et de la segmentation. Les cookie données ne sont pas compressées, car la compression des données chiffrées peut entraîner des problèmes de sécurité tels que les attaques de [criminalité](https://wikipedia.org/wiki/CRIME_(security_exploit)) et de [violation](https://wikipedia.org/wiki/BREACH_(security_exploit)) . Pour plus d’informations sur le cookie fournisseur TempData basé sur, consultez <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> .

### <a name="choose-a-tempdata-provider"></a>Choisir un fournisseur TempData

Pour choisir un fournisseur TempData, vous devez tenir compte de plusieurs points. Par exemple :

* L’application utilise-t-elle déjà l’état de session ? Dans ce cas, l’utilisation du fournisseur TempData d’état de session n’a pas de coût supplémentaire pour l’application au-delà de la taille des données.
* L’application utilise-t-elle uniquement la modération pour des quantités de données relativement petites, jusqu’à 500 octets ? Si c’est le cas, le cookie fournisseur TempData ajoute un coût réduit à chaque demande qui transporte TempData. Si ce n’est pas le cas, le fournisseur TempData d’état de session peut être utile pour éviter l’aller-retour d’une grande quantité de données dans chaque requête jusqu’à ce que le TempData soit consommé.
* L’application s’exécute-t-elle dans une batterie de serveurs sur plusieurs serveurs ? Dans ce cas, aucune configuration supplémentaire n’est requise pour utiliser le cookie fournisseur TempData en dehors de la protection des données (voir <xref:security/data-protection/introduction> et [fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers)).

La plupart des clients Web, tels que les navigateurs Web, appliquent des limites sur la taille maximale de chaque cookie et le nombre total de cookie s. Lorsque vous utilisez le cookie fournisseur TempData, vérifiez que l’application ne dépasse pas [ces limites](http://www.faqs.org/rfcs/rfc2965.html). Prenez en compte la taille totale des données. Compte de augmentation de la cookie taille en raison du chiffrement et de la segmentation.

### <a name="configure-the-tempdata-provider"></a>Configuration du fournisseur TempData

Le cookie fournisseur TempData basé sur est activé par défaut.

Pour activer le fournisseur TempData basé sur une session, utilisez la <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> méthode d’extension. Un seul appel à `AddSessionStateTempDataProvider` est requis :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>Chaînes de requête

Vous pouvez passer une quantité limitée de données d’une requête à une autre en l’ajoutant à la chaîne de la nouvelle requête. Cela est utile pour capturer l’état de manière persistante et permettre ainsi le partage de liens avec état incorporé par e-mail ou sur les réseaux sociaux. Les chaînes de requête URL étant publiques, vous ne devez jamais utiliser de chaînes de requête pour des données sensibles.

En plus du partage inattendu, les données des chaînes de requête peuvent exposer l’application à des attaques de [falsification de requête intersites (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) . Tout état de session préservé doit se protéger contre les attaques CSRF. Pour plus d’informations, consultez <xref:security/anti-request-forgery>.

## <a name="hidden-fields"></a>Champs masqués

Les données peuvent être enregistrées dans des champs de formulaire masqués et être republiées lors de la requête suivante. Cela est courant dans les formulaires de plusieurs pages. Étant donné que le client peut potentiellement falsifier les données, l’application doit toujours revalider les données stockées dans les champs masqués.

## <a name="httpcontextitems"></a>HttpContext.Items

La <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> collection est utilisée pour stocker des données lors du traitement d’une requête unique. Le contenu de la collection est supprimé une fois la requête traitée. La collection `Items` est souvent utilisée pour permettre à des composants ou des middlewares de communiquer quand ils opèrent à différents moments de l’exécution d’une requête et qu’ils ne peuvent pas passer les paramètres de façon directe.

Dans l’exemple suivant, l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) s’ajoute `isVerified` à la `Items` collection :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Pour l’intergiciel (middleware) utilisé uniquement dans une application unique, les `string` clés fixes sont acceptables. Les intergiciels (middleware) partagés entre les applications doivent utiliser des clés d’objet uniques pour éviter les collisions de clés. L’exemple suivant montre comment utiliser une clé d’objet unique définie dans une classe de middleware :

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Tout autre code peut accéder à la valeur stockée dans `HttpContext.Items` à l’aide de la clé exposée par la classe du middleware :

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Cette approche a également l’avantage d’éliminer l’utilisation des chaînes de clés dans le code.

## <a name="cache"></a>Cache

La mise en cache est un moyen efficace de stocker et récupérer des données. L’application peut contrôler la durée de vie des éléments mis en cache. Pour plus d’informations, consultez <xref:performance/caching/response>.

Les données mises en cache ne sont pas associées à une requête, un utilisateur ou une session spécifique. **Ne mettez pas en cache les données spécifiques à l’utilisateur qui peuvent être récupérées par d’autres demandes de l’utilisateur.**

Pour mettre en cache des données à l’ensemble de l’application, consultez <xref:performance/caching/memory> .

## <a name="common-errors"></a>Erreurs courantes

* « Impossible de résoudre le service pour le type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' lors de la tentative d’activation de 'Microsoft.AspNetCore.Session.DistributedSessionStore'. »

  Cela est généralement dû à l’impossibilité de configurer au moins une `IDistributedCache` implémentation. Pour plus d’informations, consultez <xref:performance/caching/distributed> et <xref:performance/caching/memory>.

Si le middleware de session ne parvient pas à rendre une session persistante :

* L’intergiciel enregistre l’exception et la demande se poursuit normalement.
* Cela entraîne un comportement imprévisible.

L’intergiciel de session ne peut pas conserver une session si le magasin de stockage n’est pas disponible. Par exemple, imaginez qu’un utilisateur stocke un panier d’achat dans la session. Il ajoute un élément au panier, mais la validation échoue. L’application n’a pas connaissance de l’échec et signale donc à l’utilisateur que l’élément a été ajouté au panier, ce qui est faux.

L’approche recommandée pour rechercher des erreurs consiste à appeler `await feature.Session.CommitAsync` lorsque l’application a terminé l’écriture dans la session. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> lève une exception si le magasin de stockage n’est pas disponible. Si `CommitAsync` échoue, l’application peut traiter l’exception. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*> lève une exception dans les mêmes conditions lorsque le magasin de données n’est pas disponible.
  
## <a name="no-locsignalr-and-session-state"></a>SignalR et état de session

SignalR les applications ne doivent pas utiliser l’état de session pour stocker des informations. SignalR les applications peuvent stocker par État de connexion dans `Context.Items` le Hub. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ressources supplémentaires

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) et [Luke Latham](https://github.com/guardrex)

HTTP est un protocole sans état. Sans effectuer des étapes supplémentaires, les requêtes HTTP sont des messages indépendants qui ne conservent pas les valeurs utilisateur ou l’état de l’application. Cet article décrit plusieurs approches pour conserver l’état de l’application et les données utilisateur entre les requêtes.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gestion de l'état

L’état peut être stocké à l’aide de plusieurs approches. Chacune d’elles est abordée plus loin dans cette rubrique.

| Approche du stockage | Mécanisme de stockage |
| ---------------- | ----------------- |
| [Cookiex](#cookies) | HTTP cookie s (peut inclure des données stockées à l’aide du code d’application côté serveur) |
| [État de session](#session-state) | HTTP cookie s et code d’application côté serveur |
| [TempData](#tempdata) | HTTP cookie s ou état de session |
| [Chaînes de requête](#query-strings) | Chaînes de requête HTTP |
| [Champs masqués](#hidden-fields) | Champs de formulaires HTTP |
| [HttpContext.Items](#httpcontextitems) | Code d’application côté serveur |
| [Cache](#cache) | Code d’application côté serveur |
| [Injection de dépendances](#dependency-injection) | Code d’application côté serveur |

## <a name="no-loccookies"></a>Cookies

Cookiestocke les données entre les demandes. Étant donné que les cookie sont envoyés avec chaque demande, leur taille doit être réduite à un minimum. Dans l’idéal, seul un identificateur doit être stocké dans un cookie avec les données stockées par l’application. La plupart des navigateurs limitent la cookie taille à 4096 octets. Seul un nombre limité de cookie s est disponible pour chaque domaine.

Étant donné que cookie les s sont sujettes à falsification, elles doivent être validées par l’application. Cookiepeuvent être supprimés par les utilisateurs et expirent sur les clients. Toutefois, cookie les s sont généralement la forme la plus durable de la persistance des données sur le client.

Cookieles s sont souvent utilisées pour la personnalisation, où le contenu est personnalisé pour un utilisateur connu. L’utilisateur est uniquement identifié, et pas authentifié dans la plupart des cas. Le cookie peut stocker le nom de l’utilisateur, le nom du compte ou un ID d’utilisateur unique (tel qu’un GUID). Vous pouvez ensuite utiliser le cookie pour accéder aux paramètres personnalisés de l’utilisateur, tels que la couleur d’arrière-plan de votre site Web préféré.

Tenez-vous au respect de la réglementation générale relative à la [protection des données (RGPD) de l’Union européenne](https://ec.europa.eu/info/law/law-topic/data-protection) lors de l’émission et du cookie traitement des problèmes de confidentialité. Pour plus d’informations, consultez [Prise en charge du règlement général sur la protection des données (RGPD) de l’Union Européenne dans ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>État de la session

L’état de session est un scénario ASP.NET Core pour le stockage des données utilisateur pendant que l’utilisateur parcourt une application web. L’état de session utilise un magasin tenu à jour par l’application afin de conserver les données entre les requêtes d’un client. Les données de session sont secondées par un cache et considérées comme des données éphémères (le site doit continuer à fonctionner sans elles). Les données d’application critiques doivent être stockées dans la base de données utilisateur et mises en cache dans la session uniquement à des fins d’optimisation des performances.

> [!NOTE]
> La session n’est pas prise en charge dans les [SignalR](xref:signalr/index) applications, car un [ SignalR Hub](xref:signalr/hubs) peut s’exécuter indépendamment d’un contexte http. Par exemple, cela peut se produire quand une longue requête d’interrogation est ouverte par un hub au-delà de la durée de vie du contexte de la requête HTTP.

ASP.NET Core conserve l’état de session en fournissant un cookie au client qui contient un ID de session, qui est envoyé à l’application avec chaque demande. Le serveur utilise l’ID de session pour récupérer les données de session.

L’état de session présente les comportements suivants :

* Étant donné que la session cookie est spécifique au navigateur, les sessions ne sont pas partagées entre les navigateurs.
* cookieLes sessions sont supprimées à la fin de la session du navigateur.
* Si une cookie est reçue pour une session ayant expiré, une nouvelle session qui utilise la même session est créée cookie .
* Les sessions vides ne sont pas conservées. La session doit avoir au moins une valeur définie pour pouvoir être conservée entre les requêtes. Quand une session n’est pas conservée, un nouvel ID de session est généré pour chaque nouvelle requête.
* L’application conserve une session pendant une période limitée après la dernière requête. L’application définit le délai d’expiration d’une session ou utilise la valeur par défaut de 20 minutes. L’état de session est idéal pour le stockage des données utilisateur qui sont propres à une session particulière, mais où les données ne nécessitent pas un stockage permanent entre les sessions.
* Les données de session sont supprimées lorsque l' <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> implémentation est appelée ou lorsque la session expire.
* Il n’existe pas de mécanisme par défaut pour informer le code de l’application qu’un navigateur client a été fermé ou lorsque la session cookie est supprimée ou a expiré sur le client.
* Les modèles MVC et Razor pages ASP.net Core incluent la prise en charge de règlement général sur la protection des données (RGPD). Les États cookie de session ne sont pas marqués comme étant essentiels par défaut, l’état de session ne fonctionne pas, sauf si le suivi est autorisé par le visiteur du site. Pour plus d’informations, consultez <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Ne stockez pas de données sensibles dans l’état de session. Il se peut que l’utilisateur ne ferme pas le navigateur et n’efface pas la session cookie . Certains navigateurs maintiennent des sessions valides cookie entre les fenêtres de navigateur. Une session peut ne pas être restreinte à un seul utilisateur &mdash; . l’utilisateur suivant peut continuer à parcourir l’application avec la même session cookie .

Le fournisseur de cache en mémoire stocke les données de session dans la mémoire du serveur où réside l’application. Dans un scénario de batterie de serveurs :

* Utilisez des *sessions persistantes* pour lier chaque session à une instance d’application spécifique sur un serveur donné. [Azure App Service](https://azure.microsoft.com/services/app-service/) utilise [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) pour appliquer les sessions persistantes par défaut. Toutefois, les sessions rémanentes peuvent impacter l’extensibilité et compliquer les mises à jour des applications web. Une meilleure approche consiste à utiliser le cache distribué Redis ou SQL Server, qui ne nécessite pas de sessions persistantes. Pour plus d’informations, consultez <xref:performance/caching/distributed>.
* La session cookie est chiffrée via <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . La protection des données doit être correctement configurée pour lire cookie les sessions sur chaque ordinateur. Pour plus d’informations, consultez <xref:security/data-protection/introduction> et [Fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurer l’état de session

Le package [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), qui est inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), fournit un middleware (intergiciel) pour gérer l’état de session. Pour activer le middleware Session, `Startup` doit contenir les éléments suivants :

* L’un des <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> caches de mémoire. L’implémentation `IDistributedCache` est utilisée comme magasin de stockage pour la session. Pour plus d’informations, consultez <xref:performance/caching/distributed>.
* Appel à <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> dans `ConfigureServices` .
* Appel à <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> dans `Configure` .

Le code suivant montre comment configurer le fournisseur de session en mémoire avec une implémentation en mémoire par défaut de `IDistributedCache` :

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

L’ordre des middlewares est important. Dans l’exemple précédent, une exception `InvalidOperationException` se produit quand `UseSession` est appelée après `UseMvc`. Pour plus d’informations, consultez [Ordre des intergiciels (middleware)](xref:fundamentals/middleware/index#order).

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> est disponible après la configuration de l’état de session.

`HttpContext.Session` n’est pas accessible avant que `UseSession` ait été appelée.

Une nouvelle session avec une nouvelle session cookie ne peut pas être créée après que l’application a commencé l’écriture dans le flux de réponse. L’exception est enregistrée dans le journal de serveur web et n’est pas affichée pas dans le navigateur.

### <a name="load-session-state-asynchronously"></a>Charger l’état de session de façon asynchrone

Dans ASP.NET Core, le fournisseur de session par défaut charge les enregistrements de session à partir du magasin de stockage sous-jacent de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> façon asynchrone uniquement si la <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> méthode est appelée explicitement avant les <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> méthodes, ou <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> . Si `LoadAsync` n’est pas appelée en premier, l’enregistrement de session sous-jacent est chargé de façon synchrone, ce qui peut entraîner une baisse des performances à l’échelle.

Pour que les applications appliquent ce modèle, encapsulez les <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> <xref:Microsoft.AspNetCore.Session.DistributedSession> implémentations et avec des versions qui lèvent une exception si la `LoadAsync` méthode n’est pas appelée avant `TryGetValue` , `Set` ou `Remove` . Inscrivez ensuite les versions incluses dans le wrapper auprès du conteneur de services.

### <a name="session-options"></a>Options de session

Pour remplacer les valeurs par défaut de la session, utilisez <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Option | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Détermine les paramètres utilisés pour créer le cookie . <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> la valeur par défaut est <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> la valeur par défaut est <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> la valeur par défaut est <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> la valeur par défaut est `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> la valeur par défaut est `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` indique la durée pendant laquelle la session peut être inactive avant que son contenu soit abandonné. Chaque accès à la session réinitialise le délai d’expiration. Ce paramètre s’applique uniquement au contenu de la session, pas au cookie . La valeur par défaut est 20 minutes. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Durée maximale autorisée pour charger une session à partir du magasin ou pour la valider dans le magasin. Ce paramètre peut s’appliquer uniquement aux opérations asynchrones. Ce délai d’expiration peut être désactivé à l’aide de <xref:System.Threading.Timeout.InfiniteTimeSpan> . La valeur par défaut est de 1 minute. |

La session utilise un cookie pour suivre et identifier les demandes d’un navigateur unique. Par défaut, ce cookie nom est nommé `.AspNetCore.Session` et utilise le chemin d’accès `/` . Étant donné que la cookie valeur par défaut ne spécifie pas de domaine, elle n’est pas mise à la disposition du script côté client sur la page (car la <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> valeur par défaut est `true` ).

Pour remplacer les cookie valeurs par défaut de la session, utilisez `SessionOptions` :

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

L’application utilise la <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> propriété pour déterminer la durée pendant laquelle une session peut être inactive avant que son contenu dans le cache du serveur soit abandonné. Cette propriété est indépendante de l' cookie expiration. Chaque requête qui passe par le [middleware Session](xref:Microsoft.AspNetCore.Session.SessionMiddleware) réinitialise le délai d’expiration.

L’état de session est *sans verrouillage*. Si deux requêtes tentent simultanément de modifier le contenu d’une session, la dernière requête remplace la première. `Session` est implémentée comme une *session cohérente*, ce qui signifie que tout le contenu est stocké au même emplacement. Quand deux requêtes tentent de modifier différentes valeurs de session, la dernière requête peut remplacer les modifications de session effectuées par la première.

### <a name="set-and-get-session-values"></a>Définir et obtenir des valeurs Session

L’état de session est accessible à partir d’une Razor classe pages ou d’une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> classe MVC avec <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Cette propriété est une <xref:Microsoft.AspNetCore.Http.ISession> implémentation.

L’implémentation de `ISession` fournit plusieurs méthodes d’extension pour définir et récupérer des valeurs de chaîne et d’entier. Les méthodes d’extension se trouvent dans l' <xref:Microsoft.AspNetCore.Http> espace de noms (ajoutez une `using Microsoft.AspNetCore.Http;` instruction pour accéder aux méthodes d’extension) quand le package [Microsoft. AspNetCore. http. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) est référencé par le projet. Les deux packages sont inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Méthodes d’extension `ISession` :

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

L’exemple suivant récupère la valeur de session de la `IndexModel.SessionKeyName` clé ( `_Name` dans l’exemple d’application) dans une Razor page pages :

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

L’exemple suivant montre comment définir et obtenir un entier et une chaîne :

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Toutes les données de session doivent être sérialisées pour activer un scénario de cache distribué, même si vous utilisez le cache en mémoire. Les sérialiseurs de chaînes et d’entiers sont fournis par les méthodes d’extension de <xref:Microsoft.AspNetCore.Http.ISession> . Les types complexes doivent être sérialisés par l’utilisateur à l’aide d’un autre mécanisme, tel que JSON.

Ajoutez les méthodes d’extension suivantes pour définir et obtenir des objets sérialisables :

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

L’exemple suivant montre comment définir et obtenir un objet sérialisable avec les méthodes d’extension :

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core expose les Razor pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) ou Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Cette propriété stocke les données jusqu’à ce qu’elles soient lues dans une autre requête. Les méthodes [Keep (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) et [Peek (String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) peuvent être utilisées pour examiner les données sans suppression à la fin de la requête. [Keep ()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) marque tous les éléments du dictionnaire pour la rétention. `TempData` est particulièrement utile pour la redirection quand des données sont requises pour plus d’une requête unique. `TempData` est implémenté par les `TempData` fournisseurs à l’aide de ou de l' cookie État de session.

## <a name="tempdata-samples"></a>Exemples TempData

Examinez la page suivante qui crée un client :

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

La page suivante affiche `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Dans le balisage précédent, à la fin de la demande, `TempData["Message"]` n’est **pas** supprimé, car `Peek` est utilisé. L’actualisation de la page s’affiche `TempData["Message"]` .

Le balisage suivant est semblable au code précédent, mais utilise `Keep` pour conserver les données à la fin de la requête :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

La navigation entre les pages *IndexPeek* et *IndexKeep* ne sera pas supprimée `TempData["Message"]` .

Le code suivant affiche `TempData["Message"]` , mais à la fin de la demande, `TempData["Message"]` est supprimé :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Fournisseurs TempData

Le cookie fournisseur TempData basé sur utilise par défaut pour stocker TempData dans cookie s.

Les cookie données sont chiffrées à l’aide de <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> , encodées avec <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> , puis segmentées. Étant donné que le cookie est segmenté, la cookie limite de taille unique trouvée dans ASP.net Core 1. x ne s’applique pas. Les cookie données ne sont pas compressées, car la compression des données chiffrées peut entraîner des problèmes de sécurité tels que les attaques de [criminalité](https://wikipedia.org/wiki/CRIME_(security_exploit)) et de [violation](https://wikipedia.org/wiki/BREACH_(security_exploit)) . Pour plus d’informations sur le cookie fournisseur TempData basé sur, consultez <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> .

### <a name="choose-a-tempdata-provider"></a>Choisir un fournisseur TempData

Pour choisir un fournisseur TempData, vous devez tenir compte de plusieurs points. Par exemple :

1. L’application utilise-t-elle déjà l’état de session ? Si c’est le cas, l’utilisation du fournisseur TempData d’état de session n’entraîne pas de surcoût pour l’application (hormis la taille des données).
2. L’application utilise-t-elle TempData seulement de façon ponctuelle, pour des quantités de données relativement petites (jusqu’à 500 octets) ? Si c’est le cas, le cookie fournisseur TempData ajoute un coût réduit à chaque demande qui transporte TempData. Si ce n’est pas le cas, le fournisseur TempData d’état de session peut être utile pour éviter l’aller-retour d’une grande quantité de données dans chaque requête jusqu’à ce que le TempData soit consommé.
3. L’application s’exécute-t-elle dans une batterie de serveurs sur plusieurs serveurs ? Dans ce cas, aucune configuration supplémentaire n’est requise pour utiliser le cookie fournisseur TempData en dehors de la protection des données (voir <xref:security/data-protection/introduction> et [fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> La plupart des clients Web (tels que les navigateurs Web) appliquent des limites sur la taille maximale de chaque cookie , le nombre total de ou cookie les deux. Lorsque vous utilisez le cookie fournisseur TempData, vérifiez que l’application ne dépasse pas ces limites. Prenez en compte la taille totale des données. Compte de augmentation de la cookie taille en raison du chiffrement et de la segmentation.

### <a name="configure-the-tempdata-provider"></a>Configuration du fournisseur TempData

Le cookie fournisseur TempData basé sur est activé par défaut.

Pour activer le fournisseur TempData basé sur une session, utilisez la <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> méthode d’extension :

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

L’ordre des middlewares est important. Dans l’exemple précédent, une exception `InvalidOperationException` se produit quand `UseSession` est appelée après `UseMvc`. Pour plus d’informations, consultez [Ordre des intergiciels (middleware)](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Si vous ciblez le .NET Framework et que vous utilisez le fournisseur TempData basé sur la session, ajoutez le package [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) au projet.

## <a name="query-strings"></a>Chaînes de requête

Vous pouvez passer une quantité limitée de données d’une requête à une autre en l’ajoutant à la chaîne de la nouvelle requête. Cela est utile pour capturer l’état de manière persistante et permettre ainsi le partage de liens avec état incorporé par e-mail ou sur les réseaux sociaux. Les chaînes de requête URL étant publiques, vous ne devez jamais utiliser de chaînes de requête pour des données sensibles.

En plus du partage accidentel, l’ajout de données dans des chaînes de requête peut favoriser les attaques par [falsification de requête intersites (CSRF, Cross Site Request Forgery)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) et conduire des utilisateurs authentifiés à visiter des sites malveillants. Les attaquants peuvent ensuite dérober des données utilisateur à partir de l’application ou effectuer des actions malveillantes en utilisant un compte d’utilisateur. Chaque état de session ou d’application conservé doit garantir une protection contre les attaques CSRF. Pour plus d’informations, consultez <xref:security/anti-request-forgery>.

## <a name="hidden-fields"></a>Champs masqués

Les données peuvent être enregistrées dans des champs de formulaire masqués et être republiées lors de la requête suivante. Cela est courant dans les formulaires de plusieurs pages. Étant donné que le client peut potentiellement falsifier les données, l’application doit toujours revalider les données stockées dans les champs masqués.

## <a name="httpcontextitems"></a>HttpContext.Items

La <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> collection est utilisée pour stocker des données lors du traitement d’une requête unique. Le contenu de la collection est supprimé une fois la requête traitée. La collection `Items` est souvent utilisée pour permettre à des composants ou des middlewares de communiquer quand ils opèrent à différents moments de l’exécution d’une requête et qu’ils ne peuvent pas passer les paramètres de façon directe.

Dans l’exemple suivant, le [middleware](xref:fundamentals/middleware/index) ajoute `isVerified` à la collection `Items`.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Plus tard dans le pipeline, un autre middleware peut accéder à la valeur de `isVerified` :

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Si un middleware est destiné uniquement à l’usage d’une seule application, les clés `string` sont acceptables. Le middleware partagé entre des instances de l’application doit utiliser des clés d’objets uniques afin d’éviter les conflits de clé. L’exemple suivant montre comment utiliser une clé d’objet unique définie dans une classe de middleware :

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Tout autre code peut accéder à la valeur stockée dans `HttpContext.Items` à l’aide de la clé exposée par la classe du middleware :

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Cette approche a également l’avantage d’éliminer l’utilisation des chaînes de clés dans le code.

## <a name="cache"></a>Cache

La mise en cache est un moyen efficace de stocker et récupérer des données. L’application peut contrôler la durée de vie des éléments mis en cache.

Les données mises en cache ne sont pas associées à une requête, un utilisateur ou une session spécifique. **Veillez à ne pas mettre en cache des données propres à l’utilisateur susceptibles d’être récupérées par les requêtes d’autres utilisateurs.**

Pour plus d’informations, consultez <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Injection de dépendances

Utilisez [l’injection de dépendances](xref:fundamentals/dependency-injection) pour rendre les données accessibles à tous les utilisateurs :

1. Définissez un service contenant les données. Par exemple, une classe nommée `MyAppData` est définie :

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Ajoutez la classe de service à `Startup.ConfigureServices` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Consommez la classe de service de données :

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Erreurs courantes

* « Impossible de résoudre le service pour le type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' lors de la tentative d’activation de 'Microsoft.AspNetCore.Session.DistributedSessionStore'. »

  Cette erreur est généralement due à un échec de configuration d’au moins une implémentation `IDistributedCache`. Pour plus d’informations, consultez <xref:performance/caching/distributed> et <xref:performance/caching/memory>.

* Dans le cas où le middleware Session ne parvient pas à rendre une session persistante (par exemple si le magasin de stockage n’est pas disponible), il enregistre l’exception dans le journal et la requête se poursuit normalement. Cela entraîne un comportement imprévisible.

  Par exemple, imaginez qu’un utilisateur stocke un panier d’achat dans la session. Il ajoute un élément au panier, mais la validation échoue. L’application n’a pas connaissance de l’échec et signale donc à l’utilisateur que l’élément a été ajouté au panier, ce qui est faux.

  L’approche recommandée pour rechercher les erreurs de ce type consiste à appeler `await feature.Session.CommitAsync();` à partir du code d’application quand l’application a terminé d’écrire dans la session. `CommitAsync` lève une exception si le magasin de stockage n’est pas disponible. Si `CommitAsync` échoue, l’application peut traiter l’exception. `LoadAsync` lève une exception dans les mêmes conditions, quand le magasin de données n’est pas disponible.
  
## <a name="no-locsignalr-and-session-state"></a>SignalR et état de session

SignalR les applications ne doivent pas utiliser l’état de session pour stocker des informations. SignalR les applications peuvent stocker par État de connexion dans `Context.Items` le Hub. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ressources supplémentaires

<xref:host-and-deploy/web-farm>
::: moniker-end
