---
title: Intergiciel (middleware) de réécriture d’URL dans ASP.NET Core
author: rick-anderson
description: Découvrez la réécriture et la redirection d’URL avec l’intergiciel (middleware) de réécriture d’URL dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
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
uid: fundamentals/url-rewriting
ms.openlocfilehash: 44e6570090de9c5f3550d18a30fcde758fb16f85
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588521"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>Intergiciel (middleware) de réécriture d’URL dans ASP.NET Core

Par [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range=">= aspnetcore-3.0"

Ce document présente la réécriture d’URL avec des instructions sur la façon d’utiliser l’intergiciel (middleware) de réécriture d’URL dans les applications ASP.NET Core.

La réécriture d’URL consiste à modifier des URL de requête en fonction d’une ou de plusieurs règles prédéfinies. La réécriture d’URL crée une abstraction entre les emplacements des ressources et leurs adresses pour que les emplacements et les adresses ne soient pas étroitement liés. La réécriture d’URL est utile dans plusieurs scénarios pour :

* Déplacer ou remplacer de façon temporaire ou permanente les ressources d’un serveur, tout en conservant des localisateurs stables pour ces ressources.
* Diviser le traitement des requêtes entre différentes applications ou entre différentes parties d’une même application.
* Supprimer, ajouter ou réorganiser les segments d’URL sur des requêtes entrantes.
* Optimiser les URL publiques pour l’optimisation du référencement d’un site auprès d’un moteur de recherche (SEO).
* Permettre l’utilisation des URL publiques conviviales pour aider les visiteurs à prédire le contenu retourné en demandant une ressource.
* Rediriger des requêtes non sécurisées vers des points de terminaison sécurisés.
* Empêcher les liaisons à chaud, où un site externe utilise une ressource statique hébergée sur un autre site en liant la ressource dans son propre contenu.

> [!NOTE]
> La réécriture d’URL peut réduire les performances d’une application. Quand c’est possible, limitez le nombre et la complexité des règles.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Redirection d’URL et réécriture d’URL

La différence de formulation entre la *redirection d’URL* et la *réécriture d’URL* est subtile, mais elle a des implications importantes sur la fourniture de ressources aux clients. L’intergiciel de réécriture d’URL d’ASP.NET Core est capables de répondre aux besoins des deux.

Une *redirection d’URL* implique une opération côté client, où le client est invité à accéder à une ressource à une autre adresse que celle demandée à l’origine. Ceci nécessite un aller-retour avec le serveur. L’URL de redirection retournée au client s’affiche dans la barre d’adresse du navigateur quand le client effectue une nouvelle requête pour la ressource.

Si `/resource` est *redirigée* vers `/different-resource`, le serveur répond que le client doit obtenir la ressource à l’emplacement `/different-resource` avec un code d’état indiquant que la redirection est temporaire ou permanente.

![Un point de terminaison de service WebAPI a été changé temporairement de la version 1 (v1) à la version 2 (v2) sur le serveur. Un client effectue une requête au service dans le chemin /v1/api de la version 1. Le serveur renvoie une réponse 302 (Trouvé) avec le nouveau chemin temporaire du service dans le chemin /v2/api de la version 2. Le client effectue une deuxième requête au service à l’URL de redirection. Le serveur répond avec le code d’état 200 (OK).](url-rewriting/_static/url_redirect.png)

Lors de la redirection des requêtes vers une URL différente, indiquez si la redirection est permanente ou temporaire en spécifiant le code d’état avec la réponse :

* Le code d’état *301 - Déplacé de façon permanente* est utilisé quand la ressource a une nouvelle URL permanente et que vous voulez indiquer au client que toutes les requêtes futures pour la ressource doivent utiliser la nouvelle URL. *Le client peut mettre en cache et réutiliser la réponse quand un code d’état 301 est reçu.*

* Le code d’état *302 - Trouvé* est utilisé quand la redirection est temporaire ou généralement susceptible d’être modifiée. Le code d’état 302 indique au client de ne pas stocker l’URL et de ne plus l’utiliser.

Pour plus d’informations sur les codes d’état, consultez [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

Une *réécriture d’URL* est une opération côté serveur qui fournit une ressource à partir d’une adresse de ressource différente de celle demandée par le client. La réécriture d’URL ne nécessite pas d’aller-retour avec le serveur. L’URL réécrite n’est pas retournée au client et n’apparaît pas dans la barre d’adresse du navigateur.

Si `/resource` est *réécrite* en `/different-resource`, le serveur récupère la ressource *en interne* à l’emplacement `/different-resource`.

Même si le client peut récupérer la ressource à l’URL réécrite, il n’est pas informé que la ressource existe à l’URL réécrite quand il fait sa requête et reçoit la réponse.

![Un point de terminaison de service WebAPI a été changé de la version 1 (v1) à la version 2 (v2) sur le serveur. Un client effectue une requête au service dans le chemin /v1/api de la version 1. L’URL de requête est réécrite pour accéder au service dans le chemin /v2/api de la version 2. Le service répond au client avec le code d’état 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Exemple d’application de réécriture d’URL

Vous pouvez explorer les fonctionnalités du middleware de réécriture d’URL avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/). L’application applique des règles de redirection et de réécriture, et montre l’URL redirigée ou réécrite pour plusieurs scénarios.

## <a name="when-to-use-url-rewriting-middleware"></a>Quand utiliser l’intergiciel (middleware) de réécriture d’URL

Utilisez le middleware de réécriture d’URL quand vous ne pouvez pas utiliser les approches suivantes :

* [Module de réécriture d’URL avec IIS sur Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Module mod_rewrite Apache sur Apache Server](https://httpd.apache.org/docs/2.4/rewrite/)
* [URL de réécriture sur Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Utilisez aussi le middleware quand l’application est hébergée sur le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement appelé WebListener).

Les principales raisons d’utiliser les technologies de réécriture d’URL basée sur le serveur dans IIS, Apache et Nginx sont les suivantes :

* Le middleware ne prend pas en charge toutes les fonctionnalités de ces modules.

  Certaines des fonctionnalités des modules serveur ne fonctionnent pas avec les projets ASP.NET Core, comme les contraintes `IsFile` et `IsDirectory` du module Réécriture IIS. Dans ces scénarios, utilisez plutôt l’intergiciel.
* Les performances du middleware ne correspondent probablement pas à celles des modules.

  Mener des tests de performances est la seule façon de savoir exactement quelle approche dégrade le plus les performances ou si la dégradation des performances est négligeable.

## <a name="package"></a>Paquet

L’intergiciel (middleware) de réécriture d’URL est fourni par le package [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite), qui est implicitement inclus dans les applications ASP.NET Core.

## <a name="extension-and-options"></a>Extension et options

Établissez des règles de réécriture et de redirection d’URL en créant une instance de la classe [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) avec des méthodes d’extension pour chacune de vos règles de réécriture. Chaînez plusieurs règles dans l’ordre dans lequel vous voulez qu’elles soient traitées. Les `RewriteOptions` sont passées dans le middleware de réécriture d’URL quand il est ajouté au pipeline de requête avec <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Redirection de demandes non-www en demandes www

Trois options permettent à l’application de rediriger des demandes non-`www` en demandes `www` :

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Rediriger définitivement la demande vers le `www` sous-domaine si la demande n’est pas `www` . Redirige avec un code d’état [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirigez la demande vers le `www` sous-domaine si la demande entrante n’est pas `www` . Redirige avec un code d’état [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect). Une surcharge vous permet de fournir le code d’état pour la réponse. Utilisez un champ de la classe <xref:Microsoft.AspNetCore.Http.StatusCodes> pour une affectation de code d’état.

### <a name="url-redirect"></a>Redirection d’URL

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> pour rediriger des requêtes. Le premier paramètre contient votre expression régulière pour la mise en correspondance sur le chemin de l’URL entrante. Le deuxième paramètre est la chaîne de remplacement. Le troisième paramètre, le cas échéant, spécifie le code d’état. Si vous ne spécifiez pas le code d’état, sa valeur par défaut est *302 - Trouvé*, ce qui indique que la ressource est temporairement déplacée ou remplacée.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

Dans un navigateur dans lequel les outils de développement sont activés, effectuez une requête à l’exemple d’application avec le chemin `/redirect-rule/1234/5678`. L’expression régulière établit une correspondance avec le chemin de la requête sur `redirect-rule/(.*)`, et le chemin est remplacé par `/redirected/1234/5678`. L’URL de redirection est renvoyée au client avec le code d’état *302 - Trouvé*. Le navigateur effectue une nouvelle requête à l’URL de redirection, qui apparaît dans la barre d’adresse du navigateur. Comme aucune règle de l’exemple d’application ne correspond sur l’URL de redirection :

* La deuxième requête reçoit une réponse *200 - OK* de l’application.
* Le corps de la réponse montre l’URL de redirection.

Un aller-retour est effectué avec le serveur quand une URL est *redirigée*.

> [!WARNING]
> Soyez prudent lors de l’établissement de règles de redirection. Les règles de redirection sont évaluées à chaque requête effectuée à l’application, notamment après une redirection. Il est facile de créer accidentellement une *boucle de redirections infinies*.

Requête d’origine : `/redirect-rule/1234/5678`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect.png)

La partie de l’expression entre parenthèses est appelée *groupe de capture*. Le point (`.`) de l’expression signifie *mettre en correspondance n’importe quel caractère*. L’astérisque (`*`) indique *mettre en correspondance zéro occurrence ou plus du caractère précédent*. Par conséquent, les deux derniers segments de chemin de l’URL, `1234/5678`, sont capturés par le groupe de capture `(.*)`. Toute valeur fournie dans l’URL de la requête après `redirect-rule/` est capturée par ce groupe de capture unique.

Dans la chaîne de remplacement, les groupes capturés sont injectés dans la chaîne avec le signe dollar (`$`) suivi du numéro de séquence de la capture. La valeur du premier groupe de capture est obtenue avec `$1`, la deuxième avec `$2`, et ainsi de suite en séquence pour les groupes de capture de votre expression régulière. Comme il n’y a qu’un seul groupe capturé dans l’expression régulière de la règle de redirection de l’exemple d’application, un seul groupe est injecté dans la chaîne de remplacement, à savoir `$1`. Quand la règle est appliquée, l’URL devient `/redirected/1234/5678`.

### <a name="url-redirect-to-a-secure-endpoint"></a>Redirection d’URL vers un point de terminaison sécurisé

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> pour rediriger les requêtes HTTP vers le même hôte et le même chemin avec le protocole HTTPS. Si le code d’état n’est pas fourni, le middleware utilise par défaut *302 - Trouvé*. Si le port n’est pas fourni :

* Le middleware utilise par défaut `null`.
* Le schéma change en `https` (protocole HTTPS), et le client accède à la ressource sur le port 443.

L’exemple suivant montre comment définir le code d’état sur *301 - Déplacé de façon permanente* et changer le port en 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> pour rediriger les requêtes non sécurisées vers le même hôte et le même chemin avec le protocole HTTPS sécurisé sur le port 443. Le middleware définit le code d’état sur *301 - Déplacé de façon permanente*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Si vous effectuez une redirection vers un point de terminaison sécurisé et que vous n’avez pas besoin de règles de redirection supplémentaires, nous vous recommandons d’utiliser le middleware de redirection HTTPS. Pour plus d’informations, consultez la rubrique [Appliquer HTTPS](xref:security/enforcing-ssl#require-https).

L’exemple d’application peut montrer comment utiliser `AddRedirectToHttps` ou `AddRedirectToHttpsPermanent`. Ajoutez la méthode d’extension à `RewriteOptions`. Effectuez une requête non sécurisée à l’application à n’importe quelle URL. Ignorez l’avertissement de sécurité du navigateur indiquant que le certificat auto-signé n’est pas approuvé ou créez une exception pour approuver le certificat.

Requête d’origine utilisant `AddRedirectToHttps(301, 5001)` : `http://localhost:5000/secure`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect_to_https.png)

Requête d’origine utilisant `AddRedirectToHttpsPermanent` : `http://localhost:5000/secure`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Réécrire URL

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> pour créer une règle pour la réécriture d’URL. Le premier paramètre contient l’expression régulière pour la mise en correspondance sur le chemin de l’URL entrante. Le deuxième paramètre est la chaîne de remplacement. Le troisième paramètre, `skipRemainingRules: {true|false}`, indique à l’intergiciel d’ignorer, ou non, les règles de réécriture supplémentaires si la règle actuelle est appliquée.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Requête d’origine : `/rewrite-rule/1234/5678`

![Fenêtre de navigateur avec la requête et la réponse suivies par les Outils de développement](url-rewriting/_static/add_rewrite.png)

Le caret (`^`) au début de l’expression signifie que la correspondance commence au début du chemin de l’URL.

Dans l’exemple précédent avec la règle de redirection, `redirect-rule/(.*)`, il n’existe pas de caret (`^`) au début de l’expression régulière. Ainsi, n’importe quel caractère peut précéder `redirect-rule/` dans le chemin pour qu’une correspondance soit établie.

| Chemin d’accès                               | Faire correspondre |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Oui   |
| `/my-cool-redirect-rule/1234/5678` | Oui   |
| `/anotherredirect-rule/1234/5678`  | Oui   |

La règle de réécriture, `^rewrite-rule/(\d+)/(\d+)`, établit une correspondance uniquement avec des chemins d’accès s’ils commencent par `rewrite-rule/`. Dans le tableau suivant, notez la différence de correspondance.

| Chemin d’accès                              | Faire correspondre |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Oui   |
| `/my-cool-rewrite-rule/1234/5678` | Non    |
| `/anotherrewrite-rule/1234/5678`  | Non    |

À la suite de la partie `^rewrite-rule/` de l’expression se trouvent deux groupes de capture, `(\d+)/(\d+)`. `\d` signifie *établir une correspondance avec un chiffre (nombre)*. Le signe plus (`+`) signifie *établir une correspondance avec une ou plusieurs occurrences du caractère précédent*. Par conséquent, l’URL doit contenir un nombre suivi d’une barre oblique, elle-même suivie d’un autre nombre. Ces groupes sont injectés dans l’URL réécrite sous la forme `$1` et `$2`. La chaîne de remplacement de la règle de réécriture place les groupes capturés dans la chaîne de requête. Le chemin demandé `/rewrite-rule/1234/5678` est réécrit pour obtenir la ressource à l’emplacement `/rewritten?var1=1234&var2=5678`. Si une chaîne de requête est présente dans la requête d’origine, elle est conservée lors de la réécriture de l’URL.

Il n’y a pas d’aller-retour avec le serveur pour obtenir la ressource. Si la ressource existe, elle est récupérée et retournée au client avec le code d’état *200 - OK*. Comme le client n’est pas redirigé, l’URL dans la barre d’adresse du navigateur ne change pas. Les clients ne peuvent pas détecter qu’une opération de réécriture d’URL s’est produite sur le serveur.

> [!NOTE]
> Quand c’est possible, utilisez `skipRemainingRules: true`, car la mise en correspondance de règles est un processus gourmand en ressources qui augmente le temps de réponse de l’application. Pour obtenir la réponse d’application la plus rapide :
>
> * Classez vos règles de réécriture en partant de la règle la plus souvent mise en correspondance jusqu’à la règle la moins souvent mise en correspondance.
> * Ignorez le traitement des règles restantes quand une correspondance est trouvée et qu’aucun traitement de règle supplémentaire n’est nécessaire.

### <a name="apache-mod_rewrite"></a>Apache mod_rewrite

Appliquez des règles Apache mod_rewrite avec <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>. Vérifiez que le fichier de règles est déployé avec l’application. Pour obtenir plus d’informations et des exemples de règles mod_rewrite, consultez [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

Un <xref:System.IO.StreamReader> est utilisé pour lire les règles à partir du fichier de règles de *ApacheModRewrite.txt* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

L’exemple d’application redirige les requêtes de `/apache-mod-rules-redirect/(.\*)` vers `/redirected?id=$1`. Le code d’état de la réponse est *302 - Trouvé*.

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

Requête d’origine : `/apache-mod-rules-redirect/1234`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_apache_mod_redirect.png)

L’intergiciel prend en charge les variables de serveur Apache mod_rewrite suivantes :

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Règles du module de réécriture d’URL IIS

Pour utiliser le même ensemble de règles que celui qui s’applique au module de réécriture d’URL IIS, utilisez <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>. Vérifiez que le fichier de règles est déployé avec l’application. N’indiquez pas au middleware d’utiliser le fichier *web.config* de l’application en cas d’exécution sur Windows Server IIS. Avec IIS, ces règles doivent être stockées en dehors du fichier *web.config* de l’application pour éviter les conflits avec le module de réécriture IIS. Pour obtenir plus d’informations et des exemples de règles du module de réécriture d’URL IIS, consultez [Utilisation du module de réécriture d’URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) et [Informations de référence sur la configuration du module de réécriture d’URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

Un <xref:System.IO.StreamReader> est utilisé pour lire les règles à partir du fichier de règles de *IISUrlRewrite.xml* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

L’exemple d’application réécrit les requêtes de `/iis-rules-rewrite/(.*)` vers `/rewritten?id=$1`. La réponse est envoyée au client avec le code d’état *200 - OK*.

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

Requête d’origine : `/iis-rules-rewrite/1234`

![Fenêtre de navigateur avec la requête et la réponse suivies par les Outils de développement](url-rewriting/_static/add_iis_url_rewrite.png)

Si vous avez un module de réécriture IIS actif pour lequel des règles au niveau du serveur qui affecteraient de façon non souhaitée votre application sont configurées, vous pouvez le désactiver pour une application. Pour plus d’informations, consultez [Désactivation de modules IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Fonctionnalités non prises en charge

L’intergiciel ne prend pas en charge les fonctionnalités de module de réécriture d’URL IIS suivantes :

* Règles de trafic sortant
* Variables serveur personnalisées
* Caractères génériques
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Variables de serveur prises en charge

L’intergiciel prend en charge les variables serveur du module de réécriture d’URL IIS suivantes :

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Vous pouvez également obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> par le biais d’un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>. Cette approche peut fournir davantage de flexibilité pour l’emplacement de vos fichiers de règles de réécriture. Vérifiez que vos fichiers de règles de réécriture sont déployés sur le serveur dans le chemin que vous fournissez.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Règle basée sur une méthode

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> pour implémenter votre propre logique de règle dans une méthode. `Add` expose <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, ce qui rend <xref:Microsoft.AspNetCore.Http.HttpContext> disponible pour une utilisation dans votre méthode. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) détermine la façon dont le traitement du pipeline supplémentaire est géré. Définissez la valeur sur un des champs <xref:Microsoft.AspNetCore.Rewrite.RuleResult> décrits dans le tableau suivant.

| Réécrire le résultat du contexte               | Action                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` (valeur par défaut) | Continuer à appliquer les règles.                                         |
| `RuleResult.EndResponse`             | Cesser d’appliquer les règles et envoyer la réponse.                       |
| `RuleResult.SkipRemainingRules`      | Cesser d’appliquer les règles et envoyer le contexte au middleware suivant. |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

L’exemple d’application présente une méthode qui redirige les requêtes de chemins qui se terminent par *.xml*. Si une requête est faite pour `/file.xml`, la requête est redirigée vers `/xmlfiles/file.xml`. Le code d’état est défini sur *301 - Déplacé de façon permanente*. Quand le navigateur fait une nouvelle requête pour */xmlfiles/file.xml*, le middleware de fichiers statiques délivre le fichier au client à partir du dossier *wwwroot/xmlfiles*. Pour une redirection, définissez explicitement le code d’état de la réponse. Sinon, un code d’état *200 - OK* est retourné et la redirection ne se produit pas sur le client.

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Cette approche peut également réécrire des requêtes. L’exemple d’application montre la réécriture du chemin pour toute requête demandant de délivrer le fichier texte *file.txt* à partir du dossier *wwwroot*. Le middleware de fichiers statiques délivre le fichier en fonction du chemin de requête mis à jour :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Règle basée sur IRule

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> pour insérer votre propre logique de règle dans une classe qui implémente l’interface <xref:Microsoft.AspNetCore.Rewrite.IRule>. `IRule` offre davantage de flexibilité par rapport à l’approche de la règle basée sur une méthode. Votre classe d’implémentation peut inclure un constructeur qui vous permet de passer des paramètres pour la méthode <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Les valeurs des paramètres dans l’exemple d’application pour `extension` et `newPath` sont vérifiées afin de remplir plusieurs conditions. `extension` doit contenir une valeur, laquelle doit être *.png*, *.jpg* ou *.gif*. Si `newPath` n’est pas valide, un <xref:System.ArgumentException> est levé. Si une requête est faite pour *image.png*, la requête est redirigée vers `/png-images/image.png`. Si une requête est faite pour *image.jpg*, la requête est redirigée vers `/jpg-images/image.jpg`. Le code d’état est défini sur *301 - Déplacé de façon permanente*, et `context.Result` est défini de façon à cesser le traitement des règles et envoyer la réponse.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Requête d’origine : `/image.png`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement pour image.png](url-rewriting/_static/add_redirect_png_requests.png)

Requête d’origine : `/image.jpg`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement pour image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Exemples d’expressions régulières

| Objectif | Chaîne d’expression régulière et<br>exemple de correspondance | Chaîne de remplacement et<br>exemple de sortie |
| ---- | ------------------------------- | -------------------------------------- |
| Réécrire le chemin dans la chaîne de requête | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Supprimer la barre oblique finale | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Appliquer une barre oblique finale | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Éviter la réécriture des requêtes spécifiques | `^(.*)(?<!\.axd)$` ou `^(?!.*\.axd$)(.*)$`<br>Oui : `/resource.htm`<br>Non : `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Réorganiser les segments d’URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Remplacer un segment d’URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ce document présente la réécriture d’URL avec des instructions sur la façon d’utiliser l’intergiciel (middleware) de réécriture d’URL dans les applications ASP.NET Core.

La réécriture d’URL consiste à modifier des URL de requête en fonction d’une ou de plusieurs règles prédéfinies. La réécriture d’URL crée une abstraction entre les emplacements des ressources et leurs adresses pour que les emplacements et les adresses ne soient pas étroitement liés. La réécriture d’URL est utile dans plusieurs scénarios pour :

* Déplacer ou remplacer de façon temporaire ou permanente les ressources d’un serveur, tout en conservant des localisateurs stables pour ces ressources.
* Diviser le traitement des requêtes entre différentes applications ou entre différentes parties d’une même application.
* Supprimer, ajouter ou réorganiser les segments d’URL sur des requêtes entrantes.
* Optimiser les URL publiques pour l’optimisation du référencement d’un site auprès d’un moteur de recherche (SEO).
* Permettre l’utilisation des URL publiques conviviales pour aider les visiteurs à prédire le contenu retourné en demandant une ressource.
* Rediriger des requêtes non sécurisées vers des points de terminaison sécurisés.
* Empêcher les liaisons à chaud, où un site externe utilise une ressource statique hébergée sur un autre site en liant la ressource dans son propre contenu.

> [!NOTE]
> La réécriture d’URL peut réduire les performances d’une application. Quand c’est possible, limitez le nombre et la complexité des règles.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Redirection d’URL et réécriture d’URL

La différence de formulation entre la *redirection d’URL* et la *réécriture d’URL* est subtile, mais elle a des implications importantes sur la fourniture de ressources aux clients. L’intergiciel de réécriture d’URL d’ASP.NET Core est capables de répondre aux besoins des deux.

Une *redirection d’URL* implique une opération côté client, où le client est invité à accéder à une ressource à une autre adresse que celle demandée à l’origine. Ceci nécessite un aller-retour avec le serveur. L’URL de redirection retournée au client s’affiche dans la barre d’adresse du navigateur quand le client effectue une nouvelle requête pour la ressource.

Si `/resource` est *redirigée* vers `/different-resource`, le serveur répond que le client doit obtenir la ressource à l’emplacement `/different-resource` avec un code d’état indiquant que la redirection est temporaire ou permanente.

![Un point de terminaison de service WebAPI a été changé temporairement de la version 1 (v1) à la version 2 (v2) sur le serveur. Un client effectue une requête au service dans le chemin /v1/api de la version 1. Le serveur renvoie une réponse 302 (Trouvé) avec le nouveau chemin temporaire du service dans le chemin /v2/api de la version 2. Le client effectue une deuxième requête au service à l’URL de redirection. Le serveur répond avec le code d’état 200 (OK).](url-rewriting/_static/url_redirect.png)

Lors de la redirection des requêtes vers une URL différente, indiquez si la redirection est permanente ou temporaire en spécifiant le code d’état avec la réponse :

* Le code d’état *301 - Déplacé de façon permanente* est utilisé quand la ressource a une nouvelle URL permanente et que vous voulez indiquer au client que toutes les requêtes futures pour la ressource doivent utiliser la nouvelle URL. *Le client peut mettre en cache et réutiliser la réponse quand un code d’état 301 est reçu.*

* Le code d’état *302 - Trouvé* est utilisé quand la redirection est temporaire ou généralement susceptible d’être modifiée. Le code d’état 302 indique au client de ne pas stocker l’URL et de ne plus l’utiliser.

Pour plus d’informations sur les codes d’état, consultez [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

Une *réécriture d’URL* est une opération côté serveur qui fournit une ressource à partir d’une adresse de ressource différente de celle demandée par le client. La réécriture d’URL ne nécessite pas d’aller-retour avec le serveur. L’URL réécrite n’est pas retournée au client et n’apparaît pas dans la barre d’adresse du navigateur.

Si `/resource` est *réécrite* en `/different-resource`, le serveur récupère la ressource *en interne* à l’emplacement `/different-resource`.

Même si le client peut récupérer la ressource à l’URL réécrite, il n’est pas informé que la ressource existe à l’URL réécrite quand il fait sa requête et reçoit la réponse.

![Un point de terminaison de service WebAPI a été changé de la version 1 (v1) à la version 2 (v2) sur le serveur. Un client effectue une requête au service dans le chemin /v1/api de la version 1. L’URL de requête est réécrite pour accéder au service dans le chemin /v2/api de la version 2. Le service répond au client avec le code d’état 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Exemple d’application de réécriture d’URL

Vous pouvez explorer les fonctionnalités du middleware de réécriture d’URL avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/). L’application applique des règles de redirection et de réécriture, et montre l’URL redirigée ou réécrite pour plusieurs scénarios.

## <a name="when-to-use-url-rewriting-middleware"></a>Quand utiliser l’intergiciel (middleware) de réécriture d’URL

Utilisez le middleware de réécriture d’URL quand vous ne pouvez pas utiliser les approches suivantes :

* [Module de réécriture d’URL avec IIS sur Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Module mod_rewrite Apache sur Apache Server](https://httpd.apache.org/docs/2.4/rewrite/)
* [URL de réécriture sur Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Utilisez aussi le middleware quand l’application est hébergée sur le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement appelé WebListener).

Les principales raisons d’utiliser les technologies de réécriture d’URL basée sur le serveur dans IIS, Apache et Nginx sont les suivantes :

* Le middleware ne prend pas en charge toutes les fonctionnalités de ces modules.

  Certaines des fonctionnalités des modules serveur ne fonctionnent pas avec les projets ASP.NET Core, comme les contraintes `IsFile` et `IsDirectory` du module Réécriture IIS. Dans ces scénarios, utilisez plutôt l’intergiciel.
* Les performances du middleware ne correspondent probablement pas à celles des modules.

  Mener des tests de performances est la seule façon de savoir exactement quelle approche dégrade le plus les performances ou si la dégradation des performances est négligeable.

## <a name="package"></a>Paquet

Pour inclure le middleware dans votre projet, ajoutez une référence de package au [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) dans le fichier projet, qui contient le package [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite).

Quand vous n’utilisez pas le métapackage `Microsoft.AspNetCore.App`, ajoutez une référence de projet au package `Microsoft.AspNetCore.Rewrite`.

## <a name="extension-and-options"></a>Extension et options

Établissez des règles de réécriture et de redirection d’URL en créant une instance de la classe [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) avec des méthodes d’extension pour chacune de vos règles de réécriture. Chaînez plusieurs règles dans l’ordre dans lequel vous voulez qu’elles soient traitées. Les `RewriteOptions` sont passées dans le middleware de réécriture d’URL quand il est ajouté au pipeline de requête avec <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Redirection de demandes non-www en demandes www

Trois options permettent à l’application de rediriger des demandes non-`www` en demandes `www` :

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Rediriger définitivement la demande vers le `www` sous-domaine si la demande n’est pas `www` . Redirige avec un code d’état [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirigez la demande vers le `www` sous-domaine si la demande entrante n’est pas `www` . Redirige avec un code d’état [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect). Une surcharge vous permet de fournir le code d’état pour la réponse. Utilisez un champ de la classe <xref:Microsoft.AspNetCore.Http.StatusCodes> pour une affectation de code d’état.

### <a name="url-redirect"></a>Redirection d’URL

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> pour rediriger des requêtes. Le premier paramètre contient votre expression régulière pour la mise en correspondance sur le chemin de l’URL entrante. Le deuxième paramètre est la chaîne de remplacement. Le troisième paramètre, le cas échéant, spécifie le code d’état. Si vous ne spécifiez pas le code d’état, sa valeur par défaut est *302 - Trouvé*, ce qui indique que la ressource est temporairement déplacée ou remplacée.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

Dans un navigateur dans lequel les outils de développement sont activés, effectuez une requête à l’exemple d’application avec le chemin `/redirect-rule/1234/5678`. L’expression régulière établit une correspondance avec le chemin de la requête sur `redirect-rule/(.*)`, et le chemin est remplacé par `/redirected/1234/5678`. L’URL de redirection est renvoyée au client avec le code d’état *302 - Trouvé*. Le navigateur effectue une nouvelle requête à l’URL de redirection, qui apparaît dans la barre d’adresse du navigateur. Comme aucune règle de l’exemple d’application ne correspond sur l’URL de redirection :

* La deuxième requête reçoit une réponse *200 - OK* de l’application.
* Le corps de la réponse montre l’URL de redirection.

Un aller-retour est effectué avec le serveur quand une URL est *redirigée*.

> [!WARNING]
> Soyez prudent lors de l’établissement de règles de redirection. Les règles de redirection sont évaluées à chaque requête effectuée à l’application, notamment après une redirection. Il est facile de créer accidentellement une *boucle de redirections infinies*.

Requête d’origine : `/redirect-rule/1234/5678`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect.png)

La partie de l’expression entre parenthèses est appelée *groupe de capture*. Le point (`.`) de l’expression signifie *mettre en correspondance n’importe quel caractère*. L’astérisque (`*`) indique *mettre en correspondance zéro occurrence ou plus du caractère précédent*. Par conséquent, les deux derniers segments de chemin de l’URL, `1234/5678`, sont capturés par le groupe de capture `(.*)`. Toute valeur fournie dans l’URL de la requête après `redirect-rule/` est capturée par ce groupe de capture unique.

Dans la chaîne de remplacement, les groupes capturés sont injectés dans la chaîne avec le signe dollar (`$`) suivi du numéro de séquence de la capture. La valeur du premier groupe de capture est obtenue avec `$1`, la deuxième avec `$2`, et ainsi de suite en séquence pour les groupes de capture de votre expression régulière. Comme il n’y a qu’un seul groupe capturé dans l’expression régulière de la règle de redirection de l’exemple d’application, un seul groupe est injecté dans la chaîne de remplacement, à savoir `$1`. Quand la règle est appliquée, l’URL devient `/redirected/1234/5678`.

### <a name="url-redirect-to-a-secure-endpoint"></a>Redirection d’URL vers un point de terminaison sécurisé

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> pour rediriger les requêtes HTTP vers le même hôte et le même chemin avec le protocole HTTPS. Si le code d’état n’est pas fourni, le middleware utilise par défaut *302 - Trouvé*. Si le port n’est pas fourni :

* Le middleware utilise par défaut `null`.
* Le schéma change en `https` (protocole HTTPS), et le client accède à la ressource sur le port 443.

L’exemple suivant montre comment définir le code d’état sur *301 - Déplacé de façon permanente* et changer le port en 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> pour rediriger les requêtes non sécurisées vers le même hôte et le même chemin avec le protocole HTTPS sécurisé sur le port 443. Le middleware définit le code d’état sur *301 - Déplacé de façon permanente*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Si vous effectuez une redirection vers un point de terminaison sécurisé et que vous n’avez pas besoin de règles de redirection supplémentaires, nous vous recommandons d’utiliser le middleware de redirection HTTPS. Pour plus d’informations, consultez la rubrique [Appliquer HTTPS](xref:security/enforcing-ssl#require-https).

L’exemple d’application peut montrer comment utiliser `AddRedirectToHttps` ou `AddRedirectToHttpsPermanent`. Ajoutez la méthode d’extension à `RewriteOptions`. Effectuez une requête non sécurisée à l’application à n’importe quelle URL. Ignorez l’avertissement de sécurité du navigateur indiquant que le certificat auto-signé n’est pas approuvé ou créez une exception pour approuver le certificat.

Requête d’origine utilisant `AddRedirectToHttps(301, 5001)` : `http://localhost:5000/secure`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect_to_https.png)

Requête d’origine utilisant `AddRedirectToHttpsPermanent` : `http://localhost:5000/secure`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Réécrire URL

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> pour créer une règle pour la réécriture d’URL. Le premier paramètre contient l’expression régulière pour la mise en correspondance sur le chemin de l’URL entrante. Le deuxième paramètre est la chaîne de remplacement. Le troisième paramètre, `skipRemainingRules: {true|false}`, indique à l’intergiciel d’ignorer, ou non, les règles de réécriture supplémentaires si la règle actuelle est appliquée.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Requête d’origine : `/rewrite-rule/1234/5678`

![Fenêtre de navigateur avec la requête et la réponse suivies par les Outils de développement](url-rewriting/_static/add_rewrite.png)

Le caret (`^`) au début de l’expression signifie que la correspondance commence au début du chemin de l’URL.

Dans l’exemple précédent avec la règle de redirection, `redirect-rule/(.*)`, il n’existe pas de caret (`^`) au début de l’expression régulière. Ainsi, n’importe quel caractère peut précéder `redirect-rule/` dans le chemin pour qu’une correspondance soit établie.

| Chemin d’accès                               | Faire correspondre |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Oui   |
| `/my-cool-redirect-rule/1234/5678` | Oui   |
| `/anotherredirect-rule/1234/5678`  | Oui   |

La règle de réécriture, `^rewrite-rule/(\d+)/(\d+)`, établit une correspondance uniquement avec des chemins d’accès s’ils commencent par `rewrite-rule/`. Dans le tableau suivant, notez la différence de correspondance.

| Chemin d’accès                              | Faire correspondre |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Oui   |
| `/my-cool-rewrite-rule/1234/5678` | Non    |
| `/anotherrewrite-rule/1234/5678`  | Non    |

À la suite de la partie `^rewrite-rule/` de l’expression se trouvent deux groupes de capture, `(\d+)/(\d+)`. `\d` signifie *établir une correspondance avec un chiffre (nombre)*. Le signe plus (`+`) signifie *établir une correspondance avec une ou plusieurs occurrences du caractère précédent*. Par conséquent, l’URL doit contenir un nombre suivi d’une barre oblique, elle-même suivie d’un autre nombre. Ces groupes sont injectés dans l’URL réécrite sous la forme `$1` et `$2`. La chaîne de remplacement de la règle de réécriture place les groupes capturés dans la chaîne de requête. Le chemin demandé `/rewrite-rule/1234/5678` est réécrit pour obtenir la ressource à l’emplacement `/rewritten?var1=1234&var2=5678`. Si une chaîne de requête est présente dans la requête d’origine, elle est conservée lors de la réécriture de l’URL.

Il n’y a pas d’aller-retour avec le serveur pour obtenir la ressource. Si la ressource existe, elle est récupérée et retournée au client avec le code d’état *200 - OK*. Comme le client n’est pas redirigé, l’URL dans la barre d’adresse du navigateur ne change pas. Les clients ne peuvent pas détecter qu’une opération de réécriture d’URL s’est produite sur le serveur.

> [!NOTE]
> Quand c’est possible, utilisez `skipRemainingRules: true`, car la mise en correspondance de règles est un processus gourmand en ressources qui augmente le temps de réponse de l’application. Pour obtenir la réponse d’application la plus rapide :
>
> * Classez vos règles de réécriture en partant de la règle la plus souvent mise en correspondance jusqu’à la règle la moins souvent mise en correspondance.
> * Ignorez le traitement des règles restantes quand une correspondance est trouvée et qu’aucun traitement de règle supplémentaire n’est nécessaire.

### <a name="apache-mod_rewrite"></a>Apache mod_rewrite

Appliquez des règles Apache mod_rewrite avec <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>. Vérifiez que le fichier de règles est déployé avec l’application. Pour obtenir plus d’informations et des exemples de règles mod_rewrite, consultez [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

Un <xref:System.IO.StreamReader> est utilisé pour lire les règles à partir du fichier de règles de *ApacheModRewrite.txt* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

L’exemple d’application redirige les requêtes de `/apache-mod-rules-redirect/(.\*)` vers `/redirected?id=$1`. Le code d’état de la réponse est *302 - Trouvé*.

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

Requête d’origine : `/apache-mod-rules-redirect/1234`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement](url-rewriting/_static/add_apache_mod_redirect.png)

L’intergiciel prend en charge les variables de serveur Apache mod_rewrite suivantes :

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Règles du module de réécriture d’URL IIS

Pour utiliser le même ensemble de règles que celui qui s’applique au module de réécriture d’URL IIS, utilisez <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>. Vérifiez que le fichier de règles est déployé avec l’application. N’indiquez pas au middleware d’utiliser le fichier *web.config* de l’application en cas d’exécution sur Windows Server IIS. Avec IIS, ces règles doivent être stockées en dehors du fichier *web.config* de l’application pour éviter les conflits avec le module de réécriture IIS. Pour obtenir plus d’informations et des exemples de règles du module de réécriture d’URL IIS, consultez [Utilisation du module de réécriture d’URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) et [Informations de référence sur la configuration du module de réécriture d’URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

Un <xref:System.IO.StreamReader> est utilisé pour lire les règles à partir du fichier de règles de *IISUrlRewrite.xml* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

L’exemple d’application réécrit les requêtes de `/iis-rules-rewrite/(.*)` vers `/rewritten?id=$1`. La réponse est envoyée au client avec le code d’état *200 - OK*.

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

Requête d’origine : `/iis-rules-rewrite/1234`

![Fenêtre de navigateur avec la requête et la réponse suivies par les Outils de développement](url-rewriting/_static/add_iis_url_rewrite.png)

Si vous avez un module de réécriture IIS actif pour lequel des règles au niveau du serveur qui affecteraient de façon non souhaitée votre application sont configurées, vous pouvez le désactiver pour une application. Pour plus d’informations, consultez [Désactivation de modules IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Fonctionnalités non prises en charge

L’intergiciel intégré à ASP.NET Core 2.x ne prend pas en charge les fonctionnalités de module de réécriture d’URL IIS suivantes :

* Règles de trafic sortant
* Variables serveur personnalisées
* Caractères génériques
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Variables de serveur prises en charge

L’intergiciel prend en charge les variables serveur du module de réécriture d’URL IIS suivantes :

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Vous pouvez également obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> par le biais d’un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>. Cette approche peut fournir davantage de flexibilité pour l’emplacement de vos fichiers de règles de réécriture. Vérifiez que vos fichiers de règles de réécriture sont déployés sur le serveur dans le chemin que vous fournissez.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Règle basée sur une méthode

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> pour implémenter votre propre logique de règle dans une méthode. `Add` expose <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, ce qui rend <xref:Microsoft.AspNetCore.Http.HttpContext> disponible pour une utilisation dans votre méthode. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) détermine la façon dont le traitement du pipeline supplémentaire est géré. Définissez la valeur sur un des champs <xref:Microsoft.AspNetCore.Rewrite.RuleResult> décrits dans le tableau suivant.

| Réécrire le résultat du contexte               | Action                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` (valeur par défaut) | Continuer à appliquer les règles.                                         |
| `RuleResult.EndResponse`             | Cesser d’appliquer les règles et envoyer la réponse.                       |
| `RuleResult.SkipRemainingRules`      | Cesser d’appliquer les règles et envoyer le contexte au middleware suivant. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

L’exemple d’application présente une méthode qui redirige les requêtes de chemins qui se terminent par *.xml*. Si une requête est faite pour `/file.xml`, la requête est redirigée vers `/xmlfiles/file.xml`. Le code d’état est défini sur *301 - Déplacé de façon permanente*. Quand le navigateur fait une nouvelle requête pour */xmlfiles/file.xml*, le middleware de fichiers statiques délivre le fichier au client à partir du dossier *wwwroot/xmlfiles*. Pour une redirection, définissez explicitement le code d’état de la réponse. Sinon, un code d’état *200 - OK* est retourné et la redirection ne se produit pas sur le client.

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Cette approche peut également réécrire des requêtes. L’exemple d’application montre la réécriture du chemin pour toute requête demandant de délivrer le fichier texte *file.txt* à partir du dossier *wwwroot*. Le middleware de fichiers statiques délivre le fichier en fonction du chemin de requête mis à jour :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Règle basée sur IRule

Utilisez <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> pour insérer votre propre logique de règle dans une classe qui implémente l’interface <xref:Microsoft.AspNetCore.Rewrite.IRule>. `IRule` offre davantage de flexibilité par rapport à l’approche de la règle basée sur une méthode. Votre classe d’implémentation peut inclure un constructeur qui vous permet de passer des paramètres pour la méthode <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Les valeurs des paramètres dans l’exemple d’application pour `extension` et `newPath` sont vérifiées afin de remplir plusieurs conditions. `extension` doit contenir une valeur, laquelle doit être *.png*, *.jpg* ou *.gif*. Si `newPath` n’est pas valide, un <xref:System.ArgumentException> est levé. Si une requête est faite pour *image.png*, la requête est redirigée vers `/png-images/image.png`. Si une requête est faite pour *image.jpg*, la requête est redirigée vers `/jpg-images/image.jpg`. Le code d’état est défini sur *301 - Déplacé de façon permanente*, et `context.Result` est défini de façon à cesser le traitement des règles et envoyer la réponse.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Requête d’origine : `/image.png`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement pour image.png](url-rewriting/_static/add_redirect_png_requests.png)

Requête d’origine : `/image.jpg`

![Fenêtre de navigateur avec les requêtes et les réponses suivies par les Outils de développement pour image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Exemples d’expressions régulières

| Objectif | Chaîne d’expression régulière et<br>exemple de correspondance | Chaîne de remplacement et<br>exemple de sortie |
| ---- | ------------------------------- | -------------------------------------- |
| Réécrire le chemin dans la chaîne de requête | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Supprimer la barre oblique finale | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Appliquer une barre oblique finale | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Éviter la réécriture des requêtes spécifiques | `^(.*)(?<!\.axd)$` ou `^(?!.*\.axd$)(.*)$`<br>Oui : `/resource.htm`<br>Non : `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Réorganiser les segments d’URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Remplacer un segment d’URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Expressions régulières dans .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Langage des expressions régulières-aide-mémoire](/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [Utilisation du module de réécriture d’URL 2.0 (pour IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [Informations de référence sur la configuration du module de réécriture d’URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [Forum du module de réécriture d’URL IIS](https://forums.iis.net/1152.aspx)
* [Maintenir une structure d’URL simple](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 conseils et astuces pour la réécriture d’URL](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [Mettre ou ne pas mettre une barre oblique](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
