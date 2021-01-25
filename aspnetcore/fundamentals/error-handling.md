---
title: Gérer les erreurs dans ASP.NET Core
author: rick-anderson
description: Découvrez comment gérer les erreurs dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: e65983fb1a440057283111ea5a79a79b765607b7
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751690"
---
# <a name="handle-errors-in-aspnet-core"></a>Gérer les erreurs dans ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Par [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)et [Steve Smith](https://ardalis.com/)

Cet article décrit les approches courantes de gestion des erreurs dans ASP.NET Core Web Apps. Consultez <xref:web-api/handle-errors> pour les API Web.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Comment télécharger](xref:index#how-to-download-a-sample).) L’onglet Réseau des outils de développement du navigateur F12 est utile lors du test de l’exemple d’application.

## <a name="developer-exception-page"></a>Page d’exceptions du développeur

La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes. Les modèles ASP.NET Core génèrent le code suivant :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

Le code en surbrillance précédent active la page d’exception du développeur lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments).

Les modèles <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> sont placés tôt dans le pipeline de l’intergiciel (middleware) afin de pouvoir intercepter les exceptions levées dans l’intergiciel (middleware) qui suit.

Le code précédent permet à la page d’exception de développeur ***only** _ lorsque l’application s’exécute dans l’environnement de développement. Les informations détaillées sur les exceptions ne doivent pas être affichées publiquement lorsque l’application s’exécute dans l’environnement de production. Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.

La page exception du développeur contient les informations suivantes sur l’exception et la requête :

_ Trace de la pile
* Paramètres de chaîne de requête, le cas échéant
* Cookiele cas échéant
* En-têtes

## <a name="exception-handler-page"></a>Page Gestionnaire d’exceptions

Pour configurer une page de gestion des erreurs personnalisée pour l' [environnement de production](xref:fundamentals/environments), appelez <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Cet intergiciel (middleware) de gestion des exceptions :

* Intercepte et consigne les exceptions.
* Réexécute la requête dans un autre pipeline en utilisant le chemin d’accès indiqué. La demande n’est pas réexécutée si la réponse a démarré. Le code généré par le modèle ré-exécute la demande à l’aide du `/Error` chemin d’accès.

Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ajoute l’intergiciel (middleware) de gestion des exceptions dans les environnements qui ne sont pas des environnements de développement :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Le Razor modèle d’application pages fournit une page d’erreur (*. cshtml*) et une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) dans le dossier *pages* . Pour une application MVC, le modèle de projet comprend une `Error` méthode d’action et un affichage des erreurs pour le contrôleur d’hébergement.

L’intergiciel (middleware) de gestion des exceptions réexécute la demande à l’aide de la méthode http *d’origine* . Si un point de terminaison de gestionnaire d’erreurs est limité à un ensemble spécifique de méthodes HTTP, il s’exécute uniquement pour ces méthodes HTTP. Par exemple, une action du contrôleur MVC qui utilise l' `[HttpGet]` attribut s’exécute uniquement pour les demandes d’extraction. Pour vous assurer que *toutes les* demandes atteignent la page de gestion des erreurs personnalisée, ne les limitez pas à un ensemble spécifique de méthodes http.

Pour gérer les exceptions différemment en fonction de la méthode HTTP d’origine :

* Pour les Razor pages, créez plusieurs méthodes de gestionnaire. Par exemple, utilisez `OnGet` pour gérer les exceptions d’extraction et utiliser `OnPost` pour gérer les exceptions de publication.
* Pour MVC, appliquez des attributs de verbe HTTP à plusieurs actions. Par exemple, utilisez `[HttpGet]` pour gérer les exceptions d’extraction et utiliser `[HttpPost]` pour gérer les exceptions de publication.

Pour autoriser les utilisateurs non authentifiés à afficher la page de gestion des erreurs personnalisée, assurez-vous qu’il prend en charge l’accès anonyme.

### <a name="access-the-exception"></a>Accéder à l'exception

Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin d’accès de la requête d’origine dans un gestionnaire d’erreurs. Le code suivant ajoute `ExceptionMessage` aux *pages/Error. cshtml. cs* par défaut générés par les modèles ASP.net Core :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients. Cela représenterait un risque de sécurité.

