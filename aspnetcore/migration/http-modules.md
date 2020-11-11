---
title: Migrer des gestionnaires et des modules HTTP vers ASP.NET Core intergiciel
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
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
uid: migration/http-modules
ms.openlocfilehash: 4abba1d4304bf537bd96623527c851d9d15774a4
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508160"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migrer des gestionnaires et des modules HTTP vers ASP.NET Core intergiciel

Cet article explique comment migrer [des modules et des gestionnaires HTTP ASP.NET existants à partir de System. webServer](/iis/configuration/system.webserver/) vers ASP.net Core [intergiciel](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Modules et gestionnaires revisités

Avant de passer à ASP.NET Core intergiciel, nous allons tout d’abord récapituler le fonctionnement des modules et gestionnaires HTTP :

![Gestionnaire de modules](http-modules/_static/moduleshandlers.png)

**Les gestionnaires sont les suivants :**

* Classes qui implémentent [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Utilisé pour gérer les demandes avec un nom de fichier ou une extension donné (par exemple, *. Report* )

* [Configuré](/iis/configuration/system.webserver/handlers/) dans *Web.config*

**Les modules sont les suivants :**

* Classes qui implémentent [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* Appelé pour chaque requête

* Possibilité de court-circuit (arrêter le traitement d’une demande)

* Peut être ajouté à la réponse HTTP ou créer son propre

* [Configuré](/iis/configuration/system.webserver/modules/) dans *Web.config*

**L’ordre dans lequel les modules traitent les demandes entrantes est déterminé par :**

1. <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>, Qui est un événement de série déclenché par ASP.net : [beginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Chaque module peut créer un gestionnaire pour un ou plusieurs événements.

2. Pour le même événement, ordre dans lequel ils sont configurés dans *Web.config*.

En plus des modules, vous pouvez ajouter des gestionnaires pour les événements de cycle de vie à votre fichier *global.asax.cs* . Ces gestionnaires s’exécutent après les gestionnaires dans les modules configurés.

## <a name="from-handlers-and-modules-to-middleware"></a>Des gestionnaires et des modules à l’intergiciel (middleware)

**Les intergiciels (middleware) sont plus simples que les gestionnaires et les modules HTTP :**

* Les modules, les gestionnaires, les *global.asax.cs* , les *Web.config* (à l’exception de la configuration IIS) et le cycle de vie de l’application ont disparu

* Les rôles des modules et des gestionnaires ont été pris en charge par l’intergiciel (middleware)

* Les intergiciels (middleware) sont configurés à l’aide du code plutôt que dans *Web.config*

::: moniker range=">= aspnetcore-3.0"

* La [branche de pipeline](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) vous permet d’envoyer des demandes à un intergiciel (middleware) spécifique, en fonction de l’URL, mais également des en-têtes de demande, des chaînes de requête, etc.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* La [branche de pipeline](xref:fundamentals/middleware/index#use-run-and-map) vous permet d’envoyer des demandes à un intergiciel (middleware) spécifique, en fonction de l’URL, mais également des en-têtes de demande, des chaînes de requête, etc.

::: moniker-end

**Les intergiciels sont très similaires aux modules :**

* Appelé en principe pour chaque requête

* Capable de court-circuiter une requête, en [ne transmettant pas la demande à l’intergiciel suivant](#http-modules-shortcircuiting-middleware)

* Possibilité de créer leur propre réponse HTTP

**Les intergiciels et les modules sont traités dans un ordre différent :**

* L’ordre des intergiciels est basé sur l’ordre dans lequel ils sont insérés dans le pipeline de demande, alors que l’ordre des modules est principalement basé sur les <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> événements

* L’ordre de l’intergiciel pour les réponses est l’inverse par rapport à celui des demandes, tandis que l’ordre des modules est le même pour les demandes et les réponses

* Consultez [création d’un pipeline d’intergiciel (middleware) avec IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Middleware](http-modules/_static/middleware.png)

Notez comment dans l’image ci-dessus, l’intergiciel (middleware) d’authentification a court-circuité la demande.

## <a name="migrating-module-code-to-middleware"></a>Migration du code du module vers l’intergiciel (middleware)

Un module HTTP existant doit ressembler à ceci :

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Comme indiqué dans la page [intergiciel (middleware](xref:fundamentals/middleware/index) ), un intergiciel (middleware) ASP.net Core est une classe qui expose une méthode qui `Invoke` prend un `HttpContext` et retourne un `Task` . Votre nouvel intergiciel ressemble à ce qui suit :

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Le modèle d’intergiciel (middleware) précédent a été extrait de la section sur l’écriture d’un [intergiciel (middleware](xref:fundamentals/middleware/write)).

La classe d’assistance *MyMiddlewareExtensions* facilite la configuration de votre intergiciel dans votre `Startup` classe. La `UseMyMiddleware` méthode ajoute votre classe middleware au pipeline de requête. Les services requis par l’intergiciel (middleware) sont injectés dans le constructeur de l’intergiciel (middleware).

<a name="http-modules-shortcircuiting-middleware"></a>

Votre module peut mettre fin à une demande, par exemple si l’utilisateur n’est pas autorisé :

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Un intergiciel gère cela en n’appelant pas `Invoke` sur l’intergiciel (middleware) suivant dans le pipeline. N’oubliez pas que cela ne termine pas complètement la requête, car les intergiciels précédents sont toujours appelés quand la réponse fait remonter le pipeline.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Lorsque vous migrez les fonctionnalités de votre module vers votre nouvel intergiciel, vous constaterez peut-être que votre code n’est pas compilé, car la `HttpContext` classe a été considérablement modifiée dans ASP.net core. [Plus tard](#migrating-to-the-new-httpcontext), vous verrez comment migrer vers le nouveau ASP.net Core HttpContext.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migration de l’insertion du module dans le pipeline de requête

Les modules HTTP sont généralement ajoutés au pipeline de requêtes à l’aide de *Web.config* :

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Convertissez ceci en [ajoutant votre nouvel intergiciel](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) au pipeline de requête dans votre `Startup` classe :

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

L’emplacement exact dans le pipeline où vous insérez votre nouvel intergiciel dépend de l’événement qu’il a traité en tant que module ( `BeginRequest` , `EndRequest` , etc.) et de son ordre dans la liste des modules de *Web.config*.

Comme indiqué précédemment, il n’existe aucun cycle de vie d’application dans ASP.NET Core et l’ordre dans lequel les réponses sont traitées par l’intergiciel diffère de l’ordre utilisé par les modules. Cela pourrait rendre votre décision de classement plus complexe.

Si le classement devient un problème, vous pouvez fractionner votre module en plusieurs composants de l’intergiciel (middleware) qui peuvent être classés indépendamment.

## <a name="migrating-handler-code-to-middleware"></a>Migration du code du gestionnaire vers l’intergiciel (middleware)

Un gestionnaire HTTP ressemble à ceci :

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

Dans votre projet ASP.NET Core, vous pouvez traduire cela en un intergiciel (middleware) semblable à ce qui suit :

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Cet intergiciel est très similaire à l’intergiciel (middleware) correspondant aux modules. La seule différence réelle est qu’il n’y a pas d’appel à `_next.Invoke(context)` . Cela est logique, étant donné que le gestionnaire se trouve à la fin du pipeline de requête, il n’y aura donc aucun middleware suivant à appeler.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migration de l’insertion du gestionnaire dans le pipeline de requête

La configuration d’un gestionnaire HTTP est effectuée dans *Web.config* et ressemble à ceci :

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Vous pouvez convertir cela en ajoutant votre nouvel intergiciel de gestionnaire au pipeline de requête de votre `Startup` classe, similaire aux middlewares convertis à partir de modules. Le problème avec cette approche est qu’elle envoie toutes les demandes à votre intergiciel de gestionnaire. Toutefois, vous souhaitez que les demandes avec une extension donnée atteignent votre intergiciel (middleware). Cela vous donnera les mêmes fonctionnalités que celles que vous aviez avec votre gestionnaire HTTP.

Une solution consiste à créer une branche du pipeline pour les requêtes avec une extension donnée, à l’aide de la `MapWhen` méthode d’extension. Pour ce faire, utilisez la même `Configure` méthode que celle où vous ajoutez l’autre intergiciel :

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` prend les paramètres suivants :

1. Une expression lambda qui prend le `HttpContext` et retourne la valeur `true` si la demande doit descendre dans la branche. Cela signifie que vous pouvez créer des branches de requêtes non seulement en fonction de leur extension, mais également sur des en-têtes de demande, des paramètres de chaîne de requête, etc.

2. Lambda qui prend un `IApplicationBuilder` et ajoute tous les intergiciels (middleware) pour la branche. Cela signifie que vous pouvez ajouter un intergiciel (middleware) supplémentaire à la branche devant l’intergiciel (middleware) de votre gestionnaire.

Intergiciel ajouté au pipeline avant que la branche ne soit appelée sur toutes les demandes ; la branche n’aura aucun impact sur ces dernières.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Chargement des options de l’intergiciel (middleware) à l’aide du modèle options

Certains modules et gestionnaires possèdent des options de configuration qui sont stockées dans *Web.config*. Toutefois, dans ASP.NET Core un nouveau modèle de configuration est utilisé à la place de *Web.config*.

Le nouveau [système de configuration](xref:fundamentals/configuration/index) vous offre les options suivantes pour résoudre ce qui suit :

* Injectez directement les options dans l’intergiciel (middleware), comme indiqué dans la [section suivante](#loading-middleware-options-through-direct-injection).

* Utilisez le [modèle d’options](xref:fundamentals/configuration/options):

1. Créez une classe pour contenir vos options d’intergiciel, par exemple :

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Stocker les valeurs d’option

   Le système de configuration vous permet de stocker les valeurs d’option où vous le souhaitez. Toutefois, la plupart des sites utilisent *appsettings.json* . nous allons donc adopter cette approche :

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* ici est un nom de section. Il ne doit pas nécessairement être le même que le nom de votre classe d’options.

3. Associer les valeurs d’option à la classe options

    Le modèle d’options utilise l’infrastructure d’injection de dépendances de ASP.NET Core pour associer le type d’options (tel que `MyMiddlewareOptions` ) à un `MyMiddlewareOptions` objet qui a les options réelles.

    Mettez à jour votre `Startup` classe :

   1. Si vous utilisez *appsettings.json* , ajoutez-le au générateur de configuration dans le `Startup` constructeur :

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Configurer le service d’options :

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Associez vos options à votre classe d’options :

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Injectez les options dans votre constructeur d’intergiciel. Cela revient à injecter des options dans un contrôleur.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   La méthode d’extension [UseMiddleware](#http-modules-usemiddleware) qui ajoute votre intergiciel à l' `IApplicationBuilder` prend en charge l’injection de dépendances.

   Cela n’est pas limité aux `IOptions` objets. Tout autre objet requis par votre intergiciel peut être injecté de cette façon.

## <a name="loading-middleware-options-through-direct-injection"></a>Chargement des options d’intergiciel par injection directe

Le modèle d’options présente l’avantage de créer un couplage faible entre les valeurs d’options et leurs consommateurs. Une fois que vous avez associé une classe options avec les valeurs d’options réelles, toute autre classe peut accéder aux options par le biais de l’infrastructure d’injection de dépendances. Il n’est pas nécessaire de transmettre des valeurs d’options.

Cela s’arrête si vous souhaitez utiliser le même intergiciel deux fois, avec des options différentes. Par exemple, un intergiciel d’autorisation utilisé dans différentes branches autorisant des rôles différents. Vous ne pouvez pas associer deux objets d’options différents à la classe d’options.

La solution consiste à récupérer les objets d’options avec les valeurs d’options réelles dans votre `Startup` classe et à les transmettre directement à chaque instance de votre intergiciel.

1. Ajouter une deuxième clé à *appsettings.json*

   Pour ajouter un deuxième ensemble d’options au *appsettings.json* fichier, utilisez une nouvelle clé pour l’identifier de manière unique :

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Récupérez les valeurs des options et transmettez-les à l’intergiciel (middleware). La `Use...` méthode d’extension (qui ajoute votre intergiciel au pipeline) est un emplacement logique à passer dans les valeurs des options : 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Activez l’intergiciel (middleware) pour accepter un paramètre d’options. Fournissez une surcharge de la `Use...` méthode d’extension (qui accepte le paramètre options et le passe à `UseMiddleware` ). Lorsque `UseMiddleware` est appelé avec des paramètres, il passe les paramètres à votre constructeur middleware lorsqu’il instancie l’objet intergiciel.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Notez comment cela encapsule l’objet d’options dans un `OptionsWrapper` objet. Cela implémente `IOptions` , comme attendu par le constructeur d’intergiciel.

## <a name="migrating-to-the-new-httpcontext"></a>Migration vers le nouveau HttpContext

Vous avez vu précédemment que la `Invoke` méthode de votre intergiciel prend un paramètre de type `HttpContext` :

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` a été considérablement modifié dans ASP.NET Core. Cette section montre comment convertir les propriétés les plus couramment utilisées de [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) vers la nouvelle `Microsoft.AspNetCore.Http.HttpContext` .

### <a name="httpcontext"></a>HttpContext

**HttpContext. Items** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**ID de demande unique (aucun équivalent System. Web. HttpContext)**

Donne un ID unique pour chaque demande. Très utile pour inclure dans vos journaux.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext. Request

**HttpContext. Request. HttpMethod** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext. Request. QueryString** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext. Request. URL** et **HttpContext. Request. RawUrl** se traduisent par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. Request. IsSecureConnection** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. Request. UserHostAddress** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext. Request. Cookie s** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext. Request. RequestContext. RouteData** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**Les en-têtes HttpContext. Request.** sont traduits par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext. Request. UserAgent** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext. Request. UrlReferrer** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext. Request. ContentType** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext. Request. Form** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Lire les valeurs de formulaire uniquement si le sous-type de contenu est *x-www-form-urlencoded* ou *Form-Data*.

**HttpContext. Request. InputStream** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Utilisez ce code uniquement dans un intergiciel de type de gestionnaire, à la fin d’un pipeline.
>
>Vous pouvez lire le corps brut comme indiqué ci-dessus une seule fois par requête. L’intergiciel tentant de lire le corps après la première lecture lira un corps vide.
>
>Cela ne s’applique pas à la lecture d’un formulaire comme indiqué plus haut, car cela s’effectue à partir d’une mémoire tampon.

### <a name="httpcontextresponse"></a>HttpContext. Response

**HttpContext. Response. Status** et **HttpContext. Response. StatusDescription** se traduisent par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext. Response. ContentEncoding** et **HttpContext. Response. ContentType** se traduisent par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext. Response. ContentType** se traduit également par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext. Response. Output** se traduit par :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext. Response. TransmitFile**

La mise en service d’un fichier est décrite dans <xref:fundamentals/request-features> .

**HttpContext. Response. en-têtes**

L’envoi d’en-têtes de réponse est compliqué par le fait que si vous les définissez après quoi tout a été écrit dans le corps de la réponse, ils ne sont pas envoyés.

La solution consiste à définir une méthode de rappel qui sera appelée juste avant le début de l’écriture dans la réponse. Cette opération est effectuée au début de la `Invoke` méthode dans votre intergiciel. C’est cette méthode de rappel qui définit vos en-têtes de réponse.

Le code suivant définit une méthode de rappel appelée `SetHeaders` :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

La `SetHeaders` méthode de rappel ressemble à ceci :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext. Response. Cookie x**

Cookiese déplace vers le navigateur dans un en-tête *Set- Cookie* Response. Par conséquent, l’envoi cookie de s requiert le même rappel que celui utilisé pour l’envoi des en-têtes de réponse :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

La `SetCookies` méthode de rappel ressemble à ce qui suit :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Vue d’ensemble des gestionnaires HTTP et des modules HTTP](/iis/configuration/system.webserver/)
* [Configuration](xref:fundamentals/configuration/index)
* [Démarrage de l'application](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