Pour tester l’exception dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Définissez l’environnement sur production.
* Supprimez les commentaires de `webBuilder.UseStartup<Startup>();` dans *Program.cs*.
* Sélectionnez **déclencher une exception** sur la page d’origine.

## <a name="exception-handler-lambda"></a>Expression lambda Gestionnaire d’exceptions

En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>, ce qui permet d’accéder à l’erreur avant de retourner la réponse.

Le code suivant utilise une expression lambda pour la gestion des exceptions :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients. Cela représenterait un risque de sécurité.

Pour tester le lambda de gestion des exceptions dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Définissez l’environnement sur production.
* Supprimez les commentaires de `webBuilder.UseStartup<StartupLambda>();` dans *Program.cs*.
* Sélectionnez **déclencher une exception** sur la page d’origine.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Par défaut, une ASP.NET Core application ne fournit pas de page de codes d’État pour les codes d’état d’erreur HTTP, par exemple *404-introuvable*. Lorsque l’application rencontre un code d’état d’erreur HTTP 400-599 qui n’a pas de corps, elle retourne le code d’État et un corps de réponse vide. Pour fournir des pages de codes d’État, utilisez l’intergiciel (middleware) pages de codes d’État. Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> dans la méthode `Startup.Configure` :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

Appelez `UseStatusCodePages` avant l’intergiciel (middleware) de gestion des demandes. Par exemple, appelez `UseStatusCodePages` avant l’intergiciel de fichiers statiques et l’intergiciel (middleware) des points de terminaison.

Lorsque `UseStatusCodePages` n’est pas utilisé, la navigation vers une URL sans point de terminaison retourne un message d’erreur dépendant du navigateur qui indique que le point de terminaison est introuvable. Par exemple, la navigation vers `Home/Privacy2` . Lorsque `UseStatusCodePages` est appelé, le navigateur retourne :

```html
Status Code: 404; Not Found
```

`UseStatusCodePages` n’est généralement pas utilisé en production, car il renvoie un message qui n’est pas utile aux utilisateurs.

Pour effectuer `UseStatusCodePages` un test dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Définissez l’environnement sur production.
* Supprimez les commentaires de `webBuilder.UseStartup<StartupUseStatusCodePages>();` dans *Program.cs*.
* Sélectionnez les liens sur la page d’hébergement sur la page d’hébergement.

> [!NOTE]
> L’intergiciel (middleware) des pages de codes d’État n’intercepte **pas** les exceptions. Pour fournir une page de gestion des erreurs personnalisée, utilisez la [page Gestionnaire d’exceptions](#exception-handler-page).

### <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages avec chaîne de format

Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend un type de contenu et une chaîne de format :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

Dans le code précédent, `{0}` est un espace réservé pour le code d’erreur.

`UseStatusCodePages` avec une chaîne de format n’est généralement pas utilisée en production, car elle retourne un message qui n’est pas utile aux utilisateurs.

Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupFormat>();` dans *Program.cs*.

### <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages avec expression lambda

Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend une expression lambda :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

`UseStatusCodePages` avec une expression lambda n’est généralement pas utilisée en production, car elle retourne un message qui n’est pas utile aux utilisateurs.

Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupStatusLambda>();` dans *Program.cs*.

### <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> :

* Envoie un code d’état [302 - Trouvé](https://developer.mozilla.org/docs/Web/HTTP/Status/302) au client.
* Redirige le client vers le point de terminaison de gestion des erreurs fourni dans le modèle d’URL. Le point de terminaison de gestion des erreurs affiche généralement les informations d’erreur et retourne HTTP 200.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

Le modèle d’URL peut inclure un `{0}` espace réservé pour le code d’État, comme indiqué dans le code précédent. Si le modèle d’URL commence par `~` (tilde), le `~` est remplacé par le de l’application `PathBase` . Lorsque vous spécifiez un point de terminaison dans l’application, créez une vue ou une Razor page MVC pour le point de terminaison. Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Cette méthode est généralement utilisée lorsque l’application :

* Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur. Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.
* Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.

Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupSCredirect>();` dans *Program.cs*.

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> :

* Retourne le code d’état d’origine au client.
* Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

Si un point de terminaison est spécifié dans l’application, créez une vue ou une Razor page MVC pour le point de terminaison. Vérifiez que `UseStatusCodePagesWithReExecute` est placé avant `UseRouting` que la demande puisse être redirigée vers la page d’État. Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Cette méthode est généralement utilisée lorsque l’application doit :

* Traiter la demande sans la rediriger vers un autre point de terminaison. Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.
* Conserver et retourner le code d’état d’origine avec la réponse.

Les modèles d’URL et de chaîne de requête peuvent inclure un espace réservé `{0}` pour le code d’État. Le modèle d’URL doit commencer par `/` .

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

Pour obtenir un Razor exemple de pages, consultez [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Pour tester `UseStatusCodePages` dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), supprimez les commentaires de `webBuilder.UseStartup<StartupSCreX>();` dans *Program.cs*.

## <a name="disable-status-code-pages"></a>Désactiver les pages de codes d’état

Pour désactiver les pages de codes d’état d’un contrôleur MVC ou d’une méthode d’action, utilisez l’attribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).

Pour désactiver les pages de codes d’État pour des demandes spécifiques dans une Razor méthode de gestionnaire de pages ou dans un contrôleur MVC, utilisez <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>Code de gestion des exceptions

Le code dans les pages de gestion des exceptions peut également lever des exceptions. Les pages d’erreurs de production doivent être testées minutieusement et prendre soin d’éviter de lever des exceptions propres.
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>En-têtes de réponse

Une fois les en-têtes d’une réponse envoyés :

* L’application ne peut pas modifier le code d’état de la réponse.
* Il est impossible d’exécuter les pages d’exception ou les gestionnaires. La réponse doit être accomplie ou la connexion abandonnée.

## <a name="server-exception-handling"></a>Gestion des exceptions de serveur

En plus de la logique de gestion des exceptions dans une application, l' [implémentation du serveur http](xref:fundamentals/servers/index) peut gérer certaines exceptions. Si le serveur intercepte une exception avant l’envoi des en-têtes de réponse, le serveur envoie une `500 - Internal Server Error` réponse sans corps de réponse. Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion. Les demandes qui ne sont pas gérées par l’application sont gérées par le serveur. Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur. Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.

## <a name="startup-exception-handling"></a>Gestion des exceptions de démarrage

Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application. L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).

La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port. Si la liaison échoue :

* La couche d’hébergement journalise une exception critique.
* Le processus dotnet tombe en panne.
* Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).

En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer. Pour plus d’informations, consultez <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Page d’erreur de base de données

Le filtre d’exception de la page développeur de bases de données `AddDatabaseDeveloperPageExceptionFilter` capture les exceptions liées aux bases de données qui peuvent être résolues à l’aide de Entity Framework Core migrations. Lorsque ces exceptions se produisent, une réponse HTML est générée avec des détails sur les actions possibles pour résoudre le problème. Cette page est activée uniquement dans l’environnement de développement. Le code suivant a été généré par les Razor modèles de Pages ASP.net core lorsque des comptes d’utilisateur individuels ont été spécifiés :

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>Filtres d’exceptions

Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action. Dans Razor les applications pages, elles peuvent être configurées globalement ou par modèle de page. Ces filtres gèrent les exceptions non gérées qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre. Pour plus d’informations, consultez <xref:mvc/controllers/filters#exception-filters>.

Les filtres d’exception sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais elles ne sont pas aussi flexibles que l’intergiciel (middleware) de [gestion des exceptions](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)intégré `UseExceptionHandler` . Nous vous recommandons `UseExceptionHandler` d’utiliser, sauf si vous devez effectuer la gestion des erreurs différemment en fonction de l’action MVC choisie.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>Erreurs d’état de modèle

Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Par  [Tom Dykstra](https://github.com/tdykstra/)et [Steve Smith](https://ardalis.com/)

Cet article décrit les approches courantes de gestion des erreurs dans ASP.NET Core Web Apps. Consultez <xref:web-api/handle-errors> pour les API Web.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Guide pratique de téléchargement](xref:index#how-to-download-a-sample).)

## <a name="developer-exception-page"></a>Page d’exceptions du développeur

La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes. Les modèles ASP.NET Core génèrent le code suivant :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Le code précédent permet à la page d’exception de développeur lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments).

Les modèles <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> sont placés avant tout intergiciel, de sorte que les exceptions sont interceptées dans l’intergiciel (middleware) qui suit.

Le code précédent active la page d’exception du développeur **uniquement lorsque l’application s’exécute dans l’environnement de développement**. Les informations détaillées sur les exceptions ne doivent pas être affichées publiquement lorsque l’application s’exécute en production. Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.

La page exception du développeur contient les informations suivantes sur l’exception et la requête :

* Arborescence des appels de procédure
* Paramètres de chaîne de requête, le cas échéant
* Cookiele cas échéant
* En-têtes

## <a name="exception-handler-page"></a>Page Gestionnaire d’exceptions

Pour configurer une page de gestion des erreurs personnalisée en fonction de l’environnement de production, utilisez le middleware de gestion des exceptions. Le middleware :

* Intercepte et consigne les exceptions.
* Réexécute la requête dans un autre pipeline pour la page ou le contrôleur indiqué(e). La demande n’est pas réexécutée si la réponse a démarré. Le code généré par le modèle ré-exécute la demande à `/Error` .

Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ajoute le middleware de gestion des exceptions dans des environnements autres que les environnements de développement :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Le Razor modèle d’application pages fournit une page d’erreur (*. cshtml*) et une <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) dans le dossier *pages* . Pour une application MVC, le modèle de projet comprend une méthode d’action d’erreur et un affichage des erreurs dans le contrôleur d’hébergement.

Ne Marquez pas la méthode d’action du gestionnaire d’erreurs avec des attributs de méthode HTTP, tels que `HttpGet` . Les verbes explicites empêchent certaines demandes d’atteindre la méthode. Autorisez l’accès anonyme à la méthode si les utilisateurs non authentifiés doivent voir l’affichage des erreurs.

### <a name="access-the-exception"></a>Accéder à l'exception

Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin de la demande d’origine dans une page ou un contrôleur de gestionnaire d’erreurs :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients. Cela représenterait un risque de sécurité.

Pour déclencher la page de gestion des exceptions précédente, définissez l’environnement sur productions et forcez une exception.

## <a name="exception-handler-lambda"></a>Expression lambda Gestionnaire d’exceptions

En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>, ce qui permet d’accéder à l’erreur avant de retourner la réponse.

Voici un exemple d’utilisation d’une expression lambda pour la gestion des exceptions :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

Dans le code précédent, `await context.Response.WriteAsync(new string(' ', 512));` est ajouté afin que le navigateur Internet Explorer affiche le message d’erreur plutôt qu’un message d’erreur IE. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients. Cela représenterait un risque de sécurité.

Pour afficher le résultat de l’expression lambda de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerLambda` et sélectionnez **Déclencher une exception** sur la page d’accueil.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Par défaut, une application ASP.NET Core ne fournit pas une page de codes d’état pour les codes d’état HTTP, comme *404 - Introuvable*. L’application retourne un code d’état et un corps de réponse vide. Pour fournir des pages de codes d’état, utilisez le middleware Pages de codes d’état.

L’intergiciel est mis à disposition par le package [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .

Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> dans la méthode `Startup.Configure` :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Appelez `UseStatusCodePages` avant les middlewares de gestion des demandes (par exemple, le middleware de fichiers statiques et le middleware MVC).

Lorsque `UseStatusCodePages` n’est pas utilisé, la navigation vers une URL sans point de terminaison retourne un message d’erreur dépendant du navigateur qui indique que le point de terminaison est introuvable. Par exemple, la navigation vers `Home/Privacy2` . Lorsque `UseStatusCodePages` est appelé, le navigateur retourne :

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages avec chaîne de format

Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend un type de contenu et une chaîne de format :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages avec expression lambda

Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> qui prend une expression lambda :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> :

* Envoie un code d’état *302 - Trouvé* au client.
* Redirige le client à l’emplacement fourni dans le modèle d’URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Le modèle d’URL peut comporter un espace réservé `{0}` pour le code d’état, comme dans l’exemple. Si le modèle d’URL commence par `~` (tilde), le `~` est remplacé par le de l’application `PathBase` . Si vous pointez vers un point de terminaison au sein de l’application, créez une vue ou une Razor page MVC pour le point de terminaison. Pour obtenir un Razor exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Cette méthode est généralement utilisée lorsque l’application :

* Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur. Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.
* Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> :

* Retourne le code d’état d’origine au client.
* Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Si vous pointez vers un point de terminaison au sein de l’application, créez une vue ou une Razor page MVC pour le point de terminaison. Vérifiez que `UseStatusCodePagesWithReExecute` est placé avant `UseRouting` que la demande puisse être redirigée vers la page d’État. Pour obtenir un Razor exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Cette méthode est généralement utilisée lorsque l’application doit :

* Traiter la demande sans la rediriger vers un autre point de terminaison. Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.
* Conserver et retourner le code d’état d’origine avec la réponse.

Les modèles d’URL et de chaîne de requête peuvent comporter un espace réservé (`{0}`) pour le code d’état. Le modèle d’URL doit commencer par une barre oblique (`/`). Si vous utilisez un espace réservé dans le chemin, vérifiez que le point de terminaison (page ou contrôleur) peut traiter le segment de chemin. Par exemple, une Razor page pour les erreurs doit accepter la valeur de segment de chemin d’accès facultative avec la `@page` directive :

```cshtml
@page "{code?}"
```

Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Désactiver les pages de codes d’état

Pour désactiver les pages de codes d’État pour un contrôleur MVC ou une méthode d’action, utilisez l' [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribut.

Pour désactiver les pages de codes d’État pour des demandes spécifiques dans une Razor méthode de gestionnaire de pages ou dans un contrôleur MVC, utilisez <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Code de gestion des exceptions

Le code dans les pages de gestion des exceptions peut lever des exceptions. Il est souvent judicieux de mettre dans les pages d’erreur de production du contenu purement statique.

### <a name="response-headers"></a>En-têtes de réponse

Une fois les en-têtes d’une réponse envoyés :

* L’application ne peut pas modifier le code d’état de la réponse.
* Il est impossible d’exécuter les pages d’exception ou les gestionnaires. La réponse doit être accomplie ou la connexion abandonnée.

## <a name="server-exception-handling"></a>Gestion des exceptions de serveur

En plus de la logique de gestion des exceptions de votre application, [l’implémentation de serveur HTTP](xref:fundamentals/servers/index) peut gérer certaines exceptions. Si le serveur intercepte une exception avant que les en-têtes de réponse ne soient envoyés, le serveur envoie une réponse *500 Erreur interne du serveur* sans corps de réponse. Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion. Les demandes qui ne sont pas gérées par votre application sont gérées par le serveur. Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur. Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.

## <a name="startup-exception-handling"></a>Gestion des exceptions de démarrage

Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application. L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).

La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port. Si la liaison échoue :

* La couche d’hébergement journalise une exception critique.
* Le processus dotnet tombe en panne.
* Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).

En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer. Pour plus d’informations, consultez <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Page d’erreur de base de données

L’intergiciel (middleware) de page d’erreur de base de données capture des exceptions liées à la base de données qui peuvent être résolues à l’aide de Entity Framework migrations. Lorsque ces exceptions se produisent, une réponse HTML comportant le détail des actions possibles pour résoudre le problème est générée. Cette page ne doit être activée que dans l’environnement de développement. Pour cela, ajoutez du code à `Startup.Configure` :

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requiert le package NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtres d’exceptions

Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action. Dans Razor les applications pages, elles peuvent être configurées globalement ou par modèle de page. Ces filtres gèrent les exceptions non prises en charge qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre. Pour plus d’informations, consultez <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Les filtres d’exceptions sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais n’offrent pas la même souplesse que le middleware de gestion des exceptions. Nous vous recommandons d’utiliser le middleware. N’utilisez des filtres que si vous devez gérer les erreurs différemment en fonction de l’action MVC choisie.

## <a name="model-state-errors"></a>Erreurs d’état de modèle

Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
